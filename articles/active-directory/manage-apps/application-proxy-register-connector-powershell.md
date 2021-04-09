---
title: Azure AD アプリケーション プロキシ コネクタのサイレント インストール | Microsoft Docs
description: Azure AD アプリケーション プロキシ コネクタの無人インストールを実行して、オンプレミスのアプリへの安全なリモート アクセスを実現する方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258152"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Azure AD アプリケーション プロキシ コネクタ用の無人インストール スクリプトを作成します。

このトピックでは、Azure AD アプリケーション プロキシ コネクタの無人インストールと登録を可能にする Windows PowerShell スクリプトの作成方法について説明します。

この機能は次の場合に便利です。

* ユーザー インターフェイスが有効でない、または Remote Desktop でアクセスできない Windows サーバーにコネクタをインストールする。
* 一度に多数のコネクタをインストールし、登録する。
* コネクタのインストールと登録を別の手順の一部として統合する。
* コネクタのビットを含むが未登録の標準的なサーバー イメージを作成する。

[アプリケーション プロキシ コネクタ](application-proxy-connectors.md)を機能させるには、アプリケーション管理者のアカウントとパスワードを使用して、Azure AD ディレクトリにコネクタを登録する必要があります。 通常、この情報は、コネクタのインストール時にポップアップ ダイアログ ボックスに入力しますが、このプロセスを PowerShell を使用して自動化できます。

無人インストールには 2 つの手順があります。 最初に、コネクタをインストールします。 次に、コネクタを Azure AD に登録します。

> [!IMPORTANT]
> Azure Government クラウドのコネクタをインストールする場合は、[前提条件](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls)と[インストール手順](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)を確認します。 これには、別の URL のセットへのアクセスを有効にし、インストールを実行するための追加のパラメーターが必要です。

## <a name="install-the-connector"></a>コネクタをインストールする
次の手順に従って、コネクタを登録なしでインストールします。

1. コマンド プロンプトを開きます。
2. 次のコマンドを実行します。/q はサイレント インストールを意味します。 サイレント インストールでは、使用許諾契約書への同意を求めるメッセージは表示されません。

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>コネクタを Azure AD に登録する
コネクタを登録するために使用できる方法は 2 つあります。

* Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
* オフラインで作成したトークンを使用してコネクタを登録する

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 資格情報オブジェクトを使用してコネクタを登録する
1. 管理者のユーザー名とディレクトリのパスワードを格納する Windows PowerShell 資格情報オブジェクト `$cred` を作成します。 次のコマンドの *\<username\>* と *\<password\>* を置き換えて実行します。

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、作成済みの `$cred` オブジェクトを使用してスクリプトを実行します。

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>オフラインで作成したトークンを使用してコネクタを登録する
1. 次のコード スニペットの値または以下の PowerShell コマンドレットを使って、AuthenticationContext クラスを使うオフライン トークンを作成します。

   **C# の使用:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **PowerShell の使用:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. トークンを作成したら、そのトークンを使用して SecureString を作成します。

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. \<tenant GUID\> を実際のディレクトリ ID に置き換えて、次の Windows PowerShell コマンドを実行します。

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>次のステップ
* [独自のドメイン名でアプリケーションを発行する](application-proxy-configure-custom-domain.md)
* [シングル サインオンを有効にする](application-proxy-configure-single-sign-on-with-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](application-proxy-troubleshoot.md)