---
title: Azure AD アプリケーション プロキシを使用して Power BI へのリモート アクセスを有効にする
description: オンプレミスの Power BI を Azure AD アプリケーション プロキシと統合する方法の基礎について説明します。
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
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f0959ff51714e8a7e69f58a3e3abeab71e8359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259374"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用して Power BI Mobile へのリモート アクセスを有効にする

この記事では、Azure AD アプリケーション プロキシを使用して Power BI Mobile アプリが Power BI Report Server (PBIRS) および SQL Server Reporting Services (SSRS) 2016 以降に接続できるようにする方法について説明します。 この統合により、企業ネットワークから離れているユーザーは、Power BI Mobile アプリから Power BI レポートにアクセスし、Azure AD 認証の保護を受けることができるようになります。 この保護には、条件付きアクセスや多要素認証などの[セキュリティ上の利点](application-proxy-security.md#security-benefits)があります。

## <a name="prerequisites"></a>前提条件

この記事では、既にレポート サービスをデプロイし、[アプリケーション プロキシを有効にしている](application-proxy-add-on-premises-application.md)ことを前提としています。

- アプリケーション プロキシを有効にするには、コネクタが Azure AD サービスと通信できるように、Windows サーバーにコネクタをインストールし、[前提条件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)を満たしている必要があります。
- Power BI を発行するときは、同じ内部ドメインと外部ドメインを使用することをお勧めします。 カスタム ドメインの詳細については、[アプリケーション プロキシでのカスタム ドメインの使用](./application-proxy-configure-custom-domain.md)に関するページを参照してください。
- この統合は、**Power BI Mobile iOS および Android** アプリケーションに使用できます。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>手順 1:Kerberos の制約付き委任 (KCD) を構成する

Windows 認証を使用するオンプレミス アプリケーションの場合は、Kerberos 認証プロトコルと、Kerberos の制約付き委任 (KCD) と呼ばれる機能を使用して、シングル サインオン (SSO) を実現できます。 KCD を構成すると、ユーザーが Windows に直接サインインしていない場合でも、アプリケーション プロキシ コネクタは、そのユーザーの Windows トークンを取得できます。 KCD の詳細については、「[Kerberos の制約付き委任の概要](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))」と「[アプリケーション プロキシを使ったアプリへのシングル サインオンの Kerberos の制約付き委任](application-proxy-configure-single-sign-on-with-kcd.md)」を参照してください。

Reporting Services 側で構成することはあまりありません。 適切な Kerberos 認証が行われるように、有効なサービス プリンシパル名 (SPN) があることを確認します。 また、Reporting Services サーバーのネゴシエート認証が有効になっていることを確認します。

Reporting Services 用に KCD を設定するには、次の手順に進みます。

### <a name="configure-the-service-principal-name-spn"></a>サービス プリンシパル名 (SPN) を構成する

SPN は、Kerberos 認証を使用するサービスの一意の識別子です。 レポート サーバー用の適切な HTTP SPN が存在することを確認する必要があります。 レポート サーバー用の適切なサービス プリンシパル名 (SPN) を構成する方法については、「[レポート サーバーのサービス プリンシパル名 (SPN) の登録](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server)」を参照してください。
-L オプションを使用して Setspn コマンドを実行すると、SPN が追加されたことを確認できます。 このコマンドについて詳しくは、「[Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)」をご覧ください。

### <a name="enable-negotiate-authentication"></a>ネゴシエート認証を有効にする

レポート サーバーで Kerberos 認証を使用できるようにするには、レポート サーバーの認証の種類を RSWindowsNegotiate に構成します。 この設定は、rsreportserver.config ファイルを使用して構成します。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

詳細については、「[Reporting Services 構成ファイルを変更する](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config)」と「[レポート サーバーで Windows 認証を構成する](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server)」を参照してください。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Reporting Services アプリケーション プール アカウントに追加された SPN への委任に対してコネクタが信頼されていることを確認する
KCD を構成して、Azure AD アプリケーション プロキシ サービスがユーザー ID を Reporting Services アプリケーション プール アカウントに委任できるようにします。 KCD を構成するには、アプリケーション プロキシ コネクタを有効にして、Azure AD で認証されているユーザーの Kerberos チケットを取得します。 その後、そのサーバーは、コンテキストを対象アプリケーション (この場合は Reporting Services) に渡します。

