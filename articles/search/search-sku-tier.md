---
title: Choose a pricing tier
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の価格レベル (SKU) について学習します。 検索サービスをプロビジョニングできるレベルは、Free、Basic、Standard です。 Standard は、さまざまなリソース構成および容量レベルで使用できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99987875"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Cognitive Search の価格レベルの選択

[検索サービスの作成](search-create-service-portal.md)の部分は、サービスの有効期間にわたって固定される価格レベル (SKU) を選択することを意味 価格 (サービスの実行にかかる推定月間コスト) は、サービスの作成時にポータルの **[Select Pricing Tier]\(価格レベルの選択\)** ページに表示されます。 PowerShell または Azure CLI を使用してプロビジョニングする場合、レベルは **`-Sku`** パラメーターを使用して指定され、推定コストの詳細については、[サービスの価格](https://azure.microsoft.com/pricing/details/search/)に関するページを確認する必要があります。

選択したレベルによって以下が決まります。

+ サービスで許可されるインデックスおよびその他のオブジェクトの最大数
+ パーティション (物理ストレージ) のサイズと速度
+ 月単位の固定コストとしての課金対象のレート (ただし、容量を追加した場合は増分のコスト)

いくつかのインスタンスでは、選択したレベルによって[プレミアム機能](#premium-features)の使用の可否が決まります。

> [!NOTE]
> "Azure SKU" に関する情報をお探しですか? [[Azure の価格]](https://azure.microsoft.com/pricing/) から開始し、下にスクロールしてサービスごとの価格ページへのリンクを表示します。

## <a name="tier-descriptions"></a>サービス レベルの説明

レベルには、**Free**、**Basic**、**Standard**、**Storage Optimized** があります。 Standard と Storage Optimized は、いくつかの構成および容量で利用できます。 次の Azure portal のスクリーンショットは、使用可能なレベルを示しています。価格は除外されていますが、ポータルと[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)で確認できます。 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="価格レベルの表" border="true":::

**Free** は、チュートリアルやサンプルの実行など、小規模なプロジェクト用の限定された検索サービスを作成します。 内部的には、複数のサブスクライバー間でシステム リソースが共有されます。 無料のサービスをスケーリングしたり、重要なワークロードを実行したりすることはできません。

**Basic** と **Standard** は最も一般的に使用される課金対象のレベルで、より柔軟にワークロードをスケーリングできる **Standard** が既定値になっています。 自分の制御下の専用リソースを使用して、より大規模なプロジェクトのデプロイ、パフォーマンスの最適化、および容量の増加を行うことができます。

一部のレベルは、特定の種類の作業向けに設計されています。 たとえば、**Standard 3 High Density (S3 HD)** は、S3 用の "*ホスティング モード*" で、基になるハードウェアが多数の小さいインデックス用に最適化されており、マルチテナント シナリオ向けです。 S3 HD のユニットあたりの料金は S3 と同じですが、ハードウェアは数多くの小さいインデックスでの高速ファイル読み取り用に最適化されています。

**Storage Optimized** レベルでは、Standard レベルより安い TB あたりの価格で、大容量のストレージが提供されます。 主なトレードオフとしてクエリの待ち時間が長くなるので、特定のアプリケーションの要件に対して妥当かどうかを確認する必要があります。 このレベルのパフォーマンスに関する考慮事項の詳細については、[パフォーマンスと最適化の考慮事項](search-performance-optimization.md)に関するページを参照してください。

さまざまなレベルの詳細については、[Azure Cognitive Search サービスの制限](search-limits-quotas-capacity.md)に関する記事の[価格に関するページ](https://azure.microsoft.com/pricing/details/search/)、およびサービスのプロビジョニング時のポータル ページを参照してください。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>階層による機能の使用の可否

ほとんどの機能は Free レベルを含むすべてのレベルで使用できます。 場合によっては、選択したレベルによって機能を実装する能力に影響が生じることがあります。 次の表では、サービス レベルに関連する機能の制約について説明します。

| 機能 | 制限事項 |
|---------|-------------|
| [インデクサー](search-indexer-overview.md) | インデクサーは S3 HD では使用できません。  |
| [AI エンリッチメント](search-security-manage-encryption-keys.md) | Free レベルで実行されますが、推奨されていません。 |
| [送信 (インデクサー) アクセス用のマネージド (信頼できる) ID](search-howto-managed-identities-data-sources.md) | Free レベルでは使用できません。|
| [顧客が管理する暗号化キー](search-security-manage-encryption-keys.md) | Free レベルでは使用できません。 |
| [IP ファイアウォール アクセス](service-configure-firewall.md) | Free レベルでは使用できません。 |
| [プライベート エンドポイント (Azure Private Link との統合)](service-create-private-endpoint.md) | 検索サービスへの受信接続の場合、Free レベルでは利用できません。 インデクサーによる他の Azure リソースへの送信接続の場合は、Free または S3 HD では使用できません。 スキルセットを使用するインデクサーの場合、Free、Basic、S1、S3 HD では使用できません。| 
| [可用性ゾーン](search-performance-optimization.md) | Free レベルと Basic レベルでは使用できません。 |

リソース集中型の機能は、十分な容量を与えない限り、うまくいかない場合があります。 たとえば、[AI エンリッチメント](cognitive-search-concept-intro.md)には、データセットのサイズが小さい場合を除いて Free サービスではタイムアウトになってしまう、実行時間の長いスキルがあります。

## <a name="upper-limits"></a>上限

レベルによって、サービス自体の最大ストレージとともに、作成できるインデックス、インデクサー、データ ソース、スキルセット、シノニム マップの最大数が決まります。 すべての制限の詳細については、「[Azure Cognitive Search におけるサービスの制限](search-limits-quotas-capacity.md)」を参照してください。 

## <a name="partition-size-and-speed"></a>パーティションのサイズと速度

各レベルの価格には、パーティションごとのストレージの詳細 (Basic レベルの 2 GB からストレージ最適化 (L2) レベルの 2 TB まで) が含まれます。 その他のハードウェア特性 (動作速度、待機時間、転送速度など) は公開されませんが、特定のソリューション アーキテクチャ向けに設計されたレベルは、そのシナリオをサポートする機能を備えたハードウェア上に構築されます。 パーティションの詳細については、[容量の見積もりと管理](search-capacity-planning.md)および[パフォーマンスのためのスケーリング](search-performance-optimization.md)に関する記事を参照してください。

## <a name="billing-rates"></a>課金レート

課金レートはレベルによって異なり、高価なハードウェア上で実行されるレベルや、より高価な機能を提供するレベルの料金は高くなります。 レベルごとの課金レートは、Azure Cognitive Search の [Azure 価格ページ](https://azure.microsoft.com/pricing/details/search/)で確認できます。

サービスの作成後、課金レートはサービスの 24 時間実行に関する "*固定コスト*" と、容量を追加した場合の "*増分コスト*" の両方になります。

検索サービスには、"*パーティション*" (ストレージ用) と "*レプリカ*" (クエリ エンジンのインスタンス) の形でコンピューティング リソースが割り当てられます。 最初にそれぞれ 1 つを使用してサービスが作成され、請求レートには両方のリソースが含まれています。 ただし、容量をスケーリングした場合は、課金対象のレートの単位でコストが増減します。

具体的な例を次に示します。 請求レートが月あたり 100 ドルだとします。 検索サービスを初期容量である 1 つのパーティションと 1 つのレプリカで保持している場合、月末に支払うことが想定される金額は 100 ドルです。 しかし、高可用性を実現するために 2 つのレプリカを追加した場合、毎月の請求額は 300 ドル (最初のレプリカとパーティションのペアに対する 100 ドルに加えて、2 つのレプリカに対する 200 ドル) に増えます。

この価格モデルは、検索サービスで使用される "*検索ユニット*" (SU) の数に請求レートを適用するという考え方に基づいています。 すべてのサービスが最初に 1 つの SU でプロビジョニングされますが、大規模なワークロードを処理するために、パーティションまたはレプリカを追加して SU を増やすことができます。 詳細については、[検索サービスのコストを見積もる方法](search-sku-manage-costs.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

価格レベルを選択する最善の方法は、最低コストのレベルから開始し、その後、使用経験とテストから得られた情報に基づいて、そのサービスを維持するか、上位のレベルで新規に作成するかを決定することです。 次のステップとして、提案するテストのレベルに対応できるレベルで検索サービスを作成し、その後、次のガイダンスでコストと容量の見積もりに関する推奨事項を確認することをお勧めします。

+ [Search Service の作成](search-create-service-portal.md)
+ [コストの見積もり](search-sku-manage-costs.md)
+ [容量の見積もり](search-sku-manage-costs.md)