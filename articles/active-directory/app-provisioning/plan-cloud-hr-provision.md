---
title: Azure Active Directory ユーザー プロビジョニングのためのクラウド人事アプリケーションの計画
description: この記事では、Workday や SuccessFactors などのクラウド人事システムを Azure Active Directory と統合するデプロイ プロセスについて説明します。 Azure AD とクラウド人事システムを統合することで、完全な ID ライフサイクル管理システムが実現します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256713"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Azure Active Directory ユーザー プロビジョニングのためのクラウド人事アプリケーションの計画

従来、IT スタッフは、手動による社員の作成、更新、削除の方法に依存していました。 これらは、CSV ファイルのアップロードや、カスタム スクリプトなどの方法を使用して社員データを同期しています。 これらのプロビジョニング プロセスは、エラーが発生しやすく、安全でなく、管理が困難です。

社員、ベンダー、臨時社員の ID ライフサイクルを管理するために、[Azure Active Directory (Azure AD) ユーザー プロビジョニング サービス](../app-provisioning/user-provisioning.md)では、クラウドベースの人事 (HR) アプリケーションとの統合を提供しています。 アプリケーションの例としては、Workday や SuccessFactors などがあります。

Azure AD では、この統合を利用して、クラウド人事アプリケーション (アプリ) の次のワークフローを有効にします。

- **Active Directory へのユーザーのプロビジョニング:** クラウド人事アプリから選択したユーザーのセットを、1 つ以上の Active Directory ドメインにプロビジョニングします。
- **Azure AD へのクラウドのみのユーザーのプロビジョニング:** Active Directory が使用されていないシナリオでは、クラウド人事アプリから Azure AD へユーザーを直接プロビジョニングします。
- **クラウド人事アプリへの書き戻し:** メール アドレスやユーザー名の属性を Azure AD からクラウド人事アプリに書き戻します。

> [!NOTE]
> このデプロイ計画は、Azure AD のユーザー プロビジョニングを使用してクラウド人事アプリのワークフローをデプロイする方法を示します。 サービスとしてのソフトウェア (SaaS) アプリへ自動ユーザー プロビジョニングをデプロイする方法については、「[自動ユーザー プロビジョニングのデプロイを計画する](./plan-auto-user-provisioning.md)」を参照してください。

## <a name="enabled-hr-scenarios"></a>有効な人事シナリオ

Azure AD のユーザー プロビジョニング サービスを使用すると、次に示す人事ベースの ID ライフサイクル管理シナリオを自動化できます。

- **新しい社員の雇用:** 新しい社員がクラウド人事アプリに追加されると、Active Directory と Azure AD でユーザー アカウントが自動的に作成され、必要に応じて、メール アドレスとユーザー名の属性がクラウド人事アプリに書き戻されます。
- **社員の属性とプロファイルの更新:** クラウド人事アプリで名前、役職、上司などの社員レコードが更新されると、そのユーザー アカウントは Active Directory と Azure AD で自動的に更新されます。
- **社員の退職:** クラウド人事アプリで社員が退職すると、その社員のユーザー アカウントは Active Directory および Azure AD で自動的に無効になります。
- **社員の再雇用:** クラウド人事アプリで社員が再雇用されると、その社員の古いアカウントが自動的に再アクティブ化されるか、Active Directory および Azure AD に再プロビジョニングされます。

## <a name="who-is-this-integration-best-suited-for"></a>この統合が最も適している組織

クラウド人事アプリと Azure AD ユーザー プロビジョニングの統合は、次のような組織に最適です。

- クラウド人事のユーザーをプロビジョニングするための、事前構築済みのクラウドベースのソリューションを必要としている。
- クラウド人事アプリから Active Directory または Azure AD に直接、ユーザーをプロビジョニングする必要がある。
- クラウド人事アプリから取得したデータを使用してユーザーをプロビジョニングする必要がある。
- クラウド人事アプリで検出された変更情報のみに基づいて、ユーザーの入社、異動、退職を 1 つ以上の Active Directory フォレスト、ドメイン、または OU と同期する必要がある。
- 電子メールに Microsoft 365 を使用する。

## <a name="learn"></a>学習する

ユーザー プロビジョニングによって、継続的な ID ガバナンスの基盤が作成されます。 これにより、正規の ID データに依存するビジネス プロセスの品質が向上します。

### <a name="terms"></a>用語

この記事では、次の用語を使用しています。

