---
title: 課金および価格モデル
description: Azure Logic Apps での価格および課金モデルのしくみに関する概要
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 2b37308bcbcd489876c21dce56878de7e0daf545
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699030"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Azure Logic Apps の価格および課金モデル

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して、スケーリング可能な自動化された統合ワークフローをクラウドに作成して実行できます。 この記事では、Logic Apps サービスおよび関連リソースに対して、課金および価格モデルがどのように機能するかについて説明します。 具体的な価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。 コストを計画、管理、監視する方法については、[Azure Logic Apps のコストの計画と管理](plan-manage-costs.md)に関するページを参照してください。

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>マルチテナントの価格

従量課金制モデルは、パブリックの "グローバル" マルチテナント Logic Apps サービスで実行されるロジック アプリに適用されます。 成功した実行も失敗した実行も、すべて従量課金されます。

たとえば、ポーリング トリガーによって行われた要求は、そのトリガーがスキップされてロジック アプリのワークフロー インスタンスが作成されない場合でも、実行として従量課金されます。

| 項目 | 説明 |
|-------|-------------|
| [組み込みの](../connectors/apis-list.md#built-in)トリガーとアクション | Logic Apps サービスでネイティブに実行され、[**アクション** の価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して従量課金されます。 <p><p>たとえば、HTTP トリガーと Request トリガーは組み込みトリガーですが、HTTP アクションと Response アクションは組み込みアクションです。 データ操作、バッチ操作、変数操作のほか、ループ、条件、スイッチ、並列分岐などの[ワークフロー制御アクション](../connectors/apis-list.md#control-workflow)も組み込みアクションです。 |
| [Standard コネクタ](../connectors/apis-list.md#managed-connectors)のトリガーとアクション <p><p>[カスタム コネクタ](../connectors/apis-list.md#custom)のトリガーとアクション | [Standard コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して従量課金されます。 |
| [エンタープライズ コネクタ](../connectors/apis-list.md#managed-connectors)のトリガーとアクション | [エンタープライズ コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して従量課金されます。 ただし、パブリック プレビュー期間中のエンタープライズ コネクタは、[*Standard* コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して従量課金されます。 |
| [ループ](logic-apps-control-flow-loops.md)内のアクション | ループで実行される各アクションは、実行されるループ サイクルごとに従量課金されます。 <p><p>たとえば、リストを処理するアクションを含む "for each" ループがあるものとします。 Logic Apps サービスでは、リスト項目の数にループ内のアクションの数を乗算することによって、そのループで実行される各アクションに従量課金し、ループを開始するアクションを加算します。 したがって、10 項目のリストの場合の計算は (10 * 1) + 1 になり、結果は 11 個のアクション実行になります。 |
| 再試行 | 最も基本的な例外とエラーを処理するために、サポートされているトリガーとアクションに対して[再試行ポリシー](logic-apps-exception-handling.md#retry-policies)を設定できます。 これらの再試行は、元の要求と共に、トリガーまたはアクションの種類 (組み込み、Standard、エンタープライズ) に応じた料金で課金されます。 たとえば、アクションを実行して再試行が 2 回だった場合、3 回のアクション実行として課金されます。 |
| [データ保持とストレージ消費](#data-retention) | データ保持料金を使用して従量課金されます。[Logic Apps の価格ページ](https://azure.microsoft.com/pricing/details/logic-apps/)の「**価格の詳細**」表で確認できます。 |
|||

詳細については、「

* [View metrics for executions and storage consumption](plan-manage-costs.md#monitor-billing-metrics) (実行とストレージ消費のメトリックを表示する)
* [Azure Logic Apps の制限](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>従量課金されないもの

* 条件が満たされていないためスキップされるトリガー
* 終了前にロジック アプリが停止したために実行されなかったアクション
* [無効なロジック アプリ](#disabled-apps)

### <a name="other-related-resources"></a>その他の関連リソース

ロジック アプリは、統合アカウント、オンプレミス データ ゲートウェイ、統合サービス環境 (ISE) など、他の関連リソースと連携します。 これらのリソースの価格については、このトピックの後半にある次のセクションで確認してください。

* [On-premises data gateway (オンプレミス データ ゲートウェイ)](#data-gateway)
* [統合アカウントの価格モデル](#integration-accounts)
* [ISE の価格モデル](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>消費コストの見積もりのヒント

以下のヒントを確認すると、より正確な消費コストの見積もりに役立ちます。

* ポーリング間隔にのみ基づいて計算するのではなく、それぞれの日に到着する可能性のあるメッセージまたはイベントの数を考慮します。

* イベントまたはメッセージがトリガー条件を満たしている場合、多くのトリガーは、条件を満たす任意のすべての他の待機イベントまたはメッセージを直ちに読み取ろうとします。 この動作は、長いポーリング間隔を選択した場合でも、ワークフロー開始の対象となる待機イベントまたはメッセージの数に基づいてトリガーが起動することを意味します。 この動作に続くトリガーには、Azure Service Bus、Azure Event Hub などがあります。

  たとえば、毎日エンドポイントをチェックするトリガーを設定したとします。 トリガーはエンドポイントを確認し、条件を満たす 15 のイベントを検出した場合、トリガーが起動し、対応するワークフローを 15 回実行します。 Logic Apps サービスでは、トリガー要求を含め、これら 15 のワークフローによって実行されるすべてのアクションが従量課金されます。

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE の価格

固定価格モデルは、[*統合サービス環境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) で実行されるロジック アプリに適用されます。 ISE は、[統合サービス環境の価格](https://azure.microsoft.com/pricing/details/logic-apps)を使用して課金されますが、この価格は、作成する [ISE のレベルまたは *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に応じて変動します。 この価格は、予約容量および専用リソースに対する、使用の有無によらない支払いとなるため、マルチテナントの価格とは異なります。

| ISE SKU | 説明 |
|---------|-------------|
| **Premium** | 基本単位は[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)であり、[Premium SKU の 1 時間あたりの料金で課金されます](https://azure.microsoft.com/pricing/details/logic-apps)。 さらにスループットが必要な場合は、ISE の作成時または作成後に[スケール ユニットをさらに追加](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)できます。 スケール ユニットごとに、[基本単位の料金のおよそ半額となる 1 時間あたりの料金で課金](https://azure.microsoft.com/pricing/details/logic-apps)されます。 <p><p>容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 |
| **開発者** | 基本単位は[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)であり、[Developer SKU の 1 時間あたりの料金で課金されます](https://azure.microsoft.com/pricing/details/logic-apps)。 ただし、この SKU には、サービス レベル アグリーメント (SLA)、スケールアップ機能、リサイクル中の冗長性がありません。これは、遅延やダウンタイムが発生する可能性があることを意味します。 バックエンドの更新により、サービスが断続的に中断する場合があります。 <p><p>**重要**:この SKU は、必ず探索、実験、開発、テストにのみ使用し、運用環境やパフォーマンス テストには使用しないでください。 <p><p>容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 |
|||

### <a name="included-at-no-extra-cost"></a>追加コストなしで含まれるもの

| 項目 | 説明 |
|-------|-------------|
| [組み込みの](../connectors/apis-list.md#built-in)トリガーとアクション | **Core** というラベルが表示され、ロジック アプリと同じ ISE で実行されます。 |
| [Standard コネクタ](../connectors/apis-list.md#managed-connectors) <p><p>[エンタープライズ コネクタ](../connectors/apis-list.md#enterprise-connectors) | - **ISE** というラベルが表示されるマネージド コネクタは、オンプレミス データ ゲートウェイなしで機能するように特別に設計されており、ロジック アプリと同じ ISE で実行されます。 ISE の価格には、必要な数のエンタープライズ接続が含まれています。 <p><p>- ISE というラベルが表示されないコネクタは、マルチテナントの Logic Apps サービスで実行されます。 ただし、ISE の価格には、ISE で実行されるロジック アプリに関するこれらの実行が含まれます。 |
| [ループ](logic-apps-control-flow-loops.md)内のアクション | ISE の価格には、実行されるループ サイクルごとにループで実行される各アクションが含まれます。 <p><p>たとえば、リストを処理するアクションを含む "for each" ループがあるものとします。 アクションの合計実行数を得るには、リスト項目の数とループ内のアクションの数を乗算し、ループを開始するアクションを加算します。 したがって、10 項目のリストの場合の計算は (10 * 1) + 1 になり、結果は 11 個のアクション実行になります。 |
| 再試行 | 最も基本的な例外とエラーを処理するために、サポートされているトリガーとアクションに対して[再試行ポリシー](logic-apps-exception-handling.md#retry-policies)を設定できます。 ISE の価格には、元の要求に加えて再試行が含まれます。 |
| [データ保持とストレージ消費](#data-retention) | ISE 内のロジック アプリには、保持およびストレージのコストはかかりません。 |
| [統合アカウント](#integration-accounts) | 1 つの統合アカウント レベルの使用量が、ISE SKU に基づいて、追加コストなしで含まれます。 |
|||

制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関するページを参照してください。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>統合アカウント

[統合アカウント](../logic-apps/logic-apps-pricing.md#integration-accounts)は、[EDI](logic-apps-enterprise-integration-b2b.md) および [XML 処理](logic-apps-enterprise-integration-xml.md)の機能を使用する B2B 統合ソリューションを探索、構築、テストできるよう、作成してロジック アプリにリンクする独立したリソースです。

Azure Logic Apps では、ロジック アプリが使用量ベースであるか ISE ベースであるかに基づいて、[価格](https://azure.microsoft.com/pricing/details/logic-apps/)と[課金モデル](logic-apps-pricing.md#integration-accounts)によって異なる統合アカウント レベルまたは階層が提供されています。

| レベル | 説明 |
|------|-------------|
| **Basic** | メッセージの処理のみを必要とするシナリオ、または大規模なビジネス エンティティと取引パートナー関係がある小規模なビジネス パートナーとして機能する場合。 <p><p>Logic Apps の SLA でサポートされています。 |
| **Standard** | より複雑な B2B 関係があり、管理する必要があるエンティティの数が増えているシナリオ。 <p><p>Logic Apps の SLA でサポートされています。 |
| **Free** | 調査シナリオ用であり、運用シナリオでは使用しないでください。 このレベルには、リージョンの可用性、スループット、使用量に関する制限があります。 たとえば、Free レベルは、米国西部や東南アジアなどの Azure のパブリック リージョンのみで利用できますが、[Azure China 21Vianet](/azure/china/overview-operations) または [Azure Government](../azure-government/documentation-government-welcome.md) で利用することはできません。 <p><p>**注**:Logic Apps の SLA でサポートされていません。 |
|||

統合アカウントの制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)に関する次のようなページをご覧ください。

* [Azure サブスクリプションごとの統合アカウントの制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [統合アカウントごとの各種アーティファクトの制限](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)。 アーティファクトには取引先、契約、マップ、スキーマ、アセンブリ、証明書、バッチ構成などが含まれます。

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>従量課金ロジック アプリ用の統合アカウント

統合アカウントは、使用するアカウント レベルに基づいた、固定の[統合アカウント価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して課金されます。

### <a name="ise-based-logic-apps"></a>ISE ベースのロジック アプリ

ISE には、ISE SKU に基づいた 1 つの統合アカウントが追加コストなしで含まれます。 コストを追加すれば、ISE の統合アカウントを増やして[合計の ISE 制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)の上限まで使用することができます。 [ISE の価格モデル](#fixed-pricing)の詳細については、このトピックの前半を参照してください。

| ISE SKU | 含まれる統合アカウント | 追加コスト |
|---------|------------------------------|-----------------|
| **Premium** | 1 つの [Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 統合アカウント | 最大 19 個の追加の Standard アカウント。 Free または Basic アカウントは使用できません。 |
| **開発者** | 1 つの [Free](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 統合アカウント | 無料アカウントを既にお持ちの場合は、Standard アカウントを最大 19 個追加でご利用いただくことができます。また、無料アカウントをお持ちでない場合は、Standard アカウントを合計 20 個までご利用いただけます。 Basic アカウントは使用できません。 |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>データ保持とストレージ消費

ロジック アプリの実行履歴の入出力はすべて、そのアプリの[実行期間および履歴保持期間](logic-apps-limits-and-config.md#run-duration-retention-limits)に基づいて保存され、従量課金されます。

* マルチテナントの Logic Apps サービス内のロジック アプリについては、ストレージ消費は固定価格で課金されます。この価格は、[Logic Apps の料金ページ](https://azure.microsoft.com/pricing/details/logic-apps)にある「**価格の詳細**」表で確認できます。

* ISE 内のロジック アプリについては、ストレージ消費によってデータ保持コストが発生することはありません。

ストレージ消費の使用量を監視するには、[実行とストレージ消費のメトリックの表示](plan-manage-costs.md#monitor-billing-metrics)に関するページを参照してください。

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイ

[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-install.md)は、特定のゲートウェイでサポートされているコネクタを使用してロジック アプリからオンプレミス データにアクセスできるように作成する、独立したリソースです。 ゲートウェイを介して実行されるコネクタの操作には課金が発生しますが、ゲートウェイ自体には課金は発生しません。

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>無効なロジック アプリ

無効なロジック アプリは新しいインスタンスを作成できないため、無効になっている間は課金されません。 ロジック アプリを無効にした場合、現在実行中のインスタンスが完全に停止するまで少し時間がかかることがあります。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のコストを計画および管理する](plan-manage-costs.md)
