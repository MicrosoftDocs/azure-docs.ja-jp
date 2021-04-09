---
title: Azure Data Box を使用してオンプレミスの HDFS ストアから Azure Storage に移行する
description: Data Box デバイスを使用することにより、オンプレミス HDFS ストアから Azure Storage (BLOB ストレージまたは Data Lake Storage Gen2) にデータを移行します。
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e58137dd680ff9a2be2bd657f0969304b526873f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913115"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Azure Data Box を使用してオンプレミスの HDFS ストアから Azure Storage に移行する

Data Box デバイスを使用することにより、Hadoop クラスターのオンプレミス HDFS ストアから Azure Storage (Blob ストレージまたは Data Lake Storage Gen2) にデータを移行できます。 Data Box Disk、80 TB の Data Box、または 770 TB の Data Box Heavy から選択できます。

この記事は、次のタスクを完了する上で役立ちます。

> [!div class="checklist"]
> * データの移行を準備します。
> * データを Data Box Disk、Data Box または Data Box Heavy デバイスにコピーします。
> * Microsoft にデバイスを返送します。
> * ファイルとディレクトリにアクセス許可を適用します (Data Lake Storage Gen2 のみ)

## <a name="prerequisites"></a>前提条件

移行を完了するには、以下が必要です。

* Azure Storage のアカウント

* ソース データを含むオンプレミス Hadoop クラスター。

