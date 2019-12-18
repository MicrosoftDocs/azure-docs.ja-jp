---
title: データ サイエンス プロジェクトのためのプラットフォームとツール - Team Data Science Process
description: Team Data Science Process を標準とする企業が利用できるデータおよび分析リソースを列記し、各リソースについて説明します。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 97dafe292ff3ed0ef5fa46eb895136e3b32e62c5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978511"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>データ サイエンス プロジェクトのためのプラットフォームとツール

Microsoft では、クラウド プラットフォームまたはオンプレミス プラットフォーム向けの各種データおよび分析サービスとリソースを提供しています。 これらをデプロイすることで、データ サイエンス プロジェクトを効率的かつスケーラブルに実行できます。 [Team Data Science Process](overview.md) (TDSP) によって、バージョン管理された追跡可能な方法でデータ サイエンス プロジェクトを共同で実装するチーム向けのガイダンスが提供されます。  このプロセスを標準とするデータ サイエンス チームの人員の役割とそれに関連したタスクの概要については、「[Team Data Science Process Roles and tasks (Team Data Science Process での役割とタスク)](roles-tasks.md)」をご覧ください。

TDSP を使用するデータ サイエンス チームが利用できるデータおよび分析サービスは次のとおりです。

- データ サイエンス仮想マシン (Windows および Linux CentOS)
- HDInsight Spark クラスター
- SQL Data Warehouse
- Azure Data Lake
- HDInsight Hive クラスター
- Azure File Storage
- SQL Server 2016 R Services

このドキュメントでは、各リソースについて簡単に説明し、TDSP チームが公開しているチュートリアルへのリンクを示します。 これらのチュートリアルは、リソースの使用方法やリソースを使用してインテリジェント アプリケーションの構築を開始する方法の学習に役立ちます。 これらのリソースの詳細については、それぞれの製品ページをご覧ください。 

## <a name="data-science-virtual-machine-dsvm"></a>データ サイエンス仮想マシン (DSVM)

Microsoft が Windows と Linux の両方で提供するデータ サイエンス仮想マシンには、データ サイエンスのモデリング作業と開発作業のための一般的なツールが含まれています。 次のようなツールがあります。

- Microsoft R Server Developer エディション 
- Anaconda Python ディストリビューション
- Python 用および R 用 Jupyter Notebook 
- Visual Studio Community Edition と Python および R ツール (Windows)/Eclipse (Linux)
- Windows 用 Power BI Desktop
- SQL Server 2016 Developer Edition (Windows )/Postgres (Linux)

これにはさらに、xgboost、mxnet、Vowpal Wabbit などの **ML や AI ツール**が含まれています。

現在、DSVM は、**Windows** オペレーティング システムと **Linux CentOS** オペレーティング システムでご利用いただけます。 実行を計画しているデータ サイエンス プロジェクトのニーズに基づいて、DSVM のサイズ (CPU コアの数とメモリ容量) を選択します。 

Windows エディションの DSVM の詳細については、Azure Marketplace の [Microsoft データ サイエンス仮想マシン](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)のページをご覧ください。 Linux エディションの DSVM については、[Linux データ サイエンス仮想マシン](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)のページをご覧ください。

DSVM で一般的なデータ サイエンス タスクを効率的に実行する方法については、「[データ サイエンス仮想マシンでできる 10 のこと](../data-science-virtual-machine/vm-do-ten-things.md)」をご覧ください。


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark クラスター

Apache Spark は、ビッグ データ分析アプリケーションのパフォーマンスを向上させるメモリ内処理をサポートする、オープン ソースの並列処理フレームワークです。 Spark 処理エンジンは、高速かつ簡単に高度な分析を行うことができるように作成されています。 Spark のメモリ内計算機能により、Spark は機械学習とグラフ計算における反復アルゴリズムに適した選択肢となっています。 また、Spark は Azure Blob Storage (WASB) と互換性があるので、Azure に格納されている既存のデータを Spark を使用して簡単に処理できます。

