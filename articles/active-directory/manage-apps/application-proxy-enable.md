---
title: Azure AD アプリケーション プロキシ - コネクタのインストールの概要 | Microsoft Docs
description: Azure Portal でアプリケーション プロキシを有効にして、リバース プロキシ用のコネクタをインストールします。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 683b5b24fe8e7da086e000ff38411d3eb1c2f781
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495742"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>アプリケーション プロキシの概要とコネクタのインストール
この記事では、Azure Active Directory (Azure AD) 内でアプリケーション プロキシを有効にする手順について説明します。

アプリケーション プロキシが組織にもたらすセキュリティと生産性の利点については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](application-proxy.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
アプリケーション プロキシを有効にするには、以下が必要です。

* [Microsoft Azure AD の Basic または Premium サブスクリプション](https://azure.microsoft.com/pricing/details/active-directory)。 
* アプリケーション管理者アカウント。

### <a name="windows-server"></a>Windows サーバー
Windows Server 2012 R2 以降が実行されていて、アプリケーション プロキシ コネクタをインストールできるサーバーが必要です。 このサーバーは、Azure 内のアプリケーション プロキシ サービスと、公開するオンプレミス アプリケーションに接続する必要があります。

アプリケーション プロキシ コネクタをインストールするには、Windows サーバーで TLS 1.2 が有効になっている必要があります。 1.5.612.0 以下のバージョンを使用した既存のコネクタは、今後さらなる通知があるまで、以前のバージョンの TLS で引き続き使用できます。 TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. サーバーを再起動します

Kerberos Contrained Delegation (KCD) を使用するアプリケーションへのシングル サインオンの場合は、Windows サーバーと公開するアプリケーションが同じ Active Directory ドメインに存在する必要があります。 詳しくは、「[KCD for single sign-on with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)」 (アプリケーション プロキシを使用したシングル サインオンのための KCD) をご覧ください。
  
### <a name="proxy-servers"></a>プロキシ サーバー

お客様の組織がプロキシ サーバーを使用してインターネットに接続する場合は、それらをアプリケーション プロキシ用に構成する必要があります。  詳しくは、「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-configure-connectors-with-proxy-servers.md)」をご覧ください。 



## <a name="open-your-ports"></a>ポートを開く

Azure AD アプリケーション プロキシの環境を準備するには、まず Azure データ センターとの通信を有効にする必要があります。 経路上にファイアウォールがある場合、コネクタからアプリケーション プロキシに HTTPS (TCP) 要求を送信できるように、ファイアウォールを開放する必要があります。

1. 以下の各ポートを**送信**トラフィックに対して開放します。

   | ポート番号 | 用途 |
   | --- | --- |
   | 80 | SSL 証明書を検証する際に証明書失効リスト (CRL) をダウンロードする |
   | 443 | アプリケーション プロキシ サービスとのすべての送信通信 |

   ご利用のファイアウォールが送信元ユーザーに応じてトラフィックを監視している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。

   > [!IMPORTANT]
   > テーブルには、バージョン 1.5.132.0 以降のコネクタのポート要件が反映されます。 コネクタのバージョンがこれより古い場合は、80 と 443 に加え、5671、8080、9090、9091、9350、9352、10100 - 10120 ポートも有効にする必要があります。
   >
   >コネクタを最新バージョンに更新する方法の詳細については、「[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-connectors.md#automatic-updates)」を参照してください。

2. ファイアウォールまたはプロキシが DNS ホワイトリストを許可している場合は、msappproxy.net と servicebus.windows.net への接続をホワイトリストに登録できます。 そうでない場合は、毎週更新される [Azure DataCenter IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可する必要があります。

3. Microsoft は 4 つのアドレスを使用して証明書を検証します。 次の URL へのアクセスを他の製品に関してまだ許可していない場合は、アクセスを許可します。
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. コネクタは登録プロセスのために login.windows.net と login.microsoftonline.com にアクセスする必要があります。


## <a name="install-and-register-a-connector"></a>コネクタのインストールと登録
1. [Azure Portal](https://portal.azure.com/) に管理者としてサインインします。
2. 右上隅のユーザー名の下に、現在のディレクトリが表示されます。 ディレクトリを変更する必要がある場合は、そのアイコンを選択します。
3. **[Azure Active Directory]** > **[アプリケーション プロキシ]** の順に選択します。

   ![アプリケーション プロキシへの移動](./media/application-proxy-enable/app_proxy_navigate.png)

4. **[コネクタのダウンロード]** を選択します。

   ![コネクタのダウンロード](./media/application-proxy-enable/download_connector.png)

5. 前提条件に従って用意したサーバーで、 **AADApplicationProxyConnectorInstaller.exe** を実行します。
6. ウィザードの指示に従ってインストールします。 インストール時に、Azure AD テナントのアプリケーション プロキシにコネクタを登録するように求められます。

   * Azure AD グローバル管理者の資格情報を指定します。 グローバル管理者のテナントは、Microsoft Azure の資格情報とは異なる場合があります。
   * コネクタを登録する管理者が属しているディレクトリは、アプリケーション プロキシ サービスを有効にしたディレクトリと同じになるようにしてください。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他のエイリアスであることが必要です。
   * コネクタをインストールするサーバーで **[IE セキュリティ強化の構成]** を **[オン]** に設定している場合、登録画面が表示されないことがあります。 アクセスするには、エラー メッセージに示された指示に従ってください。 Internet Explorer セキュリティ強化の構成が無効になっていることを確認します。

高可用性を確保するには、コネクタを少なくとも 2 つデプロイする必要があります。 各コネクタは個別に登録する必要があります。

## <a name="test-that-the-connector-installed-correctly"></a>コネクタが正しくインストールされていることをテストする

Azure Portal とサーバーのいずれかで、新しいコネクタが正しくインストールされていることを確認できます。 

Azure Portal で、テナントにサインインし、**[Azure Active Directory]** > **[アプリケーション プロキシ]** の順に移動します。 コネクタとコネクタ グループはすべてこのページに表示されます。 コネクタを選択してその詳細を見るか、別のコネクタ グループに移動させます。 

サーバーで、コネクタとコネクタ アップデーターのアクティブなサービスの一覧を確認します。 2 つのサービスがすぐに起動するはずです。起動しない場合、オンにしてください。 

   * **Microsoft AAD アプリケーション プロキシ コネクタ** : 接続を有効にします。

   * **Microsoft AAD アプリケーション プロキシ コネクタ アップデーター**は自動更新サービスです。 アップデーターはコネクタの新しいバージョンをチェックし、必要に応じてコネクタを更新します。

   ![App Proxy Connector services - screenshot](./media/application-proxy-enable/app_proxy_services.png)

コネクタの詳細とコネクタを最新の状態に維持する方法については、「[Understand Azure AD Application Proxy connectors](application-proxy-connectors.md)」 (Azure AD アプリケーション プロキシ コネクタについて) を参照してください。


## <a name="next-steps"></a>次の手順
[アプリケーション プロキシを使用してアプリケーションを発行](application-proxy-publish-azure-portal.md)する準備がこれで整いました。

別のネットワークや異なる場所にアプリケーションがある場合、コネクタ グループを使用し、さまざまなコネクタで論理ユニットを編成します。 アプリケーション プロキシ コネクタの使用方法の詳細については、 [こちら](application-proxy-connector-groups.md)を参照してください。
