---
title: "Azure HDInsight Linux クラスターに Presto をインストールする | Microsoft Docs"
description: "スクリプト アクションを使用して Linux ベースの HDInsight Hadoop クラスターに Presto と Airpal をインストールする方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: fb5e500b922522e4cdfa7dd07b5233688b916b3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスターに Presto をインストールして使用する

このトピックでは、スクリプト アクションを使用して HDInsight Hadoop クラスターに Presto をインストールする方法について説明します。 既存の Presto HDInsight クラスターに Airpal をインストールする方法についても説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する **HDInsight 3.5 Hadoop クラスター**が必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン](hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="what-is-presto"></a>Presto とは
[Presto](https://prestodb.io/overview.html) は、ビッグ データ用の高速な分散 SQL クエリ エンジンです。 Presto は、ペタバイト級のデータに対して対話型クエリを実行するのに適しています。 Presto のコンポーネントと、それらのコンポーネントがどのように連携するかについては、「[Presto concepts (Presto の概念)](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)」を参照してください。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
> 
> Presto といったカスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。
> 
> 


## <a name="install-presto-using-script-action"></a>スクリプト アクションを使用して Presto をインストールする

このセクションでは、Azure ポータルを使用して新しいクラスターを作成する際に、サンプル スクリプトを使用する方法について説明します。 

1. [Linux ベースの HDInsight クラスターのプロビジョニング](hdinsight-hadoop-create-linux-clusters-portal.md)に関するページに記載されている手順を使用して、クラスターのプロビジョニングを開始します。 このとき、**カスタム** クラスター作成フローを使用してクラスターを作成します。 作成したクラスターが次の要件を満たしていることを確認する必要があります。

    a. HDInsight Version 3.5 を使用する Hadoop クラスターである必要があります。

    b. データ ストアとして Azure Storage を使用する必要があります。 ストレージ オプションとして Azure Data Lake Store を使用するクラスターでの Presto の使用はまだサポートされていません。 

    ![カスタム オプションを使用した HDInsight クラスターの作成](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. **[詳細設定]** ブレードで **[スクリプト アクション]** を選択し、以下の情報を指定します。
   
   * **[名前]**: スクリプト アクションの表示名を入力します。
   * **[バッシュ スクリプト URI]**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **[ヘッド]**: このオプションをオンにします。
   * **[ワーカー]**: このオプションをオンにします
   * **[ZOOKEEPER]**: このチェック ボックスをオフにします。
   * **[パラメーター]**: このフィールドは空のままにします。


3. **[スクリプト アクション]** ブレードの下部で、**[選択]** をクリックして構成を保存します。 最後に、**[詳細設定]** ブレードの下部にある **[選択]** をクリックして構成情報を保存します。

4. 「 [Provision Linux-based HDInsight clusters (Linux ベースの HDInsight クラスターのプロビジョニング)](hdinsight-hadoop-create-linux-clusters-portal.md)」の説明に従って、クラスターのプロビジョニングを続行します。

    > [!NOTE]
    > スクリプト アクションは、Azure PowerShell、Azure CLI、HDInsight .NET SDK、または Azure Resource Manager のテンプレートを使用して適用することもできます。 既に実行しているクラスターにスクリプト アクションを適用することもできます。 詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>HDInsight での Presto の使用

前述の手順を使用して Presto をインストールした後、HDInsight クラスターで Presto を使用するために次の手順を実行します。

1. SSH を使用して HDInsight クラスターに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
     

2. 次のコマンドを使用して、Presto シェルを起動します。
   
        presto --schema default

3. サンプル テーブルの **hivesampletable** に対してクエリを実行します。このテーブルは、既定ですべての HDInsight クラスターで使用できます。
   
        select count (*) from hivesampletable;
   
    既定では、Presto 用に [Hive](https://prestodb.io/docs/current/connector/hive.html) コネクタと [TPCH](https://prestodb.io/docs/current/connector/tpch.html) コネクタが既に構成されています。 Hive コネクタは既定のインストール済みの Hive を使用するように構成されているため、Hive からのすべてのテーブルが Presto に自動的に表示されます。

    Presto の使用方法の詳細については、[Presto のドキュメント](https://prestodb.io/docs/current/index.html)を参照してください。

## <a name="use-airpal-with-presto"></a>Presto での Airpal の使用

[Airpal](https://github.com/airbnb/airpal#airpal) は、Presto のオープンソースの Web ベースのクエリ インターフェイスです。 Airpal の詳細については、[Airpal のドキュメント](https://github.com/airbnb/airpal#airpal)を参照してください。

このセクションでは、Presto が既にインストールされている HDInsight Hadoop クラスターの**エッジ ノードに Airpal をインストールする**手順について説明します。 これにより、Airpal の Web クエリ インターフェイスがインターネット経由で利用できるようになります。

1. SSH を使用して、Presto がインストールされている HDInsight クラスターのヘッド ノードに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 接続したら、次のコマンドを実行します。

        sudo slider registry  --name presto1 --getexp presto 
   
    出力は次のように表示されます。

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. 出力の **value** プロパティの値をメモに記録します。 この値は、Airpal をクラスターのエッジ ノードにインストールするときに必要になります。 上記の出力の場合、必要な値は **10.0.0.12:9090** です。

4. **[ここ](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)**に用意されているテンプレートを使用して、HDInsight クラスターのエッジ ノードを作成し、次のスクリーンショットに示すように値を指定します。

    ![HDInsight クラスター上の Presto 用の Airpal のインストール](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. **[購入]** をクリックします。

6. 変更がクラスター構成に適用されると、次の手順を使用して Airpal Web インターフェイスにアクセスできます。

    a. クラスター ブレードで、**[アプリケーション]** をクリックします。

    ![HDInsight クラスター上の Presto 用の Airpal の起動](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    b. **[インストール済みアプリ]** ブレードで、airpal に対して **[ポータル]** をクリックします。

    ![HDInsight クラスター上の Presto 用の Airpal の起動](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    c. 入力を求められたら、HDInsight Hadoop クラスターの作成中に指定した管理者資格情報を入力します。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>HDInsight クラスター上の Presto インストールのカスタマイズ

Presto を HDInsight Hadoop クラスターにインストールした後、メモリ設定の更新、コネクタの変更などの変更を行ってインストールをカスタマイズできます。そのためには、次の手順を実行します。

1. SSH を使用して、Presto がインストールされている HDInsight クラスターのヘッド ノードに接続します。
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` ファイルの構成を変更します。 Presto 構成の詳細については、[「Presto configuration for YARN-based clusters (YARN ベースのクラスターの Presto 構成)](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)」を参照してください。

3. Presto の現在実行中のインスタンスを停止し、中止します。

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. カスタマイズされた Presto の新しいインスタンスを開始します。

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. 新しいインスタンスの準備が完了するまで待ち、Presto コーディネーターのアドレスをメモに記録します。


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Presto を実行する HDInsight クラスターのベンチマーク データの生成

TPC-DS は、ビッグ データ システムを含む多くの意思決定支援システムのパフォーマンスを測定するための業界標準です。 HDInsight クラスターで Presto を使用してデータを生成し、独自の HDInsight ベンチマーク データと比較して評価することができます。 詳細については、[このページ](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)を参照してください。



## <a name="see-also"></a>関連項目
* [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)。 Hue は、Pig ジョブや Hive ジョブの作成、実行、保存や、HDInsight クラスターの既定のストレージの参照を容易にする Web UI です。

* [HDInsight クラスターでの Giraph のインストール](hdinsight-hadoop-giraph-install-linux.md) クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Giraph をインストールします。 Giraph は、Hadoop でグラフの処理を実行するために使用でき、Azure HDInsight で使用できます。

* [HDInsight クラスターに Solr をインストールする](hdinsight-hadoop-solr-install-linux.md)。 クラスターのカスタマイズを使用して、HDInsight Hadoop クラスターに Solr をインストールします。 Solr は、格納されたデータに対して強力な検索操作を実行することができます。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
