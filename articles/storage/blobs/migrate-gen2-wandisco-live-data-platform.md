---
title: Data Lake Storage と WANdisco LiveData Platform for Azure (プレビュー)
description: WANdisco LiveData Platform for Azure を使用して、オンプレミスの Hadoop データを Azure Data Lake Storage Gen2 に移行します。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508931"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>WANdisco LiveData Platform for Azure (プレビュー) を使用して、厳しい移行要件を満たす

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) を使用して、オンプレミスの Hadoop データを Azure Data Lake Storage Gen2 に移行します。 このプラットフォームを使用すると、アプリケーションのダウンタイムが不要になり、データ損失の可能性が排除され、オンプレミスで操作が継続中であってもデータ整合性が確保されます。  

> [!NOTE]
> WANdisco LiveData Platform for Azure はパブリック プレビュー段階です。 リージョン別の提供状況については 「[サポートされているリージョン](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)」を参照してください。

このプラットフォームは、次の 2 つのサービスで構成されています。アクティブに使用されるデータをオンプレミス環境から Azure ストレージに移行するための [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) と、変更されたすべてのデータまたは取り込みデータが一貫してレプリケートされるようにする [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) です。 

> [!div class="mx-imgBorder"]
> ![LiveData Platform の概要図](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

両方のサービスは、Azure portal と Azure CLI を使用して管理でき、どちらも他のすべての Azure サービスと同じ従量課金制の課金モデルに従います。 LiveData Platform for Azure の使用実績は、同じ毎月の Azure の請求書に記載され、使用を追跡して監視するための一貫した便利な方法を提供します。

[静的な情報を Azure Data Box にコピーする](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)か、[DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) などの Hadoop ツールを使用することによって "_オフライン_" でデータを移行する場合とは異なり、WANdisco LiveData for Azure を使用すると、"_オンライン_" 移行中にビジネス システムの完全な操作を維持することができます。 データを Azure に移行している間も、ビッグ データ環境の稼働を維持します。

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure の主な特徴

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) では、ワイドエリア ネットワーク対応の独自のコンセンサス エンジンを使用して、データの整合性を確保し、大規模なデータのレプリケーションを実行しますが、その間、アプリケーションでレプリケーション中のデータを引き続き変更できます。 <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

プラットフォームの主な特徴は次のとおりです。

- **データの一貫性**:環境間で大規模なデータ ボリュームを移行し、データが絶え間なく変化する中でも、ストレージ システムのスループット移行全体にわたってデータの整合性を維持するという課題を解決します。 WANdisco のワイドエリア ネットワーク対応の独自のコンセンサス エンジンを Azure で直接使用して、データの整合性を確保し、データ変更全体にわたって整合性を保証したデータ移行を実現します。

- **操作を維持する**:アプリケーションでは移行中にデータの作成、変更、読み取り、および削除を続行できるため、Azure にビッグ データを移行するためだけに業務を中断したり、停止期間を導入したりする必要はありません。 アプリケーション、分析インフラストラクチャ、取り込みジョブ、その他の処理の操作を継続します。

- **結果を検証する**:Azure に移行した後にデータが有効に利用できるかどうかをエンドツーエンドで検証するには、それらに対して運用アプリケーションのワークロードを実行する必要があります。 データの相違のリスクを生じることなく、これを実現するのは、LiveData Service のみです。その方法として、移行のソースまたはターゲットで変更が発生したかどうかにかかわらずデータの整合性を維持します。 プロセスとシステムにリスクや変更を加えることなく、アプリケーションの動作をテストおよび検証します。

- **複雑さを軽減する**:自動化によってデータを移行することで、データをコピーするためにスケジュールされたジョブを作成して管理する必要がなくなります。 コントロール プレーンとして Azure との緊密な統合を使用して、移行の進行状況 (選択的なデータ レプリケーション、Hive メタデータ、データのセキュリティと機密性など) を管理および監視します。

- **効率化**: 高いスループットとパフォーマンスを維持し、ビッグ データ ボリュームに合わせて簡単にスケーリングします。 帯域幅の使用を制御することで、他のシステム操作に影響を与えることなく、移行の目標を達成することができます。

