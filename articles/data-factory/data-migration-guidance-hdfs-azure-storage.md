---
title: Azure Data Factory を使用してオンプレミスの Hadoop クラスターから Azure Storage にデータを移行する | Microsoft Docs
description: Azure Data Factory を使用してオンプレミスの Hadoop クラスターから Azure Storage にデータを移行します。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211601"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory を使用してオンプレミスの Hadoop クラスターから Azure Storage にデータを移行する 

Azure Data Factory には、オンプレミスの HDFS から Azure Blob Storage または Azure Data Lake Storage Gen2 に大量のデータを移行できる、パフォーマンスと信頼性が高くコスト効率に優れたメカニズムが用意されています。 基本的に、Azure Data Factory には、オンプレミスの HDFS から Azure にデータを移行する 2 つの方法があります。 ご使用のシナリオに基づいて、それぞれを選択できます。 

- ADF DistCp モード。 ADF では、[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) を使用して Azure BLOB ([ステージング コピー](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)を含む) または Azure Data Lake Store にファイルをそのままコピーできます。この場合、ADF セルフホステッド統合ランタイム (IR) で実行する代わりに、クラスターのパワーを十分に活用できます。 特にクラスターが非常に強力な場合は、コピーのスループットが向上します。 Azure Data Factory の構成に基づいて、コピー アクティビティは、DistCp コマンドを自動的に作成し、Hadoop クラスターに送信し、コピー状態を監視します。 DistCp と統合された ADF を使用することにより、お客様は、既存の強力なクラスターを利用して最適なコピー スループットを実現できるだけでなく、ADF から柔軟なスケジュール設定と統合された監視エクスペリエンスの恩恵を得ることもできます。 既定では、オンプレミスの Hadoop クラスターから Azure にデータを移行する場合、ADF DistCp モードが推奨される方法です。
- ADF ネイティブ IR モード。 シナリオによっては、DistCp がお客様のケースに対して機能しない場合があります (たとえば、VNET 環境では、DistCp ツールは Express Route プライベート ピアリングと Azure Storage VNet エンドポイントをサポートしていません)。 また、クラスターに大量の負荷がかかり、既存の ETL ジョブのパフォーマンスに影響する可能性があるため、データを移行するためのエンジンとして既存の Hadoop クラスターを使用したくない場合があります。 その場合は、ADF ネイティブ機能を使用できます。この機能では、オンプレミスの HDFS から Azure にデータをコピーするエンジンとして ADF 統合ランタイム (IR) を使用できます。

この記事では、データ エンジニアと開発者向けに両方の方法についての次の情報を提供します。
> [!div class="checklist"]
> * パフォーマンス 
> * 回復性のコピー
> * ネットワークのセキュリティ
> * 概要ソリューション アーキテクチャ 
> * 実装のベスト プラクティス  

## <a name="performance"></a>パフォーマンス

ADF DistCp モードでは、スループットは DistCp ツールを個別に使用した場合と同じであり、既存の Hdoop クラスターの容量が活用されます。 DistCp (分散コピー) は、大規模なクラスター間/クラスター内のコピーに使用されるツールです。 MapReduce を使用して、その配布、エラー処理と復旧、およびレポートが有効にされます。 ファイルとディレクトリの一覧がマップ タスクへの入力に展開され、各タスクはソース一覧で指定されたファイルのパーティションをコピーします。 DistCp と統合された ADF を使用することにより、パイプラインを構築して、ネットワーク帯域幅およびストレージの IOPS と帯域幅を十分に活用して、環境のデータ移動のスループットを最大限に高めることができます。  

ADF ネイティブ IR モードでは、さまざまなレベルでの並列処理も可能です。これにより、セルフホステッド IR マシンを手動でスケールアップしたり、セルフホステッド IR の複数のマシンにスケールアウトしたりすることで、ネットワーク帯域幅およびストレージの IOPS と帯域幅を十分に活用して、データ移動のスループットを最大限に高めることができます。

