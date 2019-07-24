---
title: Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス | Microsoft Docs
description: Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティスについて説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 6f6440e12840538614b4092b173ab25ae37a68a6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110284"
---
# <a name="best-practices-for-costing-and-sizing-workloads-migrated-to-azure"></a>Azure に移行するワークロードの料金計算とサイズ設定のベスト プラクティス

移行の計画と設計の際には、コストに注目することで、より確実に Azure への移行で長期的な成功を収めることができます。 移行プロジェクト時は、すべてのチーム (財務チーム、管理チーム、アプリ チームなど) が関連するコストを理解しておくことが重要です。
- 移行前には、移行の支出と、月単位、四半期単位、年単位の予算目標についてベースラインを見積もっておくことが、成功を収めるためには不可欠です。
- 移行後は、コストを最適化し、継続的にワークロードを監視して、将来の使用パターンを計画する必要があります。 移行されたリソースは、ある種類のワークロードとして開始されても、使用状況、コスト、および変化するビジネス要件に基づいて、時間の経過と共に別の種類に発展することがあります。

この記事では、移行前後の料金計算とサイズ設定のベスト プラクティスについて説明します。  

> [!IMPORTANT]
> この記事で説明するベスト プラクティスと見解は、Azure プラットフォームと、記事の執筆時点で利用できるサービスの機能に基づいています。 特徴や機能は時間の経過と共に変化します。 お客様のデプロイにすべての推奨事項が当てはまるとは限らないため、お客様にとって適切なものを選択してください。


## <a name="before-migration"></a>移行前 

ワークロードをクラウドに移動する前に、それらを Azure で実行する毎月のコストを見積もります。 クラウドのコストを先を見越して管理すれば、運営費 (OpEx) の予算に準拠する助けになります。 予算が限られている場合は、移行前にこれを考慮に入れます。  コストを削減するため、適切な場合は、ワークロードを Azure のサーバーレス テクノロジに変えることを検討します。

このセクションのベスト プラクティスは、コストの見積り、VM とストレージのサイズの適正化の実行、Azure ハイブリッドのメリットの活用、予約済み VM の使用、複数のサブスクリプションにわたるクラウドの支出の見積もりに役立ちます。



## <a name="best-practice-estimate-monthly-workload-costs"></a>ベスト プラクティス:毎月のワークロード コストを見積る
 
移行されるワークロードの月額料金を予測するために、利用できるツールが多数あります。

- **Azure 料金計算ツール**:VM やストレージなど、見積りをする製品を選択します。 料金計算ツールにコストを入力し、見積りを作成します。

  ![Azure 料金計算ツール](./media/migrate-best-practices-costs/pricing.png) *Azure 料金計算ツール*

- **Azure Migrate**:コストを見積もるには、Azure でワークロードを実行するために必要なすべてのリソースを確認し、それらを計上します。 このデータを取得するには、サーバー、VM、データベース、ストレージを含む資産のインベントリを作成します。 この情報は、Azure Migrate を使用して収集できます。

  - Azure Migrate は、オンプレミス環境で検出と評価を行ってインベントリを提供します。
  - Azure Migrate では、全体像を把握できるように、VM 間の依存関係をマップして表示できます。
  - Azure Migrate の評価には、推定コストが含まれています。
    - コンピューティング コスト:Azure Migrate は、評価の作成時には推奨される Azure VM のサイズを採用し、Billing API を使用して VM の推定月間コストを計算します。 この見積りでは、オペレーティング システム、ソフトウェア アシュアランス、予約インスタンス、VM のアップタイム、場所、および通貨の設定が考慮されます。 評価に含まれるすべての VM にわたるコストが集計され、月間コンピューティング コストの合計が計算されます。
    - ストレージのコスト:Azure Migrate は、評価に含まれるすべての VM のストレージ コストを集計して、月間ストレージ コストの合計を計算します。 特定のマシンの月間ストレージ コストは、そのマシンに接続されているすべてのディスクの月間コストを集計することで計算できます。 

    ![Azure Migrate](./media/migrate-best-practices-costs/assess.png)
    "*Azure Migrate の評価*"

