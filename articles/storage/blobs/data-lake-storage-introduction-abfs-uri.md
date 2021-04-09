---
title: Azure Data Lake Storage Gen2 の URI を使用する
description: abfs スキーム識別子の URI 構文について説明します。これは、Azure Blob ファイル システム ドライバー (Azure Data Lake Storage Gen2 用 Hadoop ファイル システム ドライバー) を表します。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 948b5aa0ad015f9f3c693e13219ec034724687c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913166"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 の URI を使用する

Azure Data Lake Storage Gen2 と互換性のある [Hadoop ファイル システム](https://www.aosabook.org/en/hdfs.html) ドライバーは、そのスキーム識別子 `abfs` (Azure BLOB ファイル システム) によって認識されます。 他の Hadoop ファイル システム ドライバーと整合性のある ABFS ドライバーでは、Data Lake Storage Gen2 対応アカウント内のファイルとディレクトリをアドレス指定するために URI 形式が使用されています。

## <a name="uri-syntax"></a>URI 構文

Data Lake Storage Gen2 の URI 構文は、Data Lake Storage Gen2 を既定のファイル システムとして使用するようにストレージ アカウントが設定されているかどうかによって異なります。

アカウント作成時、アドレス指定する Data Lake Storage Gen2 対応アカウントが既定のファイル システムとして **設定されていない** 場合、簡潔な URI 構文は次のようになります。

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **スキーム識別子**:`abfs` プロトコルがスキーム識別子として使用されます。 接続時にトランスポート層セキュリティ (TLS) 接続 (以前は Secure Sockets Layer (SSL) として知られていました) を使用するかどうかを選択できます。 TLS 接続を使用して接続する場合は、`abfss` を使用します。

2. **ファイル システム**:ファイルとフォルダーを保持する親ロケーションです。 これは、Azure Storage Blob サービスのコンテナーと同じです。

3. **アカウント名**:作成時にストレージ アカウントに付与された名前です。

4. **パス**:スラッシュ (`/`) で区切って表現したディレクトリ構造です。

5. **ファイル名**:個々のファイルの名前です。 このパラメーターは、ディレクトリをアドレス指定する場合は省略可能です。

ただし、アカウント作成時、アドレス指定するアカウントが既定のファイル システムとして設定された場合、簡潔な URI 構文は次のようになります。

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **パス**:スラッシュ (`/`) で区切って表現したディレクトリ構造です。

2. **ファイル名**:個々のファイルの名前です。


## <a name="next-steps"></a>次のステップ

- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)