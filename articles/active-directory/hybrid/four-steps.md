---
title: Azure Active Directory を使って強固な ID 基盤を構築するための 4 つのステップ
description: このトピックでは、ハイブリッド ID のお客様が強固な ID 基盤を構築するために実行できる 4 つのステップについて説明します。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655942"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory を使って強固な ID 基盤を構築するための 4 つのステップ

アプリからクラウドへの急速な移行のため、アプリおよびデータへのアクセス管理は、もはや境界ネットワークやファイアウォールといった従来のネットワーク セキュリティ境界戦略には依存できなくなりました。 今や組織は、自分たちの ID ソリューションを信頼して、組織のアプリおよびデータにアクセスできる人や物を制御しなければなりません。 自分のデバイスを職場に持ってくることや、インターネットに接続可能な任意の場所から自分のデバイスを使用することを従業員に許可する組織がますます増えています。 それらのデバイスのコンプライアンスとセキュリティの確保は、組織が選択して実装する ID ソリューションにおいて重要な考慮事項となっています。 今日のデジタル ワークプレースでは、クラウドに移行するすべての組織にとって [ID は主要なコントロール プレーン](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)です。

Azure Active Directory (Azure AD) のハイブリッド ID ソリューションを採用することで、組織は、自動化、委任、セルフサービス、シングル サインオンの各機能を通じて生産性を高めるプレミアムな機能にアクセスできます。 これにより、従業員は、作業を行う必要があるどの場所からでも会社のリソースにアクセスでき、一方 IT チームは、適切な人が適切なリソースに適切にアクセスして安全な生産性を確立できるようにすることでそのアクセスを管理できます。

学習したことに基づき、このベスト プラクティスのチェックリストは、推奨されたアクションを素早く展開して組織に*強固な* ID 基盤を構築する上で役立ちます。

* アプリに簡単に接続する
* すべてのユーザーに対して 1 つの ID を自動的に確立する
* 安全にユーザーに権限を与える
* 分析情報を運用化する

## <a name="step-1---connect-to-apps-easily"></a>ステップ 1 - アプリに簡単に接続する

アプリを Azure AD と接続することにより、シングル サインオン (SSO) を有効にしてエンド ユーザーの生産性とセキュリティを向上させ、ユーザー プロビジョニングを実行することができます。 アプリを一か所 (Azure AD) で管理することにより、管理オーバーヘッドを最小限に抑え、セキュリティ ポリシーとコンプライアンス ポリシーを一元的に管理できます。

このセクションでは、アプリへのユーザー アクセスを管理し、内部アプリへの安全なリモート アクセスを有効にするためのオプション、およびアプリを Azure AD に移行した場合の利点について説明します。

### <a name="make-apps-available-to-your-users-seamlessly"></a>ユーザーがアプリをシームレスに使用できるようにする

