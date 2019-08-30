---
title: 価格と課金 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps の価格と課金のしくみについて説明します
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 62b96fa8722408aed81e87cb31218140fa4f5da8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099408"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps の価格モデル

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して、スケーリング可能な自動化された統合ワークフローをクラウドに作成して実行できます。 この記事では、Azure Logic Apps の課金と価格のしくみについて説明します。 価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>従量課金モデル

パブリック ("グローバル") な Azure Logic Apps サービスで実行される新しいロジック アプリの場合は、使用分についてのみ課金されます。 これらのロジック アプリでは、従量制のプランと価格モデルが使用されます。 ロジック アプリでは、各ステップがアクションになります。Azure Logic Apps によって、ロジック アプリで実行されるすべてのアクションが測定されます。

たとえば、アクションには以下が含まれます。

* トリガー。これは特別なアクションです。 すべてのロジック アプリには、最初のステップとしてトリガーが必要です。
* HTTP などの ["組み込み" (ネイティブ) アクション](../connectors/apis-list.md#built-in)や、Azure Functions と API Management の呼び出しなど。
* Outlook 365 や Dropbox などの[マネージド コネクタ](../connectors/apis-list.md##managed-connectors)の呼び出し。
* ループや条件付きステートメントなどの制御フローのステップ。

[標準コネクタ](../connectors/apis-list.md#managed-connectors)は、[標準コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps)で課金されます。 一般公開されている[エンタープライズ コネクタ](../connectors/apis-list.md#managed-connectors)は[エンタープライズ コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps)で課金され、パブリック プレビュー エンタープライズ コネクタは[標準コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps)で課金されます。

[トリガー](#triggers)と[アクション](#actions)に対する課金のしくみの詳細を参照してください。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定価格モデル

"[*統合サービス環境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)" には、Azure 仮想ネットワーク内のリソースにアクセスできるロジック アプリを作成して実行するための、プライベートな分離された専用の方法が用意されています。 ISE 内で実行される新しいロジック アプリでは、次の機能に対して、[固定の月額料金](https://azure.microsoft.com/pricing/details/logic-apps)を支払います。

* [組み込みのトリガーとアクション](../connectors/apis-list.md#built-in)

* [標準コネクタ](../connectors/apis-list.md#managed-connectors)

* 必要な数の接続がある[エンタープライズ コネクタ](../connectors/apis-list.md#enterprise-connectors)

* 追加コストなしの[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。お使いの [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に基づきます。

  * **Premium SKU**:単一の [Standard レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)の統合アカウント

  * **Developer SKU**:単一の[Free レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)の統合アカウント

  各 ISE SKU の統合アカウントは、合計で 5 つに制限されています。 コストを追加することで、ISE SKU に基づいて、さらに多くの統合アカウントを作成できます。

  * **Premium SKU**:最大 4 つの追加の Standard アカウント。 無料または Basic アカウントはありません。

  * **Developer SKU**:最大 4 つの追加の Standard アカウント、または合計で最大 5 つの標準 Standard アカウント。 Basic アカウントはありません。

統合アカウントの制限については、「[Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)」をご覧ください。 このトピックで後述する[統合アカウントのレベルとそれらの価格モデル](#integration-accounts)で詳細を確認できます。

Premium ISE SKU では、基本単位の容量は固定されているため、さらにスループットが必要な場合は、作成中または作成後に[スケール ユニットを追加](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)できます。 Developer ISE SKU には、スケール ユニットを追加する機能はありません。 ISE で実行するロジック アプリでは、データ保持のコストはかかりません。

> [!NOTE]
> ISE 内で、組み込みのトリガーとアクションは **Core** というラベルを表示し、ロジック アプリと同じ ISE で実行されます。 **ISE** というラベルが表示される標準およびエンタープライズ コネクタは、ロジック アプリと同じ ISE で実行されます。 ISE というラベルが表示されないコネクタは、グローバルな Logic Apps サービスで実行されます。

価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

<a name="connectors"></a>

## <a name="connectors"></a>Connectors

Azure Logic Apps のコネクタを使用すると、ご自身のロジック アプリに[トリガー](#triggers)、[アクション](#actions)、または両方を用意することで、クラウドまたはオンプレミスのアプリ、サービス、およびシステムにアクセスできます。 コネクタは、標準またはエンタープライズのいずれかとして分類されます。 これらの接続の概要については、「[Azure Logic Apps のコネクタ](../connectors/apis-list.md)」を参照してください。 ロジック アプリで使用する REST API 用の事前構築済みコネクタが利用できない場合、これらの REST API の単なるラッパーである[カスタム コネクタ](https://docs.microsoft.com/connectors/custom-connectors)を作成できます。 カスタム コネクタは標準コネクタとして課金されます。 以下のセクションで、トリガーとアクションの課金のしくみについて詳しく説明します。

<a name="triggers"></a>

## <a name="triggers"></a>トリガー

トリガーは、特定のイベントが発生したときに、ロジック アプリ インスタンスを作成する特別なアクションです。 トリガーにはさまざまな動作があり、それによってロジック アプリの課金方法が変わります。 Azure Logic Apps 内に存在するさまざまな種類のトリガーを次に示します。

* **ポーリング トリガー**:このトリガーは、ロジック アプリ インスタンスを作成してワークフローを開始する条件を満たすメッセージの有無について、継続的にエンドポイントをチェックします。 ロジック アプリ インスタンスが作成されていない場合でも、Logic Apps は各ポーリング要求を実行として課金します。 ポーリング間隔を指定するには、ロジック アプリ デザイナーを使用してトリガーを設定します。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook トリガー**:このトリガーは、クライアントが特定のエンドポイントに要求を送信するまで待機します。 Webhook のエンドポイントに送信された個々の要求がアクションの実行としてカウントされます。 たとえば、要求 Webhook トリガーと HTTP Webhook トリガーはどちらも Webhook トリガーです。

* **繰り返しトリガー**:このトリガーは、トリガー内に設定された繰り返し間隔に基づいてロジック アプリ インスタンスを作成します。 たとえば、3 日ごとに実行されるトリガーや、より複雑なスケジュールで実行されるトリガーを設定できます。

<a name="actions"></a>

## <a name="actions"></a>Actions

Logic Apps では、HTTP などの "組み込み" アクションはネイティブ アクションとして課金されます。 たとえば、組み込みアクションには、HTTP 呼び出し、Azure Functions または API Management からの呼び出し、Condition、Loop、Switch ステートメントなどの制御フローのステップが含まれます。 各アクションには、独自のアクションの種類があります。 たとえば、[コネクタ](https://docs.microsoft.com/connectors)を呼び出すアクションの種類は "ApiConnection" です。 これらのコネクタは、標準コネクタまたはエンタープライズ コネクタのいずれかに分類され、該当する[価格](https://azure.microsoft.com/pricing/details/logic-apps)に基づいて課金されます。 "*プレビュー*" 段階のエンタープライズ コネクタは、標準コネクタとして課金されます。

Azure Logic Apps では、すべての成功したアクションと失敗したアクションが実行として課金されます。 ただし、Logic Apps では、次のアクションは課金されません。

* 条件が満たされていないためスキップされたアクション
* 終了前にロジック アプリが停止したために実行されなかったアクション

ループ内で実行されるアクションについては、Azure Logic Apps では、ループ内のサイクルごとに各アクションがカウントされます。 たとえば、リストを処理する "for each" ループがあるものとします。 この場合は、リスト項目の数にループ内のアクションの数を乗算したものに、ループを開始するアクションを追加した合計のアクション数に対して課金されます。 したがって、10 項目のリストの場合の計算は (10 * 1) + 1 になり、結果は 11 個のアクション実行になります。

## <a name="disabled-logic-apps"></a>無効なロジック アプリ

無効なロジック アプリは新しいインスタンスを作成できないため、無効になっている間は課金されません。 ロジック アプリを無効にした場合、現在実行中のインスタンスが完全に停止するまで少し時間がかかることがあります。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>統合アカウント

Azure Logic Apps の [B2B および EDI 機能](logic-apps-enterprise-integration-b2b.md)と [XML 処理機能](logic-apps-enterprise-integration-xml.md)を追加コストなしで調査、開発、およびテストできる[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)には、[固定価格モデル](https://azure.microsoft.com/pricing/details/logic-apps)が適用されます。 各 Azure サブスクリプションでは、[特定の統合アカウントの制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)まで、統合アカウントを持つことができます。 各統合アカウントでは、特定の[成果物の制限](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)まで、成果物 (取引先、契約、マップ、スキーマ、アセンブリ、証明書、バッチ構成など) を格納できます。

Azure Logic Apps には、Free、Basic、Standard の統合アカウントが用意されています。 Basic および Standard レベルは、Logic Apps サービス レベル アグリーメント (SLA) によってサポートされますが、Free レベルは SLA でサポートされず、スループットと使用に制限があります。 Free レベルの統合アカウントを除いて、各 Azure リージョンで複数の統合アカウントを持つことができます。 価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」を参照してください。

[Premium または Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) のいずれかの ["*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) がある場合は、お使いの ISE で合計 5 つの統合アカウントを持つことができます。 ISE での固定価格モデルのしくみについては、このトピックの「[固定価格モデル](#fixed-pricing)」セクションを参照してください。 価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

Free、Basic、または Standard の統合アカウントから選択するには、次のユース ケースの説明を確認してください。

* **Free**:運用シナリオではなく、調査シナリオを試す場合。

* **Basic**:メッセージの処理のみを必要とする場合、または大規模なビジネス エンティティと取引パートナー関係がある小規模なビジネス パートナーとして機能する場合。

* **Standard**:より複雑な B2B 関係があり、管理する必要があるエンティティの数が増えている場合。

<a name="data-retention"></a>

## <a name="data-retention"></a>データの保持

統合サービス環境 (ISE) で実行されるロジック アプリを除き、ロジック アプリの[実行保有期間](logic-apps-limits-and-config.md#run-duration-retention-limits)に基づいて、ご利用のロジック アプリの実行履歴に格納されるすべての入力と出力に課金されます。 ISE で実行するロジック アプリでは、データ保持のコストはかかりません。 価格については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

ご自身のロジック アプリのストレージ消費量を監視するために、以下を実行できます。

* ロジック アプリで毎月使用されているストレージ ユニットの数 (GB 単位) を表示します。
* ロジック アプリの実行履歴内の特定のアクションの入力と出力のサイズを表示します。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>ロジック アプリのストレージ消費量を表示する

1. Azure Portal で、ご利用のロジック アプリを探して選択します。

1. ロジック アプリのメニューで、 **[監視]** から **[メトリック]** を選択します。

1. 右側のウィンドウで、 **[グラフのタイトル]** の下の **[メトリック]** の一覧から **[ストレージ使用実行の利用状況に応じた課金]** を選択します。

   このメトリックでは、請求対象となる 1 か月あたりのストレージ消費ユニットの数 (GB 単位) が示されます。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>アクションの入力と出力のサイズを表示する

1. Azure Portal で、ご利用のロジック アプリを探して選択します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。

1. 右側のウィンドウで、 **[実行履歴]** から、入力と出力を確認する実行を選択します。

1. **[ロジック アプリの実行]** から **[実行の詳細]** を選択します。

1. **[ロジック アプリの実行の詳細]** ウィンドウで、各アクションの状態と持続時間を一覧表示しているアクション テーブルから、表示するアクションを選択します。

1. **[ロジック アプリのアクション]** ウィンドウで、それぞれ**入力リンク**と**出力リンク**の下に表示されるアクションの入力と出力のサイズを探します。

## <a name="next-steps"></a>次の手順

* [Azure Logic Apps の詳細を確認する](logic-apps-overview.md)
* [初めてのロジック アプリの作成](quickstart-create-first-logic-app-workflow.md)
