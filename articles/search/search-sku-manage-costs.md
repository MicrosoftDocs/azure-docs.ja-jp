---
title: コストの見積もり
titleSuffix: Azure Cognitive Search
description: Cognitive Search サービスの実行コストを管理するための課金対象のイベント、価格モデル、およびヒントについて説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539558"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search サービスのコストの見積りと管理を行う方法

この記事では、Azure Cognitive Search サービスの実行コストを管理するための価格モデル、課金対象のイベント、およびヒントについて説明します。

## <a name="pricing-model"></a>価格モデル

Azure Cognitive Search のスケーラビリティ アーキテクチャは、レプリカとパーティションの柔軟な組み合わせに基づいているため、クエリやインデックス作成を行う能力がさらに必要かどうかに応じて容量を変え、必要な分だけ支払うことができます。

検索サービスによって使用されるリソースの量に、サービス レベルによって設定される課金レートを乗算することにより、サービスの実行コストが決まります。 コストと容量は緊密に結びついています。 コストを見積もるときは、インデックス作成とクエリ ワークロードを実行するために必要な容量を理解することで、予想されるコストについて詳細に把握できます。

課金の目的では、次の 2 つの簡単な式に注意する必要があります。

| Formula | 説明 |
|---------|-------------|
| **R x P = SU** | 使用されるレプリカの数と使用されるパーティションの数を乗算した値が、サービスで使用される "*検索ユニット*" (SU) の数量と等しくなります。 SU はリソースの単位で、パーティションまたはレプリカのいずれかにすることができます。 |
| **SU * 課金レート = 月額料金** | SU の数に、サービスをプロビジョニングしたサービス レベルの課金レートを乗算した値が、毎月の請求額の主な決定要因となります。 一部の機能またはワークロードは他の Azure サービスに依存しているため、ソリューションのコストがサブスクリプション レベルで増加する可能性があります。 次の「課金対象のイベント」セクションでは、請求に加算される可能性がある機能を示しています。 |

すべてのサービスは、最小値として、1 SU (1 つのレプリカと 1 つのパーティションの積) から開始します。 どのサービスも、最大値は 36 SU です。 この最大値に達するパターンは、いくつかあります。たとえば、6 パーティション x 6 レプリカ、3 パーティション x 12 レプリカなどです。 一般には、総容量よりも少ない量を使用します (たとえば、3 レプリカと 3 パーティションのサービスで、9 SU の課金)。 有効な組み合わせについては、「[パーティションとレプリカの組み合わせ](search-capacity-planning.md#chart)」の表を参照してください。

課金レートは、SU ごとの 1 時間単位です。 レベルごとに、レートが高くなっていきます。 レベルが高いほど、パーティションは大きく高速になり、そのレベルの時間あたりの料金が全体的に高くなります。 各レベルのレートは、[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページでご覧いただけます。

ほとんどのお客様は、総容量の一部だけをオンラインにし、残りを取っておきます。 課金については、オンラインにするパーティションとレプリカの数 (SU 式を使用して計算) によって、時間単位で支払う金額が決まります。 

## <a name="billable-events"></a>課金対象のイベント

Azure Cognitive Search 上に構築されたソリューションでは、次のようなコストが発生する場合があります。

+ 最小の構成 (1 つのパーティションとレプリカ) で 24 時間 365 日実行される[サービス自体のコスト](#service-costs) (基本料金)。 これは、サービスを実行する固定費と考えることができます。

+ 容量 (レプリカまたはパーティション) の追加 (コストは課金対象のレートに応じて増加します)。 高可用性がビジネス要件である場合は、3 つのレプリカが必要です。

+ 帯域幅料金 (送信データ転送)

+ 特定の機能に必要なアドオンサービス:

  + AI エンリッチメント ([Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) が必要)
  + ナレッジ ストア ([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) が必要)
  + インクリメンタル エンリッチメント ([Azure Storage](https://azure.microsoft.com/pricing/details/storage/) が必要。AI エンリッチメントに適用される)
  + カスタマー マネージド キーと二重暗号化 ([Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) が必要)
  + インターネットにアクセスできないモデル用のプライベート エンドポイント ([Azure Private Link が必要](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>サービスのコスト

課金を回避するために "一時停止" できる仮想マシンやその他のリソースとは異なり、Azure Cognitive Search サービスは、お客様が独占的に使用できる専用のハードウェア上で常に使用可能です。 そのため、サービスの作成は課金対象のイベントであり、そのサービスを作成したときに開始され、そのサービスを削除したときに終了します。 

最低料金は、課金対象のレートでの最初の検索単位 (1 つのレプリカ x 1 つのパーティション) です。 これより小さい構成ではサービスは実行できないため、この最低料金はサービスの有効期間を通して一定です。 

最小構成を超える場合は、レプリカとパーティションを互いに独立して追加できます。 レプリカおよびパーティションによって容量が徐々に増加すると、 **(レプリカ数 x パーティション数 x 課金レート)** という式に基づいて料金が増加します。この場合、課金されるレートは、選択した価格レベルによって異なります。

検索ソリューションのコストを見積もる際には、価格と容量は直線的に比例するものではないことに注意してください (容量を 2 倍にすると、コストは 2 倍以上になります)。 数式による計算の例については、「[レプリカとパーティションを割り当てる方法](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)」を参照してください。

### <a name="bandwidth-charges"></a>帯域幅の料金

Azure データ ソースが Azure Cognitive Search とは別のリージョンにある場合、[インデクサー](search-indexer-overview.md)の使用は課金に影響します。 このシナリオでは、Azure データ ソースから Azure Cognitive Search へ送信データを移動させるためのコストが発生する可能性があります。 詳細については、該当する Azure データ プラットフォームの価格に関するページを参照してください。

Azure Cognitive Search サービスをデータと同じリージョンに作成すれば、データ エグレス料金が発生する事態を回避できます。 以下に、[帯域幅の価格に関するページ](https://azure.microsoft.com/pricing/details/bandwidth/)の情報の一部を示します。

+ 受信データ:Azure 上のどのサービスも、受信データには料金がかかりません。 

+ 送信データ:送信データではクエリ結果が参照されます。 Cognitive Search では送信データに料金がかかりませんが、サービスが異なるリージョンにある場合、Azure からの送信料金が発生する可能性があります。

  これらの料金は、実際には Azure Cognitive Search の課金の一部ではありません。 これらがここで説明されているのは、他のリージョンまたは Azure 以外のアプリに結果を送信する場合、これらのコストが全体の請求額に反映される可能性があるためです。

### <a name="ai-enrichment-with-cognitive-services"></a>Cognitive Services を使用する AI エンリッチメント

[AI エンリッチメント](cognitive-search-concept-intro.md)の場合は、従量課金制の処理について、Azure Cognitive Search と同じリージョンの S0 価格レベルで、[有料の Azure Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)ように計画することをお勧めします。 Cognitive Services のアタッチには、関連する固定コストはありません。 課金の対象となるのは、必要な処理の分だけです。

| 操作 | 課金への影響 |
|-----------|----------------|
| ドキュメント解析、テキスト抽出 | Free |
| ドキュメント解析、画像抽出 | ドキュメントから抽出された画像の数に基づいて課金されます。 **インデクサー構成** で、[imageAction](/rest/api/searchservice/create-indexer#indexer-parameters) は、画像抽出をトリガーするパラメーターです。 **imageAction** が "none" (既定値) に設定されている場合、画像の抽出に対して課金されません。 画像抽出のレートは、Azure Cognitive Search の[価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページに記載されています。|
| [組み込みのコグニティブ スキル](cognitive-search-predefined-skills.md) | Cognitive Services を直接使用してそのタスクを実行した場合と同じレートで課金されます。 |
| カスタム スキル | カスタム スキルは、自分が提供する機能です。 カスタム スキルを使用した場合のコストは、カスタム コードで他の従量制サービスを呼び出しているかどうかによって決まります。 |

[インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) 機能を使用するとキャッシュを提供でき、インデクサーがコグニティブ スキル (将来、スキルセットを変更する場合に必要) のみを効率的に実行できるようになり、時間とコストが節約されます。

## <a name="tips-for-managing-costs"></a>コスト管理のヒント

次のガイダンスは、コストを削減し、コストをより効果的に管理するのに役立ちます。

+ 帯域幅の料金を最小限に抑えるかなくすために、すべてのリソースを同一のリージョンか可能な限り少ないリージョンに作成します。

+ Azure Cognitive Search、Cognitive Services、ご利用のソリューションで使用されているその他の Azure サービスなど、すべてのサービスを 1 つのリソース グループに統合します。 Azure portal で、リソース グループを見つけ、 **[コスト管理]** のコマンドを使用して、実際の支出と予想される支出を把握します。

+ フロントエンド アプリケーションには、要求と応答がデータ センターの境界内に収まるように Azure Web アプリを検討します。

+ インデックス作成などのリソースを大量に消費する操作に対してはスケールアップし、通常のクエリ ワークロードに対して再調整してスケールダウンします。 Azure Cognitive Search の最小構成 (1 つのパーティションと 1 つのレプリカで構成された 1 つの SU) から開始し、ユーザー アクティビティを監視して、容量の増加に対するニーズを示す使用パターンを特定します。 予測可能なパターンがある場合は、スケールをアクティビティと同期できることがあります (これを自動化するにはコードを記述する必要があります)。

+ コスト管理は、Azure インフラストラクチャに組み込まれています。 コスト、ツール、API の追跡の詳細については、[課金とコスト管理](../cost-management-billing/cost-management-billing-overview.md)に関する記事を参照してください。

検索サービスを一時的にシャットダウンすることはできません。 専用リソースは常に動作し、サービスの有効期間中、お客様専用として割り当てられています。 サービスの削除は永続的なものであり、関連付けられているデータも削除されます。

サービス自体に関して、課金を抑える唯一の方法は、レプリカとパーティションを、まだ許容可能なパフォーマンスと [SLA コンプライアンス](https://azure.microsoft.com/support/legal/sla/search/v1_0/)を提供する低いレベルに下げるか、より低いレベルでサービスを作成することです (S1 の時間あたりの料金は S2 または S3 の料金よりも低くなります)。 負荷予測の下限でサービスをプロビジョニングすると仮定した場合、サービスが拡大したら、2 番目に高いレベルのサービスを作成し、その 2 番目のサービスでインデックスを再構築してから、最初のサービスを削除することができます。

## <a name="next-steps"></a>次の手順

Azure サブスクリプション全体のコストを監視および管理する方法について説明します。

> [!div class="nextstepaction"]
> [Azure Cost Management および Billing のドキュメント](../cost-management-billing/cost-management-billing-overview.md)