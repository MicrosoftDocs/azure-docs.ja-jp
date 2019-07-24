---
title: Azure Data Box を使用してオンプレミス HDFS ストアから Azure Storage にデータを移行する
description: オンプレミス HDFS ストアから Azure Storage にデータを移行する
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: d0908e9edce8efb7a378ee04b6076b61cae2d2bf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998296"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box を使用してオンプレミス HDFS ストアから Azure Storage にデータを移行する

Data Box デバイスを使用することにより、Hadoop クラスターのオンプレミス HDFS ストアから Azure Storage (Blob ストレージまたは Data Lake Storage Gen2) にデータを移行できます。

この記事は、次のタスクを完了する上で役立ちます。

:heavy_check_mark:データを Data Box デバイスにコピーします。

:heavy_check_mark:Azure Data Box デバイスを Microsoft に発送します。

:heavy_check_mark:Data Lake Storage Gen2 ストレージ アカウントにデータを移動します。

## <a name="prerequisites"></a>前提条件

移行を完了するには、以下が必要です。

* 階層型名前空間が有効になって**いない** Azure Storage アカウント。

* Azure Data Lake Storage Gen2 アカウント (階層型名前空間が有効になって**いる**ストレージ アカウント) を使用する場合、この時点で作成できます。

* ソース データを含むオンプレミス Hadoop クラスター。

* [Azure Data Box デバイス](https://azure.microsoft.com/services/storage/databox/)。 

    - [お客様の Data Box を注文します](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)。 ボックスを注文するときに、階層型名前空間が有効になって**いない**ストレージ アカウントを必ず選択してください。 これは、Data Box は、Azure Data Lake Storage Gen2 への直接のインジェストをサポートしていないためです。 ストレージ アカウントにコピーしてから、ADLS Gen2 アカウントへの第 2 のコピーを行う必要があります。 これに関する方法は以下の手順で与えられます。
    - オンプレミス ネットワークに [Data Box をケーブル接続](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)します。

準備ができている場合、始めましょう。

## <a name="copy-your-data-to-a-data-box-device"></a>データを Data Box デバイスにコピーする

オンプレミス HDFS ストアから Data Box デバイスにデータをコピーするには、いくつかの事項を設定し、[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ツールを使用します。

コピーしているデータの量が単一の Data Box の容量を超える場合は、Data Box に収まるサイズにデータ セットを分割する必要があります。

以下の手順に従って、Blob/オブジェクト ストレージの REST API を介して Data Box にデータをコピーします。 REST API インターフェイスでは、Data Box はクラスターに HDFS ストアとして表示されます。 


1. REST を介してデータをコピーする前に、Data Box 上で REST インターフェイスに接続するためにセキュリティおよび接続プリミティブを識別します。 Data Box のローカル Web UI にサインインして、**[接続とコピー]** ページに移動します。 Data Box の Azure Storage アカウントに対して、**[アクセスの設定]** の下で **[REST (プレビュー)]** を探して選択します。

    ![[接続とコピー] ページ](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. [ストレージ アカウントへのアクセスとデータのアップロード] ダイアログで **[Blob service エンドポイント]** と **[ストレージ アカウント キー]** をコピーします。 Blob service エンドポイントから、`https://` と末尾のスラッシュを省略します。

    ここでは、エンドポイントは `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` になります。 使用する URI のホスト部分は `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` です。 たとえば、[HTTP 経由の REST への接続](/azure/databox/data-box-deploy-copy-data-via-rest)の方法を参照してください。 

     ![[ストレージ アカウントへのアクセスとデータのアップロード] ダイアログ](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. エンドポイントと、Data Box IP アドレスを各ノードの `/etc/hosts` に追加します。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    DNS を他のメカニズムを使用している場合は、Data Box エンドポイントを解決できることを確認する必要があります。
    
3. `hadoop-azure` および `microsoft-windowsazure-storage-sdk` jar ファイルをポイントするように、シェル変数 `azjars` を設定します。 これらのファイルは、Hadoop インストール ディレクトリの下にあります (コマンド `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` を使用することにより、これらのファイルが存在しているか確認できます。ここで、`<hadoop_install_dir>` は Hadoop をインストールしたディレクトリです)。完全パスを使用してください。 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Hadoop HDFS から Data Box Blob ストレージへデータをコピーします。

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars` オプションは、`hadoop-azure*.jar` と従属 `azure-storage*.jar` ファイルを `distcp` で使用できるようにするために使用されます。 これは、既に一部のクラスターに対して行われている場合があります。
   
   次の例は、`distcp` コマンドを使用してデータをコピーする方法を示しています。
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
コピー速度を向上させるには:
- マッパーの数を変更してみてください。 (上記の例では `m` = 4 マッパーを使用します)。
- 複数の `distcp` を並行して実行してみてください。
- 大きなファイルは小さなファイルよりもパフォーマンスが向上することに注意してください。       
    
## <a name="ship-the-data-box-to-microsoft"></a>Data Box を Microsoft に送付する

これらの手順に従って、Data Box デバイスを準備し、Microsoft に送付します。

1. データのコピーが完了したら、Data Box 上で [[配送の準備]](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest) を実行します。 デバイスの準備が完了した後は、BOM ファイルをダウンロードします。 後からこれらの BOM またはマニフェスト ファイルを使用して、データが Azure にアップロードされたことを確認します。 デバイスをシャット ダウンし、ケーブルを取り外します。 
2.  UPS での集荷をスケジュールして、[Azure に Data Box を返送](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)します。 
3.  Microsoft がデバイスを受け取ると、デバイスはデータセンターに接続され、Data Box を注文したときに (階層型名前空間を無効にして) 指定したストレージ アカウントにデータがアップロードされます。 すべてのデータが Azure にアップロードされたことを BOM ファイルに対して確認します。 Data Lake Storage Gen2 ストレージ アカウントに、このデータを移動できますようになりました。

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Data Lake Storage Gen2 ストレージ アカウントにデータを移動する

この手順は、データ ストアとして Azure Data Lake Storage Gen2 を使用している場合に必要です。 階層型名前空間を持たない Blob ストレージ アカウントだけをデータ ストアとして使用している場合は、この手順を実行する必要はありません。

これは 2 つの方法で行えます。 

- [[Azure Data Factory to move data to ADLS Gen2]](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)(ADLS Gen2 にデータを移動するための Azure Data Factory) を使用します。 ソースとして **Azure Blob Storage** を指定する必要があります。

- Azure ベースの Hadoop クラスターを使用します。 この DistCp コマンドを実行することができます。

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

このコマンドは、データとメタデータの両方を、ストレージ アカウントから Data Lake Storage Gen2 ストレージ アカウントにコピーします。

## <a name="next-steps"></a>次の手順

HDInsight クラスターでの Data Lake Storage Gen2 の動作について学習します。 「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。
