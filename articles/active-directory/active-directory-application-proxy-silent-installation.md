---
title: "Azure AD アプリケーション プロキシ コネクタのサイレント インストール | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの無人インストールを実行して、オンプレミスのアプリへの安全なリモート アクセスを実現する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 9e28c89d8f64f0ae3d4150017ca544e606075c45
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="silently-install-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタのサイレント インストール
複数の Windows サーバーまたはユーザー インターフェイスが有効になっていない Windows サーバーにインストール スクリプトを送信できます。 このトピックでは、Azure AD アプリケーション プロキシ コネクタの無人インストールおよび登録を有効にする Windows PowerShell スクリプトの作成方法について説明します。

この機能は次の場合に便利です。

* UI レイヤーがないコンピューターや、RDP 経由で接続できないコンピューターにコネクタをインストールする。
* 一度に多数のコネクタをインストールし、登録する。
* コネクタのインストールと登録を別の手順の一部として統合する。
* コネクタのビットを含むが未登録の標準的なサーバー イメージを作成する。

アプリケーション プロキシは、社内ネットワークに "コネクタ" と呼ばれる軽量の Windows Server サービスをインストールすることによって機能します。 アプリケーション プロキシ コネクタを機能させるには、グローバル管理者のアカウントとパスワードを使用して、Azure AD ディレクトリにコネクタを登録する必要があります。 通常、この情報は、コネクタのインストール時にポップアップ ダイアログ ボックスに入力します。 ただし、Windows PowerShell を使用して、登録情報を入力する資格情報オブジェクトを作成できます。 または、独自のトークンを作成し、それを使用して登録情報を入力できます。

## <a name="install-the-connector"></a>コネクタをインストールする
次のように、コネクタを登録せずにコネクタ MSI をインストールします。

1. コマンド プロンプトを開きます。
2. 次のコマンドを実行します。/q はサイレント インストールを意味します。つまり、インストールで使用許諾契約書への同意を求めるメッセージは表示されません。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>コネクタを Azure AD に登録する
コネクタを登録するために使用できる方法は 2 つあります。

* Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
* オフラインで作成したトークンを使用してコネクタを登録する

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
1. 次のコマンドを実行して Windows PowerShell 資格情報オブジェクトを作成します。 *\<username\>* と *\<password\>* は、ディレクトリのユーザー名とパスワードに置き換えてください。
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、作成済みの PowerShell 資格情報オブジェクトを使用してスクリプトを実行します。 *$cred* は、作成した PowerShell 資格情報オブジェクトの名前に置き換えてください。
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>オフラインで作成したトークンを使用してコネクタを登録する
1. コード スニペットの値を使用する AuthenticationContext クラスを使用して、オフライン トークンを作成します。

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }


2. トークンを作成したら、そのトークンを使用して SecureString を作成します。

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. \<tenant GUID\> をご使用のディレクトリ ID で置き換えて、次の Windows PowerShell コマンドを実行します。

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>次のステップ 
* [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)



