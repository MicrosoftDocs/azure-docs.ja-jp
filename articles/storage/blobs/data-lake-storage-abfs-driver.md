---
title: Azure Data Lake Storage Gen2 用の Azure BLOB ファイルシステム ドライバー
description: ABFS Hadoop ファイルシステム ドライバー
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 50d39aa3c6341e61e383c5584ab2992e3fea3189
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278086"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure BLOB ファイルシステム ドライバー (ABFS):Hadoop 専用の Azure Storage ドライバー

Azure Data Lake Storage Gen2 内のデータの主要なアクセス方法の 1 つは、[Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) です。 Data Lake Storage Gen2 により、Azure Blob Storage のユーザーは、新しいドライバーである Azure BLOB ファイル システム ドライバー (`ABFS`) にアクセスできます。 ABFS は、Apache Hadoop の一部であり、Hadoop の商用ディストリビューションの多くに含まれています。 このドライバーを使用すると、多くのアプリケーションとフレームワークは、Data Lake Storage Gen2 を明示的に参照するコードがなくても Azure Blob Storage 内のデータにアクセスできます。

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>以前の機能:Windows Azure Storage Blob ドライバー

Windows Azure Storage Blob ドライバー ([WASB ドライバー](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)) は、Azure Blob Storage のオリジナル サポートを提供しました。 このドライバーは、(Hadoop FileSystem インターフェイスでの必要に応じて) Azure Blob Storage によって公開されているオブジェクト ストア スタイルのインターフェイスのセマンティクスへのファイル システム セマンティクスのマッピングの複雑なタスクを実行しました。 このドライバーは引き続きこのモデルをサポートし、BLOB に格納されたデータへの高パフォーマンス アクセスを実現しますが、このマッピングを実行するコードが大量に含まれているため、メンテナンスが困難になります。 さらに、[FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) や [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) などの一部の操作がディレクトリに適用される場合、(オブジェクト ストアにディレクトリのサポートがないため) ドライバーが膨大な数の操作を実行する必要があり、多くの場合にパフォーマンスの低下につながります。 ABFS ドライバーは、WASB の本質的な弱点を克服するために設計されました。

## <a name="the-azure-blob-file-system-driver"></a>Azure BLOB ファイル システム ドライバー

[Azure Data Lake Storage REST インターフェイス](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)は Azure Blob Storage 上のファイル システムのセマンティクスをサポートするように設計されています。 Hadoop FileSystem も同じセマンティクスをサポートするように設計されている場合、ドライバー内での複雑なマッピングは必要ありません。 したがって、Azure BLOB ファイル システム ドライバー (または ABFS) は、REST API の単なるクライアント shim です。

ただし、ドライバーが実行する必要のある機能がいくつかあります。

### <a name="uri-scheme-to-reference-data"></a>参照データへの URI スキーム

Hadoop 内の他のファイルシステムの実装と一貫性のある ABFS ドライバーは、リソース (ディレクトリとファイル) を明確にアドレス指定できるように独自の URI スキームを定義します。 URI スキームは、「[Use the Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)」(Azure Data Lake Storage Gen2 URI の使用) に記載されています。 URI の構造は次のとおりです: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

上記の URI 形式を使用して、標準の Hadoop ツールとフレームワークを使用してこれらのリソースを参照できます。

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

内部的には、ABFS ドライバーは、URI で指定されたリソースをファイルとディレクトリに変換し、これらの参照で Azure Data Lake Storage REST API を呼び出します。

### <a name="authentication"></a>Authentication

Hadoop アプリケーションが Data Lake Storage Gen2 対応アカウントに含まれるリソースに安全にアクセスできるように、ABFS ドライバーは 2 つの認証形式をサポートしています。 使用できる認証方式の詳細については、「[Azure Storage セキュリティ ガイド](../common/storage-security-guide.md)」を参照してください。 次に例を示します。

- **共有キー:** このキーを使用すると、ユーザーはアカウント内のすべてのリソースにアクセスできます。 キーが暗号化され、Hadoop 構成に格納されます。

- **Azure Active Directory OAuth ベアラー トークン:** Azure AD ベアラー トークンは、エンド ユーザーの ID または構成されているサービス プリンシパルのいずれかを使用してドライバーによって取得および更新されます。 この認証モデルを使用すると、すべてのアクセスは、指定したトークンに関連付けられた ID を使用して呼び出しごとに承認され、割り当てられた POSIX アクセス制御リスト (ACL) に対して評価されます。

### <a name="configuration"></a>構成

ABFS ドライバーのすべての構成は <code>core-site.xml</code> 構成ファイルに格納されます。 [Ambari](https://ambari.apache.org/) を備えた Hadoop ディストリビューションでは、構成は Web ポータルまたは Ambari REST API を使用して管理することもできます。

サポートされるすべての構成エントリの詳細は、[公式 Hadoop ドキュメント](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html)で指定されています。

### <a name="hadoop-documentation"></a>Hadoop ドキュメント

ABFS ドライバーについては、[公式 Hadoop ドキュメント](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html)に完全に記載されています

## <a name="next-steps"></a>次の手順

- [Azure Databricks クラスターの作成](./data-lake-storage-quickstart-create-databricks-account.md)
- [Azure Data Lake Storage Gen2 URI の使用](./data-lake-storage-introduction-abfs-uri.md)