## <a name="migrate-big-data-faster-without-risk"></a>リスクなしでビッグ データをより迅速に移行する

WANdisco LiveData Platform for Azure の最初のサービスは、[LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) です。これは、アクティブに使用されているデータをオンプレミスの環境から Azure ストレージに移行するためのソリューションです。 LiveData Migrator for Azure は Azure portal または Azure CLI から完全にプロビジョニングおよび管理され、データの移行をすぐに開始するために、構成の変更、アプリケーションの変更、およびサービスの再起動なしでオンプレミスの Hadoop クラスターと一緒に動作します。

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure のアーキテクチャ](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

ビッグ データの移行は複雑で困難な場合があります。 オフライン データ コピー テクノロジでは、業務を中断することなくペタバイト規模の情報を移動することはできませんでした。 [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) は、単純なデプロイを提供し、アプリケーションで移行中のデータの読み取り、書き込み、変更が行われている間にデータの移行とレプリケーションを継続的に行う LiveData Service を確立できます。

移行の実行は、これらの 3 つの手順のように簡単です。

1. Azure portal からオンプレミスの Hadoop クラスターに LiveData Migrator インスタンスをプロビジョニングします。 クラスターの変更やダウンタイムは必要なく、アプリケーションは動作を継続することができます。

   > [!div class="mx-imgBorder"]
   >![LiveData Migrator インスタンスを作成する](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. ターゲットの Azure Data Lake Storage Gen2 対応のストレージ アカウントを定義します。

   > [!div class="mx-imgBorder"]
   >![LiveData Migrator ターゲットを作成する](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 移行するデータの場所 (例: `/user/hive/warehouse`) を定義し、移行を開始します。

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator の移行を作成する](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Azure CLI や Azure portal などの標準の Azure ツールを使用して移行の進行状況を監視し、オンプレミス環境全体を継続して使用します。 開始する前に、これらの[前提条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)を確認してください。

## <a name="replicate-data-under-active-change"></a>アクティブな変更中のデータをレプリケートする

オンプレミスのデータ レイクから Azure への大規模な移行には、アプリケーションのテストと検証が必要です。 簡単には調整できない真実の複数のソースを作成するデータ変更を引き起こすリスクなしにこれを実現できることは、Azure への移行に関するリスクを排除し、そのコストを最小限に抑えるために不可欠です。 [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) は、WANdisco の調整エンジン テクノロジを使用してこれらの懸念を克服します。

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure のアーキテクチャ](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

最初の移行の後、LiveData Plane for Azure を使用してオンプレミスの Hadoop クラスターと Azure ストレージの間でデータの整合性を維持してください。

1. Azure portal から開始して、オンプレミスと Azure で LiveData Plane for Azure をプロビジョニングします。 アプリケーションの変更は必要ありません。

2. 整合性を維持するデータの場所を対象とするレプリケーション ルールを構成します (例: `/user/contoso/sales/region/WA`)。

3. 必要に応じて、Hadoop と互換性のあるファイル システムとしていずれかの場所のデータにアクセスして変更するアプリケーションを実行します。

LiveData Plane for Azure によって、クラスターの操作やアプリケーションのパフォーマンスに大きなオーバーヘッドを課すことなく、データの整合性が維持されます。 すべての変更が一貫してレプリケートされる間に、データの変更または取り込みを行います。

## <a name="next-steps"></a>次のステップ

- [LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) は、他の Azure リソースと同じように使用され、現在プレビューで利用できます。 

- [前提条件](https://docs.wandisco.com/live-data-platform/docs/prereq/)を理解して、移行を計画し、LiveData Migrator for Azure を使用して大規模な移行を迅速に完了させます。

- [HDFS Sandbox](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/) を使用して、オンプレミスの Hadoop クラスターを用意することなく、LiveData Migrator を試してみます。

## <a name="see-also"></a>関連項目

- [Azure Marketplace の LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Marketplace の LiveData Plane for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator for Azure のプランと価格](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData Plane for Azure のプランと価格](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData Platform for Azure についてよく寄せられる質問](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure に関する既知の問題](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)