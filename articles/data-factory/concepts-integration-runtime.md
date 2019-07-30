---
title: Azure Data Factory の統合ランタイム | Microsoft Docs
description: Azure Data Factory の統合ランタイムについて。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: abnarain
ms.openlocfilehash: 7b0b637bdbab8f85c87d28473dda8f2e8f8a086e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312060"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory の統合ランタイム
Integration Runtime (IR) は、異なるネットワーク環境間で以下のデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。

- **Data Flow**: マネージド Azure コンピューティング環境で[データ フロー](concepts-data-flow-overview.md)を実行します。  
- **データ移動**:パブリック ネットワーク内のデータ ストアとプライベート ネットワーク (オンプレミスまたは仮想プライベート ネットワーク) 内のデータ ストアをまたいでデータをコピーします。 組み込みコネクタ、形式の変換、列のマッピング、パフォーマンスとスケーラビリティに優れたデータ転送に関するサポートを提供します。
- **アクティビティのディスパッチ**:Azure Databricks、Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server などのさまざまなコンピューティング サービスで実行される変換アクティビティをディスパッチして監視します。
- **SSIS パッケージの実行**:マネージド Azure コンピューティング環境で SQL Server Integration Services (SSIS) パッケージをネイティブに実行します。

Data Factory で、アクティビティは、実行されるアクションを定義します。 リンクされたサービスは、ターゲットのデータ ストアやコンピューティング サービスを定義します。 統合ランタイムは、アクティビティとリンクされたサービスとを橋渡しします。  統合ランタイムは、リンクされたサービスまたはアクティビティによって参照され、アクティビティが実行されたりディスパッチされたりするコンピューティング環境を提供します。 これにより、できるだけターゲットのデータ ストアやコンピューティング サービスに近いリージョンでアクティビティを実行して効率を最大化できる一方、セキュリティとコンプライアンスの必要も満たせます。

## <a name="integration-runtime-types"></a>統合ランタイムの種類
Data Factory には 3 種類の統合ランタイムが用意されているので、ご希望のデータ統合機能やネットワーク環境のニーズに最もかなっている種類を選択する必要があります。  次の 3 種類があります。

- Azure
- セルフホステッド
- Azure-SSIS

次の表で、各種の統合ランタイムの機能とネットワークのサポートについて説明します。

IR の種類 | パブリック ネットワーク | プライベート ネットワーク
------- | -------------- | ---------------
Azure | Data Flow<br/>データの移動<br/>アクティビティのディスパッチ | &nbsp;
セルフホステッド | データの移動<br/>アクティビティのディスパッチ | データの移動<br/>アクティビティのディスパッチ
Azure-SSIS | SSIS パッケージの実行 | SSIS パッケージの実行

次の図は、さまざまな統合ランタイムを組み合わせて使用し、優れたデータ統合機能やネットワークのサポートを提供する方法を示しています。