- **ソース システム**:Azure AD のプロビジョニング元である、ユーザーのリポジトリ。 例として、Workday や SuccessFactors などのクラウド人事アプリがあります。
- **ターゲット システム**:Azure AD のプロビジョニング先である、ユーザーのリポジトリ。 例として、Active Directory、Azure AD、Microsoft 365、その他の SaaS アプリがあります。
- **入社/異動/退職プロセス**:クラウド人事アプリを記録システムとして使用する新規雇用、異動、退職に対して使われる用語。 プロセスが完了するのは、サービスによって必要な属性がターゲット システムに正常にプロビジョニングされた時点です。

### <a name="key-benefits"></a>主な利点

ここで説明する人事ベースの IT プロビジョニング機能は、次に挙げるような大きなメリットをビジネスにもたらします。

- **生産性の向上:** ユーザー アカウントや Microsoft 365 ライセンスの割り当てを自動化し、重要なグループへのアクセスを提供できるようになります。 割り当ての自動化により、新しい社員は各自の業務ツールにすぐにアクセスできるため、生産性が向上します。
- **リスク管理:** クラウド人事アプリとデータをやり取りし、社員の状態またはグループ メンバーシップに基づいて変更を自動化することで、セキュリティを強化できます。 変更を自動化すると、ユーザーが異動または退職したときに、ユーザーの ID と重要なアプリへのアクセスが自動的に更新されるようになります。
- **コンプライアンスとガバナンスへの対応:** Azure AD は、ソース システムとターゲット システムの両方のアプリによって実行されるユーザー プロビジョニング要求のネイティブ監査ログをサポートします。 監査によって、誰がアプリにアクセスできるかを 1 つの画面から追跡できます。
- **コスト管理:** 自動プロビジョニングを使用すると、手動プロビジョニングに関連した非効率性や人的エラーを回避することでコストが削減されます。 従来の陳腐化したプラットフォームを使用して、長らく構築されてきた、カスタム開発のユーザー プロビジョニング ソリューションを不要にします。

### <a name="licensing"></a>ライセンス

クラウド人事アプリと Azure AD ユーザー プロビジョニングの統合を構成するには、有効な [Azure AD Premium ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)と、Workday や SuccessFactors などのクラウド人事アプリのライセンスが必要です。

また、クラウド人事アプリをソースとして Active Directory または Azure AD にプロビジョニングされるすべてのユーザーに対して、Azure AD Premium P1 以上の有効なサブスクリプション ライセンスが必要です。 クラウド人事アプリで所有されているライセンスの数が適切でない場合、ユーザーのプロビジョニング時にエラーが発生する可能性があります。

### <a name="prerequisites"></a>前提条件

