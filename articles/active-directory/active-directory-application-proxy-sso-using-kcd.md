---
title: "アプリケーション プロキシを使用したシングル サインオン | Microsoft Docs"
description: "Azure AD アプリケーション プロキシを使用してシングル サインオンを提供する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: ded0d9c9-45f6-47d7-bd0f-3f7fd99ab621
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 015cc28903bfd366c653a51b0f73512bf8b578ea
ms.openlocfilehash: aac56543b2b3b7fa8f8baf1cc719ead79b3c1b00
ms.lasthandoff: 02/28/2017

---

# <a name="provide-single-sign-on-to-your-apps-with-application-proxy"></a>アプリケーション プロキシを使用してアプリにシングル サインオンを提供
シングル サインオンは、Azure AD アプリケーション プロキシの重要な要素です。 次の手順で、最適なユーザー エクスペリエンスを提供できます。

1. ユーザーがクラウドにサインインします。  
2. クラウドですべてのセキュリティ検証 (事前認証) が実施されます。  
3. オンプレミスのアプリケーションに要求が送信されると、アプリケーション プロキシ コネクタが見かけ上ユーザーとして振る舞います。 バックエンド アプリケーションは、これをドメイン参加デバイスからの正規ユーザーであると認識します。

![アプリケーション プロキシ経由のエンド ユーザーから企業ネットワークへのアクセス図](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD アプリケーション プロキシによって、ユーザーにシングル サインオン (SSO) エクスペリエンスを提供できます。 SSO を使用するアプリを発行するには、次の手順に従います。

## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>オンプレミス IWA アプリケーションへの SSO のための KCD とアプリケーション プロキシの使用
統合 Windows 認証 (IWA) を使用してアプリケーションへのシングル サインオンができるようにするには、アプリケーション プロキシ コネクタがユーザーの代理でトークンを送受信できるように Active Directory で設定します。

### <a name="network-diagram"></a>ネットワーク図
この図は、IWA を使用するオンプレミス アプリケーションにユーザーがアクセスしようとしたときの流れを説明するものです。

![Microsoft AAD 認証のフロー図](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. ユーザーは、オンプレミスのアプリケーションにアプリケーション プロキシをとおしてアクセスするための URL を入力します。
2. この要求がアプリケーション プロキシによって Azure AD 認証サービスにリダイレクトされて、事前認証が行われます。 この時点で、Azure AD の認証および承認のポリシーのうち、該当するものが適用されます (たとえば多要素認証)。 ユーザーの正当性が確認された場合は、Azure AD によってトークンが作成されてユーザーに送信されます。
3. ユーザーは、このトークンをアプリケーション プロキシに渡します。
4. アプリケーション プロキシはこのトークンを検証し、ユーザー プリンシパル名 (UPN) をトークンから取り出してから、要求、UPN、およびサービス プリンシパル名 (SPN) をコネクタに送信します。この送信は、二重認証済みのセキュリティで保護されたチャネルをとおして行われます。
5. コネクタは、オンプレミス AD との KCD (Kerberos の制約付き委任) ネゴシエーションを実行します。このときに、ユーザーの代理でアプリケーションに対する Kerberos トークンを取得します。
6. Active Directory は、そのアプリケーション用の Kerberos トークンをコネクタに送信します。
7. コネクタは、AD から受信した Kerberos トークンを使用して、元の要求をアプリケーション サーバーに送信します。
8. アプリケーションから応答がコネクタに送信されます。この応答はアプリケーション プロキシ サービスに返され、最終的にユーザーに返されます。

### <a name="prerequisites"></a>前提条件
アプリケーション プロキシでの SSO に着手する前に、ご使用の環境が次の設定と構成に対応していることを確認してください。

* 対象のアプリ (SharePoint Web アプリなど) が統合 Windows 認証を使用するように設定されていること。 詳細については、「[Kerberos 認証のサポートを有効にする](https://technet.microsoft.com/library/dd759186.aspx)」を参照してください。SharePoint の場合は、「[SharePoint 2013 で Kerberos 認証を計画する](https://technet.microsoft.com/library/ee806870.aspx)」を参照してください。
* 対象となるすべてのアプリにサービス プリンシパル名があること。
* コネクタを実行するサーバーとアプリを実行するサーバーがドメインに参加し、かつ同じドメインまたは信頼する側のドメインに属していること。 ドメインへの参加の詳細については、「 [コンピューターをドメインに参加させる](https://technet.microsoft.com/library/dd807102.aspx)」を参照してください。
* Connector を実行しているサーバーに、ユーザーの TokenGroupsGlobalAndUniversal を読み取るためのアクセス権があること。 既定ではそのように設定されていますが、環境のセキュリティを強化する過程で変更されている可能性があります。 この設定について詳しくは、[KB2009157](https://support.microsoft.com/en-us/kb/2009157) をご覧ください。

### <a name="active-directory-configuration"></a>Active Directory の構成
Active Directory の構成は、アプリケーション プロキシ コネクタと公開されたサーバーが同じドメイン内にあるかどうかによって異なります。

#### <a name="connector-and-published-server-in-the-same-domain"></a>同じドメインにあるコネクタと公開されたサーバー
1. Active Directory で、**[ツール]** > **[ユーザーとコンピューター]** の順に移動します。
2. コネクタを実行しているサーバーを選択します。
.3. 右クリックし、**[プロパティ]** > **[委任]** を選択します。
4. **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** をクリックします。 **[このアカウントが委任された資格情報を提示できるサービス]** の下で、アプリケーション サーバーの SPN ID の値を追加します。
5. これで、アプリケーション プロキシ コネクタは、AD において、リストで定義されたアプリケーションに対してユーザーの代理となることができるようになります。

![Connector-SVR のプロパティ ウィンドウのスクリーン ショット](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>それぞれ異なるドメインにあるコネクタと公開されたサーバー
1. ドメイン間の KCD を使用するための前提条件の一覧については、「 [ドメイン間の Kerberos の制約付き委任](https://technet.microsoft.com/library/hh831477.aspx)」を参照してください。
2. Windows 2012 R2 で、コネクタ サーバーの `principalsallowedtodelegateto` プロパティを使用してアプリケーション プロキシを有効にして、コネクタ サーバーを委任します。ここで、公開先サーバーは `sharepointserviceaccount` で、委任元サーバーは `connectormachineaccount` です。

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount

> [!NOTE]
> `sharepointserviceaccount` には、SPS コンピューター アカウントか、SPS アプリ プールの実行に使用されているサービス アカウントを指定できます。
>
>

### <a name="azure-classic-portal-configuration"></a>Azure クラシック ポータルの構成
1. 「 [アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。 **[事前認証方法]** で **[Azure Active Directory]** が選択されていることを確認してください。
2. アプリケーションがアプリケーションの一覧に表示されたら、アプリケーションを選択して **[構成]**をクリックします。
3. **[プロパティ]** の下で、**[内部認証方法]** を **[統合 Windows 認証]** に設定します。  
   ![高度なアプリケーションの構成](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. アプリケーション サーバーの **[内部アプリケーション SPN]** を入力します。 この例では、公開されたアプリケーションの SPN は、http/lob.contoso.com です。  

> [!IMPORTANT]
> オンプレミスの UPN と Azure Active Directory の UPN が同一でない場合は、事前認証が機能するように[委任されたログイン ID](#delegated-login-identity) を構成する必要があります。
>
>

|  |  |
| --- | --- |
| 内部認証方法 |事前認証に Azure AD を使用する場合、内部認証方法を設定すると、ユーザーがこのアプリケーションへのシングル サインオン (SSO) を活用できるようになります。 <br><br> アプリケーションで IWA を使用する場合、**[統合 Windows 認証]** (IWA) を選択すると、このアプリケーションに対する SSO が有効になるように Kerberos の制約付き委任 (KCD) を構成することができます。 IWA を使用するアプリケーションは KCD を使用して構成する必要があります。そうしないと、アプリケーション プロキシでこれらのアプリケーションを発行できません。 <br><br> アプリケーションで IWA を使用しない場合、**[なし]** を選択します。 |
| [内部アプリケーション SPN] |これは、オンプレミスの Azure AD で構成されている、オンプレミス アプリケーションのサービス プリンシパル名 (SPN) です。 SPN は、KCD を使用しているアプリケーションの Kerberos トークンを取得するために、アプリケーション プロキシ コネクタによって使用されます。 |

## <a name="sso-for-non-windows-apps"></a>Windows 以外のアプリの SSO
Azure AD Application Proxy での Kerberos 委任フローは、Azure AD がクラウドでユーザーを認証するときから始まります。 要求がオンプレミスに到着すると、Azure AD アプリケーション プロキシ コネクタは、ローカルの Active Directory と交信することで、ユーザーに代わって Kerberos チケットを発行します。 このプロセスは Kerberos の制約付き委任 (KCD) と呼ばれます。 次のフェーズで、要求がこの Kerberos チケットを使用してバックエンド アプリケーションに送信されます。 このような要求を送信する方法を定義するプロトコルはいくつかあります。 ほとんどの Windows 以外のサーバーは Negotiate/SPNego を予期しています。これは Azure AD アプリケーション プロキシでサポートされるようになっています。

![Windows 以外の SSO の図](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

Kerberos について詳しくは、「[All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)」(Kerberos の制約付き委任 (KCD) のすべて) をご覧ください。

### <a name="delegated-login-identity"></a>委任されたログイン ID
委任されたログイン ID を使用すると、2 つの異なるログイン シナリオに対応できるようになります。

* ユーザー ID を電子メール アドレス (username@domain) ではなく、ユーザー名または SAM アカウント名の形式で通常取得する Windows 以外のアプリケーション。
* Azure AD の UPN とオンプレミスの Active Directory の UPN が異なる代替ログイン構成。

アプリケーション プロキシを使用すると、Kerberos チケットの取得に使用する ID を選択できます。 この設定は、アプリケーションごとに行います。 これらのオプションの中には、電子メール アドレスの形式を受け入れないシステムに適したものもあれば、代替ログイン用に設計されたものもあります。

![委任されたログイン ID パラメーターのスクリーンショット](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

委任されたログイン ID が使用されている場合、その値は組織内のすべてのドメインまたはフォレストで一意でないことがあります。 異なる&2; つのコネクタ グループを使用してこれらのアプリケーションを&2; 回発行することで、この問題を回避できます。 これは各アプリケーションのユーザーが異なり、そのコネクタを異なるドメインに参加させることができるためです。

## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>オンプレミス ID とクラウド ID が同一でない場合の SSO の操作
他に構成されていない限り、アプリケーション プロキシは、ユーザーのクラウド ID とオンプレミス ID は完全に同じであるとみなします。 アプリケーションごとに、シングル サインオンを実行するときに使用する ID を構成できます。  

この機能により、オンプレミス ID とクラウド ID が異なる多くの組織で、ユーザーに別のユーザー名とパスワードの入力を要求することなく、クラウドからオンプレミスのアプリに SSO させることができます。 次のような組織が含まれます。

* 内部に複数のドメインがあり (joe@us.contoso.com、joe@eu.contoso.com)、クラウドに&1; つのドメインがある (joe@contoso.com)。
* 内部にルーティングできないドメイン名があり (joe@contoso.usa)、クラウドに法的なドメイン名がある。
* 内部でドメイン名を使用していない (joe)。
* オンプレミスとクラウドで異なるエイリアスを使用している。 例: joe-johns@contoso.com vs. joej@contoso.com  

これは、Windows 以外のバックエンド サーバーで非常に一般的なシナリオである電子メール アドレス形式のアドレスを受け入れないアプリケーションでも役立ちます。

### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>クラウド ID とオンプレミス ID が異なる場合の SSO の設定
1. Azure AD Connect の設定を、メイン ID が電子メール アドレス (mail) になるように構成します。 これはカスタマイズ プロセスの一部として、同期設定の **[ユーザー プリンシパル名]** フィールドを変更することで実行します。 これらの設定は、ユーザーが Office365、Windows10 デバイス、および Azure AD を ID ストアとして使用する他のアプリケーションにログインする方法も決定します。  
   ![ユーザーを識別するスクリーンショット - [ユーザー プリンシパル名] ドロップダウン リスト](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 変更するアプリケーションの [アプリケーションの構成] 設定で、使用する **[委任されたログイン ID]** を選択します。

   * ユーザー プリンシパル名: joe@contoso.com  
   * 代替ユーザー プリンシパル名: joed@contoso.local  
   * ユーザー プリンシパル名のユーザー名部分: joe  
   * 代替ユーザー プリンシパル名のユーザー名部分: joed  
   * オンプレミスのソフトウェア アセット管理アカウント名: オンプレミスのドメイン コント ローラーの構成に依存

   ![[委任されたログインID] ドロップダウン メニューのスクリーン ショット](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>ID が異なる場合の SSO のトラブルシューティング
SSO プロセスにエラーがある場合は、「[トラブルシューティング](active-directory-application-proxy-troubleshoot.md)」で説明するように、コネクタ コンピューターのイベント ログに表示されます。
ただし、場合によっては、バックエンド アプリケーションが他のさまざまな HTTP 応答に応答している間に、要求が正常に送信されることがあります。 このような場合のトラブルシューティングは、アプリケーション プロキシ セッションのイベント ログで、コネクタ コンピューターのイベント番号 24029 を調べることから始める必要があります。 委任のために使用されたユーザー ID は、イベント詳細の [ユーザー] フィールドに表示されます。 セッション ログを有効にするには、イベント ビューアーで [表示] メニューの **[分析およびデバッグ ログの表示]** を選択します。

## <a name="see-also"></a>関連項目
* [アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)
* [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
* [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)

最新のニュースと更新プログラムについては、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

