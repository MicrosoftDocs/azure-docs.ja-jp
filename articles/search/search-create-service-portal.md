---
title: ポータルで Search サービスを作成する
titleSuffix: Azure Cognitive Search
description: このポータル クイックスタートでは、Azure portal 内で Azure Cognitive Search リソースを設定する方法を説明します。 リソース グループ、リージョン、および SKU または価格レベルを選択します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/15/2021
ms.openlocfilehash: e183d81355d4db81e677f34b02330ddb9b631957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651988"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>クイック スタート:ポータルで Azure Cognitive Search サービスを作成する

[Azure Cognitive Search](search-what-is-azure-search.md) は、カスタム アプリにフルテキスト検索エクスペリエンスを追加する際に使用される Azure リソースです。 データや二次的な処理を提供する他の Azure サービス、ネットワーク サーバー上のアプリ、他のクラウド プラットフォーム上で実行されているソフトウェアと簡単に統合できます。

検索サービスは、[Azure portal](https://portal.azure.com/) を使用して作成できます。この記事ではこれを取り上げます。 また、[Azure PowerShell](search-manage-powershell.md)、[Azure CLI](/cli/azure/search)、[Management REST API](/rest/api/searchmanagement/)、または [Azure Resource Manager サービス テンプレート](https://azure.microsoft.com/resources/templates/101-azure-search-create/)を使用することもできます。

[![アニメーション GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>開始する前に

次のサービス プロパティは、サービスの有効期間にわたって固定されます。これらのいずれかを変更するには、新しいサービスが必要です。 これらは固定されているため、各プロパティを入力するときに使用の影響を考慮してください。

+ サービス名は URL エンドポイントの一部になります (有用なサービス名については、[ヒントを再確認](#name-the-service)してください)。
+ [サービス レベル](search-sku-tier.md)によって料金が変わり、容量の上限が設定されます。 一部の機能は、Free レベルでは使用できません。
+ サービス リージョンによって、特定のシナリオの可用性が決まる場合があります。 [高セキュリティ機能](search-security-overview.md)または [AI エンリッチメント](cognitive-search-concept-intro.md)が必要な場合は、Azure Cognitive Search を他のサービスと同じリージョンに作成するか、対象の機能が提供されているリージョンに作成する必要があります。 

## <a name="subscribe-free-or-paid"></a>サブスクリプション (無料または有料)

[無料の Azure アカウントを開き](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)、無料クレジットを使って有料の Azure サービスを試用できます。 このクレジットを使い切った後は、アカウントを保持したまま、Websites などの無料の Azure サービスを使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。

[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。 

## <a name="find-azure-cognitive-search"></a>Azure Cognitive Search を検索する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左上隅のプラス記号 ( **[+ リソースの作成]** ) をクリックします。

1. 検索バーを使用して "Azure Cognitive Search" を検索するか、 **[Web]**  >  **[Azure Cognitive Search]** を選択してリソースに移動します。

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="ポータルでリソースを作成する" border="false":::

## <a name="choose-a-subscription"></a>サブスクリプションを選択します。

サブスクリプションが複数ある場合には、Search サービスに使用するものを選択します。 [二重暗号化](search-security-overview.md#double-encryption)またはマネージド サービス ID に依存する他の機能を実装している場合は、Azure Key Vault またはマネージド ID の使用対象の他のサービスに対して使用されているものと同じサブスクリプションを選択します。

## <a name="set-a-resource-group"></a>リソース グループを設定する

リソース グループは、ご使用の Azure ソリューションの関連リソースを保持するコンテナーです。 これは検索サービスに必要です。 コストを含め、リソースの管理全般にも役立ちます。 リソース グループは、1 つのサービスの場合もあれば、一緒に使用される複数のサービスから成る場合もあります。 たとえば、Azure Cognitive Search を使用して Azure Cosmos DB データベースのインデックスを作成する場合、両方のサービスを同じリソース グループに追加して管理することができます。 

リソースを単一グループに結合していない場合、または関連のないソリューションで使用されるリソースが既存のリソース グループに格納されている場合は、Azure Cognitive Search リソース用の新しいリソース グループを作成します。 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="新しいリソース グループを作成する" border="false":::

時間が経つと共に、現在のコストと予想されるコストをまとめて追跡したり、個々のリソースの料金を確認したりできるようになります。 次のスクリーンショットは、複数のリソースを 1 つのグループにまとめたときに表示されるコスト情報の種類を示しています。

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="リソース グループ レベルでコストを管理する" border="false":::

> [!TIP]
> グループを削除するとその中のすべてのサービスが削除されるため、リソース グループによってクリーンアップが簡略化されます。 複数のサービスを利用するプロトタイプ プロジェクトの場合は、すべてのサービスを同じリソース グループに配置することで、プロジェクト終了後のクリーンアップが容易になります。

## <a name="name-the-service"></a>サービスに名前を付ける

サービスの名前は、[インスタンスの詳細] の **[URL]** フィールドに入力します。 この名前は、API 呼び出しの発行対象となる URL エンドポイントの一部となります (`https://your-service-name.search.windows.net`)。 たとえば、エンドポイントを `https://myservice.search.windows.net` とする場合は、「`myservice`」と入力します。

サービス名の要件:

+ search.windows.net 名前空間内で一意である
+ 長さは 2 文字から 60 文字の範囲にする
+ 小文字、数字、ダッシュ ("-") を使用する
+ 最初の 2 文字または最後の 1 文字にダッシュ ("-") を使用しない
+ 連続するダッシュ ("-") はどこにも使用しない

> [!TIP]
> 複数のサービスを使用する予定がある場合、名前付け規則として、サービス名にリージョン (場所) を含めることをお勧めします。 同じリージョン内のサービスはデータを無料で交換することができます。したがって、Azure Cognitive Search とそれ以外のサービスが米国西部にある場合、`mysearchservice-westus` のような名前を付けておけば、リソースの組み合わせ (関連付け) を決めるときに逐一プロパティ ページを確認する手間が省けます。

## <a name="choose-a-location"></a>場所を選択する

Azure Cognitive Search は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)」に記載されているように、ほとんどのリージョンで利用できます。 一般に、複数の Azure サービスを使用している場合は、データまたはアプリケーション サービスもホストしているリージョンを選択します。 そのようにすることで、送信データの帯域幅使用料を最小限またはゼロに抑えられます (サービスが同じリージョンにある場合、送信データには課金されません)。

+ [AI エンリッチメント](cognitive-search-concept-intro.md)を使用するには、Cognitive Services が Azure Cognitive Search と同じ物理リージョンに存在する必要があります。 ごくわずかですが、どちらか一方が提供されていないリージョンがあります。 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)」ページで、縦にチェック マークが 2 つ並んでいれば、両方の機能が利用できます。 どちらも利用できなければ、チェックマークは表示されません。

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="リージョン別の提供状況" border="true":::

+ 事業継続とディザスター リカバリー (BCDR) の要件は、[リージョン ペア](../best-practices-availability-paired-regions.md#azure-regional-pairs)に複数の検索サービスを作成することによって満たすことができます。 たとえば、北米で活動している場合は、各検索サービスについて米国東部と米国西部や、米国中北部と米国中南部などを選択できます。

いくつかの機能は、リージョンによっては利用が制限されています。 制限事項については、機能のドキュメントを参照してください。

+ [二重暗号化](search-security-overview.md#double-encryption)

+ [可用性ゾーン (「パフォーマンスのためのスケール」)](search-performance-optimization.md#availability-zones)

## <a name="choose-a-pricing-tier"></a>Choose a pricing tier

Azure Cognitive Search は現在、[複数の価格レベル](https://azure.microsoft.com/pricing/details/search/) (Free、Basic、Standard、ストレージ最適化) で提供されています。 レベルごとに独自の [容量と制限](search-limits-quotas-capacity.md)があります。 ガイダンスについては、[価格レベルの選択](search-sku-tier.md) に関する記事を参照してください。

運用環境のワークロードでは Basic と Standard が最も一般的な選択肢ですが、ほとんどのお客様は Free サービスから始めています。 レベルごとの主な違いは、パーティション サイズと速度、そして作成できるオブジェクトの数に対する制限です。

サービスの作成後に価格レベルを変更することはできないことに注意してください。 高いレベルまたは低いレベルが必要な場合は、サービスを作成し直す必要があります。

## <a name="create-your-service"></a>サービスの作成

必要な入力作業を終えたら、サービスの作成に進みます。 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="サービスの確認と作成" border="false":::

サービスは数分以内にデプロイされます。 Azure 通知を使用して進行状況を監視できます。 今後アクセスしやすくするために、サービスをご自分のダッシュ ボードにピン留めすることを検討してください。

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="サービスの監視とピン留め" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>キーと URL エンドポイントを取得する

ポータルを使用していない場合、新しいサービスにプログラムからアクセスするには、URL エンドポイントと認証 API キーを指定する必要があります。

1. **[概要]** ページの右側から、URL エンドポイントを探してコピーします。

1. **[キー]** のページで、いずれかの管理者キー (同等) をコピーします。 ご利用のサービスのオブジェクトを作成、更新、削除するためには、管理者の API キーが必要です。 これに対し、クエリ キーはインデックス コンテンツへの読み取りアクセスを提供します。

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="URL エンドポイントを含むサービスの概要ページ" border="false":::

ポータル ベースのタスクにエンドポイントとキーは必要ありません。 ポータルは、ご利用の Azure Cognitive Search リソースにあらかじめ管理者権限付きでリンクされています。 ポータルのチュートリアルについては、[クイック スタート: ポータルで Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)方法のページから始めてください。

## <a name="scale-your-service"></a>サービスを拡張する

サービスのプロビジョニングが完了したら、ニーズに合わせてサービスを拡張できます。 Azure Cognitive Search サービスの Standard レベルを選択している場合は、レプリカとパーティションの 2 つのディメンションでご利用のサービスをスケーリングできます。 Basic レベルを選択した場合は、レプリカのみ追加できます。 無料サービスをプロビジョニングした場合、拡張は利用できません。

***パーティション*** を使用すると、サービスでより多くのドキュメントを格納し、検索できます。

***レプリカ*** を使用すると、より大きい検索クエリの負荷をサービスが処理できます。

リソースを追加すると、月ごとの請求が増加します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使うと、リソース追加の課金への影響を把握できます。 負荷に基づいてリソースを調整できることに注意してください。 たとえば、リソースを増やして完全な初期インデックスを作成した後、増分インデックス作成に適したレベルまでリソースを減らすことができます。

> [!Important]
> サービスでは、[読み取り専用の SLA の場合は 2 つのレプリカ、読み取り/書き込み SLA の場合は 3 つのレプリカ](https://azure.microsoft.com/support/legal/sla/search/v1_0/)が必要です。

1. Azure Portal で検索サービス ページを開きます。
1. 左のナビゲーション ウィンドウで、 **[設定]**  >  **[スケール]** を選択します。
1. スライダーを使って、いずれかの種類のリソースを追加します。

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="レプリカとパーティションを使用して容量を追加する" border="false":::

> [!Note]
> パーティションごとのストレージと速度がより高いレベルで向上します。 詳細については、[容量と制限](search-limits-quotas-capacity.md)に関するページをご覧ください。

## <a name="when-to-add-a-second-service"></a>2 番目のサービスの追加が必要になる状況

大半のお客様は、[リソースの適切なバランス](search-sku-tier.md)を提供する階層にプロビジョニングされたサービスを 1 つだけ使用します。 1 つのサービスで、相互に分離された複数のインデックスをホストできます。インデックスは、[選択した階層の上限](search-capacity-planning.md)の対象になります。 Azure Cognitive Search では、要求は 1 つのインデックスにのみ転送でき、同じサービス内の他のインデックスから偶発的または意図的にデータが取得される可能性が最小限に抑えられます。

ほとんどのお客様はサービスを 1 つしか使いませんが、運用要件に次のことが含まれる場合、サービスの冗長性が必要になる場合があります。

+ [ビジネス継続性とディザスター リカバリー (BCDR)](../best-practices-availability-paired-regions.md)。 Azure Cognitive Search では、停止時の即時フェールオーバーは提供されません。

+ [マルチテナント アーキテクチャ](search-modeling-multitenant-saas-applications.md)では、2 つ以上のサービスが呼び出されることがあります。

+ グローバルにデプロイされたアプリケーションでは、待機時間を最小限に抑えるため、地理的な場所ごとに検索サービスが必要になることがあります。

> [!NOTE]
> Azure Cognitive Search では、インデックス作成とクエリの操作を分離することはできません。このため、ワークロードを分離するために複数のサービスを作成することはありません。 インデックスのクエリは常に、インデックスが作成されたサービスで行われます (あるサービスでインデックスを作成し、それを別のサービスにコピーすることはできません)。

高可用性のために 2 番目のサービスを作成する必要はありません。 クエリの高可用性は、同じサービスで 2 つ以上のレプリカを使用することにより実現されます。 レプリカの更新はシーケンシャルです。つまり、サービスの更新が展開されているとき、少なくとも 1 つのレプリカが動作しています。アップタイムについて詳しくは、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

サービスのプロビジョニングが完了した後、ポータル内で最初のインデックスの作成に進むことができます。

> [!div class="nextstepaction"]
> [クイック スタート: ポータルで Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> [Cost Management を使用してコスト分析を開始する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)