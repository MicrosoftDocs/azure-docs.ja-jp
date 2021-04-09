---
title: アプリケーション プロキシを使った Azure Active Directory の Kerberos ベースのシングル サインオン (SSO)
description: Azure Active Directory アプリケーション プロキシを使用してシングル サインオンを提供する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a4fdd8d16854e76cdf20d27a6048694c01de8499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253885"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>アプリケーション プロキシを使ったアプリへのシングル サインオン (SSO) の Kerberos の制約付き委任

統合 Windows 認証でセキュリティ保護されているアプリケーション プロキシを使って公開されているオンプレミスのアプリケーションにシングル サインオンを提供できます。 これらのアプリケーションにアクセスするには Kerberos チケットが必要です。 アプリケーション プロキシは、Kerberos の制約付き委任 (KCD) を使って、これらのアプリケーションをサポートします。 

統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンができるようにするには、ユーザーの権限を借用できるように Active Directory でアプリケーション プロキシ コネクタにアクセス許可を与えます。 コネクタはこのアクセス許可を利用し、ユーザーの代理としてトークンを送受信します。

## <a name="how-single-sign-on-with-kcd-works"></a>KCD を使ったシングル サインオンのしくみ
この図は、IWA を使用するオンプレミス アプリケーションにユーザーがアクセスしようとしたときの流れを説明するものです。

![Microsoft AAD 認証のフロー図](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. ユーザーは、オンプレミスのアプリケーションにアプリケーション プロキシをとおしてアクセスするための URL を入力します。
2. この要求がアプリケーション プロキシによって Azure AD 認証サービスにリダイレクトされて、事前認証が行われます。 この時点で、Azure AD の認証および承認のポリシーのうち、該当するものが適用されます (たとえば多要素認証)。 ユーザーの正当性が確認された場合は、Azure AD によってトークンが作成されてユーザーに送信されます。
3. ユーザーは、このトークンをアプリケーション プロキシに渡します。
4. アプリケーション プロキシがこのトークンを検証し、ユーザー プリンシパル名 (UPN) をトークンから取り出してから、コネクタが二重認証済みのセキュリティで保護されたチャネルを介して UPN とサービス プリンシパル名 (SPN) をプルします。
5. コネクタは、オンプレミス AD との KCD (Kerberos の制約付き委任) ネゴシエーションを実行します。このときに、ユーザーの代理でアプリケーションに対する Kerberos トークンを取得します。
6. Active Directory は、そのアプリケーション用の Kerberos トークンをコネクタに送信します。
7. コネクタは、AD から受信した Kerberos トークンを使用して、元の要求をアプリケーション サーバーに送信します。
8. アプリケーションから応答がコネクタに送信されます。この応答はアプリケーション プロキシ サービスに返され、最終的にユーザーに返されます。

## <a name="prerequisites"></a>前提条件
IWA アプリケーションでのシングル サインオンに着手する前に、ご使用の環境が次の設定と構成に対応していることを確認してください。

* 対象のアプリ (SharePoint Web アプリなど) が統合 Windows 認証を使用するように設定されていること。 詳細については、「[Kerberos 認証のサポートを有効にする](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11))」を参照してください。SharePoint の場合は、「[SharePoint 2013 で Kerberos 認証を計画する](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning)」を参照してください。
* 対象となるすべてのアプリに[サービス プリンシパル名](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)があること。
* コネクタを実行するサーバーとアプリを実行するサーバーがドメインに参加し、かつ同じドメインまたは信頼する側のドメインに属していること。 ドメインへの参加の詳細については、「 [コンピューターをドメインに参加させる](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11))」を参照してください。
* Connector を実行しているサーバーに、ユーザーの TokenGroupsGlobalAndUniversal 属性を読み取るためのアクセス権があること。 既定ではそのように設定されていますが、環境のセキュリティを強化する過程で変更されている可能性があります。

### <a name="configure-active-directory"></a>Active Directory を構成する
Active Directory の構成は、アプリケーション プロキシ コネクタとアプリケーション サーバーが同じドメイン内にあるかどうかによって異なります。

