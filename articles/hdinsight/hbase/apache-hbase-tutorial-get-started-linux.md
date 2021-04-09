---
title: チュートリアル - Azure HDInsight で Apache HBase を使用する
description: この Apache HBase のチュートリアルに従って、HDInsight で Hadoop を使い始めることができます。 HBase シェルからテーブルを作成し、Hive を使用したクエリを実行します。
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 50216b83475e92cbabb5ed78c3836264ceff4600
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866609"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight で Apache HBase を使用する

このチュートリアルでは、Azure HDInsight で Apache HBase クラスターを作成する方法、HBase テーブルを作成する方法、Apache Hive を使用してテーブルのクエリを実行する方法について説明します。  HBase に関する一般的な情報については、[HDInsight HBase の概要](./apache-hbase-overview.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Apache HBase クラスターを作成する
> * HBase テーブルを作成してデータを挿入する
> * Apache Hive を使用して Apache HBase を照会する
> * Curl を使用して HBase REST API を使用する
> * クラスターの状態の確認

## <a name="prerequisites"></a>前提条件

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* Bash。 この記事の例では、curl コマンドのために Windows 10 上で Bash シェルを使用します。 インストール手順については、「[Windows Subsystem for Linux Installation Guide for Windows 10 (Windows 10 用 Windows Subsystem for Linux インストール ガイド)](/windows/wsl/install-win10)」をご覧ください。  他の [Unix シェル](https://www.gnu.org/software/bash/)も動作します。  これらの curl の例は、少し変更すれば、Windows コマンド プロンプトで動作できます。  または、Windows PowerShell コマンドレット [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) を使用できます。

## <a name="create-apache-hbase-cluster"></a>Apache HBase クラスターを作成する

以下の手順では、Azure Resource Manager テンプレートを使用して HBase クラスターを作成します。 また、依存する既定の Azure Storage アカウントもこのテンプレートで作成されます。 この手順で使用するパラメーターとその他のクラスター作成方法について理解するには、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

1. 次の画像を選択して Azure Portal でテンプレートを開きます。 テンプレートは [Azure クイック スタート テンプレート集](https://azure.microsoft.com/resources/templates/)にあります。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. **[カスタム デプロイ]** ダイアログで以下の値を入力します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|クラスターの作成に使用する Azure サブスクリプションを選択します。|
    |Resource group|Azure リソース管理グループを作成するか、または既存のグループを使用します。|
    |場所|リソース グループの [場所] を指定します。 |
    |ClusterName|HBase クラスターの名前を入力します。|
    |クラスター ログイン名とパスワード|既定のログイン名は **admin** です。|
    |SSH ユーザー名とパスワード|既定のユーザー名は **sshuser** です。|

    その他のパラメーターは省略可能です。  

    各クラスターには Azure ストレージ アカウントとの依存関係があります。 クラスターを削除すると、データはストレージ アカウントに保持されます。 クラスターの既定のストレージ アカウント名は、クラスター名に "store" が追加されたものです。 これは、テンプレートの variables セクションでハードコードされます。

3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 クラスターの作成には約 20 分かかります。

HBase クラスターを削除したら、同じ既定の BLOB コンテナーを使用して別の HBase クラスターを作成できます。 新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。 不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。

## <a name="create-tables-and-insert-data"></a>テーブルを作成してデータを挿入する

SSH を使用して HBase クラスターに接続し、[Apache HBase シェル](https://hbase.apache.org/0.94/book/shell.html)を使用して HBase テーブルの作成、データの挿入、データのクエリを行うことができます。

多くの場合、データは次のような表形式で表示されます。

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png" alt-text="HDInsight Apache HBase の表形式データ" border="false":::

HBase ([クラウド BigTable](https://cloud.google.com/bigtable/) の実装) では、同じデータが次のように表示されます。

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png" alt-text="HDInsight Apache HBase の BigTable データ" border="false":::

**HBase シェルを使用するには**

1. `ssh` コマンドを使用して HBase クラスターに接続します。 次のコマンドを編集して `CLUSTERNAME` をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `hbase shell` コマンドを使用して、HBase 対話型シェルを起動します。 SSH 接続で次のコマンドを入力します。

    ```bash
    hbase shell
    ```

1. `create` コマンドを使用して、2 つの列ファミリを持つ HBase テーブルを作成します。 テーブル名と列名は大文字と小文字が区別されます。 次のコマンドを入力します。

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. `list` コマンドを使用して、HBase 内のすべてのテーブルを一覧表示します。 次のコマンドを入力します。

    ```hbase
    list
    ```

1. `put` コマンドを使用して、特定のテーブルの指定行の指定列に値を挿入します。 次のコマンドを入力します。

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. `scan` コマンドを使用して、`Contacts` テーブルのデータをスキャンして返します。 次のコマンドを入力します。

    ```hbase
    scan 'Contacts'
    ```

    :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png" alt-text="HDInsight Apache Hadoop HBase シェル" border="false":::

1. `get` コマンドを使用して、行のコンテンツを取り込みます。 次のコマンドを入力します。

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    1 行しかないので、`scan` コマンドを使用したときと同じような結果が表示されます。

    HBase テーブル スキーマの詳細については、[Apache HBase スキーマの設計の概要](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)に関するページを参照してください。 HBase コマンドの詳細については、「[Apache HBase のリファレンス ガイド](https://hbase.apache.org/book.html#quickstart)」を参照してください。

1. `exit` コマンドを使用して、HBase 対話型シェルを停止します。 次のコマンドを入力します。

    ```hbaseshell
    exit
    ```

**Contacts HBase テーブルにデータを一括で読み込むには**

HBase では、いくつかの方法でテーブルにデータを読み込ことができます。  詳細については、 [一括読み込み](https://hbase.apache.org/book.html#arch.bulk.load)に関するページを参照してください。

サンプルのデータ ファイルは、パブリック BLOB コンテナー `wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt` にあります。  このデータ ファイルの内容は次のとおりです。

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

必要に応じて、自分でテキスト ファイルを作成し、そのファイルを自分のストレージ アカウントにアップロードできます。 手順については、「[HDInsight で Apache Hadoop ジョブのデータをアップロードする](../hdinsight-upload-data.md)」を参照してください。

この手順では、前回の手順で作成した `Contacts` HBase テーブルを使用します。

1. 開いている ssh 接続から、次のコマンドを実行してデータ ファイルを StoreFile に変換し、`Dimporttsv.bulk.output` で指定された相対パスに格納します。

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. 次のコマンドを実行して、データを `/example/data/storeDataFileOutput` から HBase テーブルにアップロードします。

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. HBase シェルを開き、`scan` コマンドを使用してテーブルの内容を一覧表示することができます。

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive を使用して Apache HBase を照会する

[Apache Hive](https://hive.apache.org/) を使用して HBase テーブルのデータを照会できます。 このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

1. 開いている ssh 接続から、次のコマンドを使用して Beeline を開始します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Beeline の詳細については、「[Beeline による HDInsight での Hive と Hadoop の使用](../hadoop/apache-hadoop-use-hive-beeline.md)」を参照してください。

1. 次の [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) スクリプトを実行して、HBase テーブルにマップする Hive テーブルを作成します。 この記事で、HBase シェルを使用して、先ほど参照したサンプル テーブルが作成されたことを確認してから、このステートメントを実行してください。

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. 次の HiveQL スクリプトを実行して、HBase テーブルのデータを照会します。

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Beeline を終了するには、 `!exit`を使用します。

1. ssh 接続を終了するには、`exit` を使用します。

### <a name="separate-hive-and-hbase-clusters"></a>Hive クラスターと HBase クラスターを分離する

HBase データにアクセスする Hive クエリは、HBase クラスターから実行する必要はありません。 次の手順が完了していれば、Hive に付属する任意のクラスター (Spark、Hadoop、HBase、Interactive Query など) を使用して、HBase データに対するクエリを実行できます。

1. 両方のクラスターが同じ仮想ネットワークとサブネットに接続されている必要があります
2. HBase クラスター ヘッドノードから Hive クラスター ヘッドノードに `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` をコピーします

### <a name="secure-clusters"></a>クラスターをセキュリティで保護する

HBase データには、ESP 対応の HBase を使用している Hive からクエリを実行することもできます。 

1. マルチクラスター パターンに従う場合、両方のクラスターで ESP が有効になっている必要があります。 
2. Hive で HBase データに対するクエリを実行できるようにするには、Hbase Apache Ranger プラグインを介して HBase データにアクセスするためのアクセス許可が `hive` ユーザーに付与されていることを確認します
3. 個別の ESP 対応クラスターを使用する場合は、HBase クラスター ヘッドノードの `/etc/hosts` の内容を、Hive クラスター ヘッドノードの `/etc/hosts` に追加する必要があります。 
> [!NOTE]
> いずれかのクラスターをスケーリングした後、`/etc/hosts` を再度追加する必要があります

## <a name="use-hbase-rest-apis-using-curl"></a>Curl を使用して HBase REST API を使用する

REST API のセキュリティは、 [基本認証](https://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。

1. HDInsight クラスターで HBase REST API を有効にするには、次のカスタム スタートアップ スクリプトを **Script Action** セクションに追加します。 クラスターを作成するとき、またはクラスターを作成した後に、スタートアップ スクリプトを追加できます。 **[ノードの種類]** では、 **[Region Servers]\(リージョン サーバー\)** を選択して、HBase リージョン サーバーだけでスクリプトが実行されるようにします。


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. 使いやすさのために環境変数を設定します。 下のコマンドを編集して `MYPASSWORD` をクラスター ログイン パスワードに置き換えます。 `MYCLUSTERNAME` を HBase クラスターの名前に置き換えます。 その後、これらのコマンドを入力します。

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. 次のコマンドを使用して、既存の HBase テーブルを一覧表示します。

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. 次のコマンドを使用して、2 つの列ファミリを含む新しい HBase テーブルを作成します。

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    スキーマは、JSon 形式で提供されます。
1. 次のコマンドを使用して、一部のデータを挿入します。

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    -d スイッチで指定する値は、Base64 でエンコードする必要があります。 この例では次のとおりです。

   * MTAwMA==:1000
   * UGVyc29uYWw6TmFtZQ==:Personal:名前
   * Sm9obiBEb2xl:John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) を使用すると、複数の (バッチ処理された) 値を挿入できます。

1. 次のコマンドを使用して、1 行を取得します。

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

HBase Rest の詳細については、「 [Apache HBase reference guide (Apache HBase リファレンス ガイド)](https://hbase.apache.org/book.html#_rest)」をご覧ください。

> [!NOTE]  
> Thrift は、HDInsight での HBase ではサポートされていません。
>
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> 次のような応答が返されます。
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>クラスターの状態の確認

HDInsight の HBase には、クラスターを監視するための Web UI が付属します。 この Web UI を使用すると、統計情報やリージョンに関する情報を要求できます。

**HBase Master UI にアクセスするには**

1. `https://CLUSTERNAME.azurehdinsight.net` の Ambari Web UI にサインインします。この `CLUSTERNAME` は HBase クラスターの名前です。

1. 左側のメニューで **[HBase]** を選択します。

1. ページの上部にある **[Quick links]\(クイック リンク\)** を選択し、アクティブな Zookeeper ノード リンクをポイントして、 **[HBase Master UI]** を選択します。  UI は別のブラウザー タブで開かれます。

   :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png" alt-text="HDInsight Apache HBase HMaster UI" border="false":::

   HBase Master UI には次のセクションがあります。

   - リージョン サーバー
   - バックアップ マスター
   - 表
   - tasks
   - ソフトウェア属性

## <a name="cluster-recreation"></a>クラスターの再作成

HBase クラスターを削除したら、同じ既定の BLOB コンテナーを使用して別の HBase クラスターを作成できます。 新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。 ただし、不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。 

HBase コマンド `disable 'Contacts'` を使用できます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順で作成した HBase クラスターを削除します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. 上部の **検索** ボックスに「**HDInsight**」と入力します。
1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。
1. 表示される HDInsight クラスターの一覧で、このチュートリアル用に作成したクラスターの横にある **[...]** をクリックします。
1. **[削除]** をクリックします。 **[はい]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Apache HBase クラスターの作成方法を学習しました。 また、テーブルを作成してそのテーブルのデータを HBase シェルから表示する方法についても学習しました。 Hive を使用して HBase テーブルのデータを照会する方法や、 HBase C# REST API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。 詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [HDInsight HBase の概要](./apache-hbase-overview.md)