Azure AD を使用すると、管理者は [Azure portal](https://portal.azure.com/) のエンタープライズ アプリケーション ギャラリーに[アプリケーションを追加](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)できます。 エンタープライズ アプリケーション ギャラリーにアプリケーションを追加すると、Azure AD を ID プロバイダーとして使用するようにアプリケーションを構成することが容易になります。 また、条件付きアクセス ポリシーを使ってアプリケーションへのユーザー アクセスを管理したり、ユーザーがパスワードを繰り返し入力することなく、自動的にオンプレミスとクラウド ベースのアプリケーションにサインインするようにアプリケーションへのシングル サインオン (SSO) を構成したりすることもできます。

アプリケーションが Azure AD ギャラリーに追加されると、ユーザーは自分に割り当てられたアプリを表示し、必要に応じて他のアプリを検索して要求することができます。 Azure AD には、ユーザーが自分のアプリにアクセスするための[いくつかの方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)が用意されています。

* アクセス パネル/マイ アプリ
* Office 365 アプリ起動ツール
* フェデレーション アプリへの直接サインオン
* 直接サインオンのリンク

アプリへのユーザー アクセスの詳細については、この記事の「**ステップ 3 -- ユーザーに権限を与える**」を参照してください。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Active Directory フェデレーション サービスから Azure AD にアプリを移行する

Active Directory フェデレーション サービス (ADFS) から Azure AD にシングル サインオン構成を移行すると、セキュリティの追加機能、より一貫性のある管理容易性、およびコラボレーションが有効になります。 最適な結果のためには、AD FS から Azure AD にアプリを移行することをお勧めします。 アプリケーション認証と承認を Azure AD に移行すると、次のような利点があります。

* コストの管理
* リスクの管理
* 生産性の向上
* コンプライアンスとガバナンスの対応

詳しくは、「[Azure Active Directory へのアプリケーションの移行](https://aka.ms/migrateapps/whitepaper)」ホワイトペーパーをご覧ください。

### <a name="enable-secure-remote-access-to-apps"></a>アプリへの安全なリモート アクセスを有効にする

[Azure AD アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)には、安全な方法で内部アプリにアクセスする必要があるリモート ユーザーのために、組織がオンプレミス アプリをクラウドに発行するためのシンプルなソリューションが用意されています。 Azure AD にシングル サインオンした後、ユーザーは、外部の URL または内部アプリケーション ポータルから、クラウドとオンプレミス両方のアプリケーションにアクセスできます。

Azure AD アプリケーション プロキシには、次のような利点があります。

* オンプレミスのリソースへの Azure AD の拡張
  * クラウド規模のセキュリティと保護
  * 簡単に有効にできる条件付きアクセスや Multi-Factor Authentication などの機能
* VPN や従来のリバース プロキシ ソリューションなどの境界ネットワーク内にコンポーネントがない
* 受信接続は不要
* クラウドとオンプレミスのデバイス、リソース、アプリ全体でのシングル サインオン (SSO)
* 場所や時間を問わず生産性を維持できるようにするための、エンド ユーザーへの権限付与

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Microsoft Cloud App Security を使ってシャドウ IT を検出する

現代の企業では、ユーザーが作業を行うために使用しているすべてのクラウド アプリケーションを IT 部門が認識していないことがよくあります。 IT 管理者は、従業員が使用しているクラウド アプリの数はどのくらいだと思うかと尋ねられたとき、平均で 30 または 40 と答えています。 実際には、平均 1,000 個を超える個別のアプリが組織内の従業員によって使用されています。 従業員の 80% が、誰も確認しておらず、セキュリティ ポリシーとコンプライアンス ポリシーに準拠していない未承認のアプリを使用しています。

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) は、ユーザーが SSO や条件付きアクセスなどの機能の恩恵を受けられるように、IT が承認してエンタープライズ アプリケーション ギャラリーに追加できる、ユーザーに人気のある便利なアプリを特定するのに役立ちます。

*"**Cloud App Security** を使用すると、従業員が、Accenture を保護するのに役立つ基本的なセキュリティ ポリシーをサポートする方法でクラウドと SaaS のアプリケーションを正しく使用していることを確認できます。"* --- [Accenture 社、情報セキュリティ部門マネージング ディレクター、John Blasi 氏](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

シャドウ IT を検出することに加え、MCAS は、アプリのリスク レベルを判別し、会社のデータへの未承認アクセス、データ漏洩の可能性、およびアプリケーションに固有のその他のセキュリティ リスクを防止することもできます。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>ステップ 2 - すべてのユーザーに対して 1 つの ID を自動的に確立する

オンプレミスとクラウド ベースのディレクトリを Azure AD ハイブリッド ID ソリューションにまとめると、既存の ID をクラウドにプロビジョニングすることで、既存のオンプレミス Active Directory の投資を再利用できます。 このソリューションはオンプレミスの ID を Azure AD と同期し、一方 IT は、既存のガバナンス ソリューションを使用して、信頼できる主要な ID ソースとしてオンプレミスの Active Directory を実行し続けます。 Microsoft の Azure AD ハイブリッド ID ソリューションは、場所に関係なくすべてのリソースに対する認証と権限付与のための共通ユーザー ID を作成して、オンプレミスの機能とクラウド ベースの機能を利用します。

オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースにアクセスするための共通の ID が提供されるため、ユーザーの生産性が向上し、ユーザーは複数のアプリおよびサービスで複数のアカウントを使用しないで済みます。 複数のアカウントを使用することは、エンド ユーザーにとっても IT にとっても困難な点となります。 エンド ユーザーの観点からは、複数のアカウントを持つということは、複数のパスワードを覚えておかなければならないことを意味します。 これを避けるために、多くのユーザーは各アカウントに同じパスワードを再利用しています。これは、セキュリティの観点から良いことではありません。 IT の観点からは、再利用によって、パスワードのリセットの回数が増え、エンド ユーザーの苦情とともにヘルプデスクのコストも増加します。

Azure AD Connect は、オンプレミスの ID を Azure AD に同期して、それをクラウド アプリケーションへのアクセスに使用できるようにするためのツールです。 ID は Azure AD に格納されると、Salesforce や Concur のような SaaS アプリケーションにプロビジョニングすることができます。

このセクションでは、高可用性およびクラウド向けの最新認証を提供して、オンプレミスのフットプリントを減らすための推奨事項を示します。

> [!NOTE]
> Azure AD Connect の詳細については、「[Azure AD Connect 同期とは](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)」を参照してください。

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect 用のステージング サーバーをセットアップして、最新状態を保つ

Azure AD Connect は、プロビジョニング プロセスにおいて重要な役割を果たします。 何らかの理由で同期サーバーがオフラインになると、オンプレミスへの変更がクラウドで更新されず、ユーザーに対するアクセス問題が生じます。 同期サーバーがオフラインになった後、管理者がすばやく同期を再開できるようにするフェールオーバー戦略を定義することが重要です。

Azure AD Connect のプライマリ サーバーがオフラインになったときに高可用性を提供するために、Azure AD Connect 用に別個の[ステージング サーバー](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)をデプロイすることをお勧めします。 サーバーをデプロイすることにより、管理者は、単純な構成の切り替えによってステージング サーバーを運用に "昇格" させることができます。 ステージング モードでスタンバイ サーバーを構成しておくことで、新しい構成変更をテストしてデプロイしたり、古いサーバーを廃止する場合に新しいものを導入したりすることもできます。

> [!TIP]
> Azure AD Connect は定期的に更新されます。 したがって、バージョンが新しくなるたびに提供されるパフォーマンスの向上、バグの修正、および新機能を利用するために、ステージング サーバーを最新状態にしておくことを強くお勧めします。

### <a name="enable-cloud-authentication"></a>クラウド認証を有効にする

オンプレミスの Active Directory を使用している組織は、Azure AD Connect を使って Azure AD にディレクトリを拡張し、適切な認証方法を構成する必要があります。 組織にとって[正しい認証方法を選択する](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)ことは、アプリをクラウドに移行する過程の第一歩です。 それによりクラウドのすべてのデータとリソースへのアクセスが制御されるため、これは重要なコンポーネントです。

Azure AD でオンプレミス ディレクトリ オブジェクトのクラウド認証を有効にするための最も単純で推奨される方法は、[パスワード ハッシュ同期](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) を有効にすることです。 または、組織によっては、[パススルー認証](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) の有効化を検討することもできます。

PHS または PTA のどちらを選択する場合も、[シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)を忘れずに有効にしてください。これにより、社内のネットワークで Windows 7 と 8 のデバイスを使用しているときに、ユーザーがアプリにユーザー名とパスワードを繰り返し入力せずにクラウド アプリにアクセスできるようになります。 シングル サインオンを使用しない場合、ユーザーはアプリケーション固有のパスワードを覚えて、各アプリケーションにサインインする必要があります。 同様に、IT スタッフは、Office 365、Box、Salesforce など、アプリケーションごとにユーザー アカウントを作成し、更新する必要があります。 ユーザーはパスワードを覚えておくことに加え、各アプリケーションにサインインするのに時間を費やす必要があります。 標準化されたシングル サインオンのメカニズムを社内全体に提供することは、最良のユーザー エクスペリエンス、リスクの削減、報告する能力、およびガバナンスにとって不可欠です。

既に AD FS または別のオンプレミス認証プロバイダーを使用している組織では、ID プロバイダーを Azure AD に移行することで、複雑さが軽減されて、可用性が向上します。 フェデレーションを使用する具体的なユース ケースがない限りは、フェデレーション認証から、PHS とシームレス SSO または PTA とシームレス SSO のいずれかに移行して、オンプレミスのフットプリントの削減と、向上したユーザー エクスペリエンスと共にクラウドで提供される柔軟性の利点を得られるようにすることをお勧めします。 詳細については、「[Azure Active Directory でフェデレーションからパスワード ハッシュ同期に移行する](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)」を参照してください。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>アカウントの自動プロビジョニング解除を有効にする

アプリケーションへの自動プロビジョニングとプロビジョニング解除を有効にすることは、複数のシステムにわたって ID のライフサイクルを管理するための最良の戦略です。 Azure AD では、[SCIM 2.0 プロトコル](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)を実装する ServiceNow や Salesforce などの人気のあるさまざまな SaaS アプリケーションやその他のアプリケーションに対するユーザー アカウントの[自動化されたポリシー ベースのプロビジョニングとプロビジョニング解除](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)をサポートしています。 カスタム コードや CSV ファイルの手動アップロードを必要とする従来のプロビジョニング ソリューションとは異なり、プロビジョニング サービスはクラウドでホストされ、Azure portal を使ってセットアップおよび管理できる事前統合されたコネクターを使用します。 自動プロビジョニング解除の主な利点は、ユーザーが組織を離れるときに、主要な SaaS アプリからその ID を即座に削除することにより、組織の安全を確保できることです。

自動ユーザー アカウント プロビジョニングの詳細とそのしくみについては、「 [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)」を参照してください。

## <a name="step-3---empower-your-users-securely"></a>ステップ 3 - 安全にユーザーに権限を与える

今日のデジタル ワークプレースでは、セキュリティと生産性のバランスをとることが重要です。 ただし、エンド ユーザーはしばしば、生産性とクラウド アプリへのアクセス速度を低下させるセキュリティ対策に反対します。 これに対処するために、Azure AD には、管理オーバーヘッドを最小限にしつつ、ユーザーの生産性を維持できるようにするセルフサービス機能が用意されています。

このセクションでは、警戒を怠らない状態を保ちつつ、ユーザーに権限を与えることで組織の負担を取り除くための推奨事項を示します。

### <a name="enable-self-service-password-reset-for-all-users"></a>すべてのユーザーに対してセルフ サービスによるパスワードのリセットを有効にする

Azure の[セルフサービスによるパスワードのリセット](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) では、IT 管理者は簡単に、ユーザーが管理者の手を借りずにパスワードやアカウントのリセットまたはロック解除を行えるようにできます。 このシステムには、ユーザーがいつシステムにアクセスしたかを追跡する詳細なレポートと、誤用または悪用について警告する通知が用意されています。

既定では、Azure AD はパスワード リセットを実行するときにアカウントをロック解除します。 ただし、Azure AD Connect の[オンプレミスの統合](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)を有効にすると、それらの 2 つの操作を分離することもでき、ユーザーはパスワードをリセットせずにアカウントをロック解除できるようになります。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>すべてのユーザーが MFA および SSPR に登録していることを確認する

Azure では、ユーザーが MFA および SSPR に登録していることをお客様およびその組織で確認するために使用できるレポートを提供しています。 登録していないユーザーには、そのプロセスについて教える必要がある場合があります。

MFA の[サインイン レポート](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)には、MFA の使用状況に関する情報が含まれており、組織で MFA がどのように機能しているかに関する分析情報を得ることができます。 Azure AD のサインイン アクティビティ (および監査とリスク イベント) へのアクセス権を持つことは、トラブルシューティング、使用状況の分析、およびフォレンジクスの調査を行うために不可欠です。

同様に、[セルフサービスによるパスワード管理レポート](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)を使用して、誰が SSPR に登録しているか (またはしていないか) を特定できます。

### <a name="self-service-app-management"></a>セルフサービス アプリの管理

ユーザーがアクセス パネルからアプリケーションを自己検出できるようにする前に、ユーザーによる自己検出およびアクセス権の要求を許可するアプリケーションに対して、[アプリケーションのセルフ サービス アクセス](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)を有効にする必要があります。 アプリケーションのセルフ サービス アクセスは、ユーザーにアプリケーションの自己検出を許可したり、必要に応じてビジネス グループによるこれらのアプリケーションへのアクセス承認を許可したりする場合に優れた方法です。 ユーザーに割り当てられた[パスワード シングル サインオン アプリケーション](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)の資格情報をビジネス グループがアクセス パネルから直接管理できるように許可できます。

### <a name="self-service-group-management"></a>セルフサービスのグループ管理

優れた柔軟性と大規模な管理が可能になるため、アプリケーションへのユーザーの割り当ては、グループを使用しているときに最適なマッピングになります。

* 動的グループ メンバーシップを使った属性ベース
* アプリの所有者への委任

Azure AD には、セキュリティ グループと Office 365 グループを使用してリソースへのアクセスを管理する機能があります。 これらのグループは、メンバーシップ要求の承認または拒否、グループ メンバーシップの制御の委任を実行できるグループ所有者によって管理できます。 [セルフサービス グループ管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)と呼ばれるこの機能では、管理者ロールを割り当てられていないグループ所有者が、要求を処理するために管理者に頼らずにグループを作成および管理できるようにすることで時間を節約します。

## <a name="step-4---operationalize-your-insights"></a>ステップ 4 - 分析情報を運用化する

セキュリティ関連イベントおよび関連するアラートの監査とログ記録は、ユーザーの生産性を維持し、組織のセキュリティを確保するための効果的な戦略に欠かせない要素です。 セキュリティ ログとレポートは、次のような項目を確認するのに役立ちます。

* 料金を支払っているサービスを使用しているか。
* テナント内に疑わしいまたは悪意のあるアクティビティが発生していないか。
* セキュリティ インシデントの発生時にどのユーザーが影響を受けたか。

セキュリティのログやレポートは、疑わしいアクティビティの電子記録となり、外部からネットワークへの侵入または内部からの攻撃が試みられたこと、または成功したことを示すパターンを検出するために役立ちます。 監査機能を使うと、ユーザー アクティビティの監視、規制へのコンプライアンスの文書化、フォレンジック分析の実行などが可能になります。 警告によってセキュリティ イベントが通知されます。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>操作に対して最小特権管理ロールを割り当てる

操作に対するアプローチを考えるとき、検討すべきいくつかの管理レベルがあります。 最初のレベルは、管理の負担をグローバル管理者に負わせます。 常にグローバル管理者ロールを使用することは、小規模の会社では適切と考えられます。 しかし、特定のタスクを担当するヘルプ デスク担当者と管理者がいる大規模な組織では、グローバル管理者のロールを割り当てることにより、本来実行できる以上のタスクを管理する能力をそれらの個人に提供するため、セキュリティ リスクになる可能性があります。

この場合は、次の管理レベルを検討する必要があります。 Azure AD を使用すると、エンド ユーザーを、低い特権ロールでタスクを管理できる "制限付き管理者" に指定することができます。 たとえば、ヘルプ デスクの担当者に[セキュリティ閲覧者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)のロールを割り当てることで、読み取り専用アクセス権を使用したセキュリティ関連機能の管理を許可できます。 あるいは、[認証管理者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)ロールを個人に割り当てることで、パスワード以外の資格情報をリセットしたり、Azure Service Health の構成や読み取りを行ったりする権限を付与することは、おそらく理にかなっています。

詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)」を参照してください。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Azure AD Connect Health を使ってハイブリッド コンポーネント (Azure AD Connect 同期、AD FS) を監視する

Azure AD Connect と AD FS は、潜在的にライフサイクル管理と認証を壊して、最終的に停止を引き起こす可能性があるクリティカルなコンポーネントです。 したがって、これらのコンポーネントを監視するために Azure AD Connect Health をデプロイする必要があります。

詳細については、「[Azure AD Connect Health を使用した AD FS の 監視](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)」を参照してください。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure Monitor を使用して分析用のデータ ログを収集する

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) は、詳細な分析情報、高度な分析、およびスマート機械学習を提供する、すべての Azure AD ログの統合監視ポータルです。 Azure Monitor を使用すると、ポータル内や API からメトリックやログを利用して、リソースの状態やパフォーマンスをより詳しく把握できます。 これにより、API を介した幅広い製品統合と、従来のサードパーティ SIEM システムをサポートするデータ エクスポート オプションを有効にしながら、ポータル内での一元管理エクスペリエンスが有効になります。 また、Azure Monitor では、リソースに影響を与える問題が発生したときに通知を受け取ったり、自動アクションを実行したりするようにアラート ルールを構成することもできます。

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>リーダーシップと日常用のカスタム ダッシュボードを作成する

