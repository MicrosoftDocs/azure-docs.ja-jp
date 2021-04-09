---
title: HDInsight での Azure Data Lake Storage Gen2 の概要
description: HDInsight での Data Lake Storage Gen2 の概要。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938860"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>HDInsight での Azure Data Lake Storage Gen2 の概要

Azure Data Lake Storage Gen2 は、Azure Data Lake Storage Gen1 のコア機能を取得し、それらを Azure Blob Storage に統合します。 これらの機能には、Hadoop と互換性のあるファイル システム、Azure Active Directory (Azure AD)、および POSIX ベースのアクセス制御リスト (ACL) などがあります。 この組み合わせにより、Azure Data Lake Storage Gen1 のパフォーマンスを活用できます。 また、BLOB ストレージの階層化とデータのライフサイクル管理も使用できます。

Azure Data Lake Storage Gen2 の詳細については、「[Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)」を参照してください。

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のコア機能

* **Hadoop と互換性のあるアクセス:** Azure Data Lake Storage Gen2 では、Hadoop 分散ファイル システム (HDFS) を利用する場合と同様に、データの管理およびアクセスが可能です。 Azure BLOB ファイル システム (ABFS) ドライバーは、Azure HDInsight や Azure Databricks を含むすべての Apache Hadoop 環境内で使用できます。 Data Lake Storage Gen2 に格納されているデータにアクセスするには、ABFS を使用します。

* **POSIX アクセス許可のスーパーセット**:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、管理ツールまたはフレームワーク (Apache Hive や Apache Spark など) を通じて構成できます。

* **コスト効率**:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 Azure Blob Storage のライフサイクルは、データのライフサイクルの進展に応じて請求金額を調整することで、コストの削減に役立ちます。

* **BLOB ストレージ ツール、フレームワーク、およびアプリとの互換性**:Data Lake Storage Gen2 では、BLOB ストレージ用の多様なツール、フレームワーク、およびアプリケーションを継続的に利用できます。

* **最適化されたドライバー**:ABFS ドライバーは、ビッグ データ分析のために特別に最適化されています。 対応する REST API は、分散ファイル システム (DFS) エンドポイント dfs.core.windows.net を介して表示されます。

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2 の新機能

### <a name="managed-identities-for-secure-file-access"></a>セキュア ファイル アクセスのためのマネージド ID

Azure HDInsight は、マネージド ID を使用して、Azure Data Lake Storage Gen2 内のファイルへのクラスター アクセスをセキュリティで保護します。 マネージド ID は、Azure サービスに自動的に管理される一連の資格情報を提供する Azure Active Directory の機能です。 これらの資格情報は、Active Directory 認証をサポートする任意のサービスを認証するために使用できます。 マネージド ID の使用には、コードまたは構成ファイルに資格情報を格納する必要はありません。

詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

### <a name="azure-blob-file-system-driver"></a>Azure BLOB ファイル システム ドライバー

Apache Hadoop アプリケーションは、ローカル ディスク ストレージからネイティブにデータを読み書きすることを想定しています。 ABFS などの Hadoop ファイル システム ドライバーにより、Hadoop アプリケーションがクラウド ストレージと連携できるようになります。 通常の Hadoop ファイル システム操作をエミュレートすることによって機能します。 このドライバーは、アプリケーションから受信されたコマンドを実際のクラウド ストレージ プラットフォームで認識される操作に変換します。

以前は、Hadoop ファイル システム ドライバーは、すべてのファイルシステム操作をクライアント側での Azure Storage REST API 呼び出しに変換していました。 その後、REST API を呼び出していました。 しかし、このクライアント側の変換では、ファイルの名前変更のような 1 つのファイルシステム操作に対して複数の REST API が呼び出されていました。 ABFS は、Hadoop ファイル システムのロジックをクライアント側からサーバー側に移行しました。 Azure Data Lake Storage Gen2 API は、Blob API と並列実行されます。 この移行により、Hadoop ファイルシステムの一般的な操作が 1 つの REST API 呼び出しで実行できるようになったため、パフォーマンスが向上します。

詳細については、「[The Azure Blob Filesystem driver (ABFS):A dedicated Azure Storage driver for Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md)」 (Azure BLOB ファイルシステム ドライバー (ABFS): Hadoop 専用の Azure Storage ドライバー) を参照してください。

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 の URI スキーム

Azure Data Lake Storage Gen2 は、HDInsight から Azure Storage 内のファイルにアクセスするための新しい URI スキームを使用します。

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI スキームでは、SSL で暗号化されたアクセスが提供されます。

`<FILE_SYSTEM_NAME>` には、Data Lake Storage Gen2 ファイル システムのパスを指定します。

`<ACCOUNT_NAME>` には、Azure Storage アカウント名を指定します。 完全修飾ドメイン名 (FQDN) を指定する必要があります。

`<PATH>` は、ファイルまたはディレクトリの HDFS パス名です。

`<FILE_SYSTEM_NAME>` と `<ACCOUNT_NAME>` の値が指定されていない場合、既定のファイルシステムが使用されます。 既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用します。 たとえば、HDInsight クラスターに付属している `hadoop-mapreduce-examples.jar` ファイルは、次のいずれかのパスを使用して参照できます。

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は `hadoop-examples.jar` です。 HDInsight の外部からファイルを操作する場合、ほとんどのユーティリティで ABFS 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

詳細については、「[Use the Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)」 (Azure Data Lake Storage Gen2 の URI を使用する) を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
* [Azure ストレージの概要](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1 の概要](./overview-data-lake-storage-gen1.md)