---
title: "Web ブラウザーを使用して Azure HDInsight (Hadoop) を作成する | Microsoft Docs"
description: "Web ブラウザーと Azure プレビュー ポータルを使用して、Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを HDInsight に作成する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 22b45634ee66ec29c089ecd0a4d9bacc60f68b94
ms.lasthandoff: 03/25/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal は、Microsoft Azure クラウドでホストされるサービスやリソースの Web ベースの管理ツールです。 この記事では、ポータルを使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **最新の Web ブラウザー**。 Azure Portal では、HTML5 と Javascript が使用されています。Web ブラウザーのバージョンが低いと正しく機能しない場合があります。

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>クラスターの作成
Azure Portal には、ほとんどのクラスターのプロパティが公開されます。 Azure Resource Manager テンプレートを使用すると、多くの詳細を非表示にできます。 詳細については、「 [Azure Resource Manager テンプレートを使用した HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[+]** をクリックし、**[インテリジェンス + 分析]**、**[HDInsight]** の順にクリックします。
   
    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Azure Portal で新しいクラスターを作成する")

3. **[HDInsight]** ブレードで、**[Custom (size, settings, apps)]** (カスタム (サイズ、設定、アプリ))、**[基本]** の順にクリックし、次の情報を入力します。

    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.basics.png "Azure Portal で新しいクラスターを作成する")

    * **クラスター名**を入力します。この名前はグローバルに一意である必要があります。

    * **[サブスクリプション]** ドロップダウンから、このクラスターに使用する Azure サブスクリプションを選択します。

    * **[クラスターの種類]** をクリックし、以下を選択します。
   
        * **[クラスターの種類]**: どれを選択すべきかわからない場合は、**[Hadoop]** を選択します。 これが最も一般的なクラスターの種類です。
     
             > [!IMPORTANT]
             > HDInsight クラスターにはさまざまな種類があり、それぞれ適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。 
             > 
             > 
        
        * **[オペレーティング システム]**: **[Linux]** を選択します。
        
        * **[バージョン]**: どれを選択すべきかわからない場合は、既定のバージョンを使用します。 詳細については、「 [HDInsight クラスターのバージョン](hdinsight-component-versioning.md)」をご覧ください。
        * **[クラスター レベル]**: Azure HDInsight では、Standard レベルと Premium レベルの 2 つのカテゴリでビッグ データのクラウド サービスを提供します。 詳細については、 [クラスター レベル](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers)に関するページをご覧ください。

    * **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、管理者ユーザーのユーザー名とパスワードを入力します。

    * **[SSH ユーザー名]** を入力し、前に指定した管理者パスワードと SSH パスワードを同じにする場合、**[クラスター ログインと同じパスワードを使用します]** チェック ボックスをオンします。 同じにしない場合、SSH ユーザーを認証するために使用される **[パスワード]** または **[公開キー]** のどちらかを入力します。 公開キーを使用することをお勧めします。 下部にある **[選択]** をクリックして資格情報の構成を保存します。
   
        詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

    * **[リソース グループ]** で、新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。

    * クラスターを作成するデータ センターの **[場所]** を指定します。

    * **[次へ]**をクリックします。

4. **[ストレージ]** ブレードで、既定のストレージとして Azure Storage (WASB) と Data Lake Store のどちらを使用するかを指定します。 詳細については、下の表をご覧ください。

    ![Azure Portal で新しいクラスターを作成する](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.storage.png "Azure Portal で新しいクラスターを作成する")

    | Storage                                      | 説明 |
    |----------------------------------------------|-------------|
    | **既定のストレージとしての Azure Storage BLOB**   | <ul><li>**[プライマリ ストレージの種類]** で、**[Azure Storage]** を選択します。 その後、**[メソッドの選択]** で、Azure サブスクリプションの一部であるストレージ アカウントを指定する場合は **[個人用サブスクリプション]** を選択して、ストレージ アカウントを選択することができます。 それ以外の場合、**[アクセス キー]** をクリックして、Azure サブスクリプションの外部から選択するストレージ アカウントの情報を提供します。</li><li>**[既定のコンテナー]** では、ポータルによって示された既定のコンテナー名を使用するか、独自の名前を指定することができます。</li><li>既定のストレージとして WASB を使用している場合、**[追加のストレージ アカウント]** をクリックして、クラスターに関連付ける追加のストレージ アカウントを指定することができます (オプション)。 **[Azure Storage キー]** ブレードで **[ストレージ キーの追加]** をクリックして、自分の Azure サブスクリプションまたはその他のサブスクリプションから (ストレージ アカウント アクセス キーを指定することで) ストレージ アカウントを指定することができます。</li><li>既定のストレージとして WASB を使用している場合、**[Data Lake Store アクセス]** をクリックし、追加のストレージとして Azure Data Lake Store を指定できます (オプション)。 詳細については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。</li></ul> |
    | **既定のストレージとしての Azure Data Lake Store** | **[プライマリ ストレージの種類]** で、**[Data Lake Store]** を選択します。手順については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。 |
    | **外部メタストア**                      | 必要に応じて、クラスターに関連付けられた Hive と Oozie のメタデータを保存するための SQL データベースを指定します。 **[Hive 用の SQL データベースを選択する]** で、SQL データベースを選択し、データベースのユーザー名とパスワードを指定します。 以上の手順を Oozie メタデータについても行います。<br><br>メタストアに対して Azure SQL Database を使用する場合、いくつかの考慮事項があります。 <ul><li>メタストアに使用される Azure SQL Database は、Azure HDInsight などの他の Azure サービスに接続できる必要があります。 Azure SQL データベース ダッシュボードの右側に表示されているサーバー名をクリックします。 これは、SQL Database インスタンスが実行されているサーバーです。 サーバー ビューが表示されたら、**[構成]** をクリックします。**[Azure サービス]** に対して **[はい]** をクリックし、**[保存]** をクリックします。</li><li>メタストアを作成するときは、データベース名にダッシュやハイフンを使用しないでください。使用すると、クラスター作成プロセスが失敗することがあります。</li></ul>                                                                                                                                                                       |

    **[次へ]**をクリックします。 

5. 必要に応じて、**[アプリケーション]** をクリックして、HDInsight クラスターで動作するアプリケーションをインストールします。 マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。 詳細については、[HDInsight アプリケーションのインストール](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)に関する記事をご覧ください。


6. **[クラスター サイズ]** をクリックして、このクラスターのために作成されるノードに関する情報を表示します。 クラスターで必要な worker ノードの数を設定します。 クラスターの推定コストがブレード内に表示されます。
   
    ![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.nodes.png "クラスター ノード数の指定")
   
   > [!IMPORTANT]
   > クラスター作成または作成後の拡大で 32 以上の worker ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
   > 
   > ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
   > 
   > 
   
   **[次へ]** をクリックして、ノードの価格構成を保存します。

7. **[詳細設定]** をクリックし、**[スクリプト アクション]** などを使用してその他のオプションの設定を構成し、クラスターをカスタマイズしてカスタム コンポーネントをインストールしたり、**[Virtual Network]** に参加したりします。 詳細については、下の表をご覧ください。

    ![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.advanced.png "クラスター ノード数の指定")

    | オプション | 説明 |
    |--------|-------------|
    | **スクリプト アクション** | クラスターを作成するときに、カスタム スクリプトを使用してクラスターをカスタマイズする場合は、このオプションを使用します。 スクリプト アクションの詳細については、「 [Script Action を使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。 |
    | **Virtual Network** | クラスターを仮想ネットワークに配置する場合は、Azure Virtual Network とサブネットを選択します。 Virtual Network の具体的な構成要件など、Virtual Network で HDInsight を使用する方法については、「 [Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。 |

    **[次へ]**をクリックします。

8. **[概要]** ブレードで、以前に入力した情報を確認し、**[作成]** をクリックします。

    ![[ノード価格レベル] ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.summary.png "クラスター ノード数の指定")
    
    > [!NOTE]
    > クラスターが作成されるまで、通常は約 15 分かかります。 プロビジョニング プロセスを確認するには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。
    > 
    > 
12. 作成が完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。 クラスター ブレードには、次の情報が表示されます。
    
    ![クラスター ブレード](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.completed.png "クラスターのプロパティ")
    
    次の説明を参照して、このブレードの上部にあるアイコンについて理解してください。
    
    * **[概要]** タブには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関するすべての基本情報が表示されます。
    * **[ダッシュ ボード]** から、クラスターに関連付けられている Ambari ポータルに移動します。
    * **[Secure Shell]**: SSH を使用してクラスターにアクセスするために必要な情報です。
    * **[クラスターのスケール設定]** では、クラスターに関連付けられているワーカー ノードの数を増やすことができます。
    * **削除**: HDInsight クラスターを削除します。
    

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
* 「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ
HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="hadoop-clusters"></a>Hadoop クラスター
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター
* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター
* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark クラスター
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)


