---
title: エラスティック プールを使用して複数のデータベースを管理する
description: エラスティック プールを使用して、Azure SQL Database 内の複数 (数百や数千) のデータベースを管理およびスケーリングします。 一定の価格で必要な場所にリソースを配布できます。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653637"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Azure SQL Database におけるエラスティック プールを利用した複数のデータベースの管理およびスケーリング
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database のエラスティック プールは、予期できない多様な使用ニーズを持つ複数のデータベースを管理しスケーリングするための、シンプルでコスト効率の高いソリューションです。 エラスティック プール内のデータベースは、単一のサーバー上にあり、設定された数のリソースを設定価格で共有します。 Azure SQL Database のエラスティック プールを使用すると、SaaS 開発者は、各データベースのパフォーマンスに弾力性を提供しながら、データベース グループの価格に対するパフォーマンスを所定の予算内で最適化できます。

## <a name="what-are-sql-elastic-pools"></a>SQL エラスティック プールとは

SaaS 開発者は、複数のデータベースで構成される大規模なデータ層の上にアプリケーションを構築します。 アプリケーションの一般的なパターンでは、顧客ごとに Single Database をプロビジョニングします。 しかし、各顧客の使用パターンは変動して予測不可能な場合が多く、各データベース ユーザーのそれぞれについてリソース要件を予測することも困難です。 これまでは、2 つのオプションがありました。

- ピーク使用量および超過支払いに基づくリソースの過剰プロビジョニング
- ピーク時のパフォーマンスと顧客満足を犠牲にした、コスト削減のための過小プロビジョニング

エラスティック プールは、データベースが必要なパフォーマンス リソースを必要なときに確保できるようにすることで、この問題を解決します。 また、予測可能な予算の範囲内でシンプルなリソース割り当てメカニズムが提供されます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)」を参照してください。
>
> [!IMPORTANT]
> エラスティック プールでは、データベース単位の請求はありません。 課金は、使用量やプールがアクティブであったのが 1 時間に満たないということに関係なく、プールが存在していた時間の最上位の eDTU または仮想コアを使用して 1 時間単位で行われます。

エラスティック プールを使用することで、開発者は、複数のデータベースで共有されるプールのリソースを購入でき、個々のデータベースの使用期間が予測しづらい場合にも対応できます。 [DTU ベースの購入モデル](service-tiers-dtu.md)または [仮想コアベースの購入モデル](service-tiers-vcore.md)のいずれかに基づいて、プールのリソースを構成できます。 プールのリソース要件は、そのデータベースの使用量の合計によって決まります。 プールで使用可能なリソースの量は、開発者の予算に応じて決められます。 開発者は、単純にプールにデータベースを追加し、データベースに対する最小および最大のリソース (選択したリソース モデルに応じて、最小および最大の DTU、または最小および最大の仮想コアのいずれか) を任意で設定してから、予算に基づいてプールのリソースを設定します。 開発者はプールを使用することで、リーン スタートアップの段階から成熟企業の段階に至るまで、サービスをシームレスに拡大し続けることができます。

プール内で、個々のデータベースには、設定されたパラメーターの範囲内で自動的にスケーリングを行う柔軟性が与えられます。 負荷が大きい場合、データベースはリソースの使用量を増やして需要に対応します。 負荷が小さい場合、データベースはリソースの使用量を減らし、負荷がない場合は、リソースを使用しません。 Single Database ではなく、プール全体に対してリソースをプロビジョニングすることで、管理タスクの簡略化を実現します。 さらに、プールにかかる予算を予測することができます。 最小限のダウンタイムで、既存のプールにさらにリソースを追加することができます。 同様に、余分なリソースが不要になった場合は、いつでも既存のプールから削除できます。 プールのデータベースを追加または削除することができます。 データベースのリソース使用率が低いと予測できる場合は、プールから削除します。

> [!NOTE]
> エラスティック プールの内外にデータベースを移動する際、操作の最後でデータベース接続が削除されるときに少しの時間 (数秒) がかかるのを除いて、ダウンタイムは発生しません。

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL Database エラスティック プールを検討すべきとき

プールは、多数のデータベースが特定の使用パターンで使用されている場合に適しています。 あるデータベースは、使用が急増することはあまりなく、平均使用量が低いパターンの特徴を持っています。 逆に、中程度から高い使用率が持続する複数のデータベースは、同じエラスティック プールに配置しないでください。

プールに追加できるデータベースが多ければ多いほど、節約量も多くなります。 アプリケーションの使用パターンにもよりますが、S3 データベースが 2 つあれば節約を実現できます。

以下のセクションでは、特定のデータベースのコレクションをプールに追加した場合にメリットがあるかどうかを評価する方法を確認できます。 この例では Standard プールを使用していますが、同じ原則は Basic プールと Premium プールにも当てはまります。

### <a name="assessing-database-utilization-patterns"></a>データベースの使用パターンの評価

次の図では、アイドル時間は長いが、定期的にアクティビティが急増するデータベースを示しています。 このような使用パターンは、プールに適しています。

   ![a single database suitable for a pool](./media/elastic-pool-overview/one-database.png)