HDInsight で Spark クラスターを作成するときは、Spark をインストールおよび構成して Azure コンピューティング リソースを作成します。 HDInsight の Spark クラスターの作成には約 10 分かかります。 処理対象のデータは Azure Blob Storage に格納します。 クラスターで Azure Blob Storage を使用する方法については、[HDInsight の Hadoop での HDFS と互換性のある Azure Blob Storage の使用](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。

Microsoft の TDSP チームは、Azure HDInsight Spark クラスターを使用してデータ サイエンス ソリューションを構築する方法を示す 2 つのエンド ツー エンド チュートリアルを公開しています。1 つは Python を使用し、もう 1 つは Scala を使用します。 Azure HDInsight **Spark クラスター**の詳細については、[HDInsight Linux の Apache Spark の概要](../../hdinsight/spark/apache-spark-overview.md)に関する記事をご覧ください。 Azure HDInsight Spark クラスターで **Python** を使用してデータ サイエンス ソリューションを構築する方法については、「[Azure HDInsight 上の Spark を使用したデータ サイエンスの概要](spark-overview.md)」をご覧ください。 Azure HDInsight Spark クラスターで **Scala** を使用してデータ サイエンス ソリューションを構築する方法については、「[Scala および Azure 上の Spark を使用したデータ サイエンス](scala-walkthrough.md)」をご覧ください。 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse では、過剰なプロビジョニングや過払いが発生することなく、コンピューティング リソースを数秒で簡単にスケールできます。 また、コンピューティング リソースの使用を一時停止する独自のオプションも用意されており、クラウドの適切なコスト管理を自由に行うことができます。 スケーラブルなコンピューティング リソースをデプロイできるので、すべてのデータを Azure SQL Data Warehouse に取り込むことができます。 ストレージ コストが最小限に抑えられており、分析する必要があるデータセットの部分だけを対象に計算を実行できます。 

Azure SQL Data Warehouse の詳細については、[SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse) の Web サイトをご覧ください。 SQL Data Warehouse を使用してエンド ツー エンドの高度な分析ソリューションを構築する方法については、「[Team Data Science Process の活用: SQL Data Warehouse の使用](sqldw-walkthrough.md)」をご覧ください。


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake は、正式な要件やスキーマを適用する前に 1 か所に収集されたあらゆる種類のデータのエンタープライズ規模のリポジトリです。 この柔軟性により、データのサイズまたは構造や、データの取り込み速度に関係なく、あらゆる種類のデータを Data Lake に保持できます。 組織では、Hadoop や高度な分析を使用して、これらの Data Lake でパターンを検出できます。 また、Data Lake は、データをキュレートし、データ ウェアハウスに移動する前に低コストでデータを準備するためのリポジトリとしても機能します。

Azure Data Lake の詳細については、「[Introducing Azure Data Lake (Azure Data Lake の概要)](https://azure.microsoft.com/blog/introducing-azure-data-lake/)」をご覧ください。 Azure Data Lake を使用してスケーラブルなエンド ツー エンドのデータ サイエンス ソリューションを構築する方法については、「[Azure Data Lake を使用したスケーラブルなデータ サイエンス:エンドツーエンド チュートリアル](data-lake-walkthrough.md)」をご覧ください。


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) クラスター

Apache Hive は Hadoop のデータ ウェアハウス システムであり、HiveQL (SQL に似たクエリ言語) を使用してデータを集約、照会、分析できます。 Hive を使用することで、データを対話的に探索したり、再利用可能なバッチ処理ジョブを作成したりすることができます。

Hive では、大規模な非構造化データに構造を投影することができます。 構造を定義したら、Hive を使用して Hadoop クラスター内でそのデータを照会できます。Java や MapReduce を使用する必要はなく、その知識も不要です。 HiveQL (Hive のクエリ言語) では、T-SQL に似たステートメントを使用してクエリを記述することができます。

データ サイエンティスト向けに、Hive では Hive クエリで Python ユーザー定義関数 (UDF) を実行してレコードを処理できます。 この機能により、データ分析における Hive クエリの機能が大幅に拡張されます。 具体的には、データ サイエンティストは使い慣れた言語 (SQL に似た HiveQL と Python) でスケーラブルな特徴エンジニアリングを行うことができます。 

Azure HDInsight Hive クラスターの詳細については、[HDInsight の Hadoop での Hive と HiveQL の使用](../../hdinsight/hadoop/hdinsight-use-hive.md)に関する記事をご覧ください。 Azure HDInsight Hive クラスターを使用して、スケーラブルなエンド ツー エンドのデータ サイエンス ソリューションを構築する方法については、「[Team Data Science Process の活用: HDInsight Hadoop クラスターの使用](hive-walkthrough.md)」をご覧ください。


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage は、標準のサーバー メッセージ ブロック (SMB) プロトコルを使用してクラウドでファイル共有を提供するサービスです。 SMB 2.1 と SMB 3.0 の両方がサポートされます。 Azure File Storage を使用すると、コストがかかる書き換えを行わずに、ファイル共有に依存しているレガシ アプリケーションをすばやく Azure に移行することができます。 Azure 仮想マシンまたはクラウド サービスで実行されているアプリケーション、またはオンプレミスのクライアントから実行されているアプリケーションは、デスクトップ アプリケーションが一般的な SMB 共有をマウントするのと同じように、クラウドにファイル共有をマウントできます。 このため、任意の数のアプリケーション コンポーネントが、File Storage 共有をマウントして同時にアクセスできます。

データ サイエンス プロジェクトで特に役立つのは、プロジェクト データをプロジェクト チームのメンバーと共有する場所として Azure File Storage を作成できることです。 これにより、各メンバーは、Azure File Storage でデータの同じコピーにアクセスできます。 また、この File Storage を使用して、プロジェクトの実行中に生成される機能セットを共有することもできます。 プロジェクトがクライアント エンゲージメントの場合、クライアントが自身の Azure サブスクリプションに Azure File Storage を作成して、プロジェクトのデータや機能をチームのメンバーと共有できます。 これにより、クライアントはプロジェクトのデータ資産を完全に制御できます。 Azure File Storage の詳細については、[Windows での Azure File Storage の使用](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files)に関する記事、および [Linux で Azure File Storage を使用する方法](../../storage/files/storage-how-to-use-files-linux.md)に関する記事をご覧ください。


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (データベース内) は、新たな洞察を発見できるインテリジェント アプリケーションを開発、デプロイするためのプラットフォームです。 R コミュニティで提供される多数のパッケージを含め、豊富で強力な R 言語を使用して、SQL Server データを使ったモデルの作成や予測の生成を行うことができます。 R Services (データベース内) では R 言語が SQL Server と統合されるため、常にデータの近くで分析が実行されます。これにより、データの移動に関連するコストやセキュリティ リスクが排除されます。

R Services (データベース内) では、SQL Server のツールとテクノロジの包括的なセットを使用してオープン ソースの R 言語をサポートします。 R Services (データベース内) は、優れたパフォーマンス、セキュリティ、信頼性、管理容易性を実現します。 使い慣れた便利なツールを使用して、R ソリューションをデプロイできます。 実稼働アプリケーションでは、R ランタイムを呼び出し、Transact-SQL を使用して予測やビジュアルを取得できます。 また、ScaleR ライブラリを使用して、R ソリューションのスケールとパフォーマンスを向上させることもできます。 詳細については、[SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services) に関する記事をご覧ください。

Microsoft の TDSP チームは、SQL Server 2016 R Services でデータ サイエンス ソリューションを構築する方法を示す 2 つのエンド ツー エンド チュートリアルを公開しています。1 つは R プログラマ向けで、もう 1 つは SQL 開発者向けです。 **R プログラマ**の場合は、[データ サイエンスのエンド ツー エンド チュートリアル](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)をご覧ください。 **SQL 開発者**の場合は、[SQL 開発者向けの高度なデータベース内分析のチュートリアル](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)をご覧ください。


## <a name="appendix"></a>付録:データ サイエンス プロジェクトを設定するためのツール

### <a name="install-git-credential-manager-on-windows"></a>Windows への Git Credential Manager のインストール

**Windows** で TDSP に従う場合、Git リポジトリと通信するために **Git Credential Manager (GCM)** をインストールする必要があります。 GCM をインストールするには、最初に **Chocolaty** をインストールする必要があります。 Chocolaty と GCM をインストールするには、Windows PowerShell で**管理者**として次のコマンドを実行します。  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Linux (CentOS) マシンへの Git のインストール

Linux (CentOS) マシンに Git をインストールするには、次の Bash コマンドを実行します。

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Linux (CentOS) マシンでの SSH 公開キーの生成

Linux (CentOS) コンピューターを使用して Git コマンドを実行している場合は、このコンピューターが Azure DevOps Services によって認識されるように、コンピューターの SSH 公開キーを Azure DevOps Services に追加する必要があります。 まず、SSH 公開キーを生成し、Azure DevOps Services のセキュリティ設定ページでそのキーを SSH 公開キーに追加する必要があります。 

1. SSH キーを生成するには、次の 2 つのコマンドを実行します。 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![SSH キーを生成するコマンド](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. *ssh-rsa* も含めた SSH キー全体をコピーします。 
1. Azure DevOps Services にログインします。 
1. ページの右上隅にある **<自分の名前\>** をクリックし、 **[セキュリティ]** をクリックします。 
    
   ![自分の名前をクリックしてから [セキュリティ] をクリックする](./media/platforms-and-tools/resources-2-user-setting.png)

1. **[SSH 公開キー]** をクリックし、 **[+追加]** をクリックします。 

   ![[SSH 公開キー] をクリックしてから [+追加] をクリックする](./media/platforms-and-tools/resources-3-add-ssh.png)

1. 先ほどコピーした SSH キーをテキスト ボックスに貼り付け、保存します。


## <a name="next-steps"></a>次の手順

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio (クラシック) を使用して Team Data Science Process の手順を実行する方法を示す例については、[Azure ML の使用](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)に関するラーニング パスを参照してください。