SIEM ソリューションを持たない組織は、Azure AD 用の [Power BI コンテンツ パック](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)をダウンロードできます。 Power BI コンテンツ パックには、ユーザーによる Azure AD 機能の採用と使用状況を把握するうえで役立つ事前構築済みのレポートが付属しています。これにより、ディレクトリ内のすべてのアクティビティに関する分析情報を得ることができます。 また、独自の[カスタム ダッシュボード](https://docs.microsoft.com/power-bi/service-dashboards)を作成し、リーダーシップ チームと共有して日常的なアクティビティを報告することもできます。 ダッシュボードは、ビジネスを監視し、最も重要なすべてのメトリックの概要を確認するための優れた方法です。 ダッシュボードの視覚化は、1 つまたは多くの基盤となるデータセットからのものである場合と、1 つまたは多くの基盤となるレポートからのものである場合があります。 ダッシュボードは、オンプレミスとクラウドのデータを結合し、データの場所に関係なく、統合されたビューを提供します。

![Power BI のカスタム ダッシュボード](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>サポート コールの原因を理解する

この記事で説明されているようにハイブリッド ID ソリューションを実装すると、最終的にサポート コールが減少することに気付くはずです。 パスワードを忘れた場合やアカウントのロックアウトなどの一般的な問題は、Azure のセルフサービスによるパスワードのリセットを実装することで軽減されます。また、アプリケーションのセルフサービス アクセスを有効にすることで、ユーザーは、IT スタッフに頼らずにアプリケーションへのアクセスを自分で検出して要求できます。

サポート コール数の減少が見られない場合は、SSPR またはアプリケーションのセルフサービス アクセスが正しく構成されているかどうか、または体系的に対応できる他の新しい問題があるかどうかを確認して、サポート コールの原因を分析することをお勧めします。

*"弊社のデジタル変革の過程で、有効なエコシステムを実現するために、弊社、パートナー、およびクラウド サービス プロバイダー間のシームレスかつ安全な統合を可能にする、信頼できる ID およびアクセス管理プロバイダーが必要でした。Azure AD は、必要な機能と可視性を提供してリスクの検出と対応を可能にする最良の選択肢でした。"* --- [Aramex 社、グローバル情報セキュリティ部門ディレクター、Yazan Almasri 氏](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>分析情報を活用するためにアプリの使用状況を監視する

シャドウ IT を検出することに加え、[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) を使用して組織全体でアプリの使用状況を監視することは、組織が、クラウド アプリケーションの可能性をフルに活用するために移行するときに役立ちます。 これにより、アクティビティの可視性を高めて資産を制御し、クラウド アプリケーション全体で重要なデータの保護を強化できます。 MCAS を使って組織内のアプリの使用状況を監視することは、次の項目を確認するのに役立ちます。

* 従業員がどのような未承認アプリを使用してデータを格納しているか。
* 機密データは、いつどこでクラウドに格納されているか。
* 誰がクラウド内の機密データにアクセスしているか。

*"Cloud App Security により、異常を素早く特定して、対策を講じることができます。"* --- [Accenture 社、情報セキュリティ部門シニア マネージャー、Eric LePenske 氏](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>まとめ

ハイブリッド ID ソリューションの実装にはさまざまな側面がありますが、この 4 つのステップのチェックリストは、ユーザーの生産性と安全性を高める ID インフラストラクチャを素早く実装するのに役立ちます。

* アプリに簡単に接続する
* すべてのユーザーに対して 1 つの ID を自動的に確立する
* 安全にユーザーに権限を与える
* 分析情報を運用化する

このドキュメントが、お客様の組織の強力な ID 基盤を確立するための役立つロードマップになれば幸いです。

## <a name="identity-checklist"></a>ID チェック リスト

組織におけるより強固な ID 基盤を確立するための対応を開始するときに、次のチェックリストを参照用に印刷することをお勧めします。

### <a name="today"></a>本日

|サインアップできましたか?|Item|
|:-|:-|
||セルフサービスによるパスワードのリセット (SSPR) をグループでパイロット運用する|
||Azure AD Connect Health を使用してハイブリッド コンポーネントを監視する|
||操作に対して最小特権管理ロールを割り当てる|
||Microsoft Cloud App Security を使ってシャドウ IT を検出する|
||Azure Monitor を使って、分析用のデータ ログを収集する|

### <a name="next-two-weeks"></a>今後 2 週間

|サインアップできましたか?|Item|
|:-|:-|
||ユーザーがアプリを使用できるようにする|
||選択した SaaS アプリで Azure AD プロビジョニングをパイロット運用する|
||Azure AD Connect 用のステージング サーバーをセットアップし、最新の状態を保つ|
||ADFS から Azure AD へのアプリの移行を開始する|
||リーダーシップと日常用のカスタム ダッシュボードを作成する|

### <a name="next-month"></a>翌月

|サインアップできましたか?|Item|
|:-|:-|
||分析情報を活用するためにアプリの使用状況を監視する|
||アプリへの安全なリモート アクセスをパイロット運用する|
||すべてのユーザーが MFA および SSPR に登録していることを確認する|
||クラウド認証を有効にする|

### <a name="next-three-months"></a>今後 3 か月間

|サインアップできましたか?|Item|
|:-|:-|
||セルフサービスのアプリ管理を有効にする|
||セルフサービスのグループ管理を有効にする|
||分析情報を活用するためにアプリの使用状況を監視する|
||サポート コールの原因を理解する|

## <a name="next-steps"></a>次の手順

Azure Active Directory の機能とこの 5 ステップのチェックリストを使ってセキュリティ体制を向上させる方法について学習します - 「[ID インフラストラクチャをセキュリティ保護する 5 つのステップ](https://aka.ms/securitysteps)」。

組織が従来のオンプレミス システムから Azure AD へとより多くの ID 管理を迅速に採用して移行できるようにするソリューションと機能を提供することで、Azure AD の ID 機能を使用してクラウド ガバナンス管理への移行を加速させる方法について学習します - 「[Azure AD がオンプレミスのワークロードに対してクラウド ガバナンス管理を提供する方法](https://aka.ms/cloudgoverned)」。
