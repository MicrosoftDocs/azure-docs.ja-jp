---
title: Azure HDInsight にサードパーティ製アプリケーションをインストールする
description: Azure HDInsight にサード パーティ製 Hadoop アプリケーションをインストールする方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9d25996db9c13a12781d369327f2b43a41fb4115
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786513"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight にサードパーティ製 Apache Hadoop アプリケーションをインストールする

Azure HDInsight にサードパーティ製 [Apache Hadoop](https://hadoop.apache.org/) アプリケーションをインストールする方法について説明します。 独自のアプリケーションのインストール手順については、[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)のページを参照してください。

HDInsight アプリケーションは、ユーザーが HDInsight クラスターにインストールできるアプリケーションです。 マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。  

以下に、公開されているアプリケーションの一覧を示します。

|Application |クラスターのタイプ | 説明 |
|---|---|---|
|[AtScale Intelligence Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale では、HDInsight クラスターがスケールアウト OLAP サーバーに変換され、Microsoft Excel、Power BI、Tableau Software、QlikView など、ユーザーが既に所有し慣れ親しんでいる BI ツールを使用して、何十億ものデータ行を対話形式でクエリすることができます。 |
|[CDAP for HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP は、Hadoop の評価時間を加速し、IT によるセルフサービス データの提供を可能にする、史上初の一元管理されたビッグ データ用統合プラットフォームです。 オープン ソースかつ拡張可能な CDAP により、イノベーションへの障壁が取り除かれます。 要件:4 つのリージョン ノード、最小 D3 v2。 |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |分析用のデータを準備、調査、および管理するための Datameer のセルフサービスかつスケーラブルなプラットフォームでは、複雑なマルチソース データのビジネスに即応できる貴重な情報への転換を加速することにより、エンタープライズ クラスのより迅速で、より高度な洞察が可能になります。 |
|[Dataiku DSS on HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop、Spark |エンタープライズ データ サイエンス プラットフォームの Dataiku DSS では、データ サイエンティストやデータ アナリストが共同作業を行い、新しいデータ製品およびサービスをより効率的に設計し実行して、生データをインパクトのある予測に変換することができます。 |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop、Spark、HBase、Storm、Kafka |分散環境でデータの一貫性を維持することは、データ操作における非常に重要な課題です。 WANdisco Fusion はエンタープライズクラスのソフトウェア プラットフォームであり、任意の環境全体で非構造化データの整合性を実現することにより、この問題を解決します。 |
|[H2O SparklingWater for HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water では、次の分散アルゴリズムがサポートされています: GLM、Naïve Bayes、Distributed Random Forest、Gradient Boosting Machine、Deep Neural Networks、Deep learning、K-means、PCA、Generalized Low Rank Models、Anomaly Detection、Autoencoders。 |
|[Striim for Real-Time Data Integration to HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop、HBase、Storm、Spark、Kafka |Striim (発音は "ストリーム") は、エンド ツー エンドのストリーミング データ統合およびインテリジェンス プラットフォームで、これにより、異種のデータ ストリームの連続した取り込み、処理、および分析が可能になります。 |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop、Spark |大まかに言えば、Jumbune は次のように企業を支援します。1. Tez、MapReduce および Spark エンジン ベースの Hive、Java、Scala のワークロード パフォーマンスの加速化。 2. プロアクティブな Hadoop クラスターの監視。3. 分散ファイル システムでのデータ品質管理の確立。 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop、HBase、Spark |Kyligence Enterprise では、Apache Kylin を活用して、ビッグ データでの BI を可能にします。 Hadoop 上のエンタープライズ OLAP エンジンである Kyligence Enterprise により、ビジネス アナリストは、業界標準のデータ ウェアハウスと BI 手法を使用して Hadoop で BI を設計することができます。 |
|[Spark Job Server for KNIME Spark Executor](https://azuremarketplace.microsoft.com/marketplace/apps/knime.spark-job-server-for-knime-spark-executor) |Spark |Spark Job Server for KNIME Spark Executor は、KNIME Analytics Platform を HDInsight クラスターに接続するために使用されます。 |
|[Starburst Presto for Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto は、高速でスケーラブルな分散 SQL クエリ エンジンです。 ストレージと計算を分離するように設計された Presto は、Azure Data Lake Storage、Azure Blob Storage、SQL および NoSQL データベース、その他のデータ ソース内のデータのクエリを実行するために最適です。 |
|[StreamSets Data Collector for HDInsight Cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop、HBase、Spark、Kafka |StreamSets Data Collector は、リアルタイムでデータをストリーミングする軽量かつ強力なエンジンです。 Data Collector を使用して、データ ストリーム内のデータをルーティングおよび処理します。 30 日間の試用版ライセンスが付与されています。 |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop、Spark、HBase |Trifacta Wrangler Enterprise for HDInsight では、あらゆる規模のデータに対して、エンタープライズ全体のデータ ラングリングがサポートされています。 Azure で Trifacta を実行するコストは、Trifacta サブスクリプションのコストと、仮想マシン用の Azure インフラストラクチャのコストとの組み合わせになります。 |
|[Unifi Data Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/unifi-software.unifi-data-catalog) |Hadoop、HBase、Storm、Spark |Unifi Data Platform は、シームレスに統合されたセルフサービス データ ツールのスイートであり、ビジネス ユーザーが増分収益を向上させ、コストや運用の複雑さを軽減するというデータの課題に取り組めるように設計されています。 |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |HDInsight Spark クラスター用の Unravel Data アプリ。 |
|[Waterline AI-Driven Data Catalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline では、AI を使用してデータをカタログ化、整理、および管理することにより、自動的にデータにビジネス用語のタグを付けます。 Waterline のビジネス リテラシー カタログは、セルフサービスの分析、コンプライアンスとガバナンス、および IT 管理イニシアチブのための重要な成功コンポーネントです。 |

この記事で説明する手順では、Azure Portal を使用します。 また、ポータルから Azure Resource Manager テンプレートをエクスポートしたり、ベンダーから Resource Manager テンプレートのコピーを入手したりして、Azure PowerShell と Azure クラシック CLI を使ってテンプレートをデプロイすることもできます。  「[Apache Resource Manager テンプレートを使用して HDInsight に Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
既存の HDInsight クラスターに HDInsight アプリケーションをインストールする場合は、対象となる HDInsight クラスターが必要です。 新たに作成する場合は、「 [クラスターの作成](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。 HDInsight クラスターを作成するときに HDInsight アプリケーションをインストールすることもできます。

## <a name="install-applications-to-existing-clusters"></a>既存のクラスターへのアプリケーションのインストール
次の手順では、既存の HDInsight クラスターに HDInsight アプリケーションをインストールする方法について説明します。

**HDInsight アプリケーションをインストールする**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、 **[すべてのサービス]**  >  **[分析]**  >  **[HDInsight クラスター]** に移動します。
3. リストから HDInsight クラスターを選択します。  HDInsight クラスターがない場合は、最初に作成する必要があります。  「 [クラスターの作成](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。
4. **[設定]** カテゴリで、 **[アプリケーション]** を選択します。 メイン ウィンドウに、インストールされているアプリケーションの一覧が表示されます。 
   
    ![HDInsight アプリケーションのポータル メニュー](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. メニューで **[+追加]** を選択します。 使用できるアプリケーションの一覧が表示されます。  **[+追加]** が淡色表示されている場合は、このバージョンの HDInsight クラスターに対応するアプリケーションがないことを意味します。
   
    ![HDInsight アプリケーションの利用可能なアプリケーション](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. いずれかの使用可能なアプリケーションを選択し、指示に従って法律条項を受け入れます。

インストールの状態はポータル通知で確認できます (ポータル上部のベル アイコンを選択)。 アプリケーションのインストール後、[インストール済みアプリ] 一覧にアプリケーションが表示されます。

## <a name="install-applications-during-cluster-creation"></a>クラスター作成時のアプリケーションのインストール
クラスターの作成時に HDInsight アプリケーションをインストールすることもできます。 このプロセスでは、クラスターが作成されて実行状態になった後に HDInsight アプリケーションがインストールされます。 クラスターの作成時に Azure portal を使用してアプリケーションをインストールするには、既定の **[簡易作成]** オプションの代わりに **[カスタム]** オプションを使用します。

## <a name="list-installed-hdinsight-apps-and-properties"></a>インストール済み HDInsight アプリとプロパティの一覧表示
ポータルには、クラスターのインストール済み HDInsight アプリケーションのほか、インストール済みの各アプリケーションのプロパティが一覧で表示されます。

**HDInsight アプリケーションを一覧表示し、プロパティを表示する**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、 **[すべてのサービス]**  >  **[分析]**  >  **[HDInsight クラスター]** に移動します。
3. リストから HDInsight クラスターを選択します。
4. **[設定]** カテゴリで、 **[アプリケーション]** を選択します。 メイン ウィンドウに、インストールされているアプリケーションの一覧が表示されます。 
   
    ![HDInsight アプリケーションのインストールされたアプリ](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. インストール済みのアプリケーションのいずれかを選択し、プロパティを表示します。 プロパティには次の項目が一覧表示されます。

    |プロパティ | 説明 |
    |---|---|
    |アプリの名前 |アプリケーション名。 |
    |Status |アプリケーション ステータス。 |
    |Web ページ |エッジ ノードにデプロイした Web アプリケーションの URL。 資格情報は、クラスター向けに構成した HTTP ユーザーの資格情報と同じです。 |
    |SSH エンドポイント |SSH を使用してエッジ ノードに接続できます。 SSH 資格情報は、クラスター向けに構成した SSH ユーザーの資格情報と同じです。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 |
    |説明 | アプリケーションの説明。 |

6. アプリケーションを削除するには、アプリケーションを右クリックし、コンテキスト メニューの **[削除]** をクリックします。

## <a name="connect-to-the-edge-node"></a>エッジ ノードへの接続
HTTP と SSH を使用してエッジ ノードに接続できます。 エンドポイント情報は [ポータル](#list-installed-hdinsight-apps-and-properties)から確認できます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

HTTP エンドポイント資格情報は、HDInsight クラスター向けに構成した HTTP ユーザーの資格情報です。また、SSH エンドポイント資格情報は、HDInsight クラスター向けに構成した SSH 資格情報です。

## <a name="troubleshoot"></a>トラブルシューティング
「 [インストールのトラブルシューティング](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)」を参照してください。

## <a name="next-steps"></a>次の手順
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md):カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN:HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx):HDInsight アプリケーションを定義する方法を確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスター、テスト HDInsight アプリケーション、およびホスティング HDInsight アプリケーションにアクセスするために空のエッジ ノードを使用する方法を確認します。