#### <a name="connector-and-application-server-in-the-same-domain"></a>同じドメインにあるコネクタとアプリケーション サーバー
1. Active Directory で、 **[ツール]**  >  **[ユーザーとコンピューター]** の順に移動します。
2. コネクタを実行しているサーバーを選択します。
3. 右クリックし、 **[プロパティ]**  >  **[委任]** を選択します。
4. **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** をクリックします。 
5. **[任意の認証プロトコルを使う]** を選択します。
6. **[このアカウントが委任された資格情報を提示できるサービス]** の下で、アプリケーション サーバーの SPN ID の値を追加します。 これで、アプリケーション プロキシ コネクタは、AD において、リストで定義されたアプリケーションに対してユーザーの代理となることができるようになります。

   ![Connector-SVR のプロパティ ウィンドウのスクリーン ショット](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>異なるドメインにあるコネクタとアプリケーション サーバー
1. ドメイン間の KCD を使用するための前提条件の一覧については、「 [ドメイン間の Kerberos の制約付き委任](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11))」を参照してください。
2. Web アプリケーションのサービス アカウント (コンピューターまたは専用ドメインのユーザー アカウント) の `principalsallowedtodelegateto` プロパティを使用して、アプリケーション プロキシ (コネクタ) からの Kerberos 認証の委任を有効にします。 アプリケーション サーバーは `webserviceaccount` のコンテキストで実行され、委任サーバーは `connectorcomputeraccount` です。 `webserviceaccount` のドメイン内の (Windows Server 2012 R2 以降を実行している) ドメイン コントローラーで次のコマンドを実行します。 両方のアカウントにフラット名 (UPN 以外) を使用します。

   `webserviceaccount` がコンピューター アカウントの場合は、次のコマンドを使用します。

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   `webserviceaccount` がユーザー アカウントの場合は、次のコマンドを使用します。

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Configure single sign-on 
1. 「 [アプリケーション プロキシを使用したアプリケーションの発行](application-proxy-add-on-premises-application.md)」で説明されている手順に従って、アプリケーションを発行します。 **[事前認証方法]** で **[Azure Active Directory]** が選択されていることを確認してください。
2. アプリケーションがエンタープライズ アプリケーションの一覧に表示されたら、アプリケーションを選択して **[シングル サインオン]** をクリックします。
3. シングル サインオン モードを **[統合 Windows 認証]** に設定します。  
4. アプリケーション サーバーの **[内部アプリケーション SPN]** を入力します。 この例では、公開されたアプリケーションの SPN は、http/www.contoso.com です。 この SPN は、コネクタが委任された資格情報を提供できるサービスの一覧に入っている必要があります。 
5. ユーザーの代わりに使うコネクタに **委任されたログイン ID** を選択します。 詳細については、「[さまざまなオンプレミス ID とクラウド ID の操作](#working-with-different-on-premises-and-cloud-identities)」をご覧ください。

   ![高度なアプリケーションの構成](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Windows 以外のアプリの SSO

Azure AD Application Proxy での Kerberos 委任フローは、Azure AD がクラウドでユーザーを認証するときから始まります。 要求がオンプレミスに到着すると、Azure AD アプリケーション プロキシ コネクタは、ローカルの Active Directory と交信することで、ユーザーに代わって Kerberos チケットを発行します。 このプロセスは Kerberos の制約付き委任 (KCD) と呼ばれます。 

次のフェーズで、要求がこの Kerberos チケットを使用してバックエンド アプリケーションに送信されます。 

このような要求で Kerberos チケットを送信する方法を定義するメカニズムはいくつかあります。 Windows 以外のほとんどのサーバーでは、SPNEGO トークンの形式でそれを受け取ることが想定されています。 このメカニズムは Azure AD アプリケーション プロキシでサポートされていますが、既定で無効になっています。 SPNEGO と標準の Kerberos トークンの両方ではなくいずれかに対してコネクタを構成することができます。

SPNEGO 用にコネクタ マシンを構成する場合は、そのコネクタ グループ内の他のすべてのコネクタも SPNEGO で構成されていることを確認してください。 標準 Kerberos トークンを使用するアプリケーションは、SPNEGO 用に構成されていない他のコネクタを介してルーティングする必要があります。 一部の Web アプリケーションでは、構成を変更せずに両方の形式を使用できます。 
 

SPNEGO を有効にするには:

1. 管理者として実行しているコマンド プロンプトを開きます。
2. コマンド プロンプトから、SPNEGO が必要なコネクタ サーバーに対して次のコマンドを実行します。

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

非 Windows アプリは通常、ドメインのメール アドレスの代わりに、ユーザー名または SAM アカウント名を使います。 お使いのアプリケーションでこの状況が当てはまる場合、指定されたログイン ID フィールドを構成して、クラウド ID をアプリケーション ID に接続する必要があります。 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>さまざまなオンプレミス ID とクラウド ID の操作
アプリケーション プロキシは、ユーザーのクラウド ID とオンプレミス ID は完全に同じであるとみなします。 しかし、環境によっては、企業ポリシーやアプリケーションの依存関係が原因で、サインインに代替 ID を使用する必要がある場合があります。 このような場合でも、シングル サインオンに引き続き KCD を使用できます。 アプリケーションごとに **指定されたログイン ID** を構成し、シングル サインオンを実行するときに使う ID を指定します。  

この機能により、オンプレミス ID とクラウド ID が異なる多くの組織で、ユーザーに別のユーザー名とパスワードの入力を要求することなく、クラウドからオンプレミスのアプリに SSO させることができます。 次のような組織が含まれます。

* 内部に複数のドメインがあり (joe@us.contoso.com、joe@eu.contoso.com)、クラウドに 1 つのドメインがある (joe@contoso.com)。
* 内部にルーティングできないドメイン名があり (joe@contoso.usa)、クラウドに法的なドメイン名がある。
* 内部でドメイン名を使用していない (joe)。
* オンプレミスとクラウドで異なるエイリアスを使用している。 たとえば、joe-johns@contoso.com と joej@contoso.com です。  

アプリケーション プロキシを使用すると、Kerberos チケットの取得に使用する ID を選択できます。 この設定は、アプリケーションごとに行います。 これらのオプションの中には、電子メール アドレスの形式を受け入れないシステムに適したものもあれば、代替ログイン用に設計されたものもあります。

![委任されたログイン ID パラメーターのスクリーンショット](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

委任されたログイン ID が使用されている場合、その値は組織内のすべてのドメインまたはフォレストで一意でないことがあります。 異なる 2 つのコネクタ グループを使用してこれらのアプリケーションを 2 回発行することで、この問題を回避できます。 これは各アプリケーションのユーザーが異なり、そのコネクタを異なるドメインに参加させることができるためです。

**オンプレミスの SAM アカウント名** をログオン ID として使用する場合は、コネクタをホストするコンピューターを、ユーザー アカウントが存在するドメインに追加する必要があります。

### <a name="configure-sso-for-different-identities"></a>ID が異なる場合の SSO の構成
1. Azure AD Connect の設定を、メイン ID が電子メール アドレス (mail) になるように構成します。 これはカスタマイズ プロセスの一部として、同期設定の **[ユーザー プリンシパル名]** フィールドを変更することで実行します。 これらの設定は、ユーザーが Office365、Windows10 デバイス、および Azure AD を ID ストアとして使用する他のアプリケーションにログインする方法も決定します。  
   ![ユーザーを識別するスクリーンショット - [ユーザー プリンシパル名] ドロップダウン リスト](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 変更するアプリケーションの [アプリケーションの構成] 設定で、使用する **[委任されたログイン ID]** を選択します。

   * ユーザー プリンシパル名 (joe@contoso.com など)
   * 代替ユーザー プリンシパル名 (joed@contoso.local など)
   * ユーザー プリンシパル名のユーザー名部分 (joe など)
   * 代替ユーザー プリンシパル名のユーザー名部分 (joed など)
   * オンプレミスのソフトウェア アセット管理アカウント名 (ドメイン コントローラーの構成に依存)

### <a name="troubleshooting-sso-for-different-identities"></a>ID が異なる場合の SSO のトラブルシューティング
SSO プロセスにエラーがある場合は、「[トラブルシューティング](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)」で説明するように、コネクタ コンピューターのイベント ログに表示されます。
ただし、場合によっては、バックエンド アプリケーションが他のさまざまな HTTP 応答に応答している間に、要求が正常に送信されることがあります。 このような場合のトラブルシューティングは、アプリケーション プロキシ セッションのイベント ログで、コネクタ コンピューターのイベント番号 24029 を調べることから始める必要があります。 委任のために使用されたユーザー ID は、イベント詳細の [ユーザー] フィールドに表示されます。 セッション ログを有効にするには、イベント ビューアーで [表示] メニューの **[分析およびデバッグ ログの表示]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Kerberos の制約付き委任を使用するようにアプリケーション プロキシ アプリケーションを構成する方法](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](application-proxy-troubleshoot.md)