![各種の統合ランタイム](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure 統合ランタイム
Azure 統合ランタイムにより、次のことが可能になります。

- Azure でデータ フローを実行する 
- クラウドのデータ ストア間でコピー アクティビティを実行する
- パブリック ネットワーク内で次の変換アクティビティをディスパッチする:Databricks Notebook/Jar/Python アクティビティ、HDInsight Hive アクティビティ、HDInsight Pig アクティビティ、HDInsight MapReduce アクティビティ、HDInsight Spark アクティビティ、HDInsight Streaming アクティビティ、Machine Learning バッチ実行アクティビティ、Machine Learning 更新リソース アクティビティ、ストアド プロシージャ アクティビティ、Data Lake Analytics U-SQL アクティビティ、.NET カスタム アクティビティ、Web アクティビティ、ルックアップ アクティビティ、メタデータの取得アクティビティ。

### <a name="azure-ir-network-environment"></a>Azure IR のネットワーク環境
Azure Integration Runtime は、だれでもアクセス可能なエンドポイントを使用して、データ ストアやコンピューティング サービスへの接続をサポートします。 Azure Virtual Network 環境にはセルフホステッド統合ランタイムを使用します。

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR のコンピューティング リソースとスケーリング
Azure 統合ランタイムは、Azure 内のフル マネージドのサーバーレス コンピューティングを提供します。  インフラストラクチャのプロビジョニング、ソフトウェアのインストール、修正プログラムの適用、容量のスケーリングについて心配する必要はありません。  さらに、実際の使用時間分だけのお支払いになります。

Azure 統合ランタイムは、ネイティブのコンピューティングを備えており、セキュリティで保護された、信頼性とパフォーマンスの高い方法で、クラウドのデータ ストア間でデータを移動します。  コピー アクティビティで使用するデータの統合単位の数を設定できます。Azure IR のコンピューティング サイズはそれに応じて柔軟にスケール アップし、Azure 統合ランタイムのサイズを明示的に調整する必要はありません。 

アクティビティのディスパッチは、アクティビティをターゲット コンピューティング サービスにルーティングする簡易な操作です。したがって、このシナリオでコンピューティング サイズをスケール アップする必要はありません。

Azure IR の作成と構成については、ハウツー ガイドで Azure IR の作成と構成の方法の説明をご覧ください。 

> [!NOTE] 
> Azure Integration Runtime には、データ フローを実行するための基盤となるコンピューティング インフラストラクチャを定義する、Data Flow ランタイムに関連するプロパティがあります。 

## <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム
セルフホステッド IR により、次のことが可能になります。

- クラウドのデータ ストアとプライベート ネットワーク内のデータ ストアの間でコピー アクティビティを実行する。
- オンプレミスまたは Azure Virtual Network 内のコンピューティング リソースに対して次の変換アクティビティをディスパッチする:HDInsight Hive アクティビティ (BYOC-Bring Your Own Cluster)、HDInsight Pig アクティビティ (BYOC)、HDInsight MapReduce アクティビティ (BYOC)、HDInsight Spark アクティビティ (BYOC)、HDInsight Streaming アクティビティ (BYOC)、Machine Learning バッチ実行アクティビティ、Machine Learning リソース更新アクティビティ、ストアド プロシージャ― アクティビティ、Data Lake Analytics U-SQL アクティビティ、カスタム アクティビティ (Azure Batch 上で実行)、ルックアップ アクティビティ、およびメタデータ取得アクティビティ。

> [!NOTE] 
> SAP Hana や MySQL などの独自ドライバーを必要とするデータ ストアをサポートするには、セルフホステッド統合ランタイムを使用します。詳細については、「[supported data stores (サポートされるデータ ストア)](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

### <a name="self-hosted-ir-network-environment"></a>セルフホステッド IR のネットワーク環境
パブリック クラウド環境からの直接の通信経路がないプライベート ネットワーク環境で、安全にデータ統合を実行しようとしている場合は、社内のファイアウォール内のオンプレミス環境か仮想プライベート ネットワーク内にセルフホステッド IR をインストールできます。  セルフホステッド統合ランタイムは、開かれたインターネットへの送信 HTTP ベースの接続のみを行います。

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>セルフホステッド IR のコンピューティング リソースとスケーリング
セルフホステッド IR のインストールには、オンプレミスのマシンか、プライベート ネットワーク内の仮想マシンが必要です。 現時点では、セルフホステッド IR の実行対象として Windows オペレーティング システムだけがサポートされています。  

高可用性とスケーラビリティを実現するには、アクティブ/アクティブ モードで論理インスタンスをオンプレミスの複数のマシンに関連付けて、セルフホステッド IR をスケールアウトできます。  詳細については、ハウツー ガイドでセルフホステッド IR の作成と構成の方法に関する記事をご覧ください。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイム
既存の SSIS ワークロードをリフトアンドシフトするには、Azure-SSIS IR を作成して SSIS パッケージをネイティブに実行できます。

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR のネットワーク環境
Azure-SSIS IR は、パブリック ネットワークかプライベート ネットワーク内でプロビジョニングできます。  オンプレミスのデータ アクセスは、オンプレミスのネットワークに接続している仮想ネットワークと Azure-SSIS IR を結合することでサポートされます。  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR のコンピューティング リソースとスケーリング
Azure-SSIS IR は、SSIS パッケージ実行専用の、Azure VM のフル マネージドのクラスターです。 独自の Azure SQL Database または Managed Instance サーバーを持ち込み、それに接続する SSIS プロジェクト/パッケージ (SSISDB) のカタログをホストすることができます。 ノードのサイズを指定してコンピューティング能力をスケールアップしたり、クラスター内のノードの数を指定してスケール アウトしたりできます。 必要に応じて Azure-SSIS 統合ランタイムを停止したり開始したりして、その実行のコストを管理できます。

詳細については、ハウツー ガイドで Azure-SSIS IR の作成と構成の方法に関する記事をご覧ください。  作成し終えたら、オンプレミスで SSIS を使用する場合と同様に、SQL Server Data Tools (SSDT) や SQL Server Management Studio (SSMS) などの使い慣れたツールを使用して、まったく、またはほとんど変更を加えずに既存の SSIS パッケージをデプロイして管理することができます。

Azure-SSIS ランタイムの詳細については、次の記事をご覧ください。 

- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md): この記事では、Azure-SSIS IR を作成し、Azure SQL データベースを使って SSIS カタログをホストする手順が説明されています。 
- [方法:Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)。 この記事では、チュートリアルを基に、Azure SQL Database Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 

## <a name="integration-runtime-location"></a>統合ランタイムの場所
Data Factory の場所とは、データ ファクトリのメタデータが格納され、パイプラインのトリガーが開始される場所のことです。 ただし、データ ファクトリは、他の Azure リージョン内のデータ ストアやコンピューティング サービスにアクセスし、データ ストア間でデータを移動したり、コンピューティング サービスを使用してデータを処理したりできます。 この動作は[グローバルに使用できる IR](https://azure.microsoft.com/global-infrastructure/services/) によって実現し、データのコンプライアンス、効率性、ネットワークのエグレスのコストの削減が保証されます。

IR の場所は、そのバックエンドのコンピューティングの場所を定義するほか、実質的にデータの移動、アクティビティのディスパッチ、SSIS パッケージの実行が行われる場所を定義します。 IR の場所は、それが属しているデータ ファクトリの場所とは別にすることができます。 

### <a name="azure-ir-location"></a>Azure IR の場所
Azure IR の特定の場所を設定することができます。その場合は、その特定のリージョンでデータの移動やアクティビティのディスパッチが行われます。 

既定の設定である**自動解決の Azure IR** を使用することを選択した場合は、次のようになります。 

- コピー アクティビティでは、ADF は、シンクとソースのデータ ストアをベスト エフォートで自動的に検出して、同じリージョン内 (使用可能な場合) または同じ地理的な場所内の最も近いリージョン内で、最適な場所を選択します。検出できなかった場合は、代わりにデータ ファクトリのリージョンを使用します。

- Lookup/GetMetadata/Delete アクティビティの実行 (パイプライン アクティビティとも呼ばれます)、変換アクティビティのディスパッチ (外部アクティビティとも呼ばれます)、およびオーサリング操作 (接続のテスト、フォルダー一覧とテーブル一覧の参照、データのプレビュー) の場合、ADF はデータ ファクトリのリージョンで IR を使用します。

- Data Flow の場合、ADF はデータ ファクトリのリージョンの IR を使用します。 

  > [!TIP] 
  > 可能な場合は、Data Flow を対応するデータ ストアと同じリージョンで実行することをお勧めします。 これを実現するには、Azure IR を自動解決するか (データ ストアの場所が Data Factory の場所と同じ場合)、データ ストアと同じリージョンに新しい Azure IR インスタンスを作成し、そこでデータ フローを実行します。 

アクティビティの実行時に有効になっている IR の場所は、UI 上のパイプラインのアクティビティの監視ビューまたはアクティビティの監視のペイロードで監視できます。

>[!TIP]
>データ コンプライアンスの要件が厳しく、データが地理的な特定の場所を離れないようにする必要がある場合は、Azure IR を明示的に特定のリージョンに作成し、リンクされたサービスが ConnectVia プロパティを使用してこの IR を指すようにすることができます。 たとえば、データを英国内に留めたまま、英国南部の BLOB から英国南部の SQL DW にデータをコピーしたい場合は、英国南部に Azure IR を作成して、両方のリンクされたサービスをこの IR にリンクします。

### <a name="self-hosted-ir-location"></a>セルフホステッド IR の場所
セルフホステッド IR は Data Factory に論理的に登録されます。その機能のサポートのために使用するコンピューティングは自分で提供します。 したがって、セルフホステッド IR の明示的な場所のプロパティはありません。 

セルフホステッド IR を使用してデータの移動を実行する場合、この IR はデータをソースから抽出して移動先に書き込みます。

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR の場所
抽出、変換、読み込み (ETL) ワークフローで高いパフォーマンスを実現するには、Azure-SSIS IR の正しい場所を選択することが重要です。

- Azure-SSIS IR の場所をデータ ファクトリの場所と同じにする必要はありませんが、SSISDB がホストされる独自の Azure SQL Database/Managed Instance サーバーの場所と同じにする必要があります。 こうすると、Azure-SSIS 統合ランタイムから SSISDB に簡単にアクセスでき、複数の場所の間で過剰なトラフィックが生じません。
- SSISDB をホストする既存の Azure SQL Database/Managed Instance サーバーがなく、オンプレミスのデータ ソース/移動先がある場合、オンプレミスのネットワークに接続している仮想ネットワークの同じ場所に新しい Azure SQL Database/Managed Instance サーバーを作成する必要があります。  この場合、新しい Azure SQL Database/Managed Instance サーバーを使用し、この仮想ネットワークを参加させ、すべて同一の場所で Azure-SSIS IR を作成することができるため、異なる場所との間でデータの移動を効果的に最小限にすることができます。
- SSISDB をホストする既存の Azure SQL Database/Managed Instance サーバーの場所と、オンプレミスのネットワークに接続している仮想ネットワークの場所が違う場合は、まず、既存の Azure SQL Database/Managed Instance サーバーを使用して Azure-SSIS IR を作成し、同じ場所の別の仮想ネットワークを参加させます。次に、異なる場所の間の仮想ネットワーク間接続を構成します。

次の図は、Data Factory とその統合ランタイムの場所の設定を示しています。

![統合ランタイムの場所](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>使用する IR の判別

### <a name="copy-activity"></a>コピー アクティビティ

コピー アクティビティの場合、データ フローの方向を定義するのに、ソースとシンクのリンクされたサービスが必要です。 どの統合ランタイム インスタンスを使用してコピーを実行するかを決めるために、次のロジックが使用されます。 

- **2 つのクラウド データ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスで Azure IR を使用している場合、ADF は、場所が指定されている場合は、そのリージョンの Azure IR を使用します。自動解決 IR (既定) が選択されている場合は、「[統合ランタイムの場所](#integration-runtime-location)」セクションで説明したとおり、Azure IR の場所を自動的に決定します。
- **クラウド データ ソースとプライベート ネットワーク内のデータ ソースの間でのコピー**: ソースかシンクのいずれかのリンクされたサービスがセルフホステッド IR を指している場合、そのセルフホステッド統合ランタイム上でコピー アクティビティが実行されます。
- **プライベート ネットワーク内の 2 つのデータ ソース間でのコピー**: ソースとシンクの両方のリンクされたサービスが同じ統合ランタイム インスタンスを指す必要があり、その統合ランタイムを使用してコピー アクティビティが実行されます。

### <a name="lookup-and-getmetadata-activity"></a>Lookup および GetMetadata アクティビティ

Lookup および GetMetadata アクティビティは、データ ストアのリンクされたサービスに関連付けられている統合ランタイム上で実行されます。

### <a name="transformation-activity"></a>変換アクティビティ

各変換アクティビティには、ターゲット コンピューティングのリンクされたサービスがあり、これは統合ランタイムに向けられています。 この統合ランタイム インスタンスから、変換アクティビティがディスパッチされます。

### <a name="data-flow-activity"></a>Data Flow アクティビティ

Data Flow アクティビティは、それに関連付けられている統合ランタイムで実行されます。 

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [Azure Integration Runtime の作成](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime (セルフホステッド統合ランタイムの作成)](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 統合ランタイムを作成](create-azure-ssis-integration-runtime.md)します。 この記事では、チュートリアルを基に、Azure SQL Database Managed Instance の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