このグラフは、12:00 から 1:00 までの 1 時間の DTU 使用率を示しています。ここでは、各データ ポイントの細分性は 1 分間です。 12:10 の時点で、DB1 のピークは 90 DTU にも上りますが、全体的な平均使用量は 5 DTU 未満です。 Single Database でこのワークロードを実行するためには S3 コンピューティング サイズが必要ですが、このパフォーマンスを確保すると、アクティビティの少ない時間帯にほとんどのリソースが使用されなくなります。

プールを利用すれば、これらの未使用の DTU を複数のデータベースで共有できるので、必要な DTU と全体的なコストを削減できます。

DB1 と類似する使用パターンを持つデータベースが他にもあると仮定し、前の例に追加しています。 次に示す 2 つの図では、DTU ベースの購入モデルを使用して、時間が経過しても使用量のパターンが重ならないことを示すために、4 個のデータベースと 20 個のデータベースの使用量が同じグラフ上に重ねられています。

   ![four databases with a utilization pattern suitable for a pool](./media/elastic-pool-overview/four-databases.png)

   ![twenty databases with a utilization pattern suitable for a pool](./media/elastic-pool-overview/twenty-databases.png)

全 20 のデータベースの DTU 使用量合計は、前の図では黒線で示されています。 これは、DTU の合計使用量が 100 DTU を超えることはなく、20 のデータベースでこの期間にわたって 100 eDTU を共有することが可能であることを示しています。 これは、Single Database のそれぞれのコンピューティング サイズが S3 であるときと比較した場合、DTU を 20 倍削減し価格を 13 倍下げます。

次の理由からこの例は理想的です。

- 各データベースのピーク時の使用量と、平均使用量に大きな差があります。
- 各データベースのピーク使用時間が異なります。
- eDTU は、多くのデータベース間で共有されます。

DTU 購入モデルでは、プールの価格は、プール eDTU の機能を表します。 プールの eDTU 単価は単一データベースの DTU 単価の 1.5 倍ですが、**プール eDTU は多数のデータベースで共有できるため、合計 eDTU は少なくて済みます**。 これらの価格と eDTU 共有の特徴が、プールで節約を可能にするベースとなります。

仮想コアの購入モデルでは、エラスティック プールの仮想コアの単価は、単一データベースの仮想コアの単価と同じです。

## <a name="how-do-i-choose-the-correct-pool-size"></a>正しいプール サイズを選択するにはどうすればよいですか?

プールに最適なサイズは、プール内のすべてのデータベースに必要な合計リソースに左右されます。 これには、次の値を特定する必要があります。

- プール内のすべてのデータベースで使用される最大コンピューティング リソース数。  コンピューティング リソースは、購入モデルの選択に応じて、eDTU または仮想コアのいずれかによってインデックスが付けられます。
- プール内のすべてのデータベースに使用される記憶域の最大バイト数。

各購入モデルのサービス レベルとリソース制限については、[DTU ベースの購入モデル](service-tiers-dtu.md)または[仮想コアベースの購入モデル](service-tiers-vcore.md)に関するページを参照してください。

プールのコスト効果が単一データベースよりも高いかどうかを確認するには、次の手順が役立ちます。

1. プールに必要な eDTU または vCore は、次のように見積もります。
   - DTU ベースの購入モデルの場合:
     - MAX(<*DB の合計数* &times; *DB あたりの平均 DTU 使用率*>, <*同時にピークとなる DB の数* &times; *DB あたりのピーク DTU 使用率*>)
   - 仮想コアベースの購入モデルの場合:
     - MAX(<*DB の合計数* &times; *DB あたりの平均 vCore 使用率*>, <*同時にピークとなる DB の数* &times; *DB あたりのピーク vCore 使用率*>)
2. プール内のすべてのデータベースに必要なデータ サイズを追加して、プールに必要な合計記憶域スペースを見積もります。 DTU 購入モデルの場合は、次に、このストレージの容量を提供する eDTU プール サイズを決定します。
3. DTU ベースの購入モデルの場合、手順 1 と手順 2 の eDTU の見積もりのうち、大きい方を使用します。 仮想コアベースの購入モデルの場合、手順 1 の vCore の見積もりを使用します。
4. 「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」ページを参照し、手順 3 の見積もりを超える最小のプール サイズを探します。
5. 手順 4 のプールの価格と、単一データベースの適切なコンピューティング サイズを使用した場合の価格を比較します。

> [!IMPORTANT]
> プール内のデータベースの数がサポートされている最大数に近づく場合は、[高密度エラスティック プールでのリソース管理](elastic-pool-resource-management.md)を検討してください。

## <a name="using-other-sql-database-features-with-elastic-pools"></a>エラスティック プールでのその他の SQL Database 機能の使用

### <a name="elastic-jobs-and-elastic-pools"></a>エラスティック ジョブとエラスティック プール

プールでは、 **[エラスティック ジョブ](elastic-jobs-overview.md)** でスクリプトを実行することで、管理タスクが簡素化されます。 エラスティック ジョブを使用すると、大量のデータベースに関連する面倒な作業のほとんどが不要になります。

