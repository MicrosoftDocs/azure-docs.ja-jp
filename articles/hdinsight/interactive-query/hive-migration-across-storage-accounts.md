---
title: Azure Storage の新しいアカウントへの Hive ワークロードの移行
description: Azure Storage の新しいアカウントへの Hive ワークロードの移行
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744048"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Azure Storage の新しいアカウントへの Hive ワークロードの移行

スクリプト アクションを使用して、HDInsight のストレージ アカウント間で Hive テーブルをコピーする方法について説明します。 これは、[`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) に移行するときに役立つ場合があります。

HDInsight 4.0 で個々の Hive テーブルを手動でコピーする場合は、[Hive のエクスポート/インポート](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* 次の構成が設定された新しい HDInsight クラスター:
  * その既定のファイルシステムが、ターゲット ストレージ アカウント内にあります。 「[Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md)」を参照してください。
  * そのクラスターのバージョンが、ソース クラスターのものと一致している必要があります。
  * これは、新しい外部 Hive メタストア DB を使用します。 [外部メタデータ ストアの使用](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)に関する記事を参照してください。
* 元のクラスターと新しいクラスターの両方にアクセスできるストレージ アカウント。 使用できるセカンダリ ストレージの種類については、「[HDInsight にストレージ アカウントを追加する](../hdinsight-hadoop-add-storage.md)」と 「[ストレージの種類と機能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)」を参照してください。

    次のことをお試しください。
  * ターゲット ストレージ アカウントを元のクラスターに追加します。
  * 元のストレージ アカウントを新しいクラスターに追加します。
  * 中間ストレージ アカウントを元のクラスターと新しいクラスターの両方に追加します。

## <a name="how-it-works"></a>しくみ

スクリプト アクションを実行して、Hive テーブルを元のクラスターから指定した HDFS ディレクトリにエクスポートします。 「[実行中のクラスターに対するスクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)」を参照してください。

次に、新しいクラスターで別のスクリプト アクションを実行して、HDFS ディレクトリから Hive テーブルをインポートします。

このスクリプトにより、新しいクラスターの既定のファイルシステムに対してテーブルが再作成されます。 ネイティブ テーブルでは、そのデータもストレージにコピーされます。 ネイティブでないテーブルでは、定義によってのみコピーされます。 ネイティブでないテーブルの詳細については、「[Hive ストレージ ハンドラー](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers)」を参照してください。

Hive ウェアハウス ディレクトリにない外部テーブルのパスは保持されます。 他のテーブルは、ターゲット クラスターの既定の Hive パスにコピーされます。 Hive のプロパティ `hive.metastore.warehouse.external.dir` および `hive.metastore.warehouse.dir` を参照してください。

これらのスクリプトでは、ターゲット クラスターでカスタム ファイルのアクセス許可は保持されません。

> [!NOTE]
>
> このガイドでは、Hive データベース、テーブル、およびパーティションに関連するメタデータ オブジェクトのコピーがサポートされています。 他のメタデータ オブジェクトは手動で再作成する必要があります。
>
> * `Views` については、Hive では HDInsight 4.0 の Hive 2.2.0 以降で `SHOW VIEWS` コマンドがサポートされています。 ビュー定義には `SHOW CREATE TABLE` を使用します。 以前のバージョンの Hive の場合は、メタストア SQL DB に対してクエリを実行して、ビューを表示します。
> * `Materialized Views` については、コマンド `SHOW MATERIALIZED VIEWS`、`DESCRIBE FORMATTED`、`CREATE MATERIALIZED VIEW` を使用します。 詳細については、「[具体化されたビュー](https://cwiki.apache.org/confluence/display/Hive/Materialized+views)」を参照してください。
> * HDInsight 4.0 の Hive 2.1.0 以降でサポートされている `Constraints` については、`DESCRIBE EXTENDED` を使用してテーブルの制約を一覧表示し、`ALTER TABLE` を使用して制約を追加します。 詳細については、[テーブル制約の変更](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints)に関する記事を参照してください。

## <a name="copy-hive-tables"></a>Hive テーブルをコピーする

1. 次のフィールドを使用して、元のクラスターに "エクスポート" スクリプト アクションを適用します。

    これにより、中間 Hive スクリプトが生成され、実行されます。 これらは、指定された `<hdfs-export-path>` に保存されます。

    必要に応じて、手動で実行する前に `--run-script=false` を使用してそれらをカスタマイズします。

    |プロパティ | 値 |
    |---|---|
    |Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |ノードの種類|Head|
    |パラメーター|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. エクスポートが正常に完了したら、次のフィールドを使用して、新しいクラスターに "インポート" スクリプト アクションを適用します。

    |プロパティ | 値 |
    |---|---|
    |Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |ノードの種類|Head|
    |パラメーター|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>検証

スクリプト [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) をダウンロードし、各クラスターのプライマリ ノードでルート ユーザーとして実行し、出力ファイル `/tmp/hive_contents.out` の内容を比較します。 「[SSH を使用して HDInsight (Apache Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

## <a name="cleanup-additional-storage-usage"></a>追加のストレージ使用をクリーンアップする

ストレージの移行が完了し、検証されたら、指定した HDFS エクスポート パスのデータを削除できます。

オプションは、HDFS コマンド `hdfs dfs -rm -R` を使用することです。

## <a name="option-reduce-additional-storage-usage"></a>オプション: 追加のストレージ使用量を減らす

エクスポート スクリプト アクションを行うと、Hive が原因でストレージ使用量が 2 倍になる恐れがあります。 ただし、一度に 1 つのデータベースまたはテーブルを手動で移行することで、追加のストレージ使用を制限することが可能です。

1. `--run-script=false` を指定して、生成された Hive スクリプトの実行をスキップします。 Hive のエクスポートとインポートの各スクリプトは、エクスポート パスに引き続き保存されます。

2. データベースごと、またはテーブルごとに、Hive のエクスポートとインポートの各スクリプトのスニペットを実行し、各データベースまたはテーブルの移行後にエクスポート パスを手動でクリーンアップします。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [外部のメタデータ ストアを使用する](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [ストレージの種類と機能](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [実行中のクラスターに対するスクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
