---
title: Azure Data Factory を使用してオンプレミスの Netezza サーバーから Azure にデータを移行する | Microsoft Docs
description: Azure Data Factory を使用してオンプレミスの Netezza サーバーから Azure にデータを移行します。
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259459"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Azure Data Factory を使用してオンプレミスの Netezza サーバーから Azure にデータを移行する 

Azure Data Factory には、オンプレミスの Netezza サーバーから Azure Storage または Azure SQL Data Warehouse に大量のデータを移行することができる、パフォーマンスと信頼性が高くコスト効率に優れたメカニズムが用意されています。 この記事では、データ エンジニアと開発者向けに次の情報を提供します。

> [!div class="checklist"]
> * パフォーマンス 
> * 回復性のコピー
> * ネットワークのセキュリティ
> * 概要ソリューション アーキテクチャ 
> * 実装のベスト プラクティス  

## <a name="performance"></a>パフォーマンス

Azure Data Factory には、さまざまなレベルで並列処理を可能にするサーバーレス アーキテクチャが用意されています。そのため、開発者は、ネットワーク帯域幅だけでなくデータベース帯域幅を利用して、データ移動のスループットが環境に合わせて最大限になるパイプラインを構築できます。

![パフォーマンス](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。Azure Integration Runtime を使用する場合は、サーバーレス方式で各コピー アクティビティに対して[最大 256 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) を指定できます。セルフホステッド統合ランタイムを使用する場合は、手動でマシンをスケールアップするか、複数のマシン ([最大 4 ノード](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) にスケールアウトすることができます。また、1 回のコピー アクティビティによって、そのパーティションが全ノードにわたって配布されます。 
- 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが行われます。 
- Azure Data Factory 制御フローでは、複数のコピー アクティビティを並列して開始できます。たとえば、[For Each ループ](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)を使用します。 

詳細については、[コピー アクティビティのパフォーマンスに関するガイド](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)を参照してください。

## <a name="resilience"></a>回復力

Azure Data Factory には、1 回のコピー アクティビティの実行で、データ ストアまたは基になるネットワークの特定のレベルの一時的なエラーを処理できる組み込みの再試行メカニズムがあります。

また、Azure Data Factory のコピー アクティビティには、ソースとシンク データ ストアの間でデータをコピーするときの互換性のない行の取り扱いについて、2 つの方法が用意されています。 互換性のないデータが検出されたときは、コピー アクティビティを中止して失敗とするか、互換性のないデータ行をスキップして残りのデータのコピーを続行できます。 さらに、互換性のない行を Azure Blob Storage または Azure Data Lake Store のログに記録してエラーの原因を把握し、データ ソースのデータを修正してから、コピーアクティビティを再試行できます。

## <a name="network-security"></a>ネットワークのセキュリティ 

既定では、Azure Data Factory は、HTTPS プロトコル経由の暗号化された接続を使用して、オンプレミスの Netezza サーバーから Azure Storage または Azure SQL Data Warehouse にデータを転送します。 これによって転送中のデータが暗号化され、盗聴や中間者攻撃が防止されます。

また、パブリック インターネット経由でデータを転送しない場合は、Azure ExpressRoute を介してプライベート ピアリング リンク経由でデータを転送することで、より高いセキュリティを実現できます。 これを実現する方法については、以下のソリューション アーキテクチャを参照してください。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

パブリック インターネット経由でデータを移行する:

![solution-architecture-public-network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- このアーキテクチャでは、データはパブリック インターネット経由で HTTPS を使用して安全に転送されます。
- このアーキテクチャを実現するには、企業ファイアウォールの内側にある Windows マシンに、Azure Data Factory セルフホステッド統合ランタイムをインストールする必要があります。 Windows マシンで Azure Data Factory セルフホステッド統合ランタイムを使用して、Netezza サーバーに直接アクセスできることを確認します。 マシンを手動でスケールアップするか、複数のマシンにスケールアウトすることで、ネットワークとデータ ストアの帯域幅を完全に活用してデータをコピーできます。
- 初回のスナップショット移行と差分の移行は、このアーキテクチャを使用して実現できます。

プライベート リンク経由でデータを移行する: 

![solution-architecture-private-network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- このアーキテクチャでは、データの移行は Azure ExpressRoute を介してプライベート ピアリング リンク経由で行われるので、データがパブリック インターネット経由で転送されることはありません。 
- このアーキテクチャを実現するには、Azure 仮想ネットワーク内の Windows VM に Azure Data Factory セルフホステッド統合ランタイムをインストールする必要があります。 VM を手動でスケールアップするか、複数の VM にスケールアウトすることで、ネットワークとデータ ストアの帯域幅を完全に活用してデータをコピーできます。
- 初回のスナップショット移行と差分の移行は、このアーキテクチャを使用して実現できます。

## <a name="implementation-best-practices"></a>実装のベスト プラクティス 

### <a name="authentication-and-credential-management"></a>認証と資格情報の管理 

- Netezza に対して認証を行うために、[接続文字列を介した ODBC 認証](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)を使用できます。 
- Azure Blob Storage に接続するには、複数の認証の種類がサポートされています。  [Azure リソースのマネージド ID](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) を使用することを強くお勧めします。これは Azure AD で自動的に管理される Azure Data Factory ID をベースに構築されており、リンクされたサービス定義で資格情報を指定せずにパイプラインを構成できます。  また、[サービス プリンシパル](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)、[共有アクセス署名](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)、または[ストレージ アカウント キー](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)を使用して Azure Blob Storage に対する認証を行うこともできます。 
- Azure Data Lake Storage Gen2 に接続するには、複数の認証の種類がサポートされています。  [サービス プリンシパル](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)または[ストレージ アカウント キー](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)を使用することもできますが、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) を使用することを強くお勧めします。 
- また、Azure SQL Data Warehouse に接続する方法として、複数の認証の種類がサポートされています。 [サービス プリンシパル](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication)や [SQL 認証](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)を使用することもできますが、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) を使用することを強くお勧めします。
- Azure リソースのマネージド ID を使用しない場合は、簡単にするために、[Azure Key Vault に資格情報を格納](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)して、Azure Data Factory のリンクされたサービスを変更せずに、キーを一元的に管理およびローテーションすることを強くお勧めします。  これは、[CI/CD のベスト プラクティス](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)の1 つでもあります。 

### <a name="initial-snapshot-data-migration"></a>初回のスナップショット データ移行 

ボリューム サイズが 100 GB より小さい、または 2 時間以内に Azure に移行できる小さいテーブルの場合は、各コピー ジョブでテーブルごとにデータを読み込むよう設定できます。 複数の Azure Data Factory コピー ジョブを実行して、異なるテーブルを同時に読み込むことで、スループットを向上させることができます。 

各コピー ジョブ内では、データをパーティション分割するオプションで [parallelCopies の設定値](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy)を使用して並列クエリを実行し、パーティションごとにデータをコピーすることで、ある程度の水準の並列処理を達成することもできます。 データをパーティション分割するオプションは 2 つあります。以下に詳細を示します。
- データ スライスから始めることをお勧めします。そのほうがより効率的であるためです。  parallelCopies の設定値の並列処理の数が、Netezza サーバー上のテーブル内のデータ スライス パーティションの合計数を下回っていることを確認します。  
- 各データ スライス パーティションのボリューム サイズが依然として大きい場合 (たとえば、10 GB を超える場合) は、動的範囲パーティションに切り替えることをお勧めします。この場合、パーティションの数と各パーティションのパーティション列ごとのボリュームのサイズやその上限と下限を柔軟に定義できます。

ボリューム サイズが 100 GB を超える、または 2 時間以内に Azure に移行できない大きなテーブルの場合は、カスタム クエリを使用してデータをパーティションに分割し、その後各コピー ジョブで一度に 1 つのパーティションをコピーするよう設定することをお勧めします。 複数の Azure Data Factory コピー ジョブを同時に実行して、スループットを向上させることができます。 カスタム クエリにより各コピー ジョブで 1 つのパーティションを読み込むよう設定している場合でも、データ スライスまたは動的範囲を介して並列処理を有効にすることで、スループットを向上させることができることに注意してください。 

ネットワークまたはデータ ストアの一時的な問題によってコピー ジョブが失敗した場合は、失敗したコピー ジョブを再実行して、そのテーブルから特定のパーティションを再度読み込むことができます。 他のパーティションを読み込む他のすべてのコピー ジョブには影響しません。

Azure SQL Data Warehouse にデータを読み込むときは、Azure Blob Storage をステージングとしてコピー ジョブ内で Polybase を有効にすることをお勧めします。

### <a name="delta-data-migration"></a>差分のデータ移行 

テーブルから新規または更新された行を識別する方法には、タイムスタンプ列またはスキーマ内のキーの増分を使用する方法があり、その後その最新の値を高基準値として外部テーブルに格納し、次にデータが読み込まれるときにデルタ データをフィルター処理するために使用できます。 

テーブルごとに異なる基準値列を使用して、新しい行や更新された行を識別できます。 Microsoft では、外部制御テーブルを作成することをお勧めします。ここで、各行は特定の基準値列名と高基準値を持つ Netezza サーバー上の 1 つのテーブルを表します。 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Azure VM またはマシン上でのセルフホステッド統合ランタイムの構成

Netezza サーバーから Azure にデータを移行する場合、Netezza サーバーが企業ファイアウォールの内側にあるか VNET 環境内にあるかに関係なく、データを移動するエンジンとして、セルフホステッド統合ランタイムを Windows マシンまたは VM にインストール必要があります。

- 各マシンまたは VM に対して最初に推奨される構成は、32 vCPU と 128 GB のメモリが搭載された構成です。 データ移行中に IR マシンの CPU とメモリの使用状況を監視して、パフォーマンスを向上させるためにマシンをさらにスケールアップする必要があるか、コストを節約するためにマシンをスケールダウンする必要があるかを確認できます。
- また、1 つのセルフホステッド IR に最大 4 つのノードを関連付けてスケールアウトすることもできます。 セルフホステッド IR に対して実行される 1 回のコピー ジョブで、すべての VM ノードが自動的に利用されてデータが並列してコピーされます。 高可用性を実現するには、データ移行中の単一障害点を回避するために、2 つの VM ノードから始めることをお勧めします。

### <a name="rate-limiting"></a>レート制限

ベスト プラクティスとして、代表的なサンプル データセットを使用してパフォーマンス POC を実施し、各コピー アクティビティにおける適切なパーティションのサイズを決定できるようにします。 各パーティションが Azure に 2 時間以内に読み込まれるように設定することをお勧めします。  

まずは 1 台のセルフホステッド IR マシンで 1 回のコピー アクティビティによりテーブルをコピーしてみてください。 テーブル内のデータ スライス パーティションの数に基づいて parallelCopies の設定値を徐々に増やし、コピー ジョブから見られるスループットに従って、テーブル全体を 2 時間以内に Azure に読み込むことができるかどうかを確認します。 

それを実現できず、かつセルフホステッド IR ノードとデータ ストアの容量が完全に使用されていない場合は、ネットワークの制限またはデータ ストアの帯域幅制限に達するまで、同時コピー アクティビティの数を徐々に増やします。 

引き続きセルフホステッド IR マシンの CPU/メモリ使用率を監視し、CPU/メモリが完全に使用されていることが確認されたときに、マシンをスケールアップするか複数のコンピューターにスケールアウトできるよう準備しておきます。 

Azure Data Factory のコピー アクティビティによって報告された調整エラーが発生した場合は、Azure Data Factory の同時実行数または parallelCopies の設定値を減らすか、ネットワークとデータ ストアの帯域幅/IOPS の制限値を大きくすることを検討してください。 


### <a name="estimating-price"></a>料金の見積り 

オンプレミスの Netezza サーバーから Azure SQL Data Warehouse にデータを移行するために、次のパイプラインが構築されているとします。

![pricing-pipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

ここでは、次のことを想定しています。 

- 合計データ量は 50 TB。 
- 最初のソリューション アーキテクチャを使用してデータを移行する (Netezza サーバーはファイアウォールの内側に設置されている)。
- 50 TB が 500 のパーティションに分割され、各コピー アクティビティで 1 つのパーティションが移行される。
- 各コピー アクティビティは 4 台のマシンに対して 1 つのセルフホステッド IR を使用して構成され、20 MBps のスループットを実現する (コピー アクティビティ内では、parallelCopies が 4 に設定され、テーブルからデータを読み込む各スレッドが 5 MBps のスループットを実現する)。
- ForEach の同時実行数は 3 に設定され、合計スループットは 60 MBps である。
- 合計では、移行が完了するまでに 243 時間かかる。

上記の前提条件に基づく推定料金は次のとおりです。 

![pricing-table](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> これは仮定の料金例です。 実際の料金は、環境の実際のスループットによって変わります。 (セルフホステッド統合ランタイムがインストールされている) Windowsマシンの料金は含まれていません。 

### <a name="additional-references"></a>その他のリファレンス 
- [Azure Data Factory を使用してオンプレミスのリレーショナル データ ウェアハウスから Azure にデータを移行する](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza コネクタ](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC コネクタ](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob Storage コネクタ](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 コネクタ](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse コネクタ](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [コピー アクティビティのパフォーマンスとチューニングに関するガイド](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [セルフホステッド統合ランタイムの作成と構成](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [セルフホステッド統合ランタイム HA とスケーラビリティ](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [データ移動のセキュリティに関する考慮事項](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault への資格情報の格納](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [1 つのテーブルからデータを増分コピーする](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [複数のテーブルからデータを増分コピーする](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure Data Factory の価格ページ](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>次の手順

- [Azure Data Factory を使用して複数のコンテナーからファイルをコピーする](solution-template-copy-files-multiple-containers.md)