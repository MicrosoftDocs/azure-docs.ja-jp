---
title: Azure Active Directory の自動ユーザー プロビジョニングのデプロイを計画する
description: 自動ユーザー プロビジョニングの計画と実行に関するガイダンス
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9f2b5bf1195682b200b5840459194506e29bcdc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645090"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>自動ユーザー プロビジョニングのデプロイを計画する

多くの組織は、エンドユーザーの生産性に関して、ServiceNow、Zscaler、Slack などのサービスとしてのソフトウェア (SaaS) アプリケーションに依存しています。 従来、IT スタッフは、各 SaaS アプリケーションのユーザー ID を安全に管理するために、CSV ファイルをアップロードしたりカスタム スクリプトを使用したりするなどの手動のプロビジョニング方法に依存してきました。 これらのプロセスは、エラーが発生しやすく、安全でなく、管理が困難です。

Azure Active Directory (Azure AD) の自動ユーザープロビジョニングは、ビジネス ルールに基づいて SaaS アプリケーションでのユーザー ID の作成、保守、削除を安全に自動化することにより、このプロセスを簡素化します。 この自動化により、クラウドベースのソリューションへの依存を拡大するときに、クラウド専用環境とハイブリッド環境の両方で ID 管理システムを効果的に拡大することができます。

この機能の詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="learn"></a>学習する

ユーザー プロビジョニングによって、継続的な ID ガバナンスの基盤が作成され、信頼性の高い ID データに依存するビジネス プロセスの品質が向上します。

### <a name="key-benefits"></a>主な利点

自動ユーザー プロビジョニングを有効にすると、主に次のような利点があります。

* **生産性の向上**。 1 つのユーザー プロビジョニング管理インターフェイスで、複数の SaaS アプリケーションにわたってユーザー ID を管理できます。 このインターフェイスには、プロビジョニング ポリシーの単一のセットがあります。

* **リスクの管理**。 ロールやアクセス権を定義する従業員の状態またはグループ メンバーシップに基づいて変更を自動化することで、セキュリティを強化できます。

* **コンプライアンスとガバナンスへの対応**。 Azure AD は、すべてのユーザー プロビジョニング要求のネイティブ監査ログをサポートしています。 要求は、ソース システムとターゲット システムの両方で実行されます。 これにより、1 つの画面から、どのユーザーがアプリケーションにアクセスできるかを追跡できます。

* **コスト削減**。 自動ユーザー プロビジョニングを使用すると、手動プロビジョニングに関連した非効率性や人的エラーを回避することでコストが削減されます。 これにより、独自開発のユーザー プロビジョニング ソリューション、スクリプト、および監査ログの必要性が軽減されます。

### <a name="licensing"></a>ライセンス

