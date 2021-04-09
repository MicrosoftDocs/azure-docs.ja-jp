---
title: Azure リソース - QnA Maker
description: QnA Maker では、それぞれ異なる目的で複数の Azure ソースを使用します。 それらが個別にどのように使用されるかを理解すると、正しい価格レベルを計画して選択し、価格レベルを変更する時期を把握できるようになります。 これらを組み合わせて使用する方法を理解すると、発生した問題を見つけて修正できるようになります。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 2427fbdaa497ccb6d9a46330dcc6eb872e1d28ac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214169"
---
# <a name="azure-resources-for-qna-maker"></a>QnA Maker 用の Azure リソース

QnA Maker では、それぞれ異なる目的で複数の Azure ソースを使用します。 それらが個別にどのように使用されるかを理解すると、正しい価格レベルを計画して選択し、価格レベルを変更する時期を把握できるようになります。 これらを "_組み合わせて_" 使用する方法を理解すると、発生した問題を見つけて修正できるようになります。

## <a name="resource-planning"></a>リソースの計画

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

プロトタイプ フェーズで初めて QnA Maker ナレッジ ベースを開発する場合は、テストと運用の両方で 1 つの QnA Maker リソースを使用することが一般的です。

プロジェクトの開発フェーズに移行するときは、次のことを考慮する必要があります。

* ナレッジ ベース システムで保持される言語の数
* ナレッジ ベースを使用できるようにする必要があるリージョンの数
* システムによって各ドメイン内に保持されるドキュメントの数

1 つの QnA Maker リソースでは、同じ言語、同じリージョン、同じ主題の領域の組み合わせを持つすべてのナレッジ ベースを保持する計画を立てます。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

プロトタイプ フェーズで初めて QnA Maker マネージド ナレッジ ベースを開発する場合は、テストと運用の両方で 1 つの QnA Maker 管理対象リソースを使用することが一般的です。

プロジェクトの開発フェーズに移行するときは、次のことを考慮する必要があります。

* ナレッジ ベース システムで保持される言語の数
* ナレッジ ベースを使用できるようにする必要があるリージョンの数
* システムによって各ドメイン内に保持されるドキュメントの数

---

## <a name="pricing-tier-considerations"></a>価格レベルに関する考慮事項

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

通常、次の 3 つのパラメーターを考慮する必要があります。

* **サービスに必要なスループット**:
    * App Service の適切な[アプリ プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)を、実際のニーズに基づいて選択します。 アプリは[スケールアップ](../../../app-service/manage-scale-up.md)またはスケールダウンすることができます。
    * この点は、Azure **Cognitive Search** の SKU の選択にも影響します。詳細については、[こちら](../../../search/search-sku-tier.md)を参照してください。 さらに、レプリカで Cognitive Search の[容量](../../../search/search-capacity-planning.md)を調整することが必要になる場合があります。

* **ナレッジ ベースのサイズと数**: 実際のシナリオに合った適切な [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) を選択してください。 通常は、さまざまな主題の領域の数に基づいて必要なナレッジ ベースの数を決定します。 (1 つの言語の) 主題の領域を 1 つのナレッジ ベースに含めるようにします。

    特定のレベルで発行できるナレッジ ベースの数は N-1 件です (N は、そのレベルで許容される最大インデックス)。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

    たとえば、レベルに 15 個の許可されたインデックスがある場合、14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックスは、作成およびテスト用にすべてのナレッジ ベースで使用されます。