KCD を構成するには、コネクタ コンピューターごとに以下の手順を繰り返します。

1. ドメイン管理者としてドメイン コントローラーにサインインし、 **[Active Directory ユーザーとコンピューター]** を開きます。
2. コネクタが実行されているコンピューターを見つけます。
3. そのコンピューターをダブルクリックし、 **[委任]** タブを選択します。
4. 委任設定を **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** に設定します。 **[任意の認証プロトコルを使う]** を選択します。
5. **[追加]** を選択し、 **[ユーザーまたはコンピューター]** を選択します。
6. Reporting Services に使用しているサービス アカウントを入力します。 これは、Reporting Services の構成内で SPN を追加したアカウントです。
7. **[OK]** をクリックします。 変更を保存するために、もう一度 **[OK]** をクリックします。

詳細については、「[アプリケーション プロキシを使ったアプリへのシングル サインオンの Kerberos の制約付き委任](application-proxy-configure-single-sign-on-with-kcd.md)」を参照してください。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>手順 2:Azure AD アプリケーション プロキシを介して Reporting Services を公開する

これで Azure AD アプリケーション プロキシを構成する準備ができました。

1. 次の設定を使用して、アプリケーション プロキシを介して Reporting Services を公開します。 アプリケーション プロキシを使用してアプリケーションを公開する方法の詳細な手順については、[Azure AD アプリケーション プロキシを使用したアプリケーションの公開](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)に関する記事を参照してください。
   - **[内部 URL]** : 企業ネットワーク内でコネクタが接続できるレポート サーバーの URL を入力します。 コネクタがインストールされているサーバーからこの URL に到達できることを確認します。 ベスト プラクティスは、`https://servername/` などのトップレベル ドメインを使用して、アプリケーション プロキシ経由で公開されていないサブパスに関する問題を回避することです。 たとえば、`https://servername/reports/` や `https://servername/reportserver/` ではなく、`https://servername/` を使用します。
     > [!NOTE]
     > レポート サーバーに対してセキュリティで保護された HTTPS 接続を使用することをお勧めします。 方法の詳細については、「[ネイティブ モードのレポート サーバーでの SSL 接続の構成](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)」を参照してください。
   - **外部 URL**:Power BI Mobile アプリの接続先となるパブリック URL を入力します。 たとえば、カスタム ドメインが使用されている場合は、`https://reports.contoso.com` のようになります。 カスタム ドメインを使用するには、ドメインの証明書をアップロードし、DNS レコードがアプリケーションの既定の msappproxy.net ドメインを指すようにします。 詳細な手順については、「[Azure AD アプリケーション プロキシでのカスタム ドメインの使用](application-proxy-configure-custom-domain.md)」を参照してください。

   - **事前認証方法**:Azure Active Directory

2. アプリケーションを発行したら、次の手順でシングル サインオンの設定を構成します。

   a. ポータルのアプリケーション ページで **[シングル サインオン]** を選択します。

   b. **シングル サインオン モード** の場合、 **[統合 Windows 認証]** を選択します。

   c. **[内部アプリケーション SPN]** を先ほど設定した値に設定します。

   d. ユーザーの代わりに使うコネクタに **委任されたログイン ID** を選択します。 詳細については、「[さまざまなオンプレミス ID とクラウド ID の操作](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)」を参照してください。

   e. **[保存]** をクリックして変更を保存します。

アプリケーションの設定を完了するには、 **[ユーザーとグループ]** セクションに移動し、このアプリケーションにアクセスするユーザーを割り当てます。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>手順 3:アプリケーションの応答 URI を変更する

Power BI モバイル アプリがレポート サービスに接続してアクセスできるようにするには、手順 2 で自動的に作成されたアプリケーションの登録を構成する必要があります。