**詳細情報:**
- Azure 料金計算ツールを[使用](https://azure.microsoft.com/pricing/calculator/)します。
- Azure Migrate の[概要を確認](https://docs.microsoft.com/azure/migrate/migrate-overview)します。
- Azure Migrate の評価[についてのページを読みます](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)。
- Database Migration Service (DMS) の[詳細を確認します](https://docs.microsoft.com/azure/dms/dms-overview)。

## <a name="best-practice-right-size-vms"></a>ベスト プラクティス:適切なサイズの VM

ワークロードをサポートする Azure VM をデプロイするときは、さまざまなオプションを選択できます。 各種類の VM には特定の機能があり、さまざまな組み合わせの CPU、メモリ、およびディスクが用意されています。 VM は以下のようにグループ化されます。

**Type** | **詳細** | **用途**
--- | --- | ---
**汎用** | CPU とメモリのバランスがとれています。 | テストと開発、小～中規模のデータベース、低～中程度のトラフィックの Web サーバーに適しています。
**コンピューティング最適化** | メモリに対する CPU の比が大きくなっています。 | トラフィックが中程度の Web サーバー、ネットワーク アプライアンス、バッチ処理、アプリ サーバーに適しています。
**メモリ最適化** | 高いメモリ対 CPU。 | リレーショナル データベース、中から大規模のキャッシュ、インメモリ分析に適しています。
**ストレージの最適化** | 高いディスク スループットと IO。 | ビッグ データ、SQL、および NoSQL のデータベースに適しています。
**GPU の最適化** | 特殊な VM。 1 つまたは複数の GPU。 | 大量のグラフィックスおよびビデオの編集。
**高性能** | 最も高速で最も強力な CPU。 オプションの、高スループットのネットワーク インターフェイス (RDMA) を備えた VM | クリティカルな高性能アプリ。

- これらの VM 間の価格の違いと、長期的な予算への影響を理解しておくことが重要です。
- 種類ごとに、内部に一定数の VM シリーズがあります。
- さらに、あるシリーズ内の VM を選択すると、そのシリーズ内の VM のみをスケールアップまたはスケールダウンできます。 たとえば、DSv2\_2 は DSv2\_4 にスケールアップできますが、Fsv2\_2 などの異なるシリーズには変更できません。

**詳細情報**:
- VM の種類とサイズ設定、およびサイズの種類へのマップに関する[詳細を確認します](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。
- VM のサイズ変更を[計画](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs)します。
- 架空の Contoso 社の評価サンプルを[確認します](https://docs.microsoft.com/azure/migrate/contoso-migration-assessment)。

## <a name="best-practice-select-the-right-storage"></a>ベスト プラクティス:適切なストレージを選択する

オンプレミス ストレージ (SAN または NAS) とそれらをサポートするネットワークのチューニングと維持管理は、コストが高く時間がかかる場合があります。 ファイル (ストレージ) のデータは、一般に、運用と管理の負担を軽減するため、クラウドに移行されます。 Microsoft では、Azure にデータを移動するためのオプションをいくつか提供しており、それらのオプションについて決定する必要があります。 データにとって適切なストレージの種類を選択すれば、組織は毎月数千ドルを節約できます。 いくつかの考慮事項があります。

- あまりアクセスされないデータやビジネス クリティカルでないデータは、最も高価なストレージに配置する必要がありません。
- 逆に、重要なビジネス クリティカル データは、より高いレベルのストレージ オプションに配置する必要があります。
- 移行計画時には、最適なストレージにマップするために、データのインベントリを作成し、重要度で分類します。 予算とコストに加えて、パフォーマンスを考慮に入れます。 コストは、必ずしも主な意思決定要因ではないでしょう。 最小コストのオプションを選択すると、ワークロードがパフォーマンスと可用性のリスクにさらされる可能性があります。 

### <a name="storage-data-types"></a>ストレージのデータの種類

Azure では、ストレージ データのさまざまな種類が提供されています。

| **データの種類** | **詳細** | **使用方法** |
|--- | --- |  --- |
|**BLOB** | テキスト データやバイナリ データなどの大量の非構造化オブジェクトを格納するために最適化されています<br/>HTTP/HTTPS 経由でどこからでもデータにアクセスします。 | ストリーミングやランダム アクセスのシナリオに使用します。 たとえば、イメージやドキュメントをブラウザーに直接提供する、ビデオやオーディオをストリーム配信する、バックアップやディザスター リカバリーのデータを格納するなどです。|
|**ファイル** | マネージド ファイル共有は SMB 3.0 経由でアクセスされます | オンプレミスのファイル共有を移行する場合に、ファイル データへの複数アクセス/接続を提供するために使用します。|
|**ディスク** | ページ BLOB に基づきます。<br/><br/> ディスクの種類 (速度):Standard (HDD または SSD) または Premium (SSD)。<br/><br/>ディスクの管理:アンマネージド (ユーザーがディスクの設定とストレージを管理する) またはマネージド (ユーザーがディスクの種類を選択し、Azure 側でディスクを管理する)。 | VM には Premium ディスクを使用します。 簡単な管理とスケーリングのためにはマネージド ディスクを使用します。|
|**キュー** | 認証された呼び出し (HTTP または HTTPS) を介してアクセスされる大量のメッセージを格納および取得します | 非同期メッセージ キューを使用してアプリのコンポーネントを接続します。|
|**テーブル** | テーブルを格納します。 | 現在では Azure Cosmos DB Table API の一部です。|



### <a name="access-tiers"></a>アクセス層
Azure のストレージには、ブロック BLOB データにアクセスするためのさまざまなオプションが用意されています。 レベルに合った適切なアクセス層を選択することで、最も費用対効果の高い方法でブロック BLOB データが格納されるようにする助けとなります。

**Type** | **詳細** | **使用方法**
--- | --- | ---
**ホット** | ストレージ コストはクールより高いです。 アクセス料金はクールよりも低いです。<br/><br/>これが既定のレベルです。 | 頻繁にアクセスされる、アクティブに使用中のデータに使用します。
**クール** | ストレージ コストはホットより低いです。 アクセス料金はホットよりも高いです。<br/><br/> 少なくとも 30 日間格納します。 | 短期に格納し、データは使用できますが、アクセス頻度は低いものです。
**アーカイブ** | 個々 のブロック BLOB に使用されます。<br/><br/> 最も費用対効果の高いストレージ オプション。 データ アクセスは、ホットとコールドよりも高価です。 | 取得の待ち時間としてのサーバー時間を許容でき、少なくとも 180 日間同じレベルに留まるデータに使用します。 

### <a name="storage-account-types"></a>ストレージ アカウントの種類

Azure では、さまざまな種類のストレージ アカウントとパフォーマンス レベルが提供されています。

**アカウントの種類** | **詳細** | **使用方法**
--- | --- | ---
**General Purpose v2 Standard** | BLOB (ブロック、ページ、追加)、ファイル、ディスク、キュー、およびテーブルをサポートしています。<br/><br/> ホット、クール、およびアーカイブのアクセス層をサポートしています。 ZRS がサポートされています。 | ほとんどのシナリオの、ほとんどの種類のデータに使用します。 Standard ストレージ アカウントは、HDD または SSD ベースとして指定できます。
**General Purpose v2 Premium** | BLOB ストレージ データ (ページ BLOB) をサポートしています。 ホット、クール、およびアーカイブのアクセス層をサポートしています。 ZRS がサポートされています。<br/><br/> SSD に格納されます。 | すべての VM のために使用することをお勧めします。
**General Purpose v1** | アクセスの階層化はサポートされません。 ZRS をサポートしていません | アプリに Azure クラシック デプロイ モデルが必要な場合に使用します。
**BLOB** | 非構造化オブジェクトを格納するための特殊なストレージ アカウント。 ブロック BLOB と 追加 BLOB のみを提供します (ファイル、キュー、テーブル、ディスクのストレージ サービスはありません)。 同じ持続性、可用性、スケーラビリティ、および General Purpose v2 と同じパフォーマンスを提供します。 | これらのアカウントにページ BLOB を格納することはできないため、VHD ファイルは格納できません。 アクセス層はホットまたはクールに設定できます。

### <a name="storage-redundancy-options"></a>ストレージ冗長オプション

ストレージ アカウントでは、回復力と高可用性のためにさまざまな種類の冗長性を使用できます。

**Type** | **詳細** | **使用方法**
--- | --- | ---
**ローカル冗長ストレージ (LRS)** | 1 つのストレージ単位内で、別個の障害ドメインと更新ドメインにレプリケートすることで、ローカルの障害から保護します。 1 つのデータ センター内に複数のデータコピーを保管します。 年間 99.999999999% 以上 (9 が 11 個) のオブジェクトの持続性を提供します。 | アプリが格納するデータは簡単に再構築できるものかどうかを考慮に入れます。
**ゾーン冗長ストレージ (ZRS)** | 1 つのリージョン内の 3 つのストレージ クラスター間でレプリケートすることで、データ センター停止の保護に冗長性を持たせます。 各ストレージ クラスターは、物理的に分離されており、独自の可用性ゾーン内に置かれています。 複数のデータセンターまたはリージョンにわたってデータのコピーを複数保持することで、年間 99.9999999999 % (9 が 12 個) 以上のオブジェクトの持続性を提供します。 | 一貫性、持続性、および高可用性が必要かどうかを考慮します。 複数のゾーンが永続的に影響を受けるリージョンの災害からは保護されないことがあります。
**geo 冗長ストレージ (GRS)** | プライマリから数百マイル離れたセカンダリ リージョンにデータをレプリケートすることで、リージョン全体の障害から保護します。 年間 99.99999999999999 % 以上 (9 が 16 個) のオブジェクトの持続性を提供します。 | Microsoft がセカンダリ リージョンへのフェールオーバーを開始しない限り、レプリカは使用できません。 フェールオーバーが発生した場合、読み取りと書き込みのアクセスは可能です。
**読み取りアクセス geo 冗長ストレージ (RA-GRS)** | GRS に似ています。 年間 99.99999999999999 % 以上 (9 が 16 個) のオブジェクトの持続性を提供します | GRS で使用される 2 番目のリージョンからの読み取りアクセスを許可することで、99.99% の読み取り可用性を提供します。

**詳細情報**:
- Azure Storage の料金を[確認します](https://azure.microsoft.com/pricing/details/storage/)。
- 大量のデータの Azure BLOB とファイルへの移行に関して、Azure Import/Export [の詳細を確認します](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)。 
- ストレージ データの種類の、BLOB、ファイル、およびディスクを[比較します](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
- アクセス レベルの[詳細を確認します](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
- さまざまな種類のストレージ アカウントを[確認します](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
- [ストレージの冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)、[LRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)、[ZRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)、[GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)、および[読み取りアクセス GRS](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#read-access-geo-redundant-storage) について学習します。
- Azure Files の[詳細を確認します](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。



## <a name="best-practice-leverage-azure-hybrid-benefits"></a>ベスト プラクティス:Azure ハイブリッド特典を活用する

Windows Server や SQL Server などのシステムへのソフトウェア投資により、Microsoft は、クラウドで顧客価値を提供する特別な立場にあり、他のクラウド プロバイダーが提供できるとは限らない大幅な割引を実現しています。 

Microsoft の統合されたオンプレミス/Azure 製品ポートフォリオによって、競争上とコスト上の長所が生み出されています。 オペレーティング システム、またはソフトウェア アシュアランス (SA) を通したその他のソフトウェア ライセンスを現在所有している場合は、Azure ハイブリッド特典によって、クラウドへの移行時にそれらのライセンスをクラウドに移動できます。

**詳細情報**:

- ハイブリッド特典の節約額計算ツールを[見てみます](https://azure.microsoft.com/pricing/hybrid-benefit/)。
- Windows Server 向けのハイブリッド特典の[詳細を確認します](https://azure.microsoft.com/pricing/hybrid-benefit/)。
- SQL Server Azure VM の料金ガイダンスを[確認します](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。


## <a name="best-practice-use-reserved-vm-instances"></a>ベスト プラクティス:予約 VM インスタンスを使用する

ほとんどのクラウド プラットフォームは、従量課金制で提供されています。 動的なワークロードがどのようになるか分かっているとは限らないため、このモデルには不利な点があります。 ワークロードの明確な意図を指定すれば、インフラストラクチャの計画に貢献することになります。

Azure 予約 VM インスタンスを使用して、1 年または 3 年の期間の VM インスタンスに対して前払いします。 

- 前払いにより、使用するリソースに関して割引が提供されます。
- VM、SQL データベースのコンピューティング、Azure Cosmos DB、またはその他のリソース コストを、従量課金制の料金の最大 72% まで大幅に削減できます。 
- 予約は課金割引を提供するもので、リソースの実行時の状態には影響しません。
- 予約インスタンスはキャンセルできます。

![予約インスタンス](./media/migrate-best-practices-costs/reserve.png)
*Azure 予約 VM*

**詳細情報**:
- Azure の予約の[詳細を確認します](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)。
- 予約インスタンスの FAQ を[読みます](https://azure.microsoft.com/pricing/reserved-vm-instances/#faq)。
- SQL Server Azure VM の[料金ガイダンスを表示します](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。


## <a name="best-practice-aggregate-cloud-spend-across-subscriptions"></a>ベスト プラクティス:サブスクリプション間のクラウド支出を集約する

最終的に複数の Azure サブスクリプションを利用することになるのは避けられません。 たとえば、開発と運用の境界を分けるために追加のサブスクリプションが必要な場合があります。また、クライアントごとに別個のサブスクリプションを必要とするプラットフォームがある場合もあります。 すべてのサブスクリプションにわたってデータのレポート作成を 1 つのプラットフォームに集約する機能があるのは、有用な特徴です。

これを行うために、Azure Cost Management API を使用できます。 その後、Azure SQL などの 1 つのソースにデータを集約したら、Power BI などのツールを使用して、集約されたデータを明示できます。 集約されたサブスクリプション レポートや、きめ細かなレポートを作成できます。 たとえば、コスト管理に関する事前対応のための分析情報が必要なユーザーの場合、部門やリソース グループなどに基づいて、コストに関する特定のビューを作成できます。彼らに Azure の課金データへのフル アクセスを提供する必要はありません。

**詳細情報**:

- Azure Consumption API の[概要を確認します](https://docs.microsoft.com/azure/billing/billing-consumption-api-overview)。
- Power BI Desktop で Azure Consumption Insights に接続すること[について学びます](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights)。
- ロール ベースのアクセス制御 (RBAC) を使用して、Azure の課金情報へのアクセスを管理する[方法について学びます](https://docs.microsoft.com/azure/billing/billing-manage-access)。
 
## <a name="after-migration"></a>移行後

ワークロードの移行を成功させた後、数週間にわたって消費データを収集すれば、リソースのコストについて明確な認識が得られます。
- データを分析するときに、Azure のリソース グループとリソースについての予算ベースラインの生成を開始できます。
- その後、クラウドの予算が費やされようとしている場所を理解したら、コストをさらに削減する方法を分析できます。

このセクションのベスト プラクティスには、コストの予算管理と分析での Azure Cost Management の使用、リソースの監視とリソース グループ予算の実施、および監視、ストレージ、VM の最適化が含まれます。

## <a name="best-practice-use-azure-cost-management"></a>ベスト プラクティス:Azure Cost Management を使用する

Microsoft は、以下のように、支出を追跡する助けとなる Azure Cost Management を提供しています。

- Azure の支出の監視と管理を行い、リソースの使用を最適化する助けになります。
- サブスクリプション全体と、そのすべてのリソースが見直されて、推奨事項が示されます。
- 外部ツールやレポート用の財務システムと統合するための完全な API が提供されています。
- リソースの使用状況を追跡し、1 つの統一されたビューでクラウドのコストを管理します。
- 情報に基づいた決定を下しやすいように、運用と財務に関する豊富な分析情報が提供されます。

Cost Management では以下のことが可能です。


- **予算を作成する**:財務上の説明責任のために予算を作成します。
  - 特定の期間 (月単位、四半期単位、年単位) と範囲 (サブスクリプション/リソース グループ) について、使用またはサブスクライブしているサービスを計算に入れることができます。 たとえば、月、四半期、または年の期間の Azure サブスクリプション予算を作成できます。
  - 作成した予算は、コストの分析に表示されます。 現在の支出に対する予算を表示することは、コストや支出を分析するときに必要な最初の手順の 1 つです。
  - 予算がしきい値に達したときに電子メールの通知を送信できます。
  - コスト管理データは、分析のために Azure のストレージにエクスポートできます。

    ![Cost Management の予算](./media/migrate-best-practices-costs/budget.png)
    "*Azure Cost Management の予算*"

- **コスト分析を行う**:コスト分析を取得し、組織のコストを探って分析すれば、コストがどのように生じるかを理解し、支出の傾向を識別する助けになります。
  - EA ユーザーがコスト分析を使用できます。
  - 部門、アカウント、サブスクリプション、リソース グループを含むいくつもの範囲別に、コスト分析データを表示できます。
  - 現在の月の総コストと、毎日の累積コストを表示するコスト分析を取得できます。 

    ![Cost Management での分析](./media/migrate-best-practices-costs/analysis.png)
    "*Azure Cost Management での分析*"
- **推奨事項を取得する**:最適化と効率向上が可能な方法を示す Advisor の推奨事項を取得します。


**詳細情報**:

- Azure Cost Management の[概要を表示します](https://docs.microsoft.com/azure/cost-management/overview)。
- Azure Cost Management を使用してクラウドへの投資を最適化する[方法を学びます](https://docs.microsoft.com/azure/cost-management/cost-mgt-best-practices)。
- Azure Cost Management のレポートを使用する[方法を学びます](https://docs.microsoft.com/azure/cost-management/use-reports)。
- 推奨事項に従ってコストを最適化することに関する[チュートリアルを表示します](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json)。
- Azure Consumption API について[確認します](https://docs.microsoft.com/rest/api/consumption/budgets)。

## <a name="best-practice-monitor-resource-utilization"></a>ベスト プラクティス:リソースの使用率を監視する

Azure では、リソースが消費されたときに使用した分だけ支払いを行います。そうでないときに支払いはしません。 VM の場合、VM が割り当てられているときに課金が発生し、VM の割り当てが解除された後は課金されません。 これを念頭に置いて使用中の VM を監視し、VM のサイズ設定を確認します。

- ベースラインを特定するために、継続的に VM のワークロードを評価します。
- たとえば、月曜日から金曜日の午前 8 時から午後 6 時まではワークロードの用頻度が高く、それ以外の時間帯はほとんど使用されない場合は、ピーク時間帯以外はVM をダウングレードできます。 これは、VM のサイズを変更すること、または仮想マシン スケール セットを使用して VM の上下の自動スケーリングを行うことを意味する場合もあります。
- 一部の企業は、いつ使用可能な必要があり、いつ必要でないかを指定するカレンダーに VM を置くことで、VM を "眠らせ" ます。 
- VM の監視に加えて、利用不足や使いすぎが起きていないか、ExpressRoute や仮想ネットワーク ゲートウェイなどの他のネットワーク リソースを監視する必要があります。
- Azure Cost Management、Azure Monitor、Azure Advisor などのさまざまな Microsoft ツールを使用して VM の使用状況を監視できます。 サードパーティ製のツールも使用できます。  

**詳細情報**:
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) と [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) の概要を表示しますします。
- Advisor のコストに関する推奨事項を[表示します](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations)。
- [推奨事項に従ってコストを最適化](https://docs.microsoft.com/azure/cost-management/tutorial-acm-opt-recommendations?toc=/azure/billing/TOC.json)し、[予期しない料金を防ぐ](https://docs.microsoft.com/azure/billing/billing-getting-started)方法を学びます。
- Azure Resource Optimization (ARO) ツールキット[について学びます](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/)

## <a name="best-practice-implement-resource-group-budgets"></a>ベスト プラクティス:リソース グループの予算を実施する

多くの場合、リソース グループはコストの境界を表すために使用されます。 この使用パターンと共に、Azure チームは、リソース グループとリソースでの予算を作成する機能など、さまざまなレベルでリソースの支出を追跡して分析する、新しい強化された方法を引き続き開発しています。  

- リソース グループの予算は、リソース グループに関連するコストを追跡するのに役立ちます。
- 予算に達したか予算を超えたときにアラートをトリガーし、さまざまなプレイブックを実行できます。 

**詳細情報**:

- Azure Budgets でコストを管理する[方法を学びます](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)。
- [チュートリアルに従って](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?toc=/azure/billing/TOC.json)、Azure の予算を作成して管理します。


## <a name="best-practice-optimize-azure-monitor-retention"></a>ベスト プラクティス:Azure Monitor の保持期間を最適化する

Azure にリソースを移動し、それらの診断ログを有効にすると、大量のログ データが生成されます。 通常、このログ データは、Log Analytics ワークスペースにマップされているストレージ アカウントに送信されます。

- ログ データの保持期間が長いほど、より多くのデータを持つことになります。
- すべてのログ データが一様なわけではなく、一部のリソースは他よりも多くのログ データを生成します。
- 規制とコンプライアンスのため、おそらく一部のリソースのログ データは、他のデータよりも長く保持する必要があります。
- ログのストレージ コストを最適化することと、必要なログ データを保管することのバランスに注意する必要があります。
- 移行の完了直後にログ記録を評価して設定を行うことをお勧めします。そうすることで、重要でないログの保持にコストがかからなくなります。

**詳細情報**:

- 使用量と推定コストの監視[について学びます](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)。
 
## <a name="best-practice-optimize-storage"></a>ベスト プラクティス:ストレージを最適化する

移行の前にベスト プラクティスに従ってストレージを選択した場合は、おそらくいくつかの利点を得ることになります。 ただし十中八九は、まだ最適化できるその他のストレージ コストがあります。 BLOB やファイルは時間の経過と共に古くなります。 データがもはや使用されない可能性もありますが、規制の要件のため、データを一定期間保管する必要がある可能性があります。 そのため、元の移行に使用したパフォーマンスが高いストレージにデータを格納する必要がない場合があります。

古いデータを識別して、より低コストのストレージ領域に移動することが、月単位のストレージ予算とコスト削減に大きな影響を及ぼすことがあります。 Azure では、こうした古いデータを識別して格納する場合に役立つ方法を多数提供しています。

- 汎用 v2 ストレージのアクセス層を活用して、重要度の低いデータをホット アクセス層からクールやアーカイブのアクセス層に移動します。
- カスタマイズしたポリシーに基づいて古いデータを移動しやすくなるように、StorSimple を使用します。 

**詳細情報**:
- アクセス レベルの[詳細を確認します](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
- StorSimple と、[StorSimple の価格](https://azure.microsoft.com/pricing/details/storsimple/)の[概要を確認します](https://docs.microsoft.com/azure/azure-monitor/overview)。

## <a name="best-practice-automate-vm-optimization"></a>ベスト プラクティス:VM の最適化を自動化する

クラウドで VM を実行する場合の最終目標は、VM が使用する CPU、メモリ、およびディスクを最大利用することです。 最適化されていない VM を検出した場合や、VM が使用されていない期間が頻繁にある場合は、それらの VM を、シャット ダウンするか、VM スケール セットを使用してスケールダウンすることが理にかなっています。

VM は、Azure Automation、VM スケール セット、自動シャット ダウン、スクリプト型のソリューション、サード パーティ製のソリューションを使用して最適化できます。 

**詳細情報**:

- 垂直方向の自動スケーリングを使用[する方法を学びます](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision)。
- VM の自動開始を[スケジュールします](https://azure.microsoft.com/updates/azure-devtest-labs-schedule-vm-auto-start/)。
- Azure Automation で業務時間外の VM を開始または停止する[方法を学びます](https://docs.microsoft.com/azure/automation/automation-solution-vm-management)。
- [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) と [Azure Resource Optimization (ARO) ツールキット](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit/)に関する[詳細情報を表示します]。

## <a name="best-practices-use-logic-apps-and-runbooks-with-budgets-api"></a>ベスト プラクティス:Budgets API によって Logic Apps と Runbook を使用する

Azure では、テナント課金情報にアクセスできる REST API が提供されています。

- Budgets API を使用して、外部システムとワークフローを統合できます。これらは、作成したメトリックによって API データからトリガーされます。
- 使用状況やリソースに関するデータを、お使いのデータ分析ツールで取得できます。
- Azure Resource Usage API と Azure Resource RateCard API は、コストを正確に予測して管理するうえで役立ちます。
- これらの API はリソース プロバイダーとして実装されていて、Azure Resource Manager が公開している API に含まれています。
- Budgets API は、Azure Logic Apps や Runbook と統合できます。

**詳細情報**:

- Budgets API の[詳細について学びます](https://docs.microsoft.com/rest/api/consumption/budgets)。
- Billing API を使用して Azure の使用状況の[分析情報を取得します](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)。


## <a name="best-practice-implement-serverless-technologies"></a>ベスト プラクティス:サーバーレス テクノロジを実装する

VM ワークロードは、多くの場合、ダウンタイムを回避するために "現状のまま" で移行されます。 VM は、断続的で実行期間が短いタスクや、その逆に長時間かかるタスクをホストしている場合もよくあります。 たとえば、Windows タスク スケジューラや PowerShell スクリプトでなどの、スケジュールされたタスクを実行する VM です。 これらのタスクが実行中でないときには、それにもかかわらず VM とディスク ストレージのコストが費やされています。

移行後に、これらの種類のタスクを徹底して見直したら、それらを Azure Functions や Azure Batch のジョブなどのサーバーレス テクノロジに移行することを検討してもかまいません。 このソリューションでは、VM の管理と維持がもはや不要になって、さらにコスト削減がもたらされます。 

**詳細情報**:
- Azure Functions [について学びます](https://azure.microsoft.com/services/functions/)
- Azure Batch の[詳細を確認します](https://azure.microsoft.com/en-us/services/batch/)
  
## <a name="next-steps"></a>次の手順 

他のベスト プラクティスを確認してください。

- 移行後のセキュリティと管理の[ベスト プラクティス](migrate-best-practices-security-management.md)。
- 移行後のネットワーキングの[ベスト プラクティス](migrate-best-practices-networking.md)。

