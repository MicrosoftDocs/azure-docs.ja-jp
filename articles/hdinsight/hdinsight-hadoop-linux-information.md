---
title: Linux ベースの HDInsight で Hadoop を使用するためのヒント - Azure
description: ここには、Azure クラウドで稼動する使い慣れた Linux 環境で、Linux ベースの HDInsight (Hadoop) クラスターを使用するための実装上のヒントを記載します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1904ab07a188e4e877a4fb2f2b7682d923c08fb2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441989"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Linux での HDInsight の使用方法

Azure HDInsight クラスターは、Azure クラウドで実行される使い慣れた Linux 環境での Apache Hadoop を提供します。 使い方は、ほとんどの点で、Linux 環境の他の Hadoop とまったく同じです。 ここでは、知っておく必要がある特定の違いについて説明します。

## <a name="prerequisites"></a>前提条件

このドキュメントの手順の多くでは次のユーティリティを使用するので、これらがシステムにインストールされている必要があります。

* [cURL](https://curl.haxx.se/) - Web ベースのサービスとの通信に使用します。
* **jq**。コマンド ライン JSON プロセッサです。  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) をご覧ください。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - Azure サービスをリモート管理するために使用します。
* **SSH クライアント**。 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="users"></a>ユーザー

HDInsight は、[ドメイン参加済み](./domain-joined/hdinsight-security-overview.md)でない限り**シングルユーザー** システムであるとみなされます。 このクラスターでは、管理者レベルのアクセス許可を持つ単一の SSH ユーザー アカウントを作成します。 追加の SSH アカウントを作成することもできますが、これらのアカウントもクラスターに対する管理者アクセスを持ちます。

ドメイン参加済みの HDInsight では、複数のユーザーがサポートされ、アクセス許可とロールの設定を詳細に行うことができます。 詳細については、[ドメイン参加済み HDInsight クラスターの管理](./domain-joined/apache-domain-joined-manage.md)に関するページを参照してください。

## <a name="domain-names"></a>ドメイン名

インターネットからクラスターへの接続時に使用する完全修飾ドメイン名 (FQDN) は、`CLUSTERNAME.azurehdinsight.net` または `CLUSTERNAME-ssh.azurehdinsight.net` (SSH のみ) です。

内部的には、クラスターの各ノードに、クラスターの構成時に割り当てられた名前が与えられます。 クラスター名を見つける方法については、Ambari Web UI の**ホスト**に関するページを参照してください。 次を使用して、Ambari REST API からホストの一覧を返すこともできます。

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