- Azure AD Connect プロビジョニング エージェントを構成するための [Azure AD ハイブリッド ID 管理者](../roles/permissions-reference.md#hybrid-identity-administrator)。
- Azure portal でプロビジョニング アプリを構成する[アプリケーション管理者](../roles/permissions-reference.md#application-administrator)ロール
- クラウド人事アプリのテストおよび運用インスタンス。
- クラウド人事アプリの管理者権限。システム統合ユーザーを作成したり、テスト目的でテスト用社員データを変更したりするために必要です。
- Active Directory へのユーザー プロビジョニングの場合、Azure AD Connect プロビジョニング エージェントをホストするために、.NET 4.7.1 以降のランタイムがインストールされた Windows Server 2012 以降を実行しているサーバーが必要です
- Active Directory と Azure AD 間でユーザーを同期する [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)。

### <a name="training-resources"></a>トレーニング リソース

| **リソース** | **リンクと説明** |
|:-|:-|
| ビデオ | [Azure Active Directory でのユーザー プロビジョニングとは](https://youtu.be/_ZjARPpI6NI) |
| | [Azure Active Directory でユーザー プロビジョニングをデプロイする方法](https://youtu.be/pKzyts6kfrw) |
| チュートリアル | [SaaS アプリと Azure AD を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md) |
| | [チュートリアル:Workday を構成し、自動ユーザー プロビジョニングに対応させる](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| よく寄せられる質問 | [自動化されたユーザー プロビジョニング](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Workday から Azure AD へのプロビジョニング](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>ソリューションのアーキテクチャ

次の例では、一般的なハイブリッド環境に向けた、エンド ツー エンドのユーザー プロビジョニング ソリューションのアーキテクチャについて説明し、次のものが含まれます。

- **正規の人事データ フロー - クラウド人事アプリから Active Directory へ。** このフローでは、クラウド人事アプリのテナントで人事イベント (入社/異動/退職プロセス) が開始されます。 Azure AD プロビジョニング サービスおよび Azure AD Connect プロビジョニング エージェントが、クラウド人事アプリのテナントから Active Directory にユーザー データをプロビジョニングします。 イベントによっては、Active Directory での作成、更新、有効化、および無効化操作が発生する可能性があります。
- **Azure AD と同期し、オンプレミスの Active Directory からクラウド人事アプリに電子メールとユーザー名を書き戻します。** Active Directory でアカウントが更新されると、Azure AD Connect によって Azure AD と同期されます。 メール アドレスやユーザー名の属性は、クラウド人事アプリ テナントに書き戻すことができます。

![ワークフロー図](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>ワークフローの説明

次の主要な手順を図に示します。  

1. **人事チーム** は、クラウド人事アプリのテナントでトランザクションを実行します。
2. **Azure AD プロビジョニング サービス** は、スケジュールされたサイクルをクラウド人事アプリのテナントから実行し、Active Directory との同期のために処理する必要がある変更を識別します。
3. **Azure AD プロビジョニング サービス** は、Active Directory アカウントの作成、更新、有効化、無効化の操作を含む要求ペイロードを使用して、Azure AD Connect プロビジョニング エージェントを呼び出します。
4. **Azure AD Connect プロビジョニング エージェント** は、サービス アカウントを使用して Active Directory アカウントのデータを管理します。
5. **Azure AD Connect** は、デルタ [同期](../hybrid/how-to-connect-sync-whatis.md)を実行して Active Directory 内の更新をプルします。
6. **Active Directory** の更新は、Azure AD と同期されます。
7. **Azure AD プロビジョニング サービス** は、Azure AD からクラウド人事アプリのテナントにメール属性とユーザー名を書き戻します。

## <a name="plan-the-deployment-project"></a>デプロイ プロジェクトを計画する

お客様の環境でこのデプロイの戦略を決定するときは、お客様の組織のニーズを考慮してください。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)します。 また、プロジェクトの利害関係者のロールを十分に把握していることを確認します。 利害関係者とそのプロジェクト入力と責務を文書化します。

既存の人事ビジネス プロセスや、社員の ID および職務データの処理要件について意見できる、人事部門の代表者を含めます。

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 エクスペリエンスの変化について、ユーザーに事前に通知します。 問題が発生した場合にサポートを受ける方法について説明します。

### <a name="plan-a-pilot"></a>パイロットを計画する

人事ビジネス プロセスと ID ワークフローをクラウド人事アプリからターゲット システムに統合するには、ソリューションを運用環境にデプロイする前に、大量のデータ検証、データ変換、データ クレンジング、そして徹底的なテストが必要です。

運用環境のすべてのユーザーに初期構成を適用する前に、[パイロット環境](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)でその構成を実行します。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>クラウド人事プロビジョニング コネクタ アプリの選択

クラウド人事アプリと Active Directory の間の Azure AD プロビジョニング ワークフローを容易にするため、Azure AD アプリ ギャラリーからプロビジョニング コネクタ アプリを複数追加できます。

- **クラウド人事アプリからAzure Active Directory へのユーザー プロビジョニング**:このプロビジョニング コネクタ アプリは、クラウド人事アプリから 1 つの Active Directory ドメインへのユーザー アカウントのプロビジョニングを容易にします。 複数のドメインがある場合は、プロビジョニング先にする必要がある Active Directory ドメインごとに 1 つ、Azure AD アプリ ギャラリーからこのアプリのインスタンスを追加できます。
- **クラウド人事アプリから Azure AD へのユーザー プロビジョニング**:Azure AD Connect は Active Directory ユーザーを Azure AD に同期するために使用するツールですが、このプロビジョニング コネクタ アプリを使用すると、クラウドのみのユーザーをクラウド人事アプリから 1 つの Azure AD テナントに容易にプロビジョニングできます。
- **クラウド人事アプリへの書き戻し**:このプロビジョニング コネクタ アプリを使用すると、ユーザーのメール アドレスを Azure AD からクラウド人事アプリに容易に書き戻すことができます。

たとえば、次の図は、Azure AD アプリ ギャラリーで入手できる Workday コネクタ アプリの一覧です。

![Azure Active Directory ポータルのアプリ ギャラリー](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>意思決定フロー チャート

以下の決定フロー チャートを使用して、お客様のシナリオに関連しているクラウド人事プロビジョニング アプリを特定します。

![意思決定フロー チャート](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Azure AD Connect プロビジョニング エージェントのデプロイ トポロジの設計

クラウド人事アプリと Active Directory 間のプロビジョニング統合には、次の 4 つのコンポーネントが必要です。

- クラウド人事アプリのテナント
- プロビジョニング コネクタ アプリ
- Azure AD Connect プロビジョニング エージェント
- Active Directory ドメイン

Azure AD Connect プロビジョニング エージェントのデプロイ トポロジは、統合を計画しているクラウド人事アプリのテナントおよび Active Directory 子ドメインの数によって異なってきます。 複数の Active Directory ドメインがある場合、Active Directory ドメインが連続しているか[分離](/windows-server/identity/ad-ds/plan/disjoint-namespace)しているかによっても異なります。

決定に基づいて、次のいずれかのデプロイ シナリオを選択します。

- 1 つのクラウド人事アプリのテナント -> 信頼されたフォレスト内の 1 つまたは複数の Active Directory 子ドメインがターゲット
- 1 つのクラウド人事アプリのテナント -> 分離された Active Directory フォレスト内の複数の子ドメインがターゲット

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>1 つのクラウド人事アプリのテナント -> 信頼されたフォレスト内の 1 つまたは複数の Active Directory 子ドメインがターゲット

次の運用環境の構成をお勧めします。

|要件|推奨|
|:-|:-|
|デプロイする Azure AD Connect プロビジョニング エージェントの数|2 つ (高可用性とフェールオーバー)
|構成するプロビジョニング コネクタ アプリの数|1 つの子ドメインあたり 1 つのアプリ|
|Azure AD Connect プロビジョニング エージェントのサーバー ホスト|geo 配置された Active Directory ドメイン コントローラーへの見通し線を備えた Windows 2012 R2 以上</br>Azure AD Connect サービスと共存可能|

![オンプレミス エージェントへのフロー](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>1 つのクラウド人事アプリのテナント -> 分離された Active Directory フォレスト内の複数の子ドメインがターゲット

このシナリオでは、クラウド人事アプリから分離された Active Directory フォレスト内のドメインにユーザーをプロビジョニングします。

次の運用環境の構成をお勧めします。

|要件|推奨|
|:-|:-|
|オンプレミスにデプロイする Azure AD Connect プロビジョニング エージェントの数|分離された Active Directory フォレストあたり 2 つ|
|構成するプロビジョニング コネクタ アプリの数|1 つの子ドメインあたり 1 つのアプリ|
|Azure AD Connect プロビジョニング エージェントのサーバー ホスト|geo 配置された Active Directory ドメイン コントローラーへの見通し線を備えた Windows 2012 R2 以上</br>Azure AD Connect サービスと共存可能|

![1 つのクラウド人事アプリのテナントと分離された Active Directory フォレスト](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect プロビジョニング エージェントの要件

クラウド人事アプリから Active Directory へのユーザー プロビジョニング ソリューションを使用するには、Windows 2012 R2 以降を実行し、1 つ以上の Azure AD Connect プロビジョニング エージェントをデプロイする必要があります。 サーバーは、4 GB 以上の RAM と .NET 4.7.1 以降のランタイムを備えている必要があります。 ホスト サーバーが、ターゲット Active Directory ドメインへのネットワーク アクセスを持っていることを確認します。

オンプレミス環境を準備するために、Azure AD Connect プロビジョニング エージェントの構成ウィザードは、エージェントを Azure AD テナントに登録し、[ポートを開き](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)、[URL へのアクセスを許可し](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)、[アウトバウンド HTTPS プロキシ構成](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)をサポートします。

プロビジョニング エージェントは、サービス アカウントを使用して Active Directory ドメインと通信します。 エージェントをインストールする前に、次の要件を満たす Active Directory ユーザーとコンピューターにサービス アカウントを作成します。

- パスワードの有効期限がない
- ユーザー アカウントを読み取り、作成、削除、管理するための管理アクセス許可が委任されている

プロビジョニング要求を処理する必要のあるドメイン コント ローラーを選択できます。 地理的に分散されたドメイン コント ローラーが複数ある場合は、優先ドメイン コント ローラーと同じサイトにプロビジョニング エージェントをインストールします。 この配置により、エンド ツー エンドのソリューションの信頼性とパフォーマンスが向上します。

高可用性については、複数の Azure AD Connect プロビジョニング エージェントをデプロイできます。 エージェントを登録して、同じオンプレミスの Active Directory ドメインのセットを処理します。

## <a name="plan-scoping-filters-and-attribute-mapping"></a>スコープ フィルターと属性マッピングの計画

クラウド人事アプリから Active Directory または Azure AD へのプロビジョニングを有効にすると、Azure portal は属性マッピングを使用して属性値を制御します。

### <a name="define-scoping-filters"></a>スコープ フィルターの定義

[スコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を使用して、クラウド人事アプリから Active Directory または Azure AD にプロビジョニングするユーザーを決定する属性ベースのルールを定義します。

入社プロセスを開始するときに、次の要件を収集します。

- クラウド人事アプリを社員と臨時社員両方のオンボードに使用しているか。
- クラウド人事アプリから Azure AD へのユーザー プロビジョニングを使用して、社員と臨時社員の両方を管理することを計画しているか。
- クラウド人事アプリのユーザーの一部のみを対象に、クラウド人事アプリから Azure AD へのユーザー プロビジョニングを実行することを計画しているか。 例として、社員のみが考えられます。

要件に応じて、属性マッピングを構成するときに **[ソース オブジェクト スコープ]** フィールドを設定して、クラウド人事アプリのどのユーザー セットを Active Directory へのプロビジョニングのスコープに含めるかを選択できます。 一般的に使用されるスコープ フィルターの詳細については、クラウド人事アプリのチュートリアルを参照してください。

### <a name="determine-matching-attributes"></a>一致属性の決定

プロビジョニングでは、ソース システムとターゲット システムの間で既存のアカウントを一致させることができます。 クラウド人事アプリを Azure AD のプロビジョニング サービスと統合するときは、[属性マッピングを構成](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)して、クラウド人事アプリと Active Directory または Azure AD の間で受け渡しするユーザー データを決定することができます。

入社プロセスを開始するときに、次の要件を収集します。

- 各ユーザーの識別に使われる、このクラウド人事アプリでの一意 ID は何か。
- ID のライフサイクルの観点から、再雇用をどのように処理するか。 再雇用時に古い社員 ID が保持されるか。
- 将来の日付の雇用を処理し、対象者の Active Directory アカウントを事前に作成するか。
- ID のライフサイクルの観点から、社員から臨時社員への (またはその逆の) 転換をどのように処理するか。
- 変換されたユーザーが、古い Active Directory アカウントを保持するか、それとも新しいアカウントを取得するか。

要件に応じて、Azure AD では、定数値の提供、または[属性マッピングの式の作成](../app-provisioning/functions-for-customizing-application-data.md)によって属性から属性への直接マッピングをサポートします。 この柔軟性により、ターゲット アプリの属性に設定される内容を完全に制御できます。 [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) と Graph Explorer を使用すると、ユーザー プロビジョニングの属性マッピングとスキーマを JSON ファイルにエクスポートし、それを Azure AD にインポートし直すことができます。

既定では、一意の社員 ID を表すクラウド人事アプリの属性が、*Active Directory の一意の属性にマップされる一致属性* として使用されます。 たとえば、Workday アプリのシナリオでは、**Workday** の **WorkerID** 属性が Active Directory の **employeeID** 属性にマップされます。

複数の一致属性を設定し、一致の優先順位を割り当てることができます。 一致の優先順位に従って評価が行われます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

また、既存の属性マッピングの変更や削除など、[既定の属性マッピングをカスタマイズ](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)することもできます。 また、ビジネスのニーズに合わせて新しい属性マッピングを作成できます。 マップするカスタム属性の一覧に関する詳細については、クラウド人事アプリのチュートリアル ([Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration) など) を参照してください。

### <a name="determine-user-account-status"></a>ユーザー アカウントの状態の決定

既定では、プロビジョニング コネクタ アプリは、人事アプリのユーザー プロファイルの状態を Active Directory または Azure AD のユーザー アカウントの状態にマップして、ユーザー アカウントを有効にするか無効にするかを決定します。

入社および退職プロセスを開始するときに、次の要件を収集します。

| Process | 必要条件 |
| - | - |
| **入社** | ID のライフサイクルの観点から、再雇用をどのように処理するか。 再雇用時に古い社員 ID が保持されるか。 |
| | 将来の日付の雇用を処理し、対象者の Active Directory アカウントを事前に作成するか。 これらのアカウントが有効または無効どちらの状態で作成されるか。 |
| | ID のライフサイクルの観点から、社員から臨時社員への (またはその逆の) 転換をどのように処理するか。 |
| | 変換されたユーザーが、古い Active Directory アカウントを保持するか、それとも新しいアカウントを取得するか。 |
| **退職** | Active Directory において、社員と臨時社員で退職の処理方法が異なっているか。 |
| | ユーザーの退職の処理にあたり、どのような発効日が考慮されるか。 |
| | 社員および臨時社員の変換は、既存の Active Directory アカウントにどのように影響するか。 |
| | Active Directory で取り消し 操作をどのように処理するか。 将来の日付の雇用が Active Directory で作成される場合、入社プロセスの中で取り消し操作を処理する必要があります。 |

要件に応じて、[Azure AD の式](../app-provisioning/functions-for-customizing-application-data.md)を使用してマッピング ロジックをカスタマイズし、データ ポイントの組み合わせに基づいて Active Directory アカウントを有効または無効にすることができます。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>クラウド人事アプリを Active Directory ユーザー属性にマップする

クラウド人事アプリごとに、クラウド人事アプリから Active Directory への既定のマッピングが存在します。

入社、異動、退職プロセスを開始するときに、次の要件を収集します。

| Process | 必要条件 |
| - | - |
| **入社** | Active Directory アカウントの作成プロセスは手動、自動、一部自動のどれか。 |
| | クラウド人事アプリから Active Directory にカスタム属性を引き継ぐことを計画しているか。 |
| **異動** | クラウド人事アプリで異動操作が発生するたびに、どの属性を処理するか。 |
| | ユーザーの更新時に特定の属性検証を実行するか。 はいの場合は、詳細を指定します。 |
| **退職** | Active Directory において、社員と臨時社員で退職の処理方法が異なっているか。 |
| | ユーザーの退職の処理にあたり、どのような発効日が考慮されるか。 |
| | 社員および臨時社員の変換は、既存の Active Directory アカウントにどのように影響するか。 |

要件に応じて、統合の目標を満たすようにマッピングを変更できます。 マップするカスタム属性の一覧に関する詳細については、特定のクラウド人事アプリのチュートリアル ([Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings) など) を参照してください。

### <a name="generate-a-unique-attribute-value"></a>一意の属性値の生成

入社プロセスを開始するにあたって、CN、samAccountName、一意の制約を持つ UPN などの属性を設定するときに、一意の属性値を生成することが必要な場合があります。

Azure AD の [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 関数は、各ルールを評価した後、生成された値のターゲット システムでの一意性を確認します。 例については、「[userPrincipalName (UPN) 属性用に一意の値を生成する](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)」を参照してください。

> [!NOTE]
> 現在、この関数は Workday to Active Directory User Provisioning でのみサポートされています。 他のプロビジョニング アプリでは使用できません。

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU コンテナーの割り当てを構成する

事業単位、所在地、部門に基づいて Active Directory ユーザー アカウントをコンテナーに配置することは一般的な要件です。 異動プロセスを開始し、監督組織の変更があった場合、Active Directory 内で、ある OU から別の OU にユーザーを移動することが必要な場合があります。

[Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) 関数を使用して、OU 割り当てのビジネス ロジックを構成し、Active Directory の **parentDistinguishedName** 属性にマップします。

たとえば、人事アプリの **Municipality** (市町村) 属性に基づいて OU にユーザーを作成する場合、次の式を使用できます。

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

この式では、Municipality の値が Dallas、Austin、Seattle、または London の場合、ユーザー アカウントは対応する OU に作成されます。 一致するものがない場合、アカウントは既定の OU に作成されます。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>新しいユーザー アカウントのパスワード交付の計画

入社プロセスの開始時に、新しいユーザー アカウントの一時パスワードを設定して交付する必要があります。 クラウド人事アプリから Azure AD へのユーザー プロビジョニングでは、Azure AD の[セルフサービス パスワード リセット ](../authentication/tutorial-enable-sspr.md) (SSPR) 機能を初日からユーザーに提供できます。

SSPR は、パスワードのリセットやアカウントのロック解除を IT 管理者がユーザーに許可するための簡単な手段です。 **Mobile Number** (携帯電話番号) 属性をクラウド人事アプリから Active Directory にプロビジョニングし、Azure AD と同期することができます。 **Mobile Number** (携帯電話番号) 属性が Azure AD に追加された後、ユーザーのアカウントに対して SSPR を有効にすることができます。 その後、新しいユーザーは初日に認証に登録および検証された携帯電話番号を使用できます。

## <a name="plan-for-initial-cycle"></a>初期サイクルの計画

Azure AD プロビジョニング サービスを初めて実行すると、クラウド人事アプリに対して[初期サイクル](../app-provisioning/how-provisioning-works.md#initial-cycle)が実行され、クラウド人事アプリ内のすべてのユーザー オブジェクトのスナップショットが作成されます。 初期サイクルにかかる時間は、ソース システムに存在するユーザーの数に正比例します。 ユーザーが 10 万人を超えるようなクラウド人事アプリのテナントでは、初期サイクルに長い時間がかかることがあります。

**大規模なクラウド人事アプリテナント (3 万人以上のユーザー) の場合、** 最初のサイクルを段階的に実行します。 増分更新を開始するのは、異なるユーザー プロビジョニングのシナリオに応じて Active Directory で正しい属性が設定されていることを検証した後のみにしてください。 この順序に従ってください。

1. [スコープ フィルター](#plan-scoping-filters-and-attribute-mapping)を設定して、限られたユーザーのセットのみを対象に初期サイクルを実行します。
2. 最初の実行のために選択したユーザーについて、Active Directory アカウントのプロビジョニングと属性値の設定を確認します。 結果が期待どおりである場合、スコープ フィルターを拡張してユーザーを徐々に増やし、2 回目の実行の結果を確認します。

テスト用のユーザーに対する初期サイクルの結果に問題がなければ、[増分更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)を開始します。

## <a name="plan-testing-and-security"></a>テストとセキュリティを計画する

初期パイロットからユーザー プロビジョニングの有効化まで、デプロイの各段階で、必ず、結果が予想どおりであることをテストし、プロビジョニング サイクルを監査するようにします。

### <a name="plan-testing"></a>テストを計画する

クラウド人事アプリから Azure AD へのユーザー プロビジョニングを構成したら、テスト ケースを実行して、このソリューションが組織の要件を満たしているかどうか確認します。

|シナリオ|予想される結果|
|:-|:-|
|クラウド人事アプリで新しい社員が採用されている。| - ユーザー アカウントが Active Directory にプロビジョニングされます。</br>- ユーザーは Active Directory ドメインのアプリにログインして目的のアクションを実行できます。</br>- Azure AD Connect 同期が構成されている場合、Azure AD にもユーザー アカウントが作成されます。
|クラウド人事アプリでユーザーの退職処理が行われる。|- ユーザー アカウントが Active Directory で無効になります。</br>- ユーザーは Active Directory によって保護されているエンタープライズ アプリにログインできません。
|クラウド人事アプリでユーザーの監督組織が更新される。|属性マッピングに基づいて、ユーザー アカウントが Active Directory 内のある OU から別の OU に移動します。|
|人事がクラウド人事アプリでユーザーの上司を更新する。|新しい上司の名前を反映するよう Active Directory の [上司] フィールドが更新されます。|
|社員を新しい役職に再雇用する人事|動作は、社員 ID の生成に関するクラウド人事アプリの構成内容によって異なります。</br>- 再雇用にあたって古い社員 ID を再利用する場合、コネクタはユーザーの既存の Active Directory アカウントを有効化します。</br>- 再雇用にあたって新しい社員 ID を使用する場合、コネクタはユーザーの新しい Active Directory アカウントを作成します。|
|人事が社員を臨時社員に (またはその逆に) 転換する。|新しいペルソナ用に新しい Active Directory アカウントが作成され、古いアカウントは転換の発効日をもって無効になります。|

以上の結果を用いて、また確定したスケジュールに基づいて、自動ユーザー プロビジョニングの実装を運用環境に移行する方法を決定します。

> [!TIP]
> プライバシーおよびセキュリティ標準に準拠するために、運用データを使用してテスト環境を更新するときは、データ整理やデータ スクラビングなどの手法を用いて、機密な個人データを削除またはマスクします。 

### <a name="plan-security"></a>セキュリティを計画する

通常、新しいサービスのデプロイの一環としてセキュリティ レビューが必要になります。 セキュリティ レビューが必要または未実施の場合は、サービスとしての ID の概要を説明している多くの Azure AD [ホワイト ペーパー](https://www.microsoft.com/download/details.aspx?id=36391)を確認してください。

### <a name="plan-rollback"></a>ロールバックを計画する

クラウドの人事ユーザー プロビジョニングの実装は、運用環境では期待通りに動作しない可能性があります。 その場合、次のロールバック手順を使用すると、以前の正常な状態に戻すことができます。

1. [プロビジョニングの概要レポート](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)と[プロビジョニング ログ](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)を確認して、影響を受けたユーザーやグループに対して行われた誤った操作を判別します。 プロビジョニングの概要レポートとログの詳細については、[クラウド人事アプリのユーザー プロビジョニングの管理](#manage-your-configuration)に関するページを参照してください。
2. 影響を受けるユーザーやグループの最後の既知の良好な状態は、プロビジョニング監査ログを調べるか、ターゲット システム (Azure AD または Active Directory) を確認することで判別できます。
3. アプリの所有者と協力し、最新の既知の正常な状態の値を使用して、アプリで直接影響を受けたユーザーやグループを更新します。

## <a name="deploy-the-cloud-hr-app"></a>クラウド人事アプリのデプロイ

ソリューションの要件に合ったクラウド人事アプリを選択します。

**Workday**:Workday から Active Directory および Azure AD に従業員プロファイルをインポートするには、「[チュートリアル:Workday を構成し、自動ユーザー プロビジョニングに対応させる](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)」を参照してください。 必要に応じて、メール アドレス、ユーザー名、電話番号を Workday に書き戻すことができます。

**SAP SuccessFactors**:SuccessFactors から Active Directory および Azure AD に従業員プロファイルをインポートするには、「[チュートリアル:SAP SuccessFactors を構成し、自動ユーザー プロビジョニングに対応させる](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)」をご覧ください。 必要に応じて、メール アドレスとユーザー名を SuccessFactors に書き戻すことができます。

## <a name="manage-your-configuration"></a>構成の管理

Azure AD は、監査ログとレポートによって組織のユーザーのプロビジョニング使用状況と操作の正常性に関する追加の分析情報を提供できます。

### <a name="gain-insights-from-reports-and-logs"></a>レポートとログから分析情報を得る

[初期サイクル](../app-provisioning/how-provisioning-works.md#initial-cycle)が正常に完了すると、Azure AD プロビジョニング サービスは、次のいずれかのイベントが発生するまで、チュートリアルで定義された各アプリに固有の間隔で、連続の増分更新を無期限に実行し続けます。

- サービスは手動で停止されます。 [Azure portal](https://portal.azure.com/) を使用するか、適切な [Microsoft Graph API](/graph/api/resources/synchronization-overview) コマンドを使用して、新しい初回サイクルがトリガーされます。
- 属性マッピングまたはスコープ フィルターの変更によって、新しい初期サイクルがトリガーされます。
- エラー率が高いため、プロビジョニング プロセスは検査に入ります。 4 週間を超えると、検査は自動的に無効になります。

これらのイベントと、プロビジョニング サービスによって実行されるその他のすべてのアクティビティを確認するには、[ログを確認してプロビジョニング アクティビティのレポートを取得する方法](../app-provisioning/check-status-user-account-provisioning.md)を参照してください。

#### <a name="azure-monitor-logs"></a>Azure Monitor ログ

プロビジョニング サービスによって実行されたアクティビティはすべて、Azure AD の監査ログに記録されます。 さらなる分析のために、Azure AD の監査ログを Azure Monitor ログにルーティングできます。 (Log Analytics ワークスペースとも呼ばれる) Azure Monitor ログを使用すると、データのクエリを行って、イベントを検索し、傾向を分析して、さまざまなデータ ソース間の相関を実行できます。 こちらの[ビデオ](https://youtu.be/MP5IaCTwkQg)をご覧になり、実用的なユーザー シナリオで Azure AD ログに対して Azure Monitor ログを使用する利点について理解してください。

[Azure AD アクティビティ ログ用の Log Analytics ビュー](../reports-monitoring/howto-install-use-log-analytics-views.md)をインストールして、環境でのプロビジョニング イベントに関する[事前構築済みレポート](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)にアクセスします。

詳細については、[Azure Monitor ログを使用して Azure AD アクティビティ ログを分析する](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)方法に関する記事を参照してください。

### <a name="manage-personal-data"></a>個人データを管理する

Windows サーバーにインストールされた Azure AD Connect プロビジョニング エージェントは、Windows イベント ログにログを作成しますが、このログには、クラウド人事アプリから Active Directory への属性マッピングの設定によっては、個人データが含まれる場合があります。 ユーザーのプライバシー義務を遵守するために、イベント ログを消去するように Windows のスケジュールされたタスクを設定し、48 時間を超えてデータが保持されないようにすることができます。

Azure AD プロビジョニング サービスでは、レポートの生成、分析の実行、30 日を超えた分析情報の提供は行われません。これは、サービスが 30 日を超えてデータを格納、処理、または保持しないためです。

### <a name="troubleshoot"></a>トラブルシューティング

プロビジョニング中に発生する可能性がある問題のトラブルシューティングについては、次の記事を参照してください。

- [Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題](application-provisioning-config-problem.md)
- [アプリケーションにプロビジョニングするためにオンプレミスの Active Directory から Azure AD に属性を同期する](user-provisioning-sync-attributes-for-mapping.md)
- [Azure Active Directory ギャラリー アプリケーションへのユーザー プロビジョニングを構成している間の管理者の資格情報の保存に関する問題](./user-provisioning.md)
- [Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない](application-provisioning-config-problem-no-users-provisioned.md)
- [Azure AD ギャラリー アプリケーションへプロビジョニングされた間違ったユーザー グループ](../manage-apps/add-application-portal-assign-users.md)
- [エージェントのトラブルシューティングのための Windows イベント ビューアーの設定](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [サービスのトラブルシューティングのための Azure portal 監査ログの設定](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [AD ユーザー アカウントの作成操作のログの概要](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [マネージャーの更新操作のログの概要](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [よく発生するエラーの解決](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>次のステップ

- [属性マッピングの式の書き方](functions-for-customizing-application-data.md)
- [Azure AD 同期 API の概要](/graph/api/resources/synchronization-overview)
- [スコープ外に出るユーザー アカウントの削除をスキップする](skip-out-of-scope-deletions.md)
- [Azure AD Connect プロビジョニング エージェント: バージョンのリリース履歴](provisioning-agent-release-version-history.md)」を参照してください