* **ソースとしてのドキュメントの数**: QnA Maker 管理サービスの無料の SKU では、ポータルと API で管理できるドキュメントの数が 3 つ (サイズはそれぞれ 1 MB) に限定されます。 Standard SKU では、管理できるドキュメントの数に制限はありません。 詳細については、[こちら](https://aka.ms/qnamaker-pricing)を参照してください。

次の表は、いくつかの基本的なガイドラインを示したものです。

|                            | QnA Maker 管理 | App Service | Azure Cognitive Search | 制限事項                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **実験**        | 無料の SKU             | Free レベル   | Free レベル    | 発行できる KB は 2 つまで (最大サイズ 50 MB)  |
| **Dev/Test 環境**   | Standard SKU         | 共有      | Basic        | 発行できる KB は 14 個まで (最大サイズ 2 GB)    |
| **運用環境** | Standard SKU         | Basic       | Standard     | 発行できる KB は 49 個まで (最大サイズ 25 GB) |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

通常、次の 3 つのパラメーターを考慮する必要があります。

* **サービスに必要なスループット**:
    * QnA Maker マネージド (プレビュー) は無料サービスであり、現在のスループットは、管理 API と予測 API の両方について 10 TPS に制限されています。
    * この点は、Azure **Cognitive Search** の SKU の選択にも影響します。詳細については、[こちら](../../../search/search-sku-tier.md)を参照してください。 さらに、レプリカで Cognitive Search の[容量](../../../search/search-capacity-planning.md)を調整することが必要になる場合があります。

* **ナレッジ ベースのサイズと数**: 実際のシナリオに合った適切な [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) を選択してください。 通常は、さまざまな主題の領域の数に基づいて必要なナレッジ ベースの数を決定します。 (1 つの言語の) 主題の領域を 1 つのナレッジ ベースに含めるようにします。

    QnA Maker マネージド (プレビュー) を使用するときは、KB 用の QnA Maker サービスを 1 つの言語または複数の言語のどちらで設定するかを選択できます。 この選択は、QnA Maker マネージド (プレビュー) サービスで最初のナレッジ ベースを作成するときに行うことができます。

    ![QnA Maker マネージド (プレビュー) 多言語ナレッジ ベース選択](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    特定のレベルで発行できるナレッジ ベースの数は、単一言語の場合は N-1 件、複数言語の場合は N/2 件です。N は、そのレベルで許容される最大インデックスです。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

    たとえば、レベルで 15 個のインデックスが許可されている場合、同じ言語で 14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックスは、作成およびテスト用にすべてのナレッジ ベースで使用されます。 異なる言語でナレッジ ベースを作成することを選択した場合は、発行できるナレッジ ベースの数は 7 個だけです。

* **ソースとしてのドキュメントの数**: QnA Maker マネージド (プレビュー) は無料サービスであり、ソースとして追加できるドキュメントの数に制限はありません。 詳細については、[こちら](https://aka.ms/qnamaker-pricing)を参照してください。

次の表は、いくつかの基本的なガイドラインを示したものです。

|                            |Azure Cognitive Search | 制限事項                      |
| -------------------------- |------------ | -------------------------------- |
| **実験**        |Free レベル    | 発行できる KB は 2 つまで (最大サイズ 50 MB)  |
| **Dev/Test 環境**   |Basic        | 発行できる KB は 14 個まで (最大サイズ 2 GB)    |
| **運用環境** |Standard     | 発行できる KB は 49 個まで (最大サイズ 25 GB) |

---

## <a name="recommended-settings"></a>推奨設定

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

|ターゲット QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1、1 個のレプリカ   | S1、1 個のレプリカ    |
| 50         | S3、10 個のレプリカ       | S1、12 個のレプリカ         |
| 80         | S3、10 個のレプリカ      |  S3、12 個のレプリカ  |
| 100         | P3V2、10 個のレプリカ  | S3、12 個のレプリカ、3 つのパーティション   |
| 200 - 250         | P3V2、20 個のレプリカ | S3、12 個のレプリカ、3 つのパーティション    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージドは無料サービスであり、現在のスループットは、管理 API と予測 API の両方について 10 トランザクション/秒に制限されています。 サービスの目標を 10 トランザクション/秒にするには、Azure Cognitive Search の S1 (1 インスタンス) SKU をお勧めします。

---

## <a name="when-to-change-a-pricing-tier"></a>価格レベルを変更する場合

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

|アップグレード|理由|
|--|--|
|QnA Maker 管理 SKU の[アップグレード](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)|ナレッジ ベースではさらに多くの QnA ペアまたはドキュメント ソースが必要です。|
|App Service SKU の[アップグレード](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service)、Cognitive Search のレベルの確認、および [Cognitive Search レプリカの作成](../../../search/search-capacity-planning.md)|ナレッジ ベースでクライアント アプリ (チャット ボットなど) からのより多くの要求に対応する必要がある。|
|Azure Cognitive Search サービスの[アップグレード](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)|多数のナレッジ ベースを使用する予定である。|

[Azure portal で App Service を更新](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)して、最新のランタイム更新プログラムを取得します。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

多数のナレッジ ベースを使用する予定の場合は、Azure Cognitive Search サービスを[アップグレードします](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)。

---

## <a name="keys-in-qna-maker"></a>QnA Maker のキー

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

QnA Maker サービスでは、App Service でホストされるランタイムと共に使用される **オーサリング キー** と **クエリ エンドポイント キー** の 2 種類のキーが扱われます。

これらのキーは、API を介してサービスに要求を行うときに使用します。

![キー管理](../media/authoring-key.png)

|名前|場所|目的|
|--|--|--|
|オーサリングまたはサブスクリプション キー|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|これらのキーは、[QnA Maker 管理サービス API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) へのアクセスに使用されます。 これらの API を使用して、ナレッジ ベース内の質問と回答を編集したり、ナレッジ ベースを公開したりできます。 これらのキーは、新しい QnA Maker サービスを作成するときに作成されます。<br><br>これらのキーは、 **[キーとエンドポイント]** ページの **Cognitive Services** リソースで確認できます。|
|クエリ エンドポイント キー|[QnA Maker ポータル](https://www.qnamaker.ai)|これらのキーは、公開されたナレッジ ベースのエンドポイントにクエリを実行して、ユーザーの質問に対する回答を取得する目的で使用されます。 通常、このクエリ エンドポイントは、QnA Maker サービスに接続するチャット ボットまたはクライアント アプリケーション コードで使用されます。 これらのキーは、QnA Maker ナレッジ ベースを公開するときに作成されます。<br><br>これらのキーは、 **[サービスの設定]** ページで確認できます。 このページは、ページの右上にあるドロップダウン メニュー内のユーザーのメニューに含まれています。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Azure portal でオーサリング キーを確認する

QnA Maker リソースを作成した Azure portal で、オーサリング キーの表示とリセットを実行できます。

1. Azure portal で QnA Maker リソースに移動し、種類が _Cognitive Services_ であるリソースを選択します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **[キーとエンドポイント]** に移動します。

    ![QnA Maker マネージド (プレビュー) サブスクリプション キー](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker ポータルでクエリ エンドポイント キーを確認する

ナレッジ ベースの呼び出しにはエンドポイント キーが使用されるため、エンドポイントはリソースと同じリージョンにあります。

エンドポイント キーは、[QnA Maker ポータル](https://qnamaker.ai)から管理することができます。

1. [QnA Maker ポータル](https://qnamaker.ai)にサインインし、お使いのプロファイルに移動してから、 **[サービスの設定]** を選択します。

    ![エンドポイント キー](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. キーを表示またはリセットします。

    > [!div class="mx-imgBorder"]
    > ![エンドポイント キー マネージャー](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >キーが漏えいしていると思われる場合は、キーを更新してください。 それに合わせて、クライアント アプリまたはボットのコードの変更が必要になることがあります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) サービスにより、顧客のサブスクリプションのサービスにアクセスするために、**オーサリング キー** と **Azure Cognitive Search キー** の 2 種類のキーが処理されます。

これらのキーは、API を介してサービスに要求を行うときに使用します。

![キー管理マネージド プレビュー](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|名前|場所|目的|
|--|--|--|
|オーサリングまたはサブスクリプション キー|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|これらのキーは、[QnA Maker 管理サービス API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) へのアクセスに使用されます。 これらの API を使用して、ナレッジ ベース内の質問と回答を編集したり、ナレッジ ベースを公開したりできます。 これらのキーは、新しい QnA Maker サービスを作成するときに作成されます。<br><br>これらのキーは、 **[キーとエンドポイント]** ページの **Cognitive Services** リソースで確認できます。|
|Azure Cognitive Search の管理者キー|[Azure Portal](../../../search/search-security-api-keys.md)|これらのキーは、ユーザーの Azure サブスクリプションにデプロイされている Azure Cognitive Search サービスとの通信に使用されます。 Azure Cognitive Search を QnA Maker マネージド (プレビュー) サービスに関連付けると、管理者キーが QnA Maker サービスに自動的に渡されます。 <br><br>これらのキーは、**Azure Cognitive Search** リソースの **[キー]** ページで確認できます。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Azure portal でオーサリング キーを確認する

QnA Maker マネージド (プレビュー) リソースを作成した Azure portal で、オーサリング キーの表示とリセットを実行できます。

1. Azure portal で QnA Maker マネージド (プレビュー) リソースに移動し、種類が *Cognitive Services* であるリソースを選択します。

    ![QnA Maker マネージド (プレビュー) リソース リスト](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. **[キーとエンドポイント]** に移動します。

    ![QnA Maker マネージド (プレビュー) サブスクリプション キー](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>リソースを更新する

ナレッジ ベースによって使用されるリソースをアップグレードする方法について説明します。 QnA Maker マネージド (プレビュー) は、プレビュー期間中、**無料** となります。 

---

## <a name="management-service-region"></a>管理サービスのリージョン

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

QnA Maker の管理サービスは、QnA Maker ポータルと初期データ処理でのみ使用されます。 このサービスは、**米国西部** リージョンでのみ利用できます。 この米国西部のサービスでは、顧客データは保存されません。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) では、管理サービスと予測サービスの両方が同じリージョンに併置されます。 現在 QnA Maker マネージド (プレビュー) は、**米国中南部、北ヨーロッパ、オーストラリア東部** で利用できます。

---

## <a name="resource-naming-considerations"></a>リソースの名前付けに関する考慮事項

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

`qna-westus-f0-b` などの QnA Maker リソースのリソース名は、他のリソースの名前付けにも使用されます。

Azure portal の作成ウィンドウを使うと、QnA Maker リソースを作成し、他のリソースの価格レベルを選択できます。

> [!div class="mx-imgBorder"]
> ![QnA Maker リソース作成の Azure portal のスクリーンショット](../media/concept-azure-resource/create-blade.png)

名前の末尾に文字が追加されるオプションの Application Insights リソースを除き、リソースが作成された後は同じ名前になります。

> [!div class="mx-imgBorder"]
> ![Azure portal リソース一覧のスクリーンショット](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> QnA Maker リソースを作成するときに新しいリソース グループを作成します。 これで、リソース グループで検索するときに、QnA Maker リソースに関連付けられているすべてのリソースを表示できるようになります。

> [!TIP]
> 名前付け規則を使用して、リソースまたはリソース グループの名前内の価格レベルを示します。 新しいナレッジ ベースの作成または新しいドキュメントの追加でエラーが発生する場合、Cognitive Search の価格レベルの制限が一般的な問題です。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

`qna-westus-f0-b` などの QnA Maker マネージド (プレビュー) リソースのリソース名は、他のリソースの名前付けにも使用されます。

Azure portal の作成ウィンドウを使用すると、QnA Maker マネージド (プレビュー) リソースを作成し、他のリソースの価格レベルを選択できます。

> [!div class="mx-imgBorder"]
> ![QnA Maker マネージド (プレビュー) リソース作成用の Azure portal のスクリーンショット](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) リソースは同じ名前で作成されます。

> [!div class="mx-imgBorder"]
> ![Azure portal の QnA Maker マネージド (プレビュー) リソース一覧のスクリーンショット](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> QnA Maker リソースを作成するときに新しいリソース グループを作成します。 これで、リソース グループで検索するときに、QnA Maker マネージド (プレビュー) リソースに関連付けられているすべてのリソースを表示できるようになります。

> [!TIP]
> 名前付け規則を使用して、リソースまたはリソース グループの名前内の価格レベルを示します。 新しいナレッジ ベースの作成または新しいドキュメントの追加でエラーが発生する場合、Cognitive Search の価格レベルの制限が一般的な問題です。

---

## <a name="resource-purposes"></a>リソースの目的

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

QnA Maker で作成される各 Azure リソースには特定の目的があります。

* QnA Maker リソース
* Cognitive Search リソース
* App Service
* App Plan サービス
* Application Insights サービス

### <a name="qna-maker-resource"></a>QnA Maker リソース

QnA Maker リソースを使うと、オーサリングおよび公開 API だけでなく、実行時に QnA ペアの中で自然言語処理 (NLP) ベースの 2 番目の順位のレイヤー (ランカー #2) にアクセスできます。

2 番目の順位では、メタデータとフォローアップ プロンプトを含めることができるインテリジェント フィルターが適用されます。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker リソースの構成設定

[QnA Maker ポータル](https://qnamaker.ai)で新しいナレッジ ベースを作成する場合、リソース レベルで適用される設定は **言語** 設定のみです。 リソースに最初のナレッジ ベースを作成するときに、言語を選択します。

### <a name="cognitive-search-resource"></a>Cognitive Search リソース

[Cognitive Search](../../../search/index.yml) リソースは次の目的で使用されます。

* QnA ペアを格納する
* 実行時に QnA ペアの初期順位 (ランカー #1) を指定する

#### <a name="index-usage"></a>インデックスの使用量

リソースには、テスト インデックスとして機能する 1 つのインデックスが保持され、残りのインデックスは、それぞれ 1 つの公開されたナレッジ ベースに関連付けられます。

15 個のインデックスを保持できる価格設定のリソースには、14 個の公開されたナレッジ ベースが保持され、1 つのインデックスがすべてのナレッジ ベースのテストに使用されます。 このテスト インデックスはナレッジ ベースによってパーティション分割されているため、対話型テスト ペインを使用するクエリにはテスト インデックスが使用されますが、特定のナレッジ ベースに関連付けられた特定のパーティションの結果のみが返されます。

#### <a name="language-usage"></a>言語の使用方法

QnA Maker リソースで作成された最初のナレッジ ベースは、Cognitive Search リソースとそのすべてのインデックス用の "_1 つの_" 言語セットを決定するために使用されます。 1 つの QnA Maker サービスには "_1 つの言語セット_" のみを使用できます。

#### <a name="using-a-single-cognitive-search-service"></a>1 つの Cognitive Search サービスの使用

ポータルを使用して QnA サービスとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成され、QnA Maker サービスにリンクされます。 これらのリソースが作成された後、既存の Search サービスを使用するように App Service 設定を更新して、先ほど作成されたサービスを削除できます。

QnA Maker リソース作成プロセスの一環で作成されたものとは異なる Cognitive Service リソースを使用するように QnA Maker を[構成する方法](../How-To/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)を確認してください。

### <a name="app-service-and-app-service-plan"></a>App Service と App Service プラン

[App Service](../../../app-service/index.yml) は、クライアント アプリケーションからランタイム エンドポイントを介して公開されたナレッジ ベースにアクセスするときに使用されます。

公開されたナレッジ ベースのクエリを実行する場合、公開されたナレッジ ベースにはすべて同じ URL エンドポイントが使用されますが、ルート内の **ナレッジ ベース ID** を指定します。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) は、チャット ログとテレメトリを収集するために使用されます。 サービスの詳細については、一般的な [Kusto クエリ](../how-to/get-analytics-knowledge-base.md)に関する記事を参照してください。

### <a name="share-services-with-qna-maker"></a>サービスを QnA Maker と共有する

QnA Maker はいくつかの Azure リソースを作成します。 管理を軽減し、コスト共有のメリットを得るために、次の表を使用して、共有できるものとできないものを理解してください。

|サービス|共有|理由|
|--|--|--|
|Cognitive Services|X|仕様により不可能|
|App Service プラン|✔|App Service プランに対して固定のディスク領域が割り当てられています。 同じ App Service プランを共有している他のアプリによって大量のディスク領域が使用された場合、QnA Maker App Service インスタンスで問題が発生します。|
|App Service|X|仕様により不可能|
|Application Insights|✔|共有できます|
|検索サービス|✔|1. `testkb` は QnA Maker サービス用の予約された名前です。他のサービスで使用することはできません。<br>2.`synonym-map` という名前のシノニム マップは、QnA Maker サービス用に予約されています。<br>3.公開されるナレッジ ベースの数は、Search サービスのレベルによって制限されます。 使用可能な空きインデックスがある場合は、他のサービスでそれらを使用できます。|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker マネージド (プレビュー) で作成される各 Azure リソースには特定の目的があります。

* QnA Maker リソース
* Cognitive Search リソース

### <a name="qna-maker-resource"></a>QnA Maker リソース

QnA Maker マネージド (プレビュー) リソースにより、作成および発行 API へのアクセスが提供され、ランク付けランタイムがホストされ、テレメトリが提供されます。

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search リソース

[Cognitive Search](../../../search/index.yml) リソースは次の目的で使用されます。

* QnA ペアを格納する
* 実行時に QnA ペアの初期順位 (ランカー #1) を指定する

#### <a name="index-usage"></a>インデックスの使用量

特定のレベルで発行できるナレッジ ベースの数は、単一言語の場合は N-1 件、複数言語の場合は N/2 件です。N は、Azure Cognitive Search レベルで許容される最大インデックスです。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

たとえば、レベルで 15 個のインデックスが許可されている場合、同じ言語で 14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックスは、作成およびテスト用にすべてのナレッジ ベースで使用されます。 異なる言語でナレッジ ベースを作成することを選択した場合は、発行できるナレッジ ベースの数は 7 個だけです。

#### <a name="language-usage"></a>言語の使用方法

QnA Maker マネージド (プレビュー) を使用するときは、ナレッジ ベース用の QnA Maker サービスを 1 つの言語または複数の言語のどちらで設定するかを選択できます。 この選択は、QnA Maker サービスで最初のナレッジ ベースを作成するときに行います。 ナレッジ ベースごとに言語の設定を有効にする方法については、[こちら](#pricing-tier-considerations)を参照してください。

---

## <a name="next-steps"></a>次のステップ

* QnA Maker の[ナレッジ ベース](../How-To/manage-knowledge-bases.md)について確認します
* [ナレッジ ベースのライフ サイクル](development-lifecycle-knowledge-base.md)を理解します
* サービスとナレッジ ベースの[制限](../limits.md)を確認します