`CLUSTERNAME` をクラスターの名前に置き換えます。 メッセージが表示されたら、管理者アカウントのパスワードを入力します。 このコマンドで返される JSON ドキュメントにクラスター内のホストの一覧が含まれます。 [jq](https://stedolan.github.io/jq/) により、各ホストの `host_name` 要素値が取り出されます。

特定のサービスのノード名を見つける必要がある場合、そのコンポーネントについて Ambari に問い合わせることができます。 たとえば、HDFS 名のノードのホストを見つけるには、次のコマンドを利用します。

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

このコマンドで、サービスの説明が記載された JSON ドキュメントが返されます。[jq](https://stedolan.github.io/jq/) により、ホストの `host_name` 値のみが引き出されます。

## <a name="remote-access-to-services"></a>サービスへのリモート アクセス

* **Ambari (web)**  - https://CLUSTERNAME.azurehdinsight.net

    クラスター管理者のユーザー名とパスワードを使用して認証した後、Ambari にサインインします。

    認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

    > [!IMPORTANT]  
    > Ambari から使用できる Web UI の中には、内部ドメイン名を使ってノードにアクセスするものがあります。 内部ドメイン名には、インターネット経由でパブリックにアクセスできません。 インターネット経由で機能にアクセスしようとすると、"サーバーが見つからない" ことを示すエラーが発生する可能性があります。
    >
    > Ambari Web UI の全機能を使用するには、SSH トンネルを使用して、クラスター ヘッド ノードに対する Web トラフィックがプロキシ経由になるようにします。 「[SSH トンネリングを使用して Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **WebHCat (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > クラスター管理者のユーザー名とパスワードを使用して認証します。
    >
    > 認証はプレーンテキストです。接続をセキュリティで確実に保護するために、常に HTTPS を使用してください。

* **SSH** - ポート 22 または 23 上の CLUSTERNAME-ssh.azurehdinsight.net。 ポート 22 はプライマリ ヘッドノードへの接続に、23 はセカンダリ ヘッドノードへの接続に使用されます。 ヘッド ノードの詳細については、「[HDInsight における Apache Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

    > [!NOTE]  
    > クラスター ヘッド ノードにアクセスするには、クライアント コンピューターから SSH を使用する必要があります。 接続されたら、ヘッドノードから SSH を使用して worker ノードにアクセスできます。

詳しくは、「[HDInsight 上の Apache Hadoop サービスで使用されるポート](hdinsight-hadoop-port-settings-for-services.md)」をご覧ください。

## <a name="file-locations"></a>ファイルの場所

Hadoop 関連ファイルは、 `/usr/hdp`のクラスター ノードにあります。 このディレクトリには、次のサブディレクトリが含まれます。

* **2.6.5.3006-29**:このディレクトリは、HDInsight が使用する Hortonworks Data Platform のバージョンから名前が付けられます。 クラスター上の番号は、ここに記載されているものと異なる場合があります。
* **current**:このディレクトリには、**2.6.5.3006-29** ディレクトリ下のサブディレクトリへのリンクが含まれています。 バージョン番号を記憶する必要がないのは、このディレクトリが存在するためです。

サンプル データ ファイルと JAR ファイルは、Hadoop 分散ファイル システムの `/example` と `/HdiSamples` にあります。

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS、Azure Storage、および Data Lake Storage

ほとんどの Hadoop ディストリビューションでは、データは HDFS に格納されています。この HDFS は、クラスター内のコンピューターのローカル ストレージによって支えられています。 ローカル ストレージの使用コストは、コンピューティング リソースが時間または分単位で課金されるクラウドベースのソリューションでは高くなる場合があります。

HDInsight を使用する場合、データ ファイルは、Azure Blob Storage、および必要に応じて Azure Data Lake Storage を使用して、回復性があるスケーラブルな方法でクラウドに格納されます。 これらのサービスには次のような利点があります。

* 低コストの長期ストレージ。
* Websites、ファイル アップロード/ダウンロード ユーティリティ、さまざまな言語の SDK、Web ブラウザーなどの外部サービスからアクセスできます。
* 大きなファイル容量とスケーラブルな大容量ストレージ。

詳細については、[BLOB の概要](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)に関するページと [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) に関するページを参照してください。

Azure Storage または Data Lake Storage を使用している場合、HDInsight からデータにアクセスするときに特別な処理を行う必要はありません。 たとえば、次のコマンドは、`/example/data` フォルダーの保存場所 (Azure Storage または Data Lake Storage) に関係なく、そのフォルダー内のファイルの一覧を表示します。

    hdfs dfs -ls /example/data

HDInsight では、データ ストレージ リソース (Azure Blob Storage と Azure Data Lake Storage) は、コンピューティング リソースから切り離されています。 したがって、必要なコンピューティング処理を実行するために HDInsight クラスターを作成し、作業完了時にそのクラスターを削除できます。一方で、お使いのデータ ファイルは、必要なだけクラウド ストレージに安全に保持し続けることができます。


### <a name="URI-and-scheme"></a>URI およびスキーム

いくつかのコマンドでは、ファイルにアクセスするときに、URI の一部としてスキームを指定する必要があります。 たとえば、Storm-HDFS コンポーネントでは、スキームを指定する必要があります。 既定ではない記憶域 (記憶域クラスターに "追加の" ストレージとして追加した記憶域) を使用する場合は、URI の一部として常にスキームを使用する必要があります。

__Azure Storage__ を使用する場合は、次のいずれかの URI スキームを使用します。

* `wasb:///`:暗号化されていない通信を使用して既定のストレージにアクセスします。

* `wasbs:///`:暗号化された通信を使用して既定のストレージにアクセスします。  wasbs スキームは、HDInsight バージョン 3.6 以降でのみサポートされます。

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`:既定以外のストレージ アカウントを使用して通信するときに使用します。 (追加のストレージ アカウントがある場合や、パブリックにアクセス可能なストレージ アカウントにる格納されているデータにアクセスする場合など)。

__Azure Data Lake Storage Gen2__ を使用する場合は、次のいずれかの URI スキームを使用します。

* `abfs:///`:暗号化されていない通信を使用して既定のストレージにアクセスします。

* `abfss:///`:暗号化された通信を使用して既定のストレージにアクセスします。  abfss スキームは、HDInsight バージョン 3.6 以降でのみサポートされます。

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`:既定以外のストレージ アカウントを使用して通信するときに使用します。 (追加のストレージ アカウントがある場合や、パブリックにアクセス可能なストレージ アカウントにる格納されているデータにアクセスする場合など)。

__Azure Data Lake Storage Gen1__ を使用する場合は、次のいずれかの URI スキームを使用します。

* `adl:///`:クラスターの既定の Data Lake Storage にアクセスします。

* `adl://<storage-name>.azuredatalakestore.net/`:既定以外の Data Lake Storage と通信するときに使用します。 HDInsight クラスターのルート ディレクトリ以外のデータにアクセスするときにも使用できます。

> [!IMPORTANT]  
> Data Lake Storage を HDInsight の既定のストアとして使用する場合は、HDInsight のストレージのルートとして使用するストア内のパスを指定する必要があります。 既定のパスは `/clusters/<cluster-name>/` です。
>
> `/` または `adl:///` を使用してデータにアクセスすると、クラスターのルートに格納されているデータのみにアクセスできます (たとえば `/clusters/<cluster-name>/`)。 ストア内の任意の場所にあるデータにアクセスするには、`adl://<storage-name>.azuredatalakestore.net/` 形式を使用します。

### <a name="what-storage-is-the-cluster-using"></a>クラスターで使用しているストレージ

Ambari を使用して、クラスターの既定のストレージ構成を取得することができます。 次のコマンドを使用して、curl を使用する HDFS の構成情報を取得し、 [jq](https://stedolan.github.io/jq/)を使用してフィルター処理します。

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> このコマンドは、サーバーに適用された最初の構成 (`service_config_version=1`) を返し、その中にこの情報が含まれています。 最新の構成を確認するには、構成バージョンの一覧を表示しなければならない場合があります。

このコマンドにより、次の URI のような値が返されます。

* Azure Storage アカウントを使用している場合: `wasb://<container-name>@<account-name>.blob.core.windows.net`

    アカウント名は Azure ストレージ アカウントの名前です。 コンテナー名はクラスター ストレージのルートである BLOB コンテナーです。

* Azure Data Lake Storage を使用している場合は `adl://home`。 Data Lake Storage の名前を取得するには、次の REST 呼び出しを使用します。

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    このコマンドからは、ホスト名として `<data-lake-store-account-name>.azuredatalakestore.net` が返されます。

    HDInsight のルートであるストア内のディレクトリを取得するには、次の REST 呼び出しを使用します。

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    このコマンドにより、`/clusters/<hdinsight-cluster-name>/` のようなパスが返されます。

次のように Azure Portal を使用して、ストレージ情報を検索することもできます。

1. [Azure ポータル](https://portal.azure.com/)で HDInsight クラスターを選択します。

2. **[プロパティ]** セクションの **[ストレージ アカウント]** を選択します。 クラスターのストレージ情報が表示されます。

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>HDInsight の外部からファイルにアクセスする方法

HDInsight クラスターの外部からデータにアクセスする方法は複数あります。 データを操作するために使用できるユーティリティと SDK へのいくつかのリンクを次に示します。

__Azure Storage__ を使用している場合は、次のリンクを参照して、データにアクセスする方法を確認してください。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2):Azure を操作するためのコマンド ライン インターフェイス コマンド。 インストール後、ストレージの使用方法については `az storage`、BLOB 特有のコマンドについては `az storage blob` をご覧ください。
* [blobxfer.py](https://github.com/Azure/blobxfer):Azure Storage で BLOB を使用するための Python スクリプト。
* さまざまな SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

__Azure Data Lake Storage__ を使用している場合は、次のリンクを参照して、データにアクセスする方法を確認してください。

* [Web ブラウザー](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>クラスターのスケーリング

クラスターのスケール設定機能を使用すると、クラスターによって使用されるデータ ノードの数を動的に変更できます。 クラスターで他のジョブまたはプロセスを実行している間にスケーリング操作を実行できます。  「[Scale HDInsight clusters](./hdinsight-scaling-best-practices.md)」 (HDInsight クラスターをスケールする) も参照してください

別のクラスターの種類は、次のようにスケーリングすることによって影響を受けます。

* **Hadoop**:クラスター内のノードの数をスケール ダウンすると、クラスター内のサービスの一部が再起動されます。 スケーリング操作の完了時に、実行中および保留中のジョブが失敗する可能性があります。 ただし、操作が完了した時点で、ジョブを再送信できます。
* **HBase**:リージョンのサーバーは、スケーリング操作の完了から数分以内に自動的に調整されます。 リージョンのサーバーを手動で調整するには、次の手順を使用します。

    1. SSH を使用して HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

    2. HBase シェルを開始するには、次のコマンドを使用します。

            hbase shell

    3. HBase シェルが読み込まれたら、次のコマンドを使用してリージョンのサーバーを手動で調整します。

            balancer

* **Storm**:スケーリング操作が実行された後に、実行中の Storm トポロジをすべて再調整する必要があります。 これにより、トポロジがクラスター内のノードの新しい数に基づいて並列処理の設定を再調整できます。 実行中のトポロジを再調整するには、次のオプションのいずれかを使用します。

    * **SSH**:サーバーに接続し、次のコマンドを使用してトポロジを再調整します。

            storm rebalance TOPOLOGYNAME

        また、トポロジによって最初に提供された並列処理のヒントをオーバーライドするパラメーターも指定できます。 たとえば、`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` は、トポロジを再構成して、5 個の worker プロセス、blue-spout コンポーネント用の 3 個の実行プログラム、yellow-bolt コンポーネント用の 10 個の実行プログラムにします。

    * **Storm UI**:次の手順により、Storm UI を使用してトポロジを再調整します。

        1. Web ブラウザーで `https://CLUSTERNAME.azurehdinsight.net/stormui` を開きます。ここで、`CLUSTERNAME` は実行中の Storm クラスターの名前です。 メッセージが表示されたら、HDInsight クラスター管理者 (admin) の名前と、クラスターの作成時に指定したパスワードを入力します。
        2. 再調整するトポロジを選択し、 **[Rebalance]** \(再調整) ボタンをクリックします。 再調整の操作が実行されるまでの待ち時間を入力します。

* **Kafka**:スケーリング操作の後で、パーティションのレプリカを再調整する必要があります。 詳しくは、「[HDInsight 上の Apache Kafka によるデータの高可用性](./kafka/apache-kafka-high-availability.md)」をご覧ください。

HDInsight クラスターのスケーリングに関する具体的な情報については、以下を参照してください。

* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Azure CLI を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hue (またはその他の Hadoop コンポーネント) のインストール方法

HDInsight は管理されたサービスです。 Azure によってクラスターに関する問題が検出された場合、障害の発生したノードが削除され、新たに作成されたノードで置き換えられる可能性があります。 クラスターに何かを手動でインストールした場合、この操作の実行後は維持されません。 代わりに、[HDInsight スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)を使用してください。 次の変更は、スクリプト アクションで行うことができます。

* サービスや Web サイトをインストールして構成する。
* クラスターの複数のノードで構成変更を必要とするコンポーネントをインストールして構成する。

スクリプト アクションとは、Bash スクリプトです。 このスクリプトは、クラスターの作成中に実行されるほか、追加コンポーネントのインストールと構成に使用されます。 次のコンポーネントをインストールするスクリプトの例が用意されています。

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

独自のスクリプト アクションを開発する方法の詳細については、「 [HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」を参照してください。

### <a name="jar-files"></a>Jar ファイル

一部の Hadoop 技術は自己完結型の jar ファイルで提供されます。このファイルには MapReduce ジョブの一環として、あるいは Pig または Hive 内から使用される関数が含まれています。 これらは多くの場合、セットアップを必要とせず、作成後にクラスターにアップロードして直接使用することができます。 クラスターの再イメージ化の後もコンポーネントが残っていることを確認するには、クラスターの既定のストレージ (WASB または ADL) に jar ファイルを保存します。

たとえば、最新版の [Apache DataFu](https://datafu.incubator.apache.org/) を使用する場合、プロジェクトを含む jar をダウンロードし、それを HDInsight クラスターにアップロードできます。 その後、Pig や Hive からそれを使用する方法については、DataFu のドキュメントに従います。

> [!IMPORTANT]  
> スタンドアロン jar ファイルである一部のコンポーネントは HDInsight で提供され、パスにはありません。 特定のコンポーネントを探している場合、次を利用し、クラスターでそれを検索できます。
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> このコマンドからは、一致する jar ファイルのパスが返されます。

異なるバージョンのコンポーネントが必要になった場合は、必要なバージョンをアップロードして、ジョブで使用してください。

> [!IMPORTANT]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [https://stackoverflow.com](https://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[https://apache.org](https://apache.org) に[Hadoop](https://hadoop.apache.org/)、[Spark](https://spark.apache.org/) などのプロジェクト サイトもあります。

## <a name="next-steps"></a>次の手順

* [Apache Ambari REST API を使用した HDInsight クラスターの管理](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hadoop/hdinsight-use-mapreduce.md)
