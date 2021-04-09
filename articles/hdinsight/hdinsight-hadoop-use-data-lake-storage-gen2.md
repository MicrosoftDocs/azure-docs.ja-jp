---
title: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する
description: Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する方法について学習します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945379"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) は、ビッグ データ分析専用のクラウド ストレージ サービスで、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 上に構築されています。 Data Lake Storage Gen2 は Azure Blob Storage と Azure Data Lake Storage Gen1 の機能を結合したものです。 その結果、このサービスでは、Azure Data Lake Storage Gen1 に由来するファイル システム セマンティクス、ディレクトリ レベルとファイル レベルのセキュリティ、スケーラビリティなどの機能が提供されます。 さらに、Azure Blob Storage から、低コスト、階層型ストレージ、高可用性、ディザスター リカバリー機能を利用できます。

Data Lake Storage Gen2 を使用したクラスター作成オプションの詳細な比較については、「[Azure HDInsight クラスターで使用するストレージ オプションを比較する](hdinsight-hadoop-compare-storage-options.md)」を参照してください。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 の可用性

Data Lake Storage Gen2 は、ほぼすべての Azure HDInsight クラスターの種類のストレージ オプションとして、既定のストレージ アカウントと追加のストレージ アカウントの両方で使用できます。 ただし、HBase では、Data Lake Storage Gen2 のアカウントを 1 つだけ持つことができます。

> [!Note]  
> **プライマリ ストレージの種類** として Data Lake Storage Gen2 を選択すると、追加のストレージとして Data Lake Storage Gen1 を選択できなくなります。

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で HDInsight クラスターを作成する

Data Lake Storage Gen2 にアクセスできる HDInsight クラスターを作成する方法の詳しい手順については、以下のリンクを参照してください。

* [ポータルの使用](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI の使用](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* Azure Data Lake Storage Gen2 を使用する HDInsight クラスターを作成するための PowerShell の使用は、現在サポートされていません。

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight での Data Lake Storage Gen2 のアクセス制御

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 をサポートするアクセス許可の種類

Data Lake Storage Gen2 では、Azure ロールベースのアクセス制御 (Azure RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートするアクセス制御モデルが使用されています。 Data Lake Storage Gen1 では、データへのアクセス制御の場合のみアクセス制御リストがサポートされていました。

Azure RBAC では、ロールの割り当てを使用して、Azure リソースのユーザー、グループ、サービス プリンシパルにアクセス許可のセットを効果的に適用します。 通常、これらの Azure リソースは、最上位のリソース (例: Azure Blob Storage アカウント) に制約されます。 Azure Blob Storage と Data Lake Storage Gen2 では、このメカニズムがファイル システムのリソースにまで拡張されています。

Azure RBAC を使用したファイルのアクセス許可の詳細については、「[Azure のロールベースのアクセス制御 (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)」を参照してください。

ACL を使用したファイルのアクセス許可の詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md)」を参照してください。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 で自分のデータへのアクセスを制御する方法

Data Lake Storage Gen2 で HDInsight クラスターがファイルにアクセスする機能は、マネージド ID によって制御されます。 マネージド ID とは、資格情報が Azure によって管理されている Azure Active Directory (Azure AD) に登録されている ID です。 マネージド ID を使用すると、Azure AD 内でサービス プリンシパルを登録する必要はありません。 または、証明書などの資格情報を保持します。

Azure サービスには、システム割り当てとユーザー割り当ての 2 種類のマネージド ID があります。 HDInsight は、ユーザー割り当てマネージド ID を使用して Data Lake Storage Gen2 にアクセスします。 `user-assigned managed identity` はスタンドアロンの Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。

ユーザー割り当て ID のライフサイクルは、その ID が割り当てられている Azure サービス インスタンスのライフサイクルとは個別に管理されます。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hive またはその他のサービスを使用して Data Lake Storage Gen2 でデータをクエリするためのアクセス許可を Azure AD ユーザーに設定する方法

データをクエリするためのアクセス許可をユーザーに設定するには、ACL で割り当て済みのプリンシパルとして Azure AD セキュリティ グループを使用します。 個々のユーザーまたはサービス プリンシパルにファイルのアクセス許可を直接割り当てないでください。 アクセス許可のフローを制御する Azure AD セキュリティ グループを使用すると、ACL をディレクトリ構造全体に再適用することなく、ユーザーまたはサービス プリンシパルを追加および削除できます。 適切な Azure AD セキュリティ グループからユーザーまたはサービス プリンシパルを追加または削除するだけです。 ACL は継承されないため、ACL を再適用するには、すべてのファイルとサブディレクトリで ACL を更新する必要があります。

## <a name="access-files-from-the-cluster"></a>クラスターからファイルにアクセスする

複数の方法で、HDInsight クラスターから Data Lake Storage Gen2 のファイルにアクセスできます。

* **完全修飾名の使用**。 この方法により、アクセスするファイルへの完全パスを指定します。

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **短縮されたパスの使用**。 この方法により、クラスター ルートへのパスを次に置き換えます。

    ```
    abfs:///<file.path>/
    ```

* **相対パスの使用**。 この方法により、アクセスするファイルへの相対パスのみを指定します。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>データ アクセスの例

例は、クラスターのヘッド ノードへの [ssh 接続](./hdinsight-hadoop-linux-use-ssh-unix.md)に基づいています。 この例では、3 つの URI スキームがすべて使用されています。 `CONTAINERNAME` と `STORAGEACCOUNT` を関連する値に置き換えます。

#### <a name="a-few-hdfs-commands"></a>いくつかの hdfs コマンド

1. ローカル ストレージにファイルを作成します。

    ```bash
    touch testFile.txt
    ```

1. クラスター ストレージにディレクトリを作成します。

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. ローカル ストレージからクラスター ストレージにデータをコピーします。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. クラスター ストレージのディレクトリの内容を一覧表示します。

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive テーブルの作成

3 つのファイルの場所は説明目的で提示されています。 実際の実行では、`LOCATION` エントリを 1 つだけ使用します。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight と Data Lake Storage Gen2 プレビューとの統合 - ACL とセキュリティ更新プログラム](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
* [チュートリアル:Azure HDInsight で対話型クエリを使用してデータの抽出、変換、読み込みを行う](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
