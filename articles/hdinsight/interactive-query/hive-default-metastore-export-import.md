---
title: Azure HDInsight で既定の Hive メタストアを外部メタストアに移行する
description: Azure HDInsight で既定の Hive メタストアを外部メタストアに移行する
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 825204fe40125a65e8e6f27c6973417813700a9e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743693"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>既定の Hive メタストア DB を外部メタストア DB に移行する

この記事では、Hive の[既定のメタストア DB](../hdinsight-use-external-metadata-stores.md#default-metastore) から HDInsight 上の外部 SQL Database にメタデータを移行する方法について説明します。 

## <a name="why-migrate-to-external-metastore-db"></a>外部メタストア DB に移行する理由

* 既定のメタストア DB は Basic SKU に限定されており、運用スケールのワークロードを処理することはできません。

* 外部メタストア DB を使用すると、同じメタストア DB を共有する新しい HDInsight クラスターを追加することで、Hive コンピューティング リソースを水平方向にスケーリングできます。

* HDInsight 3.6 から 4.0 への移行では、Hive スキーマのバージョンをアップグレードする前に、外部メタストア DB にメタデータを移行することが必須です。 [Hdinsight 3.6 から HDInsight 4.0 へのワークロードの移行](./apache-hive-migrate-workloads.md)に関する記事を参照してください。

既定のメタストア DB のコンピューティング能力は限られているため、メタデータの移行中にクラスター上の他のジョブの使用率を低くすることをお勧めします。

ソースとターゲットの DB で、同じバージョンの HDInsight と同じストレージ アカウントを使用する必要があります。 HDInsight のバージョンを 3.6 から 4.0 にアップグレードする場合は、最初にこの記事の手順を完了してください。 その後、[こちら](./apache-hive-migrate-workloads.md)に記載されている公式のアップグレード手順に従います。

## <a name="prerequisites"></a>前提条件

[Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md) を使用している場合、Hive テーブルの場所が、Azure Data Lake Storage Gen1 に対するクラスターの HDFS 構成に依存している可能性があります。 次のスクリプト アクションを実行して、これらの場所を他のクラスターに移植できるようにします。 「[実行中のクラスターに対するスクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)」を参照してください。

アクションは、シンボリック リンクを完全なパスに置き換えるのと似ています。

|プロパティ | 値 |
|---|---|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|ノードの種類|Head|
|パラメーター|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>sqlpackage を使用してエクスポートとインポートにより移行する

Hive の既定のメタストア DB の `sqlpackage` を使用した SQL のエクスポートとインポートがサポートされているのは、2020 年 10 月 15 日より後に作成された HDInsight クラスターだけです。

1. [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) をクラスターにインストールします。

2. 次のコマンドを実行して、既定のメタストア DB を BACPAC ファイルにエクスポートします。

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. BACPAC ファイルを保存します。 以下はオプションです。

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. [こちら](../../azure-sql/database/database-import.md)に記載されている手順に従って、BACPAC ファイルを新しいデータベースにインポートします。

5. 新しいデータベースを[新しい HDInsight クラスターで外部メタストア DB として構成する](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)準備ができました。

## <a name="migrate-using-hive-script"></a>Hive スクリプトを使用して移行する

2020 年 10 月 15 日より前に作成されたクラスターでは、既定のメタストア DB のエクスポートとインポートはサポートされていません。

そのようなクラスターの場合は、[ストレージ アカウント間での Hive テーブルのコピー](./hive-migration-across-storage-accounts.md)に関するガイドに従い、[外部 Hive メタストア DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) が含まれる第 2 のクラスターを使用します。 第 2 のクラスターでは同じストレージ アカウントを使用できますが、新しい既定のファイル システムを使用する必要があります。

### <a name="option-to-shallow-copy"></a>"簡易" コピーのオプション
上記のガイドを使用してテーブルの "ディープ" コピーを行うと、ストレージの消費量が 2 倍になります。 ソース ストレージ コンテナー内のデータを手動で消去する必要があります。
代わりに、テーブルが非トランザクションである場合は、テーブルを "簡易" コピーすることができます。 HDInsight 3.6 ではすべての Hive テーブルが既定で非トランザクションですが、HDInsight 4.0 では外部テーブルのみが非トランザクションです。 トランザクション テーブルはディープ コピーする必要があります。 非トランザクション テーブルを簡易コピーするには、次の手順のようにします。

1. ソース クラスターのプライマリ ヘッドノードでスクリプト [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) を実行して、すべての Hive テーブルの DDL を生成します。
2. DDL は、`/tmp/hdi_hive_ddls.hql` という名前のローカル Hive スクリプトに書き込まれます。 これを、外部 Hive メタストア DB を使用するターゲット クラスターで実行します。

## <a name="verify-that-all-hive-tables-are-imported"></a>すべての Hive テーブルがインポートされたことを確認する

次のコマンドを実行すると、メタストア DB での SQL クエリを使用して、すべての Hive テーブルとそのデータの場所が出力されます。 新しいクラスターと古いクラスターの出力を比較し、新しいメタストア DB に足りないテーブルがないことを確認します。

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>関連項目

* [HDInsight 3.6 から 4.0 にワークロードを移行する](./apache-hive-migrate-workloads.md)
* [ストレージ アカウント間での Hive ワークロードの移行](./hive-migration-across-storage-accounts.md)
* [HDInsight で Beeline に接続する](../hadoop/connect-install-beeline.md)
* [テーブル作成時のアクセス許可エラーのトラブルシューティング](./interactive-query-troubleshoot-permission-error-create-table.md)
