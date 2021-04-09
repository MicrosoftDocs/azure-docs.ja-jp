---
title: Azure HDInsight クラスターで使用するストレージ オプションを比較する
description: ストレージの種類と、それらが Azure HDInsight でどのように動作するかの概要を提供します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945858"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで使用するストレージ オプションを比較する

HDInsight クラスターを作成する際、次のいくつかの異なる Azure Storage サービスを選択できます。

* [HDInsight での Azure Blob Storage](./overview-azure-storage.md)
* [HDInsight での Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [HDInsight での Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

この記事では、これらのストレージの種類とそれらの固有の機能の概要を提供します。

## <a name="storage-types-and-features"></a>ストレージの種類と機能

次の表は、HDInsight のさまざまなバージョンでサポートされている Azure Storage サービスをまとめたものです。

| ストレージ サービス | アカウントの種類 | 名前空間の種類 | サポートされているサービス | サポートされているパフォーマンス レベル | サポートされているアクセス層 | HDInsight のバージョン | クラスターの種類 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 汎用 v2 | 階層構造 (ファイルシステム) | BLOB | Standard | ホット、クール、アーカイブ | 3.6 以降 | Spark 2.1 および 2.2 を除くすべて|
|Azure Storage| 汎用 v2 | Object | BLOB | Standard | ホット、クール、アーカイブ | 3.6 以降 | All |
|Azure Storage| 汎用 v1 | Object | BLOB | Standard | 該当なし | All | All |
|Azure Storage| Blob Storage** | Object | ブロック BLOB | Standard | ホット、クール、アーカイブ | All | All |
|Azure Data Lake Storage Gen1| 該当なし | 階層構造 (ファイルシステム) | 該当なし | 該当なし | 該当なし | 3.6 のみ | HBase を除くすべて |
|Azure Storage| ブロック BLOB| Object | ブロック BLOB | Premium | 該当なし| 3.6 以降 | 高速書き込みが可能なのは HBase のみ|
|Azure Data Lake Storage Gen2| ブロック BLOB| 階層構造 (ファイルシステム) | ブロック BLOB | Premium | 該当なし| 3.6 以降 | 高速書き込みが可能なのは HBase のみ|

** HDInsight クラスターの場合、セカンダリ ストレージ アカウントのみに BlobStorage の種類を使用できます。ページ BLOB は、サポートされるストレージ オプションではありません。

ストレージ アカウントの種類について詳しくは、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」をご覧ください。

Azure Storage アクセス層の詳細については、「[Azure Blob Storage:Premium ストレージ層 (プレビュー)、ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層](../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

プライマリと省略可能なセカンダリ ストレージ用のサービスの組み合わせを使用してクラスターを作成することができます。 次の表に、HDInsight で現在サポートされているクラスター ストレージの構成をまとめています。

| HDInsight のバージョン | プライマリ ストレージ | セカンダリ ストレージ | サポートされています |
|---|---|---|---|
| 3.6 と 4.0 | General Purpose V1、General Purpose V2 | General Purpose V1、General Purpose V2、BlobStorage (ブロック BLOB) | はい |
| 3.6 と 4.0 | General Purpose V1、General Purpose V2 | Data Lake Storage Gen2 | いいえ |
| 3.6 と 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | はい |
| 3.6 と 4.0 | Data Lake Storage Gen2* | General Purpose V1、General Purpose V2、BlobStorage (ブロック BLOB) | はい |
| 3.6 と 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | いいえ |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | はい |
| 3.6 | Data Lake Storage Gen1 | General Purpose V1、General Purpose V2、BlobStorage (ブロック BLOB) | はい |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | いいえ |
| 4.0 | Data Lake Storage Gen1 | Any | いいえ |
| 4.0 | General Purpose V1、General Purpose V2 | Data Lake Storage Gen1 | いいえ |

*=すべてがクラスター アクセスに同じマネージド ID を使用するように設定されている限り、これは 1 つ以上の Data Lake Storage Gen2 である可能性があります。

> [!NOTE]
> Data Lake Storage Gen2 プライマリ ストレージは、Spark 2.1 または 2.2 クラスターではサポートされていません。

## <a name="data-replication"></a>データのレプリケーション

Azure HDInsight では顧客データを格納しません。 クラスターでの主な格納手段は、それに関連付けられているストレージ アカウントです。 クラスターを既存のストレージ アカウントにアタッチすることも、クラスターの作成プロセス中に新しいストレージ アカウントを作成することもできます。 新しいアカウントが作成されると、そのアカウントはローカル冗長ストレージ (LRS) アカウントとして作成され、リージョン内データ所在地要件 ([セキュリティ センター](https://azuredatacentermap.azurewebsites.net)で指定されたものを含む) を満たします。

HDInsight が 1 つのリージョンにデータを格納するように正しく構成されていることを検証するには、HDInsight に関連付けられているストレージ アカウントが LRS であるか、[セキュリティ センター](https://azuredatacentermap.azurewebsites.net)に記載された別のストレージ オプションであることを確認します。
 
## <a name="next-steps"></a>次のステップ

* [HDInsight での Azure Storage の概要](./overview-azure-storage.md)
* [HDInsight での Azure Data Lake Storage Gen1 の概要](./overview-data-lake-storage-gen1.md)
* [HDInsight での Azure Data Lake Storage Gen2 の概要](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
* [Azure ストレージの概要](../storage/common/storage-introduction.md)