複数のデータベースを操作するための他のデータベース ツールの詳細については、「[Azure SQL Database によるスケール アウト](elastic-scale-introduction.md)」を参照してください。

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースのためのビジネス継続性オプション

プールされたデータベースは、一般的に、単一データベースで使用できるのと同じ[ビジネス継続性機能](business-continuity-high-availability-disaster-recover-hadr-overview.md)をサポートしています。

- **ポイントインタイム リストア**

  ポイントインタイム リストアは、自動データベース バックアップを使用して、プール内のデータベースを特定の時点に復元します。 「 [ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)

- **geo リストア**

  geo リストアは、データベースがホストされているリージョンでのインシデントのためにデータベースが利用できない場合にも既定の復旧オプションを提供します。 「 [Azure SQL Database を復元する、またはセカンダリにフェールオーバーする](disaster-recovery-guidance.md)

- **アクティブ geo レプリケーション**

  geo リストアが提供できるものよりアグレッシブな復旧要件があるアプリケーションの場合は、[アクティブ geo レプリケーション](active-geo-replication-overview.md)または[自動フェールオーバー グループ](auto-failover-group-overview.md)を構成します。

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure Portal を使用した新しい SQL Database エラスティック プールの作成

Azure Portal でエラスティック プールを作成できる方法には次の 2 つがあります。

1. [Azure portal](https://portal.azure.com) に移動してエラスティック プールを作成します。 **Azure SQL** を検索して選択します。
2. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[データベース]** タイルで **[詳細の表示]** を選択して、さまざまなエラスティック プールに関する追加情報を表示できます。
3. **[データベース]** タイルで、 **[リソースの種類]** ドロップダウンから **[エラスティック プール]** を選択した後、 **[作成]** を選択します。

   ![エラスティック プールの作成](./media/elastic-pool-overview/create-elastic-pool.png)

4. または、既存のサーバーに移動し、 **[+ 新しいプール]** をクリックすることで、サーバー内にエラスティック プールを直接作成できます。

> [!NOTE]
> サーバーに複数のプールを作成することはできますが、同じプールに異なるサーバーからデータベースを追加することはできません。

プールのエラスティックに使用できる機能のほか、各データベースに使用可能な最大リソース量が、プールのサービス レベルによって決定されます。 詳細については、[DTU モデル](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)でのエラスティック プールのリソース上限をご覧ください。 エラスティック プールに対する仮想コア ベースのリソース制限については、[エラスティック プールでの仮想コアベースのリソース制限](resource-limits-vcore-elastic-pools.md)に関するページをご覧ください。

プールのリソースと価格を構成するには、 **[プールの構成]** をクリックします。 そのため、サービス レベルを選択し、プールにデータベースを追加して、プールとそのデータベースに対するリソース上限を構成します。

プールの構成を完了したら、[適用] をクリックしてプールに名前を付け、[OK] をクリックするとプールを作成できます。

## <a name="monitor-an-elastic-pool-and-its-databases"></a>エラスティック プールとプール内のデータベースの監視

Azure Portal では、エラスティック プールとそのプール内のデータベースの使用率を監視できます。 また、エラスティック プールに対して複数の変更を行い、すべての変更を同時に送信できます。 このような変更としては、データベースの追加または削除、エラスティック プールの設定の変更、データベース設定の変更などがあります。

組み込みの[パフォーマンス監視](./performance-guidance.md)および[アラート ツール](./alerts-insights-configure-portal.md)と、パフォーマンス評価とを組み合わせて使用できます。  さらに、SQL Database では、監視を容易にするために[メトリックとリソース ログを出力する](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal)ことができます。

## <a name="customer-case-studies"></a>お客様導入事例

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart は、Azure SQL Database とエラスティック プールを使用して、新しい Azure SQL Database を 1 か月あたり 1,000 台追加し、自社のビジネス サービスを急速に拡大しました。

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco は、Azure SQL Database とエラスティック プールを使用して、クラウド上の数千のテナント向けに迅速にサービスをプロビジョニングしてスケーリングしています。

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI は、Azure SQL Database とエラスティック プールを使用して、開発サイクルを迅速化し、顧客サービスとパフォーマンスを強化しています。

## <a name="next-steps"></a>次のステップ

- 料金情報については、[エラスティック プールの価格](https://azure.microsoft.com/pricing/details/sql-database/elastic)に関するページをご覧ください。
- エラスティック プールをスケーリングするには、[エラスティック プールのスケーリング](elastic-pool-scale.md)および[エラスティック プールのスケーリングのサンプル コード](scripts/monitor-and-scale-pool-powershell.md)に関するページをご覧ください
- エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](saas-tenancy-app-design-patterns.md)」を参照してください。
- エラスティック プールを使用した SaaS チュートリアルについては、「[Introduction to the Wingtip SaaS application (Wingtip SaaS アプリケーションの概要)](saas-dbpertenant-wingtip-app-overview.md)」を参照してください。
- 多くのデータベースを持つエラスティック プールでのリソース管理については、「[高密度エラスティック プールでのリソース管理](elastic-pool-resource-management.md)」を参照してください。
