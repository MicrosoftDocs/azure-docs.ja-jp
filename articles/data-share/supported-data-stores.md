---
title: Azure Data Share でサポートされているデータ ストア
description: Azure Data Share での使用がサポートされているデータ ストアについて説明します。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 0e81d04edff667b0526f1d286701b2e8701528dc
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258599"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share でサポートされているデータ ストア

Azure Data Share は、さまざまなデータ ストアとの間で共有する機能など、オープンで柔軟なデータ共有を提供します。 データ プロバイダーは、1 つの種類のデータ ストアからデータを共有できます。また、データ コンシューマーは、データを受信するデータ ストアを選択できます。 

この記事では、Azure Data Share でサポートされている Azure データ ストアの豊富なセットについて説明します。 また、データ プロバイダーとデータ コンシューマーが活用できるデータ ストアの組み合わせに関する情報も確認できます。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure Data Share でサポートされているデータ ストア 

次の表は、Azure Data Share でサポートされているデータ ソースの詳細を示しています。 

| データ ストア | スナップショット ベースの共有 | インプレース共有 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL データベース |パブリック プレビュー | |
| Azure Synapse Analytics (以前の Azure SQL DW) |パブリック プレビュー | |
| Azure Data Explorer | |✓ |

## <a name="data-store-support-matrix"></a>データ ストアのサポート マトリックス

Azure Data Share では、データ コンシューマーがデータを受け入れるデータ ストアを柔軟に決定できます。 たとえば、Azure SQL Database から共有されているデータは、Azure Data Lake Store Gen2、Azure SQL Database、または Azure Synapse Analytics に受信できます。 お客様は、受信したデータ共有を構成するときに、データを受信する形式を選択できます。 

次の表では、データ共有を受け入れて構成する際にデータ コンシューマーが利用できるさまざまな組み合わせと選択肢の詳細を示しています。 データセット マッピングを構成する方法の詳細については、「[データセット マッピングを構成する方法](how-to-configure-mapping.md)」を参照してください。

| データ ストア | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL データベース | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure BLOB ストレージ | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL データベース | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (以前の Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有
Azure Data Share では、Azure Data Lake Gen1 および Azure Data Lake Gen2 からのファイル、フォルダー、ファイル システムの共有がサポートされています。 また、Azure Blob Storage からの BLOB、フォルダー、コンテナーの共有もサポートされています。 現時点では、ブロック BLOB のみがサポートされています。 スナップショット ベースの共有でファイル システム、コンテナー、またはフォルダーが共有されている場合、データ コンシューマーは、共有データの完全なコピーを作成するか、増分スナップショット機能を利用して新規または更新されたファイルのみをコピーするかを選択できます。 増分スナップショットは、ファイルの最終更新時刻に基づいています。 同じ名前の既存のファイルは上書きされます。

## <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有
Azure Data Share では、Azure SQL Database および Azure Synapse Analytics (旧称 Azure SQL DW) からのテーブルまたはビューの共有がサポートされています。 データ コンシューマーは、データを Azure Data Lake Store Gen2 または Azure Blob Storage に、csv または parquet ファイルとして受け入れることができます。 既定のファイル形式は csv であることに注意してください。 データ コンシューマーは、必要に応じて Parquet 形式でデータを受け取ることができます。 これは、データを受信するときにデータセットのマッピングの設定で行うことができます。 

データを Azure Data Lake Store Gen2 または Azure Blob Storage に受け入れると、完全なスナップショットによってターゲット ファイルの内容が上書きされます。 

データ コンシューマーは、選択したテーブルでデータを受け取ることができます。 このシナリオでは、ターゲット テーブルがまだ存在しない場合、Azure Data Share によってソース スキーマで SQL テーブルが作成されます。 受け入れ先テーブルと同じ名前のテーブルが既に存在する場合、そのテーブルは削除され、最新の完全スナップショットで上書きされます。 受け入れ先テーブルをマップするときに、代わりのスキーマとテーブル名を指定できます。 増分スナップショットは現在はサポートされていません。 

SQL ベースのソースからの共有には、ファイアウォール規則およびアクセス許可に関する前提条件があります。 詳しくは、[データの共有](share-your-data.md)に関するチュートリアルの前提条件のセクションをご覧ください。

## <a name="share-from-azure-data-explorer"></a>Azure Data Explorer からの共有
Azure Data Share では、Azure Data Explorer クラスターからデータベースをインプレース共有する機能がサポートされています。 データ プロバイダーは、データベース レベルまたはクラスター レベルで共有することができます。 データベース レベルで共有された場合、データ コンシューマーは、データ プロバイダーが共有した特定のデータベースにのみアクセスできます。 クラスター レベルで共有された場合、データ コンシューマーは、データ プロバイダーが今後作成するデータベースも含めて、プロバイダーのクラスターからすべてのデータベースにアクセスできます。

共有データベースにアクセスするには、データ コンシューマーが独自の Azure Data Explorer クラスターを持っている必要があります。 データ コンシューマーの Azure Data Explorer クラスターは、データ プロバイダーの Azure Data Explorer クラスターと同じ Azure データ センターに配置されている必要があります。 共有関係が確立されると、Azure Data Share によって、プロバイダーとコンシューマーの Azure Data Explorer クラスターの間にシンボリック リンクが作成されます。 ソースの Azure Data Explorer クラスターにバッチ モードを使用して取り込まれたデータは、数秒から数分以内にターゲット クラスターに表示されます。

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