* [Azure Data Box デバイス](https://azure.microsoft.com/services/storage/databox/)。

  * [Data Box](../../databox/data-box-deploy-ordered.md) または [Data Box Heavy を注文します](../../databox/data-box-heavy-deploy-ordered.md)。 

  * オンプレミス ネットワークに [Data Box](../../databox/data-box-deploy-set-up.md) または [Data Box Heavy](../../databox/data-box-heavy-deploy-set-up.md) をケーブル接続します。

準備ができている場合、始めましょう。

## <a name="copy-your-data-to-a-data-box-device"></a>データを Data Box デバイスにコピーする

データが 1 つの Data Box デバイスに収まる場合は、そのデータを Data Box デバイスにコピーします。 

データ サイズが Data Box デバイスの容量を超える場合は、[オプションの手順を使用してデータを複数の Data Box デバイスに分割し](#appendix-split-data-across-multiple-data-box-devices)、この手順を実行します。 

オンプレミス HDFS ストアから Data Box デバイスにデータをコピーするには、いくつかの事項を設定し、[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ツールを使用します。

以下の手順に従って、Blob/オブジェクト ストレージの REST API を介して Data Box デバイスにデータをコピーします。 REST API インターフェイスでは、デバイスはクラスターに HDFS ストアとして表示されます。

1. REST を介してデータをコピーする前に、Data Box または Data Box Heavy 上で REST インターフェイスに接続するためにセキュリティおよび接続プリミティブを識別します。 Data Box のローカル Web UI にサインインして、 **[接続とコピー]** ページに移動します。 デバイスの Azure ストレージ アカウントに対して、 **[アクセスの設定]** の下で **[REST]** を探して選択します。

    ![[接続とコピー] ページ](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. [ストレージ アカウントへのアクセスとデータのアップロード] ダイアログで **[Blob service エンドポイント]** と **[ストレージ アカウント キー]** をコピーします。 Blob service エンドポイントから、`https://` と末尾のスラッシュを省略します。

    ここでは、エンドポイントは `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` になります。 使用する URI のホスト部分は `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` です。 たとえば、[HTTP 経由の REST への接続](../../databox/data-box-deploy-copy-data-via-rest.md)の方法を参照してください。 

     ![[ストレージ アカウントへのアクセスとデータのアップロード] ダイアログ](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. エンドポイントと、Data Box または Data Box Heavy ノードの IP アドレスを各ノードの `/etc/hosts` に追加します。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    DNS を他のメカニズムを使用している場合は、Data Box エンドポイントを解決できることを確認する必要があります。

4. シェル変数 `azjars` を、`hadoop-azure` および `azure-storage` jar ファイルの場所に設定します。 これらのファイルは Hadoop インストール ディレクトリ以下にあります。

    これらのファイルが存在するかどうかを確認するには、次のコマンドを使用します`ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 `<hadoop_install_dir>` プレースホルダーは、Hadoop をインストールしたディレクトリのパスに置き換えます。 必ず完全修飾パスを使用します。

    例 :

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. データのコピーに使用するストレージ コンテナーを作成します。 このコマンドの一部として宛先ディレクトリも指定する必要があります。 この時点では、これはダミーの宛先ディレクトリになる可能性があります。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` プレースホルダーは、実際の BLOB サービス エンドポイントの名前に置き換えます。

    * `<account_key>` プレースホルダーは、実際のアカウントのアクセス キーに置き換えます。

    * `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

    * `<destination_directory>` プレースホルダーは、データのコピー先であるディレクトリの名前に置き換えます。

6. リスト コマンドを実行してコンテナーとディレクトリが作成されたことを確認します。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * `<blob_service_endpoint>` プレースホルダーは、実際の BLOB サービス エンドポイントの名前に置き換えます。

   * `<account_key>` プレースホルダーは、実際のアカウントのアクセス キーに置き換えます。

   * `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

7. Data Box Blob ストレージ内の先ほど作成したコンテナーに、Hadoop HDFS からデータをコピーします。 コピー先のディレクトリが見つからない場合、コマンドにより自動的に作成されます。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * `<blob_service_endpoint>` プレースホルダーは、実際の BLOB サービス エンドポイントの名前に置き換えます。

    * `<account_key>` プレースホルダーは、実際のアカウントのアクセス キーに置き換えます。

    * `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

    * `<exlusion_filelist_file>` プレースホルダーは、ファイルの除外一覧を含むファイルの名前に置き換えます。

    * `<source_directory>` プレースホルダーは、コピーするデータが格納されているディレクトリの名前に置き換えます。

    * `<destination_directory>` プレースホルダーは、データのコピー先であるディレクトリの名前に置き換えます。

    `-libjars` オプションは、`hadoop-azure*.jar` と従属 `azure-storage*.jar` ファイルを `distcp` で使用できるようにするために使用されます。 これは、既に一部のクラスターに対して行われている場合があります。

    次の例は、`distcp` コマンドを使用してデータをコピーする方法を示しています。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    コピー速度を向上させるには:

    * マッパーの数を変更してみてください。 (上記の例では `m` = 4 マッパーを使用します)。

    * 複数の `distcp` を並行して実行してみてください。

    * 大きなファイルは小さなファイルよりもパフォーマンスが向上することに注意してください。

## <a name="ship-the-data-box-to-microsoft"></a>Data Box を Microsoft に送付する

これらの手順に従って、Data Box デバイスを準備し、Microsoft に送付します。

1. まず [Data Box または Data Box Heavy 上で発送準備を行います](../../databox/data-box-deploy-copy-data-via-rest.md)。

2. デバイスの準備が完了した後は、BOM ファイルをダウンロードします。 後からこれらの BOM またはマニフェスト ファイルを使用して、データが Azure にアップロードされたことを確認します。

3. デバイスをシャット ダウンし、ケーブルを取り外します。

4. UPS で集荷のスケジュールを設定します。

    * Data Box デバイスの場合は、[Data Box の送付](../../databox/data-box-deploy-picked-up.md)に関するページを参照してください。

    * Data Box Heavy デバイスの場合は、[Data Box Heavy の送付](../../databox/data-box-heavy-deploy-picked-up.md)に関するページを参照してください。

5. Microsoft がデバイスを受け取ると、データ センター ネットワークに接続され、デバイスを注文したときに指定したストレージ アカウントにデータがアップロードされます。 すべてのデータが Azure にアップロードされたことを BOM ファイルに対して確認します。 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>ファイルとディレクトリにアクセス許可を適用します (Data Lake Storage Gen2 のみ)

Azure Storage アカウントに既にデータがあります。 次に、ファイルとディレクトリにアクセス許可を適用します。

> [!NOTE]
> この手順は、データ ストアとして Azure Data Lake Storage Gen2 を使用している場合にのみ必要です。 階層型名前空間を持たない BLOB ストレージ アカウントだけをデータ ストアとして使用している場合は、このセクションをスキップできます。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 アカウントのサービス プリンシパルを作成します。

「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」のガイダンスに従って、サービス プリンシパルを作成します。

* 記事の「[アプリケーションをロールに割り当てる](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)」セクションの手順を実行するときに、必ず **ストレージ BLOB データ共同作成者** ロールをサービス プリンシパルに割り当ててください。

* 記事の「[サインインするための値を取得する](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」セクションの手順を実行するときは、アプリケーション ID、クライアント シークレット値をテキスト ファイルに保存します。 これらはすぐに必要になります。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>それらのアクセス許可を使用してコピーされたファイルの一覧を生成する

オンプレミスの Hadoop クラスターから、次のコマンドを実行します。

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

このコマンドでは、それらのアクセス許可を使用してコピーされたファイルの一覧が生成されます。

> [!NOTE]
> HDFS 内のファイル数によっては、このコマンドの実行に時間がかかることがあります。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>ID の一覧を生成し、それらを Azure Active Directory (ADD) の ID にマップする

1. `copy-acls.py` スクリプトをダウンロードします。 この記事の「[ヘルパー スクリプトをダウンロードし、それらを実行するようにエッジ ノードを設定する](#download-helper-scripts)」セクションを参照してください。

2. このコマンドを実行して、固有の ID の一覧を生成します。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   このスクリプトで、ADD ベースの ID にマップする必要がある ID を含む `id_map.json` という名前のファイルが生成されます。

3. テキスト エディターで `id_map.json` ファイルを開きます。

4. ファイルに出現する各 JSON オブジェクトについて、マップされた適切な ID を使用して AAD ユーザー プリンシパル名 (UPN) または ObjectId (OID) のいずれかの `target` 属性を更新します。 完了したら、ファイルを保存します。 次の手順でこのファイルが必要になります。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>コピーしたファイルにアクセス許可を適用し、ID マッピングを適用する

このコマンドを実行して、Data Lake Storage Gen2 アカウントにコピーしたデータにアクセス許可を適用します。

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

* `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

* `<application-id>` と `<client-secret>` のプレースホルダーは、サービス プリンシパルの作成時に収集したアプリケーション ID とクライアント シークレットに置き換えます。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>付録: 複数の Data Box デバイスにデータを分割する

データを Data Box デバイスに移動する前に、ヘルパー スクリプトをダウンロードし、データが Data Box に収まるように編成されていることを確認し、不要なファイルを除外する必要があります。

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>ヘルパー スクリプトをダウンロードし、それらを実行するようにエッジ ノードを設定する

1. オンプレミス Hadoop クラスターのエッジ ノードまたはヘッド ノードから、次のコマンドを実行します。

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   このコマンドで、ヘルパー スクリプトを含む GitHub リポジトリが複製されます。

2. ローカル コンピューターに [jq](https://stedolan.github.io/jq/) パッケージがインストールされていることを確認します。

   ```bash
   
   sudo apt-get install jq
   ```

3. [Requests](https://2.python-requests.org/en/master/) python パッケージをインストールします。

   ```bash
   
   pip install requests
   ```

4. 必要なスクリプトに実行アクセス許可を設定します。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>データが Data Box デバイスに収まるように編成されていることを確認する

データのサイズが 1 台の Data Box デバイスのサイズを超える場合は、複数のグループに分割してファイルを複数の Data Box デバイスに保存できるようにします。

データが 1 台の Data Box デバイスのサイズを超えない場合は、次のセクションに進むことができます。

1. 管理者特権のアクセス許可を使用して、前のセクションのガイダンスに従ってダウンロードした `generate-file-list` スクリプトを実行します。

   コマンド パラメーターの説明を次に示します。

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 生成されたファイル一覧を HDFS にコピーして、[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ジョブにアクセスできるようにします。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>不要なファイルを除外する

DisCp ジョブからいくつかのディレクトリを除外する必要があります。 たとえば、クラスターの稼働を維持する状態情報を含むディレクトリを除外します。

DistCp ジョブを開始する予定のオンプレミス Hadoop クラスター上に、除外するディレクトリの一覧を指定するファイルを作成します。

次に例を示します。

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>次のステップ

HDInsight クラスターでの Data Lake Storage Gen2 の動作について学習します。 「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。