1. Azure Active Directory の **[概要]** ページで、 **[アプリの登録]** を選択します。
2. **[すべてのアプリケーション]** タブで、手順 2 で作成したアプリケーションを検索します。
3. そのアプリケーションを選択し、 **[認証]** を選択します。
4. 使用しているプラットフォームに基づいて、次のリダイレクト URI を追加します。

   Power BI Mobile **iOS** 向けのアプリを構成する場合は、パブリック クライアント (モバイルとデスクトップ) という種類の次のリダイレクト URI を追加します。
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Power BI Mobile **Android** 向けのアプリを構成する場合は、パブリック クライアント (モバイルとデスクトップ) という種類の次のリダイレクト URI を追加します。
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > アプリケーションが正常に動作するには、リダイレクト URI を追加する必要があります。 Power BI Mobile iOS と Android 両方に対応したアプリを構成する場合は、iOS 用に構成されたリダイレクト URI の一覧に、パブリック クライアント (モバイルとデスクトップ) という種類のリダイレクト URI として `urn:ietf:wg:oauth:2.0:oob` を追加してください。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>手順 4:Power BI Mobile アプリから接続する

1. Power BI Mobile アプリで、Reporting Services インスタンスに接続します。 これを行うには、アプリケーション プロキシ経由で公開したアプリケーションの **[外部 URL]** を入力します。

   ![外部 URL を使用する Power BI Mobile アプリ](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **[接続]** を選択します。 Azure Active Directory サインイン ページが表示されます。

3. ユーザーの有効な資格情報を入力して、 **[サインイン]** を選択します。 Reporting Services サーバーの要素が表示されます。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>手順 5:マネージド デバイスの Intune ポリシーを構成する (省略可能)

Microsoft Intune を使用して、会社の従業員が使用するクライアント アプリを管理できます。 Intune では、データの暗号化や追加のアクセス要件などの機能を使用できます。 Intune を使用したアプリ管理の詳細については、Intune App の管理に関する記事を参照してください。 Power BI Mobile アプリケーションが Intune ポリシーと連携できるようにするには、次の手順を実行します。

1. **[Azure Active Directory]** 、 **[アプリの登録]** の順に移動します。
2. ネイティブ クライアント アプリケーションを登録する手順 3 で構成したアプリケーションを選択します。
3. アプリケーションのページで、 **[API のアクセス許可]** を選択します。
4. **[アクセス許可の追加]** をクリックします。
5. **[所属する組織で使用している API]** で、"Microsoft モバイル アプリケーション管理" を検索して選択します。
6. **DeviceManagementManagedApps.ReadWrite** アクセス許可をアプリケーションに追加します。
7. **[管理者の同意を与えます]** をクリックして、アプリケーションへのアクセス許可を付与します。
8. 「[アプリ保護ポリシーを作成して割り当てる方法](/intune/app-protection-policies)」を参照して、必要な Intune ポリシーを構成します。

## <a name="troubleshooting"></a>トラブルシューティング

レポートの読み込みが数分以上試行された後にアプリケーションからエラー ページが返された場合、タイムアウト設定の変更が必要になることがあります。 既定では、アプリケーション プロキシは、要求に応答するまでに最大 85 秒かかるアプリケーションをサポートします。 この設定を 180 秒に延長するには、アプリケーションの [アプリ プロキシ] 設定ページでバックエンドのタイムアウト を **[Long]\(長い\)** に設定します。 高速で信頼性の高いレポートを作成する方法のヒントについては、[Power BI レポートのベスト プラクティス](/power-bi/power-bi-reports-performance)に関するページを参照してください。

条件付きアクセス ポリシーでは、承認されたクライアント アプリとして Microsoft Power BI アプリが必要となります。ここでは、Azure AD アプリケーション プロキシを使用して、Power BI モバイル アプリからオンプレミスの Power BI Report Server に接続できるようにすることはできません。

## <a name="next-steps"></a>次のステップ

- [ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにします](application-proxy-configure-native-client-application.md)。
- [Power BI モバイル アプリでオンプレミスのレポート サーバーのレポートと KPI を表示する](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