- 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。 セルフホステッド統合ランタイムでは、マシンを手動でスケールアップしたり、複数のマシン ([最大 4 台のノード](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) にスケールアウトしたりすることができます。また、1 回のコピー アクティビティにより、すべてのノードでファイル セットがパーティション分割されます。 
- 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが行われます。 
- ADF 制御フローでは、複数のコピー アクティビティを並列して開始できます。たとえば、[For Each ループ](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)を使用します。 

詳細については、[コピー アクティビティのパフォーマンスに関するガイド](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)を参照してください。

## <a name="resilience"></a>回復力

ADF DistCp モードでは、さまざまな DistCp コマンドライン パラメーター (たとえば、-i はエラーを無視する、-update はソース ファイルと宛先ファイルのサイズが異なる場合にデータを書き込む) を利用して、さまざまなレベルの回復性を実現できます。

ADF ネイティブ IR モードでは、1 回のコピー アクティビティ実行における組み込みの再試行メカニズムがあります。これにより、データ ストアまたは基盤となるネットワークで一定レベルの一時的な障害を処理できます。 オンプレミスの HDFS から BLOB、およびオンプレミスの HDFS から ADLS Gen2 へのバイナリ コピーを実行しているときに、ADF は大部分のチェックポイント処理を自動的に行います。 コピー アクティビティの実行が失敗した場合、またはタイムアウトした場合、後続の再試行 (再試行回数が 1 を超える場合) では、最初から開始するのではなく、最後の障害個所からコピーが再開されます。

## <a name="network-security"></a>ネットワークのセキュリティ 

既定では、ADF は、HTTPS プロトコル経由の暗号化された接続を使用して、オンプレミスの HDFS から Azure Blob Storage または Azure Data Lake Storage Gen2 にデータを転送します。  HTTPS によって転送中のデータが暗号化され、盗聴や中間者攻撃が防止されます。 

また、パブリック インターネット経由でデータを転送しない場合は、Azure ExpressRoute を介してプライベート ピアリング リンク経由でデータを転送することで、より高いセキュリティを実現できます。 これを実現する方法については、以下のソリューション アーキテクチャを参照してください。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

パブリック インターネット経由でデータを移行する:

![solution-architecture-public-network](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- このアーキテクチャでは、データはパブリック インターネット経由で HTTPS を使用して安全に転送されます。 
- ADF DistCp モードは、パブリック ネットワーク環境で使用することをお勧めします。 それにより、既存の強力なクラスターを利用して最適なコピー スループットを実現できるだけでなく、ADF から柔軟なスケジュール設定と統合された監視エクスペリエンスの恩恵を得ることができます。
- Hadoop クラスターに DistCp コマンドを送信し、コピー状態を監視するには、ADF セルフホステッド統合ランタイムを企業ファイアウォール内の Windows コンピューターにインストールする必要があります。 このマシンはデータを移動するエンジンではないため (制御目的のみ)、マシンの容量はデータ移動のスループットに影響を与えません。
- DistCp コマンドの既存のパラメーターがサポートされています。


プライベート リンク経由でデータを移行する: 

![solution-architecture-private-network](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- このアーキテクチャでは、データの移行は Azure ExpressRoute を介してプライベート ピアリング リンク経由で行われるため、データがパブリック インターネット経由で転送されることはありません。
- DistCp ツールでは、Express Route プライベート ピアリングと Azure Storage VNet エンドポイントはサポートされていません。したがって、データを移行するには、統合ランタイムを介して ADF ネイティブ機能を使用することをお勧めします。
- このアーキテクチャを実現するには、Azure 仮想ネットワーク内の Windows VM に ADF セルフホステッド統合ランタイムをインストールする必要があります。 VM を手動でスケールアップするか、複数の VM にスケールアウトすることで、ネットワークとストレージの IOPS/帯域幅を十分に活用できます。
- (ADF セルフホステッド統合ランタイムがインストールされた) 各 Azure VM に対して最初に推奨される構成は、32 vCPU と 128 GB のメモリが搭載された Standard_D32s_v3 です。 データ移行中に VM の CPU とメモリの使用状況を監視しながら、パフォーマンスを向上させるために VM をさらにスケールアップする必要があるか、またはコストを節約するために VM をスケールダウンする必要があるかを確認できます。
- また、1 つのセルフホステッド IR に最大 4 つの VM ノードを関連付けてスケールアウトすることもできます。 セルフホステッド IR に対して実行される 1 回のコピー ジョブで、ファイル セットが自動的にパーティション分割され、すべての VM ノードを利用してファイルが並列してコピーされます。 高可用性を実現するには、データ移行中の単一障害点を回避するために、2 台の VM ノードから始めることをお勧めします。
- 初回のスナップショット移行と差分の移行は、このアーキテクチャを使用して実現できます。


## <a name="implementation-best-practices"></a>実装のベスト プラクティス 

### <a name="authentication-and-credential-management"></a>認証と資格情報の管理 

- HDFS に対して認証を行うには、[Windows (Kerberos) または匿名のいずれか](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)を使用できます。 
- Azure Blob Storage に接続するには、複数の認証の種類がサポートされています。  [Azure リソースにはマネージド ID](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) を使用することを強くお勧めします。Azure AD で自動的に管理されるADF ID を基にして構築されており、リンクされたサービス定義で資格情報を指定せずにパイプラインを構成できます。  また、[サービス プリンシパル](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共有アクセス署名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)、または[ストレージ アカウント キー](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)を使用して Azure Blob Storage に対する認証を行うこともできます。 
- Azure Data Lake Storage Gen2 に接続するには、複数の認証の種類がサポートされています。  [サービス プリンシパル](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)または[ストレージ アカウント キー](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)を使用することもできますが、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) を使用することを強くお勧めします。 
- Azure リソースに対してマネージド ID を使用していない場合は、[Azure Key Vault に資格情報を格納](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)して、ADF のリンクされたサービスを変更せずに、キーを一元的に管理およびローテーションすることを強くお勧めします。  これは、[CI/CD のベスト プラクティス](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)の1 つでもあります。 

### <a name="initial-snapshot-data-migration"></a>初回のスナップショット データ移行 

ADF DistCp モードでは、1 つのコピー アクティビティを作成し、さまざまなパラメーターを指定して DistCp コマンドを送信することで、最初のデータ移行動作を制御できます。 

ADF ネイティブ IR モードでは、特に 10 TB を超えるデータを移行する場合にデータ パーティションが推奨されます。 データをパーティション分割するには、HDFS のフォルダー名を活用します。それにより、各 ADF コピー ジョブで、一度に 1 つのフォルダー パーティションをコピーできます。 複数の ADF コピー ジョブを同時に実行して、スループットを向上させることができます。
ネットワークまたはデータ ストアの一時的な問題によってコピー ジョブが失敗した場合は、失敗したコピー ジョブを再実行して、その特定のパーティションを HDFS から再度読み込むことができます。 他のパーティションを読み込む他のすべてのコピー ジョブには影響しません。

### <a name="delta-data-migration"></a>差分のデータ移行 

ADF DistCp モードでは、DistCp コマンドライン パラメーター "-update、ソース ファイルと宛先ファイルのサイズが異なる場合にデータを書き込む" を利用して、差分データ移行を実現できます。

ADF ネイティブ IR モードでは、HDFS から新規または変更されたファイルを識別する最も効果的な方法は、時間でパーティション分割された名前付け規則を使用することです。HDFS のデータが、ファイル名またはフォルダー名のタイム スライス情報でパーティション分割されている場合 (たとえば、/yyyy/mm/dd/file.csv)、増分的にコピーするファイル/フォルダーをパイプラインで簡単に識別できます。
また、HDFS のデータが時間でパーティション分割されていない場合、ADF は LastModifiedDate によって新しいファイルまたは変更されたファイルを識別できます。 これは、ADF で HDFS からすべてのファイルがスキャンされ、最後に変更されたタイム スタンプが特定の値よりも大きい新しいファイルと更新されたファイルのみがコピーされるというしくみです。 HDFS に多数のファイルがある場合、フィルター条件に一致するファイルの数に関係なく、初回のファイル スキャンに時間がかかる場合があることに注意してください。 この場合は、初回のスナップショットの移行に同じパーティションを使用してデータを最初にパーティション分割することをお勧めします。こうすることで、ファイルのスキャンが並列処理されるようになります。

### <a name="estimating-price"></a>料金の見積り 

HDFS から Azure Blob Storage にデータを移行するために、次のパイプラインが構築されているとします。 

![pricing-pipeline](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

ここでは、次のことを想定しています。 

- 合計データ量は 1 PB
- 2 番目のソリューション アーキテクチャを使用したデータの移行 (ADF ネイティブ IR モード)
- 1 PB は 1000 パーティションに分割され、各コピーで 1 つのパーティションが移行される
- 各コピー アクティビティは、4 台のマシンに関連付けられた 1 つのセルフホステッド IR を使用して構成され、500 MBps のスループットを実現する。
- ForEach の同時実行数は 4 に設定され、合計スループットは 2 GBps
- 合計では、移行が完了するまでに 146 時間かかる。


上記の前提条件に基づく推定料金は次のとおりです。 

![pricing-table](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> これは仮定の料金例です。  実際の料金は、環境の実際のスループットによって変わります。
> (セルフホステッド統合ランタイムがインストールされている) Azure Windows VM の料金は含まれていません。

### <a name="additional-references"></a>その他のリファレンス 
- [HDFS コネクタ](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob Storage コネクタ](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 コネクタ](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [コピー アクティビティのパフォーマンスとチューニングに関するガイド](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [セルフホステッド統合ランタイムの作成と構成](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [セルフホステッド統合ランタイム HA とスケーラビリティ](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [データ移動のセキュリティに関する考慮事項](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault への資格情報の格納](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [時間でパーティション分割されたファイル名に基づいてファイルを増分コピーする](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate に基づいて新しいファイルと変更されたファイルをコピーする](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 料金ページ](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>次の手順

- [Azure Data Factory を使用して複数のコンテナーからファイルをコピーする](solution-template-copy-files-multiple-containers.md)