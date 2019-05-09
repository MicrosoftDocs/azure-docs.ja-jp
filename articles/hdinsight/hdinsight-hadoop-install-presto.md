---
title: Azure HDInsight Linux クラスターに Presto をインストールする
description: スクリプト アクションを使用して Linux ベースの HDInsight Hadoop クラスターに Presto と Airpal をインストールする方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 435c041bb5fb0a398f92914f943166108cc20080
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258345"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Hadoop ベースの HDInsight クラスターに Presto をインストールして使用する

この記事では、スクリプト アクションを使用して Hadoop ベースの Azure HDInsight クラスターに Presto をインストールする方法について説明します。 既存の Presto HDInsight クラスターに Airpal をインストールする方法についても説明します。

HDInsight では、Apache Hadoop クラスター向けの Starburst Presto アプリケーションも提供しています。 詳細については、「[Azure HDInsight にサードパーティ製 Apache Hadoop アプリケーションをインストールする](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications)」を参照してください。

> [!IMPORTANT]  
> この記事の手順では、Linux を使用する HDInsight 3.5 Hadoop クラスターが必要です。 Linux は、バージョン 3.4 以降の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン](hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="what-is-presto"></a>Presto とは
[Presto](https://prestodb.io/overview.html) は、ビッグ データ用の高速な分散 SQL クエリ エンジンです。 Presto は、ペタバイト級のデータに対して対話型クエリを実行するのに適しています。 Presto のコンポーネントと、それらのコンポーネントがどのように連携するかについては、「[Presto concepts (Presto の概念)](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)」を参照してください。

> [!WARNING]  
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
> 
> Presto のようなカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 このサポートで、問題が解決する可能性があります。 または、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 役に立つコミュニティ サイトが数多くあります。 たとえば、[HDInsight の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や、[Stack Overflow](https://stackoverflow.com) などです。 
>
> Apache プロジェクトにも、[Apache の Web サイト](https://apache.org)にプロジェクトのサイトがあります。 たとえば、[Hadoop](https://hadoop.apache.org/) などです。


## <a name="install-presto-by-using-script-actions"></a>スクリプト アクションを使用して Presto をインストールする

このセクションでは、Azure portal を使用して新しいクラスターを作成するときに、サンプル スクリプトを使用する方法について説明します。 

1. 「[Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する](hdinsight-hadoop-create-linux-clusters-portal.md)」の手順を実行して、クラスターのプロビジョニングを始めます。 このとき、**カスタム** クラスター作成フローを使用してクラスターを作成します。 そのクラスターは次の要件を満たしている必要があります。

   * HDInsight version 3.6 を使用する Hadoop クラスターである必要があります。

   * データ ストアとして Azure Storage を使用する必要があります。 ストレージ オプションとして Azure Data Lake Storage を使用するクラスターで Presto を使用するという選択肢はまだありません。

     ![HDInsight、カスタム (サイズ、設定、アプリ)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. **[詳細設定]** 領域で、**[スクリプト アクション]** を選択します。 次の情報を指定します。 スクリプトの種類として **[Presto のインストール]** オプションを選択することもできます。
   
   * **NAME**。 スクリプト アクションのフレンドリ名を入力します。
   * **[バッシュ スクリプト URI]**。 `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **[ヘッド]**。 このオプションを選択します。
   * **[ワーカー]**。 このオプションを選択します。
   * **[ZooKeeper]**。 このチェック ボックスはオフのままにします。
   * **[パラメーター]**。 このフィールドは空のままにします。


3. **[スクリプト アクション]** 領域の下部で、**[選択]** を選択して構成を保存します。 最後に、**[詳細設定]** 領域の下部にある **[選択]** ボタンを選択して構成情報を保存します。

4. 「[Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する](hdinsight-hadoop-create-linux-clusters-portal.md)」で説明されているように、クラスターのプロビジョニングを続けます。

    > [!NOTE]  
    > スクリプト アクションは、Azure PowerShell、Azure クラシック CLI、HDInsight .NET SDK、または Azure Resource Manager テンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳しくは、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>HDInsight での Presto の使用

HDInsight クラスターで Presto を使用するには、次の手順のようにします。

1. SSH を使って HDInsight クラスターに接続します。
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    詳しくは、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
     

2. 次のコマンドを実行して、Presto シェルを起動します。
   
    `presto --schema default`

3. サンプル テーブルの **hivesampletable** に対してクエリを実行します。このテーブルは、既定ですべての HDInsight クラスターで使用できます。
   
    `select count (*) from hivesampletable;`
   
    既定では、Presto 用に [Apache Hive](https://prestodb.io/docs/current/connector/hive.html) コネクタと [TPCH](https://prestodb.io/docs/current/connector/tpch.html) コネクタが既に構成されています。 Hive コネクタは、Hive の既定のインストールを使用するように構成されます。 そのため、Hive のすべてのテーブルが、Presto に自動的に表示されます。

    詳細については、[Presto のドキュメンテーション](https://prestodb.io/docs/current/index.html)を参照してください。

## <a name="use-airpal-with-presto"></a>Presto での Airpal の使用

[Airpal](https://github.com/airbnb/airpal#airpal) は、Presto のオープンソースの Web ベースのクエリ インターフェイスです。 Airpal の詳細については、[Airpal のドキュメント](https://github.com/airbnb/airpal#airpal)を参照してください。

次の手順のようにして、エッジ ノードに Airpal をインストールします。

1. SSH を使用して、Presto がインストールされている HDInsight クラスターのヘッド ノードに接続します。
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    詳しくは、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

2. 接続した後、次のコマンドを実行します。

    `sudo slider registry  --name presto1 --getexp presto` 
   
    次の JSON のような出力が表示されます。

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 出力の **value** プロパティの値をメモに記録します。 この値は、Airpal をクラスターのエッジ ノードにインストールするときに必要になります。 前記の出力で必要な値は **10.0.0.12:9090** です。

4. [このテンプレート](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)を使用して、HDInsight クラスターのエッジ ノードを作成します。 次のスクリーンショットで示されている値を指定します。

    ![カスタム デプロイ](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. **[購入]** を選択します。

6. クラスターの構成に変更を適用した後、[Azure portal](https://portal.azure.com) から次の手順を使用して Airpal の Web インターフェイスにアクセスします。

    1. 左側のメニューから、**[すべてのサービス]** を選択します。

    1. **[ANALYTICS]** で **[HDInsight クラスター]** を選択します。

    1. 一覧からクラスターを選択します。これにより、既定のビューが開きます。

    1. 既定のビューの **[設定]** で、**[アプリケーション]** を選択します。

        ![HDInsight、アプリケーション](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. **[インストール済みアプリ]** ページで **airpal** のテーブル エントリに移動します。 **[ポータル]** を選択します。

        ![インストール済みアプリ](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. 入力を求められたら、Hadoop ベースの HDInsight クラスターを作成するときに指定した管理者資格情報を入力します。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>HDInsight クラスター上の Presto インストールのカスタマイズ

インストールをカスタマイズするには、次の手順のようにします。

1. SSH を使用して、Presto がインストールされている HDInsight クラスターのヘッド ノードに接続します。
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    詳しくは、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

2. `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` ファイルの構成を変更します。 Presto の構成について詳しくは、「[Presto configuration options for YARN-based clusters (YARN ベースのクラスターの Presto 構成オプション)](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)」をご覧ください。

3. Presto の現在実行中のインスタンスを停止し、中止します。

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. カスタマイズされた Presto の新しいインスタンスを開始します。

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. 新しいインスタンスの準備ができるまで待ちます。 Presto コーディネーターのアドレスを書き留めます。

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Presto を実行する HDInsight クラスターのベンチマーク データの生成

TPC-DS は、ビッグ データ システムを含む多くの意思決定支援システムのパフォーマンスを測定するための業界標準です。 Presto を使用してデータを生成し、独自の HDInsight ベンチマーク データと比較して評価することができます。 詳しくは、「[tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)」をご覧ください。



## <a name="next-steps"></a>次の手順
* [HDInsight Hadoop クラスターに Hue をインストールして使用します](hdinsight-hadoop-hue-linux.md)。 色合いは、Apache Pig ジョブと Hive ジョブを作成、実行、保存しやすくする Web の UI です。

* [HDInsight Hadoop クラスターに Apache Giraph をインストールし、Giraph を使用して大規模なグラフを処理します](hdinsight-hadoop-giraph-install-linux.md)。 クラスターのカスタマイズを使用して、Hadoop ベースの HDInsight クラスターに Giraph をインストールします。 Giraph では、Hadoop を使用してグラフの処理を実行できます。 Azure HDInsight でも使用できます。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