Azure AD は、アプリケーション ギャラリー メニューに用意されているテンプレートを使用して、任意のアプリケーションのセルフサービス統合を提供します。 ライセンス要件の完全な一覧については、[Azure AD のライセンスに関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

#### <a name="application-licensing"></a>アプリケーションのライセンス

自動的にプロビジョニングするアプリケーションの適切なライセンスが必要になります。 アプリケーションに割り当てられたユーザーがアプリケーション ロールに適したライセンスを持っているかどうかを、アプリケーション所有者と話し合ってください。 Azure AD がロールに基づいて自動プロビジョニングを管理している場合、Azure AD で割り当てられるロールは、アプリケーション ライセンスに合わせる必要があります。 アプリケーションで所有されているライセンスが正しくないと、ユーザーのプロビジョニングまたは更新中にエラーが発生する可能性があります。

### <a name="terms"></a>用語

この記事では、次の用語を使用しています。

* CRUD 操作 - ユーザー アカウントに対して実行されるアクション: 作成、読み取り、更新、削除。

* シングル サインオン (SSO) - ユーザーが一度のサインオンですべての SSO 対応アプリケーションにアクセスできるようにする機能。 ユーザー プロビジョニングのコンテキストにおいて、SSO は、自動ユーザー プロビジョニングを使用するすべてのシステムにアクセスするための単一アカウントをユーザーが持つことの結果です。

* ソース システム - Azure AD のプロビジョニング元である、ユーザーのリポジトリ。 Azure AD は、事前統合されているほとんどのプロビジョニング コネクタのソース システムです。 ただし、SAP、Workday、AWS などのクラウド アプリケーションにはいくつかの例外があります。 例として、[Workday から AD へのユーザー プロビジョニング](../saas-apps/workday-inbound-tutorial.md)に関する記事を参照してください。

* ターゲット システム - Azure AD のプロビジョニング先である、ユーザーのリポジトリ。 ターゲット システムは、通常、ServiceNow、Zscaler、Slack などの SaaS アプリケーションです。 ターゲット システムは、AD などのオンプレミス システムにすることもできます。

* [System for Cross-domain Identity Management (SCIM)](https://aka.ms/scimoverview) -  ユーザー プロビジョニングの自動化を可能にするオープン スタンダード。 SCIM は、Microsoft などの ID プロバイダーと、ユーザー ID 情報を必要とする Salesforce やその他の SaaS アプリのようなサービス プロバイダーとの間で、ユーザー ID データをやりとりします。

### <a name="training-resources"></a>トレーニング リソース

| リソース| リンクと説明 |
| - | - |
| オンデマンド ウェビナー| [Azure AD を使用したエンタープライズ アプリケーションの管理](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>‎Azure AD を利用してエンタープライズ SaaS アプリケーションへの SSO を実現する方法と、アクセスを制御するためのベスト プラクティスについて学習します。 |
| ビデオ| [Azure Active Directory でのユーザー プロビジョニングとは](https://youtu.be/_ZjARPpI6NI) <br> [Azure Active Directory でユーザー プロビジョニングをデプロイする方法](https://youtu.be/pKzyts6kfrw) <br> [Salesforce と Azure AD の統合: ユーザー プロビジョニングを自動化する方法](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| オンライン コース| SkillUp オンライン:[ID の管理](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Azure AD を多くの SaaS アプリケーションと統合し、それらのアプリケーションへのユーザー アクセスをセキュリティで保護する方法について学習します。 |
| 書籍| [Modern Authentication with Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)。  <br> ‎これは、これらの新しい環境向けに Active Directory 認証ソリューションを構築するための、信頼性の高い詳細なガイドです。 |
| チュートリアル| [SaaS アプリと Azure AD を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)を参照します。 |
| よく寄せられる質問| 自動化されたユーザー プロビジョニングに関して[よく寄せられる質問](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>ソリューションのアーキテクチャ

Azure AD プロビジョニング サービスは、各アプリケーション ベンダーから提供されるユーザー管理 API エンドポイントに接続することによって、SaaS アプリや他のシステムにユーザーをプロビジョニングします。 これらのユーザー管理 API エンドポイントを使用すると、Azure AD ではプログラムによってユーザーを作成、更新、削除できます。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>ハイブリッド エンタープライズ向けの自動ユーザー プロビジョニング

この例では、ユーザーとグループは、オンプレミスのディレクトリに接続されている HR データベースに作成されます。 Azure AD プロビジョニング サービスは、ターゲット SaaS アプリケーションへの自動ユーザー プロビジョニングを管理します。

 ![ユーザー プロビジョニング](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**ワークフローの説明:**

1. ユーザー/グループは、オンプレミスの HR アプリケーション/システム (SAP など) で作成されます。 

1. **Azure AD Connect エージェント** は、ローカル AD から Azure AD に、 ID (ユーザーおよびグループ) のスケジュールされた同期を実行します。

1. **Azure AD プロビジョニング サービス** は、ソース システムとターゲット システムに対して [初回サイクル](../app-provisioning/user-provisioning.md)を実行します。 

1. **Azure AD プロビジョニング サービス** は、初回サイクル以降に変更されたすべてのユーザーとグループについてソース システムに対してクエリを実行し、変更を [増分サイクル](../app-provisioning/user-provisioning.md)にプッシュします。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>クラウド専用エンタープライズの自動ユーザー プロビジョニング

この例では、ユーザーの作成は Azure AD で行われ、Azure AD プロビジョニング サービスは、ターゲット (SaaS) アプリケーションへの自動ユーザー プロビジョニングを管理します。

![Azure AD プロビジョニング サービスを使用したオンプレミス HR アプリケーションからターゲット SaaS アプリケーションまでのユーザーおよびグループの作成プロセスを示す図。](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**ワークフローの説明:**

1. ユーザー/グループは Azure AD で作成されます。

1. **Azure AD プロビジョニング サービス** は、ソース システムとターゲット システムに対して [初回サイクル](../app-provisioning/user-provisioning.md)を実行します。 

1. **Azure AD プロビジョニング サービス** は、初回サイクル以降に更新されたすべてのユーザーとグループについてソース システムに対してクエリを実行し、[増分サイクル](../app-provisioning/user-provisioning.md)を実行します。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>クラウド HR アプリケーションの自動ユーザー プロビジョニング 

この例では、ユーザーとグループは、Workday や SuccessFactors などのクラウド人事アプリケーションで作成されます。 Azure AD プロビジョニング サービスおよび Azure AD Connect プロビジョニング エージェントが、クラウド人事アプリのテナントから AD にユーザー データをプロビジョニングします。 AD でアカウントが更新されると、Azure AD Connect を介して Azure AD と同期され、電子メール アドレスとユーザー名の属性をクラウド人事アプリのテナントに書き戻すことができます。

![画像 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **人事チーム** は、クラウド人事アプリのテナントでトランザクションを実行します。
2.  **Azure AD プロビジョニング サービス** は、スケジュールされたサイクルをクラウド人事アプリのテナントから実行し、AD との同期のために処理する必要がある変更を識別します。
3.  **Azure AD プロビジョニング サービス** は、AD アカウントの作成/更新/有効化/無効化の操作を含む要求ペイロードを使用して、Azure AD プロビジョニング エージェントを呼び出します。
4.  **Azure AD Connect プロビジョニング エージェント** は、サービス アカウントを使用して AD アカウントのデータを管理します。
5.  **Azure AD Connect** は、デルタ同期を実行して AD 内の更新をプルします。
6.  **AD** の更新が Azure AD と同期されます。 
7.  **Azure AD プロビジョニング サービス** は、Azure AD からクラウド人事アプリのテナントにメール属性とユーザー名を書き戻します。

## <a name="plan-the-deployment-project"></a>デプロイ プロジェクトを計画する

組織のニーズを考慮して、環境にユーザー プロビジョニングをデプロイするための戦略を決定します。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者を関与させ](../fundamentals/active-directory-deployment-plans.md)、利害関係者およびそのプロジェクトの情報と説明責任を文書化することで、プロジェクトでの利害関係者の役割をよく理解させます。

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

### <a name="plan-a-pilot"></a>パイロットを計画する

自動ユーザー プロビジョニングの初期構成は、運用環境のすべてのユーザーに拡張する前に、小規模なユーザー サブセットを含むテスト環境で行うことをお薦めします。 パイロットの実行については、[ベストプラクティス](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)に関する記事を参照してください。

#### <a name="best-practices-for-a-pilot"></a>パイロットのベスト プラクティス  

パイロットを使用することにより、すべてのユーザーに対して機能をデプロイする前に、小規模なグループでテストすることができます。 テストの一部として、組織内の各ユース ケースが十分にテストされていることを確認します。

最初の段階では、テストを受けてフィードバックを提供できる IT、ユーザビリティ、およびその他の適切なユーザーを対象にします。 このフィードバックを使用して、ユーザーに伝える情報と指示をさらに発展させ、サポート スタッフが直面する可能性がある問題の種類に関する分析情報を提供します。

対象となるグループの範囲を広げて、ロールアウトをより大きなユーザー グループに拡大します。 これは、[動的グループ メンバーシップ](../enterprise-users/groups-dynamic-membership.md)を使用するか、対象グループにユーザーを手動で追加することで実行できます。

## <a name="plan-application-connections-and-administration"></a>アプリケーションの接続と管理を計画する

Azure AD ポータルを使用して、プロビジョニングをサポートするすべてのアプリケーションを表示および管理します。 「[ポータルでアプリを検索する](../app-provisioning/configure-automatic-user-provisioning-portal.md)」を参照してください。

### <a name="determine-the-type-of-connector-to-use"></a>使用するコネクタの種類を決定する

自動プロビジョニングを有効にし、構成するために実際に必要な手順は、アプリケーションによって異なります。 自動的にプロビジョニングしたいアプリケーションが [Azure AD SaaS アプリ ギャラリー](../saas-apps/tutorial-list.md)に一覧表示されている場合は、[アプリ固有の統合チュートリアル](../saas-apps/tutorial-list.md)を選択して、事前統合されたユーザー プロビジョニング コネクタを構成してください。

そうなっていない場合は、次の手順に従ってください。

1. 事前統合されたユーザー プロビジョニング コネクタの[要求を作成](../develop/v2-howto-app-gallery-listing.md)します。 Microsoft チームがお客様およびアプリケーション開発者と協力し、SCIM がサポートされている場合、お客様のアプリケーションを Microsoft のプラットフォームにオンボードします。

1. アプリに対する [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 汎用ユーザー プロビジョニング サポートを使用します。 これは、事前に統合されたプロビジョニング コネクタのないアプリにユーザーをプロビジョニングするための Azure AD の要件です。

1. アプリケーションが BYOA SCIM コネクタを利用できる場合は、[BYOA SCIM 統合チュートリアル](../app-provisioning/use-scim-to-provision-users-and-groups.md)を参照して、アプリケーションに対して BYOA SCIM コネクタを構成します。

詳細については、[Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム](../app-provisioning/user-provisioning.md)に関する記事を参照してください。

### <a name="collect-information-to-authorize-application-access"></a>アプリケーションへのアクセスを承認するための情報を収集する

自動ユーザー プロビジョニングの設定は、アプリケーションごとのプロセスです。 ターゲット システムのユーザー管理エンドポイントに接続するには、アプリケーションごとに[管理者の資格情報](../app-provisioning/configure-automatic-user-provisioning-portal.md)を指定する必要があります。

次の画像は、必要な管理者資格情報の 1 つのバージョンを示しています。

![ユーザー アカウントでのプロビジョニング設定を管理する [プロビジョニング] 画面](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

アプリケーションの中には、管理者のユーザー名とパスワードが必要なものもあれば、ベアラー トークンが必要なものもあります。

## <a name="plan-user-and-group-provisioning"></a>ユーザーとグループのプロビジョニングを計画する

エンタープライズ アプリに対してユーザー プロビジョニングを有効にすると、[Azure portal](https://portal.azure.com/) は属性マッピングを通じてその属性値を制御します。

### <a name="determine-operations-for-each-saas-app"></a>各 SaaS アプリの操作を決定する

各アプリケーションには、Azure AD 内の属性にマップする必要がある一意のユーザーまたはグループの属性が含まれている場合があります。 アプリケーションは、使用可能な CRUD 操作のサブセットのみを持つことができます。

アプリケーションごとに、次の情報を文書化します。

* ターゲット システムのユーザー オブジェクトとグループ オブジェクトに対して実行される CRUD プロビジョニング操作。 たとえば、各 SaaS アプリのビジネス所有者は、可能なすべての操作を必要としない場合があります。

* ソース システムで使用可能な属性

* ターゲット システムで使用可能な属性

* システム間での属性のマッピング。

### <a name="choose-which-users-and-groups-to-provision"></a>プロビジョニングするユーザーとグループを選択する

自動ユーザー プロビジョニングを実装する前に、アプリケーションにプロビジョニングするユーザーとグループを決定する必要があります。

* [スコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を使用して、アプリケーションにプロビジョニングするユーザーを決める、属性ベースのルールを定義します。

* 次に、必要に応じて[ユーザーとグループの割り当て](../manage-apps/assign-user-or-group-access-portal.md)を使用して、追加のフィルター処理を行います。

### <a name="define-user-and-group-attribute-mapping"></a>ユーザーとグループの属性マッピングを定義する

自動ユーザー プロビジョニングを実装するには、アプリケーションに必要なユーザーとグループの属性を定義する必要があります。 Azure AD ユーザー オブジェクトと各 SaaS アプリケーションのユーザー オブジェクトの間には、構成済みの一連の属性と[属性マッピング](../app-provisioning/configure-automatic-user-provisioning-portal.md)が存在します。 すべての SaaS アプリでグループ属性が有効化されるわけではありません。

Azure AD では、属性から属性への直接マッピング、定数値の提供、または[属性マッピングの式の作成](../app-provisioning/functions-for-customizing-application-data.md)によってサポートされます。 この柔軟性により、ターゲット システムの属性に設定される内容を細かく制御できます。 [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) と Graph Explorer を使用すると、ユーザー プロビジョニングの属性マッピングとスキーマを JSON ファイルにエクスポートし、それを Azure AD にインポートし直すことができます。

詳細については、「[Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ](../app-provisioning/customize-application-attributes.md)」を参照してください。

### <a name="special-considerations-for-user-provisioning"></a>ユーザー プロビジョニングに関する特別な考慮事項

デプロイ後の問題を減らすために、次の点を考慮してください。

* ソース アプリケーションとターゲット アプリケーションの間でユーザー/グループ オブジェクトをマップするために使用される属性が回復性を備えていることを確認します。 属性が変更されても (ユーザーが社内の別の場所に移動した場合など)、ユーザーやグループが誤ってプロビジョニングされないようにする必要があります。

* アプリケーションには、ユーザー プロビジョニングが正しく機能するために満たす必要がある特定の制限や要件がある場合があります。 たとえば、Slack では、特定の属性の値が切り捨てられます。 各アプリケーションに固有の[自動ユーザー プロビジョニング チュートリアル](../saas-apps/tutorial-list.md)を参照してください。

* ソース システムとターゲット システム間のスキーマの整合性を確認します。 よくある問題としては、一致しない UPN やメールなどの属性があります。 たとえば、UPN は Azure AD では *john_smith@contoso.com* と設定され、アプリでは *jsmith@contoso.com* になります。 詳細については、[ユーザーとグループのスキーマ リファレンス](../app-provisioning/use-scim-to-provision-users-and-groups.md)に関する記事を参照してください。

## <a name="plan-testing-and-security"></a>テストとセキュリティを計画する

デプロイの各段階で、必ず、結果が予想どおりであることをテストし、プロビジョニング サイクルを監査するようにします。

### <a name="plan-testing"></a>テストを計画する

アプリケーションに対して自動ユーザー プロビジョニングを構成したら、テスト ケースを実行して、このソリューションが組織の要件を満たしていることを確認します。

| シナリオ| 予想される結果 |
| - | - |
| ターゲット システムに割り当てられているグループにユーザーが追加される | ユーザー オブジェクトがターゲット システムでプロビジョニングされている。 <br>ユーザーがターゲット システムにサインインして、目的のアクションを実行できる。 |
| ターゲット システムに割り当てられたグループからユーザーが削除される | ユーザー オブジェクトがターゲット システムでプロビジョニング解除される。<br>ユーザーがターゲットシステムにサインインできない。 |
| ユーザー情報が任意の方法によって Azure AD で更新される | 増分サイクル後、更新されたユーザー属性がターゲット システムに反映される |
| ユーザーが対象範囲外である | ユーザー オブジェクトが無効化または削除される。 <br>注:[Workday のプロビジョニング](skip-out-of-scope-deletions.md)では、この動作はオーバーライドされます。 |

### <a name="plan-security"></a>セキュリティを計画する

通常、デプロイの一環としてセキュリティ レビューが必要になります。 セキュリティ レビューが必要な場合は、Identity as a Service の概要が記載されている Azure AD の多くの[ホワイトペーパー](https://www.microsoft.com/download/details.aspx?id=36391)を参照してください。

### <a name="plan-rollback"></a>ロールバックを計画する

自動ユーザー プロビジョニングの実装が運用環境で希望どおりに機能しない場合は、次のロールバック手順を実行して、以前の既知の正常な状態に戻すことができます。

1. [プロビジョニングの概要レポート](../app-provisioning/check-status-user-account-provisioning.md)と[プロビジョニング ログ](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)を確認して、影響を受けたユーザーやグループに対して行われた誤った操作を判別します。

1. プロビジョニング監査ログを使用して、影響を受けたユーザーまたはグループについて最新の既知の正常な状態を判別します。 また、ソース システム (Azure AD または AD) も確認します。

1. アプリケーションの所有者と協力し、最新の既知の正常な状態の値を使用して、アプリケーションで直接影響を受けたユーザーやグループを更新します。

## <a name="deploy-automatic-user-provisioning-service"></a>自動ユーザー プロビジョニング サービスをデプロイする

ソリューションの要件に合わせて手順を選択します。

### <a name="prepare-for-the-initial-cycle"></a>初回サイクルの準備をする

Azure AD プロビジョニング サービスを初めて実行すると、ソース システムとターゲット システムに対する初回サイクルにより、各ターゲット システムのすべてのユーザー オブジェクトのスナップショットが作成されます。

アプリケーションに対して自動プロビジョニングを有効にする場合、初回サイクルには 20 分から数時間かかることがあります。 この期間は、Azure AD ディレクトリのサイズと、プロビジョニングの対象となるユーザーの数によって異なります。

プロビジョニング サービスは、初回サイクル後の両方のシステムの状態を保管し、後続の増分サイクルのパフォーマンスを改善します。

### <a name="configure-automatic-user-provisioning"></a>自動ユーザー プロビジョニングの構成

[Azure portal](https://portal.azure.com/) を使用して、アプリケーションでサポートされている自動ユーザー アカウント プロビジョニングとプロビジョニング解除を管理します。 [アプリケーションへの自動プロビジョニングの設定方法](../app-provisioning/user-provisioning.md)に関する記事の手順に従ってください。

Azure AD ユーザー プロビジョニング サービスは、[Microsoft Graph API](/graph/api/resources/synchronization-overview) を使用して構成および管理することもできます。

## <a name="manage-automatic-user-provisioning"></a>自動ユーザー プロビジョニングを管理する

これでデプロイが完了したので、ソリューションを管理する必要があります。

### <a name="monitor-user-provisioning-operation-health"></a>ユーザー プロビジョニング操作の正常性を監視する

[初回サイクル](../app-provisioning/user-provisioning.md)が正常に完了すると、Azure AD プロビジョニング サービスは、次のいずれかのイベントが発生するまで、各アプリケーションに固有の間隔で無制限に増分更新を実行します。

* サービスが手動で停止され、[Azure portal](https://portal.azure.com/) を使用するか、適切な [Microsoft Graph API](/graph/api/resources/synchronization-overview) コマンドを使用して、新しい初回サイクルがトリガーされる。

* 属性マッピングまたはスコープ フィルターの変更によって、新しい初回サイクルがトリガーされる。

* 高いエラー率によりプロビジョニング プロセスが検疫に移行し、そのまま、自動的に無効になる 4 週間を超えて検疫にとどまっている。

これらのイベントと、プロビジョニング サービスによって実行されるその他のすべてのアクティビティを確認するには、Azure AD の[プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)を参照してください。

プロビジョニング サイクルの所要時間を把握し、プロビジョニング ジョブの進行状況を監視するために、[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)ことができます。

### <a name="gain-insights-from-reports"></a>レポートから分析情報を得る

Azure AD は、監査ログとレポートによって組織のユーザーのプロビジョニング使用状況と操作の正常性に関する[追加の分析情報](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を提供できます。

管理者は、プロビジョニングの概要レポートを確認して、プロビジョニング ジョブの操作の正常性を監視する必要があります。 プロビジョニング サービスによって実行されたアクティビティはすべて、Azure AD の監査ログに記録されます。 「[チュートリアル:自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

これらのレポートの所有権を引き受け、組織の要件が満たされる頻度で使用することをお勧めします。 Azure AD は、ほとんどの監査データを 30 日間保持します。

### <a name="troubleshoot"></a>トラブルシューティング

プロビジョニング中に発生する可能性がある問題のトラブルシューティングについては、次のリンクを参照してください。

* [Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題](../app-provisioning/application-provisioning-config-problem.md)

* [アプリケーションにプロビジョニングするためにオンプレミスの Active Directory から Azure AD に属性を同期する](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Azure Active Directory ギャラリー アプリケーションへのユーザー プロビジョニングを構成している間の管理者の資格情報の保存に関する問題](./user-provisioning.md)

* [Azure AD ギャラリー アプリケーションにユーザーがプロビジョニングされない](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Azure AD ギャラリー アプリケーションへプロビジョニングされた間違ったユーザー グループ](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>役に立つドキュメント

* [属性マッピングの式の書き方](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同期 API の概要](/graph/api/resources/synchronization-overview)

* [スコープ外に出るユーザー アカウントの削除をスキップする](skip-out-of-scope-deletions.md)

* [Azure AD Connect プロビジョニング エージェント: バージョンのリリース履歴](provisioning-agent-release-version-history.md)」を参照してください

#### <a name="resources"></a>リソース

* [製品フィードバックの提供](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Azure AD の更新情報](https://azure.microsoft.com/updates/?product=active-directory)

* [Microsoft Q&A Azure AD フォーラム](/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>次のステップ
* [自動ユーザー プロビジョニングの構成](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Microsoft Graph API を使用してプロビジョニング構成をエクスポートまたはインポートする](../app-provisioning/export-import-provisioning-configuration.md)

* [Azure Active Directory における属性マッピングの式の書き方](../app-provisioning/functions-for-customizing-application-data.md)