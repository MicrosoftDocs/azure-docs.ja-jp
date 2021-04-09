---
title: Azure Cosmos DB の価格モデル
description: この記事では、Azure Cosmos DB の価格モデルと、それによってコスト管理とコスト計画がどのように合理化されるかについて説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247456"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Azure Cosmos DB の価格モデル
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB の価格モデルにより、コストの管理と計画が合理化されます。 Azure Cosmos DB では、データベースに対して行った操作と、データに使用されるストレージに対して課金されます。

- **データベース操作**:データベース操作に対する課金方法は、使用している Azure Cosmos アカウントの種類によって異なります。

  - **プロビジョニング済みのスループット**:[プロビジョニング済みのスループット](set-throughput.md) (予約済みスループットとも呼ばれます) により、あらゆる規模でハイ パフォーマンスが実現します。 ユーザーが必要なスループットを 1 秒あたりの[要求ユニット](request-units.md) (RU/s) で指定すると、Azure Cosmos DB では構成されたスループットを提供するために必要なリソースが専用に確保されます。 [データベースまたはコンテナーに対してスループットをプロビジョニングする](set-throughput.md)ことができます。 ワークロードのニーズに基づいて、いつでもスループットを増減したり、[自動スケーリング](provision-throughput-autoscale.md)を使用したりできます (ただし、SLA を保証するため、データベースまたはコンテナーでは最低限のスループットを確保する必要があります)。 特定の 1 時間での最大のプロビジョニング スループットに対し、時間単位で請求されます。

   > [!NOTE]
   > プロビジョニング済みスループット モデルでは、コンテナーまたはデータベース専用にリソースが割り当てられるので、ワークロードを何も実行しない場合でも、プロビジョニングしたスループットの料金が発生します。

  - **サーバーレス**:[サーバーレス](serverless.md) モードでは、Azure Cosmos アカウントでリソースを作成するときに、スループットをプロビジョニングする必要はありません。 請求期間が終了すると、データベース操作で使用した要求ユニットの量に対して課金されます。

- **ストレージ**: 対象となる 1 時間にデータおよびインデックスで使用したストレージの合計量 (GB) に対して固定料金が請求されます。 ストレージは使用量に基づいて課金されるため、前もってストレージを予約する必要はありません。 使用したストレージに対してのみ課金されます。

Azure Cosmos DB の価格モデルは、すべての API について同じです。 詳細については、[Azure Cosmos DB の価格ページ](https://azure.microsoft.com/pricing/details/cosmos-db/)、「[Azure Cosmos DB の課金内容の理解](understand-your-bill.md)」、および [Azure Cosmos DB の価格モデルがお客様にとっていかにコスト効率がよいか](total-cost-ownership.md)に関するページを参照してください。

Azure Cosmos DB アカウントを米国の非政府リージョンにデプロイした場合、プロビジョニング済みスループット モードでは、データベースとコンテナー ベースのスループットの両方に対して最低料金が発生します。 サーバーレス モードには、最低料金はありません。 価格は、お客様が使用しているリージョンによって異なります。最新の価格情報については、「[Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)」ページを参照してください。

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB を無料で試す

Azure Cosmos DB では、開発者向けに無料のオプションが多数提供されています。 設定できるオプションは次のとおりです。

* **Azure Cosmos DB の Free レベル**: Azure Cosmos DB の Free レベルを使用すると、アプリケーションの利用開始、開発、およびテストを簡単に行えるようになります。また、小規模な実稼働ワークロードを無料で実行することもできます。 アカウント上で Free レベルを有効にすると、そのアカウントでの最初の 400 RU/秒と 5 GB のストレージが無料で利用できるようになります (アカウントの有効期間中)。 Azure サブスクリプションごとに所有できる Free レベル アカウントは 1 つまでです。また、アカウントの作成時に選択する必要があります。 開始するには、[Free レベルで有効化されている Azure portal で新しいアカウントを作成する](create-cosmosdb-resources-portal.md)か、[ARM テンプレート](./manage-with-templates.md#free-tier)を使用します。

* **Azure 無料アカウント**:Azure の [Free レベル](https://azure.microsoft.com/free/)では、最初の 30 日間に使用できる 200 ドルの Azure クレジットと 12 か月の無料サービス (数に制限あり) が提供されます。 詳細については、[Azure 無料アカウント](../cost-management-billing/manage/avoid-charges-free-account.md)に関するページをご覧ください。 Azure Cosmos DB は Azure 無料アカウントの一部です。 具体的には、Azure Cosmos DB の場合、この無料アカウントでは、25 GB のストレージと 400 RU/s のプロビジョニング済みスループットが 1 年間提供されます。

* **Azure Cosmos DB を無料で試す**:Azure Cosmos DB では、Azure Cosmos DB を無料で試すアカウントを使用することで、時間制限をのあるエクスペリエンスが提供されます。 Azure Cosmos DB アカウントを作成して、データベースとコレクションを作成し、クイック スタートとチュートリアルを使用してサンプル アプリケーションを実行できます。 Azure アカウントをサブスクライブしたり、クレジット カードを使用したりせずに、サンプル アプリケーションを実行できます。 「[Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」では、Azure Cosmos DB が 1 か月間提供され、何回でもアカウントを更新できます。

* **Azure Cosmos DB エミュレーター**:Azure Cosmos DB エミュレーターでは、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 エミュレーターは無料で提供され、クラウド サービスとほとんど同じです。 Azure Cosmos DB エミュレーターを使用すると、ローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 運用環境に移行する前に、ローカル環境でエミュレーターを使用してアプリケーションを開発できます。 エミュレーターでのアプリケーションの機能に満足した後で、クラウドの Azure Cosmos DB アカウントを使用するように切り替えると、コストを大幅に削減できます。 エミュレーターについて詳しくは、[開発とテストへの Azure Cosmos DB の使用](local-emulator.md)に関する記事をご覧ください。

## <a name="pricing-with-reserved-capacity"></a>予約容量での価格

Azure Cosmos DB の[予約容量](cosmos-db-reserved-capacity.md)は、リソースの料金 1 年分または 3 年分を前払いすることで、プロビジョニング済みスループット モードを使用する際の経費を節減できるサービスです。 1 年分または 3 年分を事前にコミットすることでコストを大幅に減らすことができ、通常価格に比べ、20% から 65% の割引になります。 Azure Cosmos DB の予約容量を利用すると、プロビジョニング済みスループット (RU/秒) を 1 年間または 3 年間前払いすることでコストを削減でき、プロビジョニング済みスループットの割り引きが受けられます。 

予約容量では、割引が適用されても、Azure Cosmos DB リソースのランタイム状態は維持されます。 予約容量は、すべての API (MongoDB、Cassandra、SQL、Gremlin、Azure Tables を含む) および世界中のすべてのリージョンで、同じように利用できます。 予約容量について詳しくは、[予約容量での Azure Cosmos DB リソースの前払い](cosmos-db-reserved-capacity.md)に関する記事をご覧ください。また、予約容量は [Azure portal](https://portal.azure.com/) で購入できます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB リソースのコストの最適化について詳しくは、以下の記事をご覧ください。

* [開発とテストのための最適化](optimize-dev-test.md)について確認する
* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
* [スループット コストの最適化](optimize-cost-throughput.md)の詳細について学習します
* [ストレージ コストの最適化](optimize-cost-storage.md)の詳細について学習します
* [読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)の詳細について学習します
* [クエリ コストの最適化](./optimize-cost-reads-writes.md)の詳細について学習します
* [複数リージョンの Cosmos アカウント コストの最適化](optimize-cost-regions.md)について確認する
* [Azure Cosmos DB の予約容量](cosmos-db-reserved-capacity.md)について確認する
* [Azure Cosmos DB Emulator](local-emulator.md) について確認する