---
title: "Linux ベースの HDInsight で Hadoop を使用するためのヒント - Azure | Microsoft Docs"
description: "ここには、Azure クラウドで稼動する使い慣れた Linux 環境で、Linux ベースの HDInsight (Hadoop) クラスターを使用するための実装上のヒントを記載します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 792907646a602004904eef5c4000f464881c6c4b
ms.lasthandoff: 03/25/2017


---
# <a name="information-about-using-hdinsight-on-linux"></a>Linux での HDInsight の使用方法

Azure HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Hadoop を提供します。 使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。 ここでは、知っておく必要がある特定の違いについて説明します。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

このドキュメントの手順の多くでは次のユーティリティを使用するので、これらがシステムにインストールされている必要があります。

* [cURL](https://curl.haxx.se/) - Web ベースのサービスとの通信に使用します
* [jq](https://stedolan.github.io/jq/) - JSON ドキュメントの解析に使用します
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (プレビュー) - Azure サービスをリモート管理するために使用します

## <a name="users"></a>Users

HDInsight は、[ドメイン参加済み](hdinsight-domain-joined-introduction.md)でない限り**シングルユーザー** システムであるとみなされます。 このクラスターでは、管理者レベルのアクセス許可を持つ単一の SSH ユーザー アカウントを作成します。 追加の SSH アカウントを作成することもできますが、これらのアカウントもクラスターに対する管理者アクセスを持ちます。

ドメイン参加済みの HDInsight では、複数のユーザーがサポートされ、アクセス許可とロールの設定を詳細に行うことができます。 詳細については、[ドメイン参加済み HDInsight クラスターの管理](hdinsight-domain-joined-manage.md)に関するページを参照してください。

## <a name="domain-names"></a>ドメイン名

インターネットからクラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、**&lt;<clustername>.azurehdinsight.net** または (SSH のみ) **&lt;clustername-ssh>.azurehdinsight.net** です。

内部的には、クラスターの各ノードに、クラスターの構成時に割り当てられた名前が与えられます。 クラスター名を見つける方法については、Ambari Web UI の**ホスト**に関するページを参照してください。 次を使用して、Ambari REST API からホストの一覧を返すこともできます。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

**PASSWORD** は管理者アカウントのパスワードに、**CLUSTERNAME** はクラスターの名前に置き換えます。 このコマンドで返される JSON ドキュメントにクラスター内のホストの一覧が含まれます。jq により、クラスター内の各ホストの `host_name` 要素値が取り出されます。

特定のサービスのノード名を見つける必要がある場合、そのコンポーネントについて Ambari に問い合わせることができます。 たとえば、HDFS 名のノードのホストを見つけるには、次のコマンドを利用します。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

このコマンドで、サービスの説明が記載された JSON ドキュメントが返されます。jq により、ホストの `host_name` 値のみが引き出されます。

## <a name="remote-access-to-services"></a>サービスへのリモート アクセス

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

    クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にログインします。 クラスター管理者のユーザー名とパスワードを使用して認証する必要があります。

    認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

    > [!IMPORTANT]
    > クラスター用の Ambari にはインターネットから直接アクセスできますが、一部の機能では、クラスターが使用する内部ドメイン名によってノードにアクセスします。 内部ドメイン名はパブリックにアクセスできないため、インターネット経由で機能にアクセスしようとすると、サーバーが見つからないことを示すエラーが発生する可能性があります。
    >
    > Ambari Web UI の全機能を使用するには、SSH トンネルを使用して、クラスター ヘッド ノードに対する Web トラフィックがプロキシ経由になるようにします。 「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

    > [!NOTE]
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

    > [!NOTE]
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** -  ポート 22 または 23 上の &lt;clustername>-ssh.azurehdinsight.net。 ポート 22 はプライマリ ヘッドノードへの接続に、23 はセカンダリ ヘッドノードへの接続に使用されます。 ヘッド ノードの詳細については、「 [HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

    > [!NOTE]
    > クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。 接続されたら、ヘッドノードから SSH を使用して worker ノードにアクセスできます。

## <a name="file-locations"></a>ファイルの場所

Hadoop 関連ファイルは、 `/usr/hdp`のクラスター ノードにあります。 このディレクトリには、次のサブディレクトリが含まれます。

* **2.2.4.9-1**: このディレクトリは HDInsight が使用する Hortonworks Data Platform のバージョンから名前が付けられるため、クラスター上の番号はここに記載されたものと異なる場合があります。
* **current**: このディレクトリには、**2.2.4.9-1** ディレクトリ下のサブディレクトリへのリンクが含まれています。 このディレクトリは、(変わる可能性のある) バージョン番号をファイルにアクセスするたびに入力しなくて済むように存在します。

サンプル データ ファイルと JAR ファイルは、Hadoop 分散ファイル システムの `/example` と `/HdiSamples` にあります。

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS、Azure Storage、Data Lake Store

ほとんどの Hadoop ディストリビューションでは、HDFS はクラスター内のコンピューターのローカル ストレージによって支えられています。 ローカル ストレージの使用は効率的である一方、コンピューティング リソースが時間または分単位で課金されるクラウドベースのソリューションではコストが高くなります。

HDInsight では、既定のストアとして Azure Data Lake Store または Azure Storage の BLOB が使用されます。 これらのサービスには次のような利点があります。

* 低コストの長期ストレージ
* Websites、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。

> [!WARNING]
> __汎用__の Azure Storage アカウントがサポートされるのは HDInsight のみです。 現時点では、__Blob Storage__ タイプのアカウントはサポートされません。

Azure Storage アカウントは最大 4.75 TB を保持できますが、個々の BLOB (HDInsight から見たファイル) は最大 195 GB を保持できます。 Azure Data Lake Store は、膨大な数のファイルを保持するように動的に拡張可能であり、ペタバイト以上のファイルも保持できます。 詳細については、[BLOB の概要](https://docs.microsoft.com/rest/api/storageservices/fileservices/understanding-block-blobs--append-blobs--and-page-blobs)に関するページと「[Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)」を参照してください。

Azure Storage または Data Lake Store を使用している場合、HDInsight からデータにアクセスするときに特別な処理を行う必要はありません。 たとえば、次のコマンドは、`/example/data` フォルダーの保存場所 (Azure Storage または Data Lake Store) に関係なく、そのフォルダー内のファイルの一覧を表示します。

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI およびスキーム

いくつかのコマンドでは、ファイルにアクセスするときに、URI の一部としてスキームを指定する必要があります。 たとえば、Storm-HDFS コンポーネントでは、スキームを指定する必要があります。 既定ではない記憶域 (記憶域クラスターに "追加の" ストレージとして追加した記憶域) を使用する場合は、URI の一部として常にスキームを使用する必要があります。

__Azure Storage__ を使用する場合は、次のいずれかの URI スキームを使用します。

* `wasb:///`: 暗号化されていない通信を使用して既定のストレージにアクセスします。

* `wasbs:///`: 暗号化された通信を使用して既定のストレージにアクセスします。

* `wasbs://<container-name>@<account-name>.blob.core.windows.net/`: 既定以外のストレージ アカウントを使用して通信するときに使用します  (追加のストレージ アカウントがある場合や、パブリックにアクセス可能なストレージ アカウントにる格納されているデータにアクセスする場合など)。

__Data Lake Store__ を使用する場合は、次のいずれかの URI スキームを使用します。

* `adl:///`: クラスターの既定の Data Lake Store にアクセスします。

* `adl://<storage-name>.azuredatalakestore.net/`: 既定以外の Data Lake Store と通信するとき、または HDInsight クラスターのルート ディレクトリ以外のデータにアクセスするときに使用します。

> [!IMPORTANT]
> Data Lake Store を HDInsight の既定のストアとして使用する場合は、HDInsight のストレージのルートとして使用するためのパスをストア内に指定する必要があります。 既定のパスは `/clusters/<cluster-name>/` です。
>
> `/` または `adl:///` を使用してデータにアクセスすると、クラスターのルートに格納されているデータのみにアクセスできます (たとえば `/clusters/<cluster-name>/`)。 ストア内の任意の場所にあるデータにアクセスするには、`adl://<storage-name>.azuredatalakestore.net/` 形式を使用します。

### <a name="what-storage-is-the-cluster-using"></a>クラスターで使用しているストレージ

Ambari を使用して、クラスターの既定のストレージ構成を取得することができます。 次のコマンドを使用して、curl を使用する HDFS の構成情報を取得し、 [jq](https://stedolan.github.io/jq/)を使用してフィルター処理します。

```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> このコードは、サーバーに適用された最初の構成 (`service_config_version=1`) を返し、その中にこの情報が含まれています。 クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

このコマンドにより、次のような値が返されます。

* Azure Storage アカウントを使用している場合: `wasbs://<container-name>@<account-name>.blob.core.windows.net`

    アカウント名は Azure Storage アカウントの名前であり、コンテナー名はクラスター ストレージのルートである BLOB コンテナーです。

* Azure Data Lake Store を使用している場合: `adl://home` Data Lake Store の名前を取得するには、次の REST 呼び出しを使用します。

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    このコマンドからは、ホスト名として `<data-lake-store-account-name>.azuredatalakestore.net` が返されます。

    HDInsight のルートであるストア内のディレクトリを取得するには、次の REST 呼び出しを使用します。

    ```curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    このコマンドにより、`/clusters/<hdinsight-cluster-name>/` のようなパスが返されます。

次のように Azure Portal を使用して、ストレージ情報を検索することもできます。

1. [Azure Portal](https://portal.azure.com/) で HDInsight クラスターを選択します。

2. **[プロパティ]** セクションの **[ストレージ アカウント]** を選択します。 クラスターのストレージ情報が表示されます。

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>HDInsight の外部からファイルにアクセスする方法

HDInsight クラスターの外部からデータにアクセスする方法は複数あります。 データを操作するために使用できるユーティリティと SDK へのいくつかのリンクを次に示します。

__Azure Storage__ を使用している場合は、次のリンクを参照して、データにアクセスする方法を確認してください。

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): Azure を使用するためのコマンド ライン インターフェイス コマンド。 インストール後、ストレージの使用方法については `az storage`、BLOB 特有のコマンドについては `az storage blob` をご覧ください。
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure Storage で BLOB を使用するための Python スクリプト。
* さまざまな SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

__Azure Data Lake Store__ を使用している場合は、次のリンクを参照して、データにアクセスする方法を確認してください。

* [Web ブラウザー](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>クラスターのスケーリング

クラスターのスケール設定機能を使用すると、クラスターによって使用されるデータ ノードの数を、クラスターを削除して再作成することなく、変更できます。 クラスターで他のジョブまたはプロセスを実行している間にスケーリング操作を実行できます。

別のクラスターの種類は、次のようにスケーリングすることによって影響を受けます。

* **Hadoop**: クラスター内のノードの数をスケール ダウンすると、クラスター内のサービスの一部が再起動されます。 これにより、スケーリング操作の完了時に、実行中および保留中のジョブが失敗する可能性があります。 ただし、操作が完了した時点で、ジョブを再送信できます。
* **HBase**: リージョンのサーバーは、スケーリング操作の完了から数分以内に自動的に調整されます。 リージョンのサーバーを手動で調整するには、次の手順を使用します。

    1. SSH を使用して HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

    2. HBase シェルを開始するには、次のコマンドを使用します。

            hbase shell
    
    3. HBase シェルが読み込まれたら、次のコマンドを使用してリージョンのサーバーを手動で調整します。

            balancer

* **Storm**: スケーリング操作が実行された後に、実行中の Storm トポロジをすべて再調整する必要があります。 これにより、トポロジはクラスター内のノードの新しい数に基づいて並列処理の設定を再調整できます。 実行中のトポロジを再調整するには、次のオプションのいずれかを使用します。

    * **SSH**: サーバーに接続し、次のコマンドを使用してトポロジを再調整します。

            storm rebalance TOPOLOGYNAME

        また、トポロジによって最初に提供された並列処理のヒントを上書きするパラメーターも指定できます。 たとえば、`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` は、トポロジを再構成して、5 個の worker プロセス、blue-spout コンポーネント用の 3 個の実行プログラム、yellow-bolt コンポーネント用の 10 個の実行プログラムにします。

    * **Storm UI**: 次の手順により、Storm UI を使用してトポロジを再調整します。

        1. Web ブラウザーで **https://CLUSTERNAME.azurehdinsight.net/stormui** にアクセスします。CLUSTERNAME は実際の Storm クラスターの名前です。 メッセージが表示されたら、HDInsight クラスター管理者 (admin) の名前と、クラスターの作成時に指定したパスワードを入力します。
        2. 再調整するトポロジを選択し、 **[Rebalance]** \(再調整) ボタンをクリックします。 再調整の操作が実行されるまでの待ち時間を入力します。

HDInsight クラスターのスケーリングに関する具体的な情報については、以下を参照してください。

* [Azure Portal を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hue (またはその他の Hadoop コンポーネント) のインストール方法

HDInsight は管理されたサービスです。 Azure によってクラスターに関する問題が検出された場合、障害の発生したノードが削除され、新たに作成されたノードで置き換えられる可能性があります。 クラスターに何かを手動でインストールした場合、この操作の実行後は維持されません。 代わりに、[HDInsight スクリプト アクション](hdinsight-hadoop-customize-cluster.md)を使用してください。 次の変更は、スクリプト アクションで行うことができます。

* サービスや Web サイト (Spark、Hue など) をインストールして構成する。
* クラスターの複数のノードで構成変更を必要とするコンポーネントをインストールして構成する。 たとえば、必要な環境変数、ログ ディレクトリの作成、構成ファイルの作成。

スクリプト アクションとは、クラスターのプロビジョニング中に実行される Bash スクリプトで、クラスター上に追加のコンポーネントをインストールし、構成するために使用できます。 次のコンポーネントをインストールするスクリプトの例が用意されています。

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

独自のスクリプト アクションを開発する方法の詳細については、「 [HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

### <a name="jar-files"></a>Jar ファイル

一部の Hadoop 技術は自己完結型の jar ファイルで提供されます。このファイルには MapReduce ジョブの一環として、あるいは Pig または Hive 内から使用される関数が含まれています。 ファイルはスクリプト アクションでインストールできますが、多くの場合、セットアップを必要とせず、プロビジョニング後にクラスターにアップロードし、直接使用できます。 クラスターの再イメージ化の後もコンポーネントが残っていることを確認するには、クラスターの既定のストレージ (WASB または ADL) に jar ファイルを保存します。

たとえば、最新版の [DataFu](http://datafu.incubator.apache.org/)を使用する場合、プロジェクトを含む jar をダウンロードし、それを HDInsight クラスターにアップロードできます。 その後、Pig や Hive からそれを使用する方法については、DataFu のドキュメントに従います。

> [!IMPORTANT]
> スタンドアロン jar ファイルである一部のコンポーネントは HDInsight で提供され、パスにはありません。 特定のコンポーネントを探している場合、次を利用し、クラスターでそれを検索できます。
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> このコマンドからは、一致する jar ファイルのパスが返されます。

クラスターに付属するバージョンとは異なるバージョンが必要になった場合は、新しいバージョンのコンポーネントをアップロードして、実際のジョブから使用してください。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。

## <a name="next-steps"></a>次のステップ

* [Windows ベースの HDInsight から Linux ベースへの移行](hdinsight-migrate-from-windows-to-linux.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

