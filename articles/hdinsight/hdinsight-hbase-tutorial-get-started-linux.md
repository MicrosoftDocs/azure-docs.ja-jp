---
title: "Azure HDInsight での HBase の使用 | Microsoft Docs"
description: "HDInsight の Hadoop で Apache HBase を使用するには、この HBase チュートリアルの手順に従ってください。 HBase シェルからテーブルを作成し、Hive を使用したクエリを実行します。"
keywords: "Apache HBase, HBase, HBase シェル, HBase チュートリアル"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>HBase チュートリアル: HDInsight で Apache HBase を使用する

HDInsight で HBase クラスターを作成する方法、HBase テーブルを作成する方法、Hive を使用してテーブルを照会する方法について説明します。 HBase の概要については、[HDInsight HBase の概要][hdinsight-hbase-overview]に関する記事を参照してください。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>前提条件
この HBase のチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md)。 
* [curl](http://curl.haxx.se/download.html)。

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>HBase クラスターの作成
次の手順では、Azure Resource Manager テンプレートを使用して、バージョン 3.4 Linux ベースの HBase クラスターと依存する既定の Azure Storage アカウントを作成します。 この手順で使用するパラメーターとその他のクラスター作成方法について理解するには、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

1. 次の画像をクリックして Azure ポータルでテンプレートを開きます。 テンプレートは、パブリック BLOB コンテナー内にあります。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **[カスタム デプロイ]** ブレードで以下を入力します。
   
   * **サブスクリプション**: クラスターの作成に使用する Azure サブスクリプションを選択します。
   * **リソース グループ**: 新しい Azure リソース管理グループを作成するか、既存のグループを使用します。
   * **場所**: リソース グループの場所を指定します。 
   * **ClusterName**: 作成する HBase クラスターの名前を入力します。
   * **クラスターのログイン名とパスワード**: 既定のログイン名は **admin** です。
   * **SSH ユーザー名とパスワード**: 既定のユーザー名は **sshuser** です。  この名前は変更できます。
     
     その他のパラメーターは省略可能です。  
     
     各クラスターには Azure ストレージ アカウントとの依存関係があります。 クラスターを削除すると、データはストレージ アカウントに保持されます。 クラスターの既定のストレージ アカウント名は、クラスター名に "store" が追加されたものです。 これは、テンプレートの variables セクションでハードコードされます。
3. **[上記の使用条件に同意する]** を選択し、**[購入]** をクリックします。 クラスターの作成には約 20 分かかります。

> [!NOTE]
> HBase クラスターを削除したら、同じ既定の BLOB コンテナーを使用して別の HBase クラスターを作成できます。 新しいクラスターでは、元のクラスターで作成した HBase テーブルを選択します。 不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。
> 
> 

## <a name="create-tables-and-insert-data"></a>テーブルを作成してデータを挿入する
SSH を使用して HBase クラスターに接続し、HBase シェルを使用して HBase テーブルの作成、データの挿入、データの照会を行うことができます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

多くの場合、データは次のような表形式で表示されます。

![HDInsight HBase の表形式データ][img-hbase-sample-data-tabular]

BigTable の実装である HBase では、同じデータが次のように表示されます。

![HDInsight HBase の Bigtable データ][img-hbase-sample-data-bigtable]

次の手順を完了すると、この操作をよく理解できます。  

**HBase シェルを使用するには**

1. SSH から次のコマンドを実行します。
   
        hbase shell
2. 2 つの列ファミリを持つ HBase を作成します。
   
        create 'Contacts', 'Personal', 'Office'
        list
3. いくつかのデータを挿入します。
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![HDInsight Hadoop HBase シェル][img-hbase-shell]
4. 1 つの行を取得します。
   
        get 'Contacts', '1000'
   
    行は 1 行のみのため、スキャン コマンドを使用した場合と同じ結果が得られます。
   
    HBase テーブル スキーマの詳細については、「[Introduction to HBase Schema Design (HBase スキーマの設計の概要)][hbase-schema]」を参照してください。 HBase コマンドの詳細については、「[Apache HBase reference guide (Apache HBase リファレンス ガイド)][hbase-quick-start]」を参照してください。
5. シェルを終了します。
   
        exit

**Contacts HBase テーブルにデータを一括で読み込むには**

HBase では、いくつかの方法でテーブルにデータを読み込ことができます。  詳細については、 [一括読み込み](http://hbase.apache.org/book.html#arch.bulk.load)に関するページを参照してください。

サンプル データ ファイルがパブリック BLOB コンテナー (*wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*) にアップロードされています。  このデータ ファイルの内容は次のとおりです。

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

必要に応じて、自分でテキスト ファイルを作成し、そのファイルを自分のストレージ アカウントにアップロードできます。 手順については、「[HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]」を参照してください。

> [!NOTE]
> この手順では、前回の手順で作成した Contacts HBase テーブルを使用します。
> 
> 

1. SSH から次のコマンドを実行して、データ ファイルを StoreFile に変換し、Dimporttsv.bulk.output で指定された相対パスに格納します。  HBase シェル内にいる場合は、exit コマンドを使用して終了します。
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. 次のコマンドを実行して、/example/data/storeDataFileOutput から HBase テーブルにデータをアップロードします。
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. HBase シェルを開き、スキャン コマンドを使用して、テーブルの内容の一覧を表示することができます。

## <a name="use-hive-to-query-hbase"></a>Hive を使用して HBase を照会する
Hive を使用して HBase テーブルのデータを照会できます。 このセクションでは、HBase テーブルにマッピングする Hive テーブルを作成し、作成した Hive テーブルを使用して HBase テーブルのデータを照会します。

> [!NOTE]
> Hive と HBase が同じ VNet 内の異なるクラスター上にある場合、Hive シェルの呼び出し中に zookeeper クォーラムを渡す必要があります。
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. **PuTTY**を開き、クラスターに接続します。  前の手順の指示を参照してください。
2. Hive シェルを開きます。
   
       hive
       
3. 次の HiveQL スクリプトを実行して、HBase テーブルにマップする Hive テーブルを作成します。 ここで、HBase シェルを使用して、先ほど参照したサンプル テーブルが HBase に作成されたことを確認してから、このステートメントを実行してください。
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. 次の HiveQL スクリプトを実行して、HBase テーブルのデータを照会します。
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Curl を使用して HBase REST API を使用する
> [!NOTE]
> Curl、または WebHCat を使用したその他の REST 通信を使用する場合は、HDInsight クラスター管理者のユーザー名とパスワードを指定して要求を認証する必要があります。 また、サーバーへの要求の送信に使用する Uniform Resource Identifier (URI) にクラスター名を含める必要があります。
> 
> このセクションのコマンドでは、 **USERNAME** をクラスターに対して認証するユーザーの名前に置き換え、 **PASSWORD** をユーザー アカウントのパスワードに置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
> 
> REST API のセキュリティは、 [基本認証](http://en.wikipedia.org/wiki/Basic_access_authentication)を通じて保護されています。 資格情報をサーバーに安全に送信するには、必ずセキュア HTTP (HTTPS) を使用して要求を行う必要があります。
> 
> 

1. コマンド ラインで次のコマンドを使用して、HDInsight クラスターに接続できることを確認します。
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    次のような応答を受け取ります。
   
        {"status":"ok","version":"v1"}
   
    このコマンドで使用されるパラメーターの意味は次のとおりです。
   
   * **-u** : 要求の認証に使用するユーザー名とパスワード
   * **-G** : GET 要求であることを示します。
2. 次のコマンドを使用して、既存の HBase テーブルを一覧表示します。
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. 次のコマンドを使用して、2 つの列ファミリを含む新しい HBase テーブルを作成します。
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    スキーマは、JSon 形式で提供されます。
4. 次のコマンドを使用して、一部のデータを挿入します。
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    -d スイッチで指定された値に base64 エンコードを使用する必要があります。  たとえば、次のようになります。
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) を使用すると、複数の (バッチ処理された) 値を挿入できます。
5. 次のコマンドを使用して、1 行を取得します。
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

HBase Rest の詳細については、「 [Apache HBase reference guide (Apache HBase リファレンス ガイド)](https://hbase.apache.org/book.html#_rest)」をご覧ください。

>
> [!NOTE]
> Thrift は、HDInsight での HBase ではサポートされていません。
>

## <a name="check-cluster-status"></a>クラスターの状態の確認
HDInsight の HBase には、クラスターを監視するための Web UI が付属します。 この Web UI を使用すると、統計情報やリージョンに関する情報を要求できます。

**HBase Master UI にアクセスするには**

1. https://&lt;クラスター名>.azurehdinsight.net で Ambari Web UI を開きます。
2. 左側のメニューで **[HBase]** をクリックします。
3. ページの上部にある **[Quick links (クイック リンク)]** をクリックし、アクティブな Zookeeper ノード リンクをポイントして、**[HBase Master UI]** をクリックします。  UI は別のブラウザー タブで開かれます。

  ![HDInsight HBase Master UI](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  HBase Master UI には次のセクションがあります。

  - リージョン サーバー
  - バックアップ マスター
  - tables
  - タスク
  - ソフトウェア属性

## <a name="delete-the-cluster"></a>クラスターを削除する
不整合を回避するために、クラスターを削除する前に HBase テーブルを無効にしておくことをお勧めします。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ
この HDInsight の HBase のチュートリアルでは、HBase クラスターの作成方法と、テーブルを作成してそのテーブルのデータを HBase シェルから表示する方法について学習しました。 また、HBase テーブルのデータに対して Hive クエリを使用する方法と、HBase C# REST API を使用して HBase テーブルを作成し、テーブルからデータを取得する方法についても学習しました。

詳細については、次を参照してください。

* [HDInsight HBase の概要][hdinsight-hbase-overview]: HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

