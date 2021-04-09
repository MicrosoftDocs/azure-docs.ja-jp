---
title: SharePoint へのリモート アクセスを有効にする - Azure AD アプリケーション プロキシ
description: オンプレミスの SharePoint サーバーを Azure AD アプリケーション プロキシと統合する方法の基礎について説明します。
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
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b5895d016e2d9d9b471218bc083ea7585254b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258683"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化

この手順ガイドでは、オンプレミスの SharePoint ファームと Azure Active Directory (Azure AD) アプリケーション プロキシを統合する方法について説明します。

## <a name="prerequisites"></a>前提条件

構成を実行するには、次のリソースが必要です。
- SharePoint 2013 以降のファーム。
- アプリケーション プロキシが含まれるプランを使用する Azure AD テナント。 詳細については、[Azure AD のプランと料金](https://azure.microsoft.com/pricing/details/active-directory/)をご覧ください。
- Azure AD テナント内の[カスタム検証済みドメイン](../fundamentals/add-custom-domain.md)。
- Azure AD Connect と同期されたオンプレミスの Active Directory。これによりユーザーは [Azure にサインイン](../hybrid/plan-connect-user-signin.md)できます。
- 企業ドメイン内のコンピューターにインストールされて実行されているアプリケーション プロキシ コネクタ。

アプリケーション プロキシで SharePoint を構成するには、次の2つの URL が必要です。
- 外部 URL。エンド ユーザーによって認識され、Azure AD で決定されます。 この URL ではカスタム ドメインを使用できます。 詳しくは、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」をご覧ください。
- 内部 URL。企業ドメイン内でのみ認識され、直接使用されることはありません。

> [!IMPORTANT]
> リンクが正しくマップされるようにするには、内部 URL について次の推奨事項に従います。
> - HTTPS を使用する。
> - カスタム ポートを使用しない。
> - 企業ドメイン ネーム システム (DNS) では、エイリアス (CName) ではなく、SharePoint WFE (またはロード バランサー) を指すようにホスト (A) を作成します。

この記事では、次の値を使用しています。
- 内部 URL: `https://sharepoint`
- 外部 URL: `https://spsites-demo1984.msappproxy.net/`
- SharePoint Web アプリケーションのアプリケーション プール アカウント: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>ステップ 1: Azure AD でアプリケーション プロキシを使用するアプリケーションを構成する

この手順では、アプリケーション プロキシを使用するアプリケーションを Azure Active Directory テナントに作成します。 外部 URL を設定し、内部 URL を指定します。どちらも、後で SharePoint において使用します。

1. 次の設定で説明されているように、アプリを作成します。 詳細な手順については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」をご覧ください。
   * **[内部 URL]** : 後で SharePoint において設定される SharePoint の内部 URL (`https://sharepoint` など)。
   * **[事前認証]** : [Azure Active Directory]
   * **[Translate URLs in Headers]\(ヘッダーの URL を変換する\)** : [いいえ]
   * **[Translate URLs in Application Body]\(アプリケーション本文の URL を変換する\)** : [いいえ]

   ![アプリケーションとして SharePoint を発行する](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. アプリケーションを発行したら、次の手順に従ってシングル サインオンの設定を構成します。

   1. ポータルのアプリケーション ページで **[シングル サインオン]** を選択します。
   1. **シングル サインオン モード** の場合、 **[統合 Windows 認証]** を選択します。
   1. **[内部アプリケーション SPN]** には前に設定した値を設定します。 この例では、値は `HTTP/sharepoint` です。
   1. **[委任されたログイン ID]** で、お使いの Active Directory フォレストの構成に最も適したオプションを選択します。 たとえば、フォレスト内に単一の Active Directory ドメインがある場合は、**[オンプレミスのソフトウェア アセット管理アカウント名]** を選択します (次のスクリーンショットを参照)。 ただし、ユーザーが SharePoint およびアプリケーション プロキシ コネクタ サーバーと同じドメインに属していない場合は、**[オンプレミス ユーザー プリンシパル名]** を選択します (スクリーンショットには示されていません)。

   ![SSO のための統合 Windows 認証の構成](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. アプリケーションの設定を完了するには、 **[ユーザーとグループ]** セクションに移動し、このアプリケーションにアクセスするユーザーを割り当てます。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>ステップ 2: SharePoint Web アプリケーションを構成する

SharePoint Web アプリケーションが Azure AD アプリケーション プロキシで正しく動作するには、Kerberos と適切な代替アクセス マッピングを使用して構成する必要があります。 次の 2 つのオプションを使用できます。

- 新しい Web アプリケーションを作成し、既定のゾーンのみを使用します。 これは、SharePoint で最適なエクスペリエンスが得られるため推奨されるオプションです (たとえば、SharePoint によって生成されるメール アラートのリンクが常に既定のゾーンを指します)。
- 既存の Web アプリケーションを拡張して、既定以外のゾーンで Kerberos を構成します。

> [!IMPORTANT]
> Kerberos が正常に機能するためには、使用するゾーンに関係なく、SharePoint Web アプリケーションのアプリケーション プール アカウントは、ドメイン アカウントである必要があります。

### <a name="provision-the-sharepoint-web-application"></a>SharePoint Web アプリケーションをプロビジョニングする

- 新しい Web アプリケーションを作成し、既定のゾーンのみを使用する場合 (推奨されるオプション):

    1. **SharePoint 管理シェル** を起動し、次のスクリプトを実行します。

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. **[SharePoint サーバーの全体管理]** サイトを開きます。
    1. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。 **[代替アクセス マッピング コレクション]** ボックスが開きます。
    1. 新しい Web アプリケーションで表示をフィルター処理し、次のような内容が表示されることを確認します。

       ![Web アプリケーションの代替アクセス マッピング](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 既存の Web アプリケーションを新しいゾーンに拡張する場合 (既定のゾーンを使用できない場合):

    1. SharePoint 管理シェルを起動し、次のスクリプトを実行します。

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. **[SharePoint サーバーの全体管理]** サイトを開きます。
    1. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。 **[代替アクセス マッピング コレクション]** ボックスが開きます。
    1. 拡張された Web アプリケーションで表示をフィルター処理し、次のような内容が表示されることを確認します。

        ![拡張されたアプリケーションの代替アクセス マッピング](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint Web アプリケーションがドメイン アカウントで実行されていることを確認する

SharePoint Web アプリケーションのアプリケーション プールが実行されているアカウントを特定し、それがドメイン アカウントであることを確認するには、次の手順を実行します。

1. **[SharePoint サーバーの全体管理]** サイトを開きます。
1. **[セキュリティ]** に移動し、**[サービス アカウントの構成]** を選択します。
1. **[Web アプリケーション プール - <Web アプリケーション名>]** を選択します。

   ![サービス アカウントを構成するための選択肢](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. **[このコンポーネントのアカウントの選択]** にドメイン アカウントが表示されていることを確認し、次の手順で必要になるので記録しておきます。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>エクストラネット ゾーンの IIS サイト用に HTTPS 証明書が構成されていることを確認する

内部 URL では HTTPS プロトコル (`https://SharePoint/`) が使用されるため、インターネット インフォメーション サービス (IIS) サイトで証明書を設定する必要があります。

1. Windows PowerShell コンソールを開きます。
1. 次のスクリプトを実行して自己署名証明書を生成し、それをコンピューターの MY ストアに追加します。

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自己署名証明書はテスト目的にのみ適しています。 運用環境では、代わりに証明機関が発行した証明書を使用することを強くお勧めします。

1. インターネット インフォメーション サービス マネージャー コンソールを開きます。
1. ツリー ビューでサーバーを展開し、**[サイト]** を展開し、**[SharePoint - AAD Proxy]** サイトを選択して **[バインド]** を選択します。
1. **https バインド** を選択して、**[編集]** を選択します。
1. TLS/SSL 証明書フィールドで、**SharePoint** 証明書を選択してから **[OK]** をクリックします。

これで、Azure AD アプリケーション プロキシ経由で外部から SharePoint サイトにアクセスできるようになりました。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>ステップ 3: Kerberos の制約付き委任 (KCD) を構成する

ユーザーは、最初に Azure AD で認証を行った後、Azure AD プロキシ コネクタ経由で Kerberos を使用して SharePoint に対する認証を行います。 Azure AD ユーザーに代わってコネクタで Kerberos トークンを取得できるようにするには、プロトコルの切り替えで Kerberos の制約付き委任 (KCD) を構成する必要があります。 KCD の詳細については、「[Kerberos の制約付き委任の概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))」をご覧ください。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>SharePoint サービス アカウントの SPN を設定する

この記事では、内部 URL が `https://sharepoint` のため、サービス プリンシパル名 (SPN) は `HTTP/sharepoint` です。 これらの値は、実際の環境に対応する値に置き換える必要があります。
SharePoint アプリケーション プール アカウント `Contoso\spapppool` に SPN `HTTP/sharepoint` を登録するには、ドメインの管理者としてコマンド プロンプトから次のコマンドを実行します。

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn` コマンドでは、追加前にその SPN が検索されます。 SPN が既に存在する場合は、**"Duplicate SPN Value" (重複する SPN 値)** エラーが発生します。 この場合、既存の SPN が正しいアプリケーション プール アカウントで設定されていない場合は、それを削除することを検討してください。 -L オプションを使用して `Setspn` コマンドを実行すると、SPN が正常に追加されたことを確認できます。 このコマンドについて詳しくは、「[Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))」をご覧ください。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>SharePoint アプリケーション プール アカウントに追加された SPN への委任に対してコネクタが信頼されていることを確認する

KCD を構成して、Azure AD アプリケーション プロキシ サービスがユーザー ID を SharePoint アプリケーション プール アカウントに委任できるようにします。 KCD を構成するには、アプリケーション プロキシ コネクタを有効にして、Azure AD で認証されているユーザーの Kerberos チケットを取得します。 その後、そのサーバーは、コンテキストを対象アプリケーション (この場合は SharePoint) に渡します。

KCD を構成するには、コネクタ コンピューターごとに以下の手順を実行します。

1. ドメイン管理者としてドメイン コントローラーにサインインし、[Active Directory ユーザーとコンピューター] を開きます。
1. Azure AD プロキシ コネクタが実行されているコンピューターを見つけます。 この例では、SharePoint サーバー自体です。
1. そのコンピューターをダブルクリックし、 **[委任]** タブを選択します。
1. 委任オプションが **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** に設定されていることを確認します。 **[任意の認証プロトコルを使う]** を選択します。
1. **[追加]** ボタンを選択し、**[ユーザーまたはコンピューター]** を選択して、SharePoint アプリケーション プール アカウントを特定します。 たとえば、`Contoso\spapppool` のように指定します。
1. SPN の一覧で、先ほどサービス アカウント用に作成した SPN を選びます。
1. **[OK]** を選択してから、もう一度 **[OK]** を選択して変更を保存します。
  
   ![Delegation settings](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

これで、外部 URL を使用して SharePoint にサインインし、Azure で認証を行うことができるようになりました。

## <a name="troubleshoot-sign-in-errors"></a>サインイン エラーのトラブルシューティング

サイトへのサインインが機能していない場合は、コネクタ ログで問題に関する詳細情報を取得できます。コネクタを実行しているコンピューターから、イベント ビューアーを開き、 **[アプリケーションとサービス ログ]**  >  **[Microsoft]**  >  **[AadApplicationProxy]**  >  **[Connector]** に移動して、 **[管理者]** のログを調べます。

## <a name="next-steps"></a>次のステップ

* [Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)
* [Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)