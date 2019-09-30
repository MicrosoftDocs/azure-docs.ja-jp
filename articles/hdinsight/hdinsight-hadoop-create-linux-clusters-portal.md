---
title: Web ブラウザーを使用して Apache Hadoop クラスターを作成する、Azure HDInsight
description: Web ブラウザーと Azure プレビュー ポータルを使用して、HDInsight 用の Linux ベースの Apache Hadoop、Apache HBase、Apache Storm、または Apache Spark クラスターを作成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b2a5168cbda209d1d975db5e5f1c810a798ddb97
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098713"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal は、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの Azure HDInsight クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 「[How to get Azure Free trial for testing Hadoop in HDInsight (HDInsight で Hadoop をテストするために Azure の無料試用版を入手する方法)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)」をご覧ください。
* **最新の Web ブラウザー**。 Azure portal では、HTML5 と JavaScript が使用されます。 古い Web ブラウザーでは正常に機能しない可能性があります。

## <a name="create-clusters"></a>クラスターの作成

Azure Portal には、ほとんどのクラスターのプロパティが公開されます。 Azure Resource Manager テンプレートを使用すると、多くの詳細を非表示にできます。 詳しくは、「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」をご覧ください。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[+ リソースの作成]** を選択します。

1. **[Azure Marketplace]** の **[分析]** を選択します。

1. **[おすすめ]** の **[HDInsight]** を選択します。

    ![Azure portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Azure portal で新しいクラスターを作成する")

1. **[HDInsight]** ページで、 **[カスタム (サイズ、設定、アプリ)]** を選択します。

1. **[1 基本]** を選択します。 そして、次の情報を入力します。

    ![HDInsight のクラスター作成の基礎](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Azure portal での新しいクラスターの作成")

    * **[クラスター名]** を入力します。 この名前はグローバルに一意である必要があります。

    * **[サブスクリプション]** ドロップダウン リストから、このクラスターに使用する Azure サブスクリプションを選択します。

    * **[クラスターの種類]** を選択します。 次に、作成するクラスターの種類を選択します。 たとえば、Hadoop や Apache Spark です。 **[オペレーティング システム]** は **[Linux]** になります。 次に、クラスターの種類のバージョンを選択します。 どれを選択すべきかわからない場合は、既定のバージョンを使用します。 詳細については、「 [HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。
     
        > [!IMPORTANT]  
        > HDInsight クラスターにはさまざまな種類があります。 それぞれに対応するワークロードやテクノロジがあり、それに合わせてクラスターが調整されます。 複数の種類を組み合わせたクラスターを作成する方法はサポートされていません。 たとえば、Storm と HBase を 1 つのクラスターにするような場合です。
        
    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、管理者ユーザーのユーザー名とパスワードを入力します。

    * **[SSH ユーザー名]** を入力します。 SSH パスワードを前に指定した管理者パスワードと同じにする場合は、 **[クラスター ログインと同じパスワードを使用します]** チェック ボックスをオンします。 そうでない場合は、SSH ユーザーを認証するための **[パスワード]** または **[公開キー]** を指定します。 公開キーを使用することをお勧めします。 下部にある **[選択]** を選択して、資格情報の構成を保存します。
   
        詳しくは、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

    * **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。

    * クラスターを作成するデータ センターの **[場所]** を指定します。

    * **[次へ]** を選択して、次のページに進みます。

4. **[2 セキュリティとネットワーク]** で、表示されているドロップダウン メニューを使用してクラスターを仮想ネットワークに接続できます。 クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。 仮想ネットワークでの HDInsight の使用については、[Azure HDInsight クラスター用の仮想ネットワーク デプロイの計画](hdinsight-plan-virtual-network-deployment.md)に関するページを参照してください。 その記事には、仮想ネットワークの具体的な構成要件が含まれます。 

    **Enterprise セキュリティ パッケージ**を使用する場合は、「[Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)」の手順に従うこともできます。

    **[次へ]** を選択して、次のページに進みます。

5. **[3 ストレージ]** で、既定のストレージとして Azure Storage と Azure Data Lake Storage のどちらを使用するかを指定します。 詳細については、後の表を参照してください。

     ![HDInsight のクラスター ストレージの作成](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Azure portal での新しいクラスターの作成")

     | Storage                                      | 説明 |
     |----------------------------------------------|-------------|
     | **既定のストレージとしての Azure Storage Blob**   | <ul><li>**[プライマリ ストレージの種類]** で、 **[Azure Storage]** を選択します。 **[選択方法]** では、自分の Azure サブスクリプションの一部であるストレージ アカウントを指定する場合は、 **[個人用サブスクリプション]** を選択します。 次に、ストレージ アカウントを選択します。 それ以外の場合は、 **[アクセス キー]** を選択します。 次に、自分の Azure サブスクリプションの外部から選択するストレージ アカウントの情報を指定します。</li><li>**[既定のコンテナー]** では、ポータルによって示された既定のコンテナー名を使用するか、独自の名前を指定します。</li><li>Azure Blob ストレージが既定のストレージである場合は、 **[追加のストレージ アカウント]** を選択して、クラスターに関連付ける追加のストレージ アカウントを指定することもできます。 **[Azure Storage キー]** では、 **[ストレージ キーの追加]** を選択します。 その後、自分の Azure サブスクリプションまたは他のサブスクリプションから、ストレージ アカウントを指定できます。 ストレージ アカウントのアクセス キーを指定します。</li><li>BLOB ストレージが既定のストレージである場合は、 **[Data Lake Storage access]\(Data Lake Storage アクセス\)** を選択し、追加のストレージとして Azure Data Lake Storage を指定することもできます。 詳細については、「[クイック スタート: HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。</li></ul> |
     | **既定のストレージとしての Azure Data Lake Storage** | **[プライマリ ストレージの種類]** で、 **[Azure Data Lake Storage Gen1]** または **[Azure Data Lake Storage Gen2]** を選択します。 その後、「[クイック スタート:HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」の手順を参照してください。 |
     | **外部メタストア**                      | 必要に応じて、クラスターに関連付けられた Apache Hive と Apache Oozie のメタデータを保存するための SQL データベースを指定します。 **[Hive 用の SQL データベースを選択する]** で、SQL データベースを選択します。 次に、データベースのユーザー名とパスワードを指定します。 以上の手順を Oozie メタデータについても行います。<br><br>メタストアに Azure SQL データベースを使用する場合の考慮事項は次のとおりです。 <ul><li>メタストアに使用される Azure SQL データベースは、Azure HDInsight などの他の Azure サービスに接続できる必要があります。 Azure SQL データベース ダッシュボードの右側で、サーバー名を選択します。 これは SQL データベース インスタンスが実行されているサーバーです。 サーバー ビューに移動した後、 **[構成]** を選択します。 次に、 **[Azure サービス]** で **[はい]** を選択します。 次に、 **[保存]** を選択します。</li><li>メタストアを作成するとき、データベース名にダッシュやハイフンを使用しないでください。 これらの文字が含まれると、クラスター作成プロセスが失敗する可能性があります。</li></ul> |

     > [!WARNING]  
     > HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

     **[次へ]** を選択して、次のページに進みます。

6. **[4 アプリケーション (オプション)]** で、必要なアプリケーションを選択します。 これらのアプリケーションは、Microsoft、独立系ソフトウェア ベンダー (ISV)、またはご自身が独自に開発できます。 詳しくは、「[クラスター作成時のアプリケーションのインストール](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)」をご覧ください。

    **[次へ]** を選択して、次のページに進みます。

6. **[5 クラスター サイズ]** に、このクラスターのために使用されるノードに関する情報が表示されます。 クラスターで必要な worker ノードの数を設定します。 クラスター実行の推定コストが表示されます。

    ![HDInsight のクラスター ノードの作成](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "クラスター ノード数の指定")

   > [!IMPORTANT]  
   > 32 個を超えるワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14 GB 以上のサイズのヘッド ノードを選択します。 クラスターの作成時に、または作成後にクラスターのスケーリングで、ノードを計画します。
   >
   > ノードのサイズと関連コストについて詳しくは、「[Azure HDInsight の料金](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。

    **[次へ]** を選択して、次のページに進みます。

8. **[6 スクリプト操作]** で、カスタム コンポーネントをインストールするようにクラスターをカスタマイズできます。 このオプションは、クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合に使用します。 スクリプト アクションについて詳しくは、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。

   **[次へ]** を選択して、次のページに進みます。

9. **[7 概要]** で、これまでに入力した情報を確認します。 **[作成]** を選択します。

     ![HDInsight のクラスター作成の概要](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "クラスター ノード数の指定")
    
    > [!NOTE]  
    > クラスターが作成されるまで、通常は約 20 分かかります。 **[通知]** を監視して、プロビジョニング プロセスをチェックします。

10. 作成プロセスが完了したら、 **[デプロイが成功しました]** という通知から **[リソースに移動]** を選択します。 クラスター ウィンドウには、次の情報が表示されます。

    ![HDI Azure portal クラスターの概要](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "クラスターのプロパティ")

    ウィンドウのアイコンの説明は以下のとおりです。

    * **[概要]** タブでは、クラスターに関するすべての重要な情報が提供されます。 たとえば、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などです。
    * **[ダッシュ ボード]** から、クラスターに関連付けられている Ambari ポータルに移動します。
    * **[Secure Shell]** では、SSH を使用してクラスターにアクセスするために必要な情報が提供されます。
    * **[クラスターのスケール設定]** を使用すると、クラスターに関連付けられているワーカー ノードの数を増やすことができます。
    * **[削除]** は、HDInsight クラスターを削除します。

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

HDInsight クラスターが正常に作成されました。 次に、クラスターを操作する方法を学習してください。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター

* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm クラスター

* [HDInsight での Apache Storm の Java トポロジの開発](storm/apache-storm-develop-java-topology.md)
* [HDInsight の Apache Storm での Python コンポーネントの使用](storm/apache-storm-develop-python-topology.md)
* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジのデプロイと監視)](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark クラスター

* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用して対話型データ分析を実行する](spark/apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)

