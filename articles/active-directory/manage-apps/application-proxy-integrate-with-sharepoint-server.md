---
title: Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化 | Microsoft Docs
description: オンプレミスの SharePoint サーバーを Azure AD アプリケーション プロキシと統合する方法の基礎について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d15bb4a1cd19af70b29d1d74f43e137cf884c4db
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164092"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化

この記事では、オンプレミスの SharePoint サーバーと Azure Active Directory (Azure AD) アプリケーション プロキシを統合する方法について説明します。

Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスを有効にするには、この記事の手順に従います。

## <a name="prerequisites"></a>前提条件

この記事では、SharePoint 2013 以降が環境に用意されていることを前提にしています。 さらに、次の前提条件を検討してください。

* SharePoint では、ネイティブの Kerberos がサポートされています。 そのため、Azure AD アプリケーション プロキシを経由して内部サイトにリモートでアクセスするユーザーは、シングル サインオン (SSO) エクスペリエンスを想定できます。

* このシナリオでは、SharePoint サーバーの構成を変更します。 ステージング環境を使用することをお勧めします。 これにより、ステージング サーバーを最初に更新し、実稼働に移行する前のテスト サイクルを容易にすることができます。

* 公開 URL で SSL が必要です。 リンクが正しく送信/マッピングされるようにするために、内部 URL にも SSL が必要です。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>手順 1:Kerberos の制約付き委任 (KCD) を構成する

Windows 認証を使用するオンプレミス アプリケーションの場合は、Kerberos 認証プロトコルと、Kerberos の制約付き委任 (KCD) と呼ばれる機能を使用して、シングル サインオン (SSO) を実現できます。 KCD を構成すると、ユーザーが Windows に直接サインインしていない場合でも、アプリケーション プロキシ コネクタは、そのユーザーの Windows トークンを取得できます。 KCD の詳細については、「[Kerberos の制約付き委任の概要](https://technet.microsoft.com/library/jj553400.aspx)」をご覧ください。

SharePoint サーバーの KCD を設定するには、以下のセクションの手順を順番に実行します。

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint Web アプリケーションがドメイン アカウントで実行されていることを確認する

まず、SharePoint Web アプリケーションがローカル システム、ローカル サービス、ネットワーク サービスのいずれでもなく、ドメイン アカウントで実行されていることを確認します。 これを行って、このアカウントにサービス プリンシパル名 (SPN) をアタッチできるようにします。 SPN は、Kerberos プロトコルがさまざまなサービスを特定する方法です。 そして、KCD を構成するために後でアカウントが必要になります。

> [!NOTE]
> サービス用に事前に作成した Azure AD アカウントが必要です。 パスワードの自動変更を許可することをお勧めします。 問題のトラブルシューティングの完全な手順の詳細については、「[SharePoint Server でのパスワードの自動変更の構成](https://technet.microsoft.com/library/ff724280.aspx)」を参照してください。

サイトが定義済みのサービス アカウントで実行されていることを確認するには、次の手順に従います。

1. **[SharePoint サーバーの全体管理]** サイトを開きます。
2. **[セキュリティ]** に移動し、 **[サービス アカウントの構成]** を選択します。
3. **[Web アプリケーション プール - SharePoint - 80]** を選択します。 Web プールの名前に応じて、オプションが若干異なることがあります。Web プールが既定で SSL を使用している場合も同様です。

   ![サービス アカウントを構成するための選択肢](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. **[このコンポーネントのアカウントの選択]** フィールドが **[ローカル サービス]** または **[ネットワーク サービス]** の場合は、アカウントを作成する必要があります。 アカウントがそのどちらでもない場合は完了しているため、次のセクションに進むことができます。
5. **[新しい管理アカウントの登録]** を選択します。 アカウントを作成したら、アカウントを使用する前に **Web アプリケーション プール**を設定する必要があります。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>SharePoint サービス アカウントのサービス プリンシパル名の設定

KCD を構成する前に、次の手順を実行する必要があります。

* Azure AD Proxy で公開する SharePoint Web アプリケーションを実行しているドメイン アカウントを特定します。
* Azure AD プロキシと SharePoint の両方で構成される内部 URL を選択します。 この内部 URL は Web アプリケーションでまだ使用しないでください。また、これは Web ブラウザーには表示されません。

選択された内部 URL が <https://sharepoint> とすると、SPN は次のようになります。

```
HTTP/SharePoint
```

> [!NOTE]
> 内部 URL については、次の推奨事項を考慮してください。
> * HTTPS を使用する
> * カスタム ポートを使用しない
> * DNS では、エイリアス (CName) ではなく、SharePoint WFE (またはロード バランサー) を指すようにホスト (A) を作成します。

この SPN を登録するには、ドメインの管理者として、コマンド プロンプトから次のコマンドを実行します。

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

このコマンドで、SharePoint アプリケーション プール アカウント _demo\spAppPoolAccount_ の SPN _HTTP/SharePoint_ が設定されます。

_HTTP/SharePoint_ を内部 URL の SPN に置き換え、_demo\spAppPoolAccount_ を実際の環境内のアプリケーション プール アカウントに置き換えます。 Setspn コマンドでは、追加前にその SPN が検索されます。 既に存在する場合は、 **"Duplicate SPN Value" (重複する SPN 値)** エラーが発生します。 このとき、既存の SPN が正しいアプリケーション プール アカウントで設定されてい場合は、それを削除することを検討してください。

-L オプションを使用して Setspn コマンドを実行すると、SPN が追加されたことを確認できます。 このコマンドについて詳しくは、「[Setspn](https://technet.microsoft.com/library/cc731241.aspx)」をご覧ください。

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>SharePoint アプリケーション プール アカウントに追加された SPN への委任に対してコネクタが信頼されていることを確認する

KCD を構成して、Azure AD アプリケーション プロキシ サービスがユーザー ID を SharePoint アプリケーション プール アカウントに委任できるようにします。 KCD を構成するには、アプリケーション プロキシ コネクタを有効にして、Azure AD で認証されているユーザーの Kerberos チケットを取得します。 その後、そのサーバーは、コンテキストを対象アプリケーション (この場合は SharePoint) に渡します。

KCD を構成するには、コネクタ コンピューターごとに以下の手順を繰り返します。

1. ドメイン管理者として DC にログインし、 **[Active Directory ユーザーとコンピューター]** を開きます。
2. コネクタが実行されているコンピューターを見つけます。 この例では、同じ SharePoint サーバーです。
3. そのコンピューターをダブルクリックし、 **[委任]** タブをクリックします。
4. 委任設定が **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** に設定されていることを確認し、 **[任意の認証プロトコルを使う]** を選択します。
5. **[追加]** ボタンをクリックし、 **[ユーザーまたはコンピューター]** をクリックして、SharePoint アプリケーション プール アカウント (_demo\spAppPoolAccount_ など) を特定します。
6. SPN の一覧で、先ほどサービス アカウント用に作成した SPN を選びます。
7. Click **OK**. もう一度 **[OK]** をクリックして変更を保存します。
  
   ![Delegation settings](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>手順 2:Azure AD プロキシを構成する

KCD を構成したので、Azure AD アプリケーション プロキシを構成する準備が整いました。

1. 次の設定を使用して SharePoint サイトを発行します。 詳細な手順については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」をご覧ください。
   * **内部 URL**:以前に選択した SharePoint 内部 URL ( **<https://SharePoint/>** など)。
   * **事前認証方法**:Azure Active Directory
   * **ヘッダーの URL を変換する**:NO

   >[!TIP]
   >SharePoint では、 _[ホスト ヘッダー]_ 値を使用してサイトを検索します。 この値に基づいてリンクも生成されます。 実際の効果は、SharePoint で生成されたリンクはどれも公開 URL であり、外部 URL を使用するように適切に設定されているというものです。 この値を **[はい]** に設定すると、コネクタが要求をバックエンド アプリケーションに転送できるようにもなります。 一方、値を **[いいえ]** に設定することは、コネクタが内部のホスト名を送信しないことを意味します。 代わりに、コネクタは、ホスト ヘッダーをバックエンド アプリケーションに公開 URL として送信します。

   ![アプリケーションとして SharePoint を発行する](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. アプリケーションを発行したら、次の手順でシングル サインオンの設定を構成します。

   1. ポータルのアプリケーション ページで **[シングル サインオン]** を選択します。
   2. シングル サインオン モードとして **[統合 Windows 認証]** を選択します。
   3. [内部アプリケーション SPN] を先ほど設定した値に設定します。 この例では、**HTTP/SharePoint** を使用します。
   4. [委任されたログイン ID] で、お使いの Active Directory フォレスト構成に最も適したオプションを選択します。 たとえば、フォレスト内に単一の AD ドメインがある場合は、(下に示しているように) **[オンプレミスのソフトウェア アセット管理アカウント名]** を選択しますが、ユーザーが SharePoint サーバーおよび App Proxy Connector サーバーと同じドメインにいない場合は、 **[オンプレミス ユーザー プリンシパル名]** (ここでは示されていません) を選択します。

   ![SSO のための統合 Windows 認証の構成](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. アプリケーションの設定を完了するには、 **[ユーザーとグループ]** セクションに移動し、このアプリケーションにアクセスするユーザーを割り当てます。 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>手順 3:Kerberos と Azure AD のプロキシ URL を使用するように SharePoint を構成する

次の手順では、SharePoint が内部 URL に送信された受信要求を処理し、外部 URL 用に構築されたリンクで応答できるように、Kerberos と適切な代替アクセス マッピングで構成された新しいゾーンに SharePoint Web アプリケーションを拡張します。

1. **SharePoint 管理シェル**を起動します。
2. 次のスクリプトを実行して Web アプリケーションをエクストラネット ゾーンに拡張し、Kerberos 認証を有効にします。

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. **[SharePoint サーバーの全体管理]** サイトを開きます。
4. **[システム設定]** で、 **[代替アクセス マッピングの構成]** を選択します。 [代替アクセス マッピング] ボックスが開きます。
5. サイト (例: **[SharePoint - 80]** ) を選択します。 この時点で、エクストラネット ゾーンの内部 URL はまだ正しく設定されていません。

   ![[代替アクセス マッピング] ボックス](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. **[内部 URL の追加]** をクリックします。
7. **[URL のプロトコル、ホスト、およびポート]** テキストボックスに Azure AD プロキシで構成した**内部 URL** (<https://SharePoint/> など) を入力します。
8. [ゾーン] のドロップダウン リストで **[エクストラネット]** を選択します。
9. **[Save]** をクリックします。
10. [代替アクセス マッピング] は次のようになります。

    ![正しい [代替アクセス マッピング]](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>手順 4:エクストラネット ゾーンの IIS サイト用に HTTPS 証明書が構成されていることを確認する

SharePoint の構成はこれで完了しましたが、エクストラネット ゾーンの内部 URL は <https://SharePoint/> なので、このサイト用に証明書を設定する必要があります。

1. Windows PowerShell コンソールを開きます。
2. 次のスクリプトを実行して自己署名証明書を生成し、それをコンピューターの MY ストアに追加します。

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > 自己署名証明書はテスト目的にのみ適しています。 運用環境では、代わりに証明機関が発行した証明書を使用することを強くお勧めします。

3. [インターネット インフォメーション サービス マネージャー] コンソールを開きます。
4. ツリー ビューでサーバーを展開し、[サイト] を展開し、サイト [SharePoint - AAD Proxy] を選択して **[バインド]** をクリックします。
5. https バインドを選択して、 **[編集]** をクリックします。
6. SSL 証明書フィールドで **SharePoint** 証明書を選択して [OK] をクリックします。

これで、Azure AD アプリケーション プロキシ経由で外部から SharePoint サイトにアクセスできるようになりました。

## <a name="next-steps"></a>次の手順

* [Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)
* [Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)
