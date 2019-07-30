---
title: ポータルで Azure Search サービスを作成する - Azure Search
description: Azure portal で Azure Search リソースをプロビジョニングします。 リソース グループ、リージョン、および SKU または価格レベルを選択します。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d0d1dbb81f00f500f3eb95c605ed0c15c634f624
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706821"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>ポータルでの Azure Search サービスの作成

Azure Search は、カスタム アプリに検索エクスペリエンスを追加するために使用されるスタンドアロンのリソースです。 Azure Search は他の Azure サービスと簡単に統合できますが、スタンドアロン コンポーネントとして使用することも、ネットワーク サーバー上のアプリまたは他のクラウド プラットフォーム上で実行されているソフトウェアと統合することもできます。

この記事では、[Azure portal](https://portal.azure.com/) 内で Azure Search リソースを作成する方法を説明します。

[![アニメーション GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

PowerShell をお好みですか? Azure Resource Manager [サービス テンプレート](https://azure.microsoft.com/resources/templates/101-azure-search-create/)をご使用ください。 作業の開始にあたっては、[PowerShell を使用して Azure Search サービスを管理する方法](search-manage-powershell.md)に関するページを参照してください。

## <a name="subscribe-free-or-paid"></a>サブスクリプション (無料または有料)

[無料の Azure アカウントを開き](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)、無料クレジットを使って有料の Azure サービスを試用できます。 このクレジットを使い切った後は、アカウントを保持したまま、Websites などの無料の Azure サービスを使用できます。 明示的に設定を変更して課金を了承しない限り、クレジット カードに課金されることはありません。

[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。 MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。 

## <a name="find-azure-search"></a>Azure Search を探す

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 左上隅のプラス記号 ([+ リソースの作成]) をクリックします。
3. 検索バーを使用して "Azure Search" を検索するか、 **[Web]**  >  **[Azure Search]** を使用してリソースに移動します。

![Azure Search リソースに移動](./media/search-create-service-portal/find-search3.png "Azure Search へのナビゲーション パス")

## <a name="name-the-service-and-url-endpoint"></a>サービスと URL エンドポイントに名前を付ける

サービス名は、API 呼び出しを発行する対象となる URL エンドポイントの一部です。`https://your-service-name.search.windows.net` **[URL]** フィールドにサービス名を入力します。

たとえば、エンドポイントを `https://my-app-name-01.search.windows.net` とする場合は、「`my-app-name-01`」と入力します。

サービス名の要件:

* search.windows.net 名前空間内で一意である
* 2 ～ 60 文字である
* 小文字、数字、ダッシュ ("-") のみを使用する
* 最初の 2 文字または最後の 1 文字にダッシュ ("-") を使用していない
* 連続するダッシュ ("-") をどこにも使用していない

## <a name="select-a-subscription"></a>サブスクリプションの選択

サブスクリプションが複数ある場合には、データまたはファイル ストレージ サービスがあるものを 1 つ選択します。 Azure Search では、"[*インデクサー*](search-indexer-overview.md)" 経由でインデックスが作成されている場合に、Azure テーブルおよび Blob Storage、SQL Database、Azure Cosmos DB の自動検出が可能ですが、これは同じサブスクリプション内のサービスのみで有効です。

## <a name="select-a-resource-group"></a>リソース グループの選択

リソース グループとは、一緒に使用される Azure サービスとリソースのコレクションです。 たとえば、Azure Search を使用して SQL Database のインデックスを作成する場合、これら両方のサービスを同じリソース グループに含める必要があります。

リソースを単一グループに結合していない場合、または関連のないソリューションで使用されるリソースが既存のリソース グループに格納されている場合は、Azure Search リソース用の新しいリソース グループを作成します。

> [!TIP]
> リソース グループを削除すると、その中のサービスも削除されます。 複数のサービスを利用するプロトタイプ プロジェクトの場合は、すべてのサービスを同じリソース グループに配置することで、プロジェクト終了後のクリーンアップが容易になります。

## <a name="select-a-location"></a>場所を選択します。

Azure サービスの 1 つである Azure Search は、世界中のデータ センターでホストできます。 サポートされているリージョンの一覧は、[価格のページ](https://azure.microsoft.com/pricing/details/search/)にあります。 

別の Azure サービス (Azure Storage、Azure Cosmos DB、Azure SQL Database) によって提供されたデータにインデックスを付ける場合は、帯域幅の料金を避けるために、同じリージョン内に Azure Search サービスを作成することをお勧めします。 サービスが同じリージョン内にある場合、アウトバウンド データに料金はかかりません。

コグニティブ検索 AI エンリッチメントを使用している場合は、Cognitive Services リソースと同じリージョンにサービスを作成します。 *Azure Search と Cognitive Services を同じリージョンに配置することは、AI を強化するための要件です*。

> [!Note]
> インド中部は、現在、新しいサービスには使用できません。 既にインド中部で使用できるサービスについては、制限なしでスケールアップでき、サービスはそのリージョンで完全にサポートされます。 このリージョンに関する制限は一時的なものであり、新しいサービスのみに限定されます。 制限が適用されなったら、この注記を削除する予定です。

## <a name="select-a-pricing-tier-sku"></a>価格レベルの選択 (SKU)

[Azure Search は現在、複数の価格レベルで提供されています](https://azure.microsoft.com/pricing/details/search/)(Free、Basic、Standard)。 レベルごとに独自の [容量と制限](search-limits-quotas-capacity.md)があります。 ガイダンスについては、 [価格レベルまたは SKU の選択](search-sku-tier.md) に関する記事をご覧ください。

運用環境のワークロードでは通常 Standard を選ぶことになりますが、ほとんどのお客様は Free サービスから始めています。

サービスの作成後に価格レベルを変更することはできません。 後で高いレベルまたは低いレベルが必要になった場合は、サービスを作成し直す必要があります。

## <a name="create-your-service"></a>サービスの作成

サービスを作成するための必要な入力を入力します。 

![サービスの確認と作成](./media/search-create-service-portal/new-service3.png "サービスの確認と作成")

ご利用のサービスは数分以内にデプロイされ、Azure の通知を介してそれを監視できます。 今後アクセスしやすくするために、サービスをご自分のダッシュ ボードにピン留めすることを検討してください。

![サービスの監視とピン留め](./media/search-create-service-portal/monitor-notifications.png "サービスの監視とピン留め")

## <a name="get-a-key-and-url-endpoint"></a>キーと URL エンドポイントを取得する

ポータルを使用していない場合、新しいサービスにアクセスするには、URL エンドポイントと認証 API キーを指定する必要があります。

1. サービス概要ページの右側から、URL エンドポイントを探してコピーします。

2. 左側のナビゲーション ウィンドウから **[キー]** を選択し、いずれかの管理者キー (どちらも働きは同じです) をコピーします。 ご利用のサービスのオブジェクトを作成、更新、削除するためには、管理者の API キーが必要です。

   ![URL エンドポイントが表示されるサービス概要ページ](./media/search-create-service-portal/get-url-key.png "URL エンドポイントと他のサービス詳細")

ポータル ベースのタスクにエンドポイントとキーは必要ありません。 ポータルは、ご利用の Azure Search リソースにあらかじめ管理者権限付きでリンクされています。 ポータルのチュートリアルについては、[クイック スタート: ポータルで Azure Search インデックスを作成する](search-get-started-portal.md)から始めてください。

## <a name="scale-your-service"></a>サービスを拡張する

サービスのプロビジョニングが完了したら、ニーズに合わせてサービスを拡張できます。 Azure Search サービスの Standard レベルを選択している場合は、レプリカとパーティションの 2 つのディメンションでご利用のサービスをスケーリングできます。 Basic レベルを選択した場合は、レプリカのみ追加できます。 無料サービスをプロビジョニングした場合、拡張は利用できません。

***パーティション***を使用すると、サービスでより多くのドキュメントを格納し、検索できます。

***レプリカ***を使用すると、より大きい検索クエリの負荷をサービスが処理できます。

リソースを追加すると、月ごとの請求が増加します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使うと、リソース追加の課金への影響を把握できます。 負荷に基づいてリソースを調整できることに注意してください。 たとえば、リソースを増やして完全な初期インデックスを作成した後、増分インデックス作成に適したレベルまでリソースを減らすことができます。

> [!Important]
> サービスでは、[読み取り専用の SLA の場合は 2 つのレプリカ、読み取り/書き込み SLA の場合は 3 つのレプリカ](https://azure.microsoft.com/support/legal/sla/search/v1_0/)が必要です。

1. Azure Portal で検索サービス ページを開きます。
2. 左のナビゲーション ウィンドウで、 **[設定]**  >  **[スケール]** を選択します。
3. スライダーを使って、いずれかの種類のリソースを追加します。

![容量を追加する](./media/search-create-service-portal/settings-scale.png "レプリカとパーティションで容量を追加する")

> [!Note]
> パーティションごとのストレージと速度がより高いレベルで向上します。 詳細については、[容量と制限](search-limits-quotas-capacity.md)に関するページをご覧ください。

## <a name="when-to-add-a-second-service"></a>2 番目のサービスの追加が必要になる状況

大半のお客様は、[リソースの適切なバランス](search-sku-tier.md)を提供する階層にプロビジョニングされたサービスを 1 つだけ使用します。 1 つのサービスで、相互に分離された複数のインデックスをホストできます。インデックスは、[選択した階層の上限](search-capacity-planning.md)の対象になります。 Azure Search では、要求は 1 つのインデックスにのみ転送でき、同じサービス内の他のインデックスから偶発的または意図的にデータが取得される可能性が最小限に抑えられます。

ほとんどのお客様はサービスを 1 つしか使いませんが、運用要件に次のことが含まれる場合、サービスの冗長性が必要になる場合があります。

* 障害復旧 (データ センターの停止)。 Azure Search では、停止時の即時フェールオーバーは提供されません。 推奨事項とガイダンスについては、「[Azure Portal での Azure Search のサービス管理](search-manage.md)」をご覧ください。
* マルチ テナント モデルの調査により、サービスを追加するのが最適な設計であると判断された場合。 詳しくは、「[マルチテナント SaaS アプリケーションと Azure Search の設計パターン](search-modeling-multitenant-saas-applications.md)」をご覧ください。
* グローバルにデプロイされるアプリケーションで、アプリケーションの国際トラフィックの待機時間を最小限に抑えるため、複数のリージョンに Azure Search のインスタンスが必要な場合。

> [!NOTE]
> Azure Search では、インデックス作成とクエリのワークロードを分離することはできません。このため、ワークロードを分離するために複数のサービスを作成することはありません。 インデックスのクエリは常に、インデックスが作成されたサービスで行われます (あるサービスでインデックスを作成し、それを別のサービスにコピーすることはできません)。

高可用性のために 2 番目のサービスを作成する必要はありません。 クエリの高可用性は、同じサービスで 2 つ以上のレプリカを使用することにより実現されます。 レプリカの更新はシーケンシャルです。つまり、サービスの更新が展開されているとき、少なくとも 1 つのレプリカが動作しています。アップタイムについて詳しくは、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/search/v1_0/)」をご覧ください。

## <a name="next-steps"></a>次の手順

Azure Search サービスのプロビジョニングが完了した後、ポータル内で最初のインデックスの作成に進むことができます。

> [!div class="nextstepaction"]
> [クイック スタート:ポータルで Azure Search インデックスを作成する](search-get-started-portal.md)
