---
title: Azure Data Share でサポートされているデータ ストア
description: Azure Data Share での使用がサポートされているデータ ストアについて説明します。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963681"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share でサポートされているデータ ストア

Azure Data Share は、さまざまなデータ ストアとの間で共有する機能など、オープンで柔軟なデータ共有を提供します。 データ プロバイダーは、1 つの種類のデータ ストアからデータを共有できます。また、データ コンシューマーは、データを受信するデータ ストアを選択できます。 

この記事では、Azure Data Share でサポートされている Azure データ ストアの豊富なセットについて説明します。 また、データ プロバイダーとデータ コンシューマーがさまざまなデータ ストアを組み合わせる方法についても説明します。 

## <a name="supported-data-stores"></a>サポートされているデータ ストア 

次の表では、Azure Data Share でサポートされるデータ ストアについて説明します。 

| データ ストア | 完全なスナップショットに基づく共有 | 増分スナップショットに基づく共有 | 場所に基づく共有 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL データベース |✓ | | |
| Azure Synapse Analytics (旧称 Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics (ワークスペース) の専用 SQL プール |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>データ ストアのサポート マトリックス

Azure Data Share を使用すると、データ コンシューマーはデータを受け入れるデータ ストアを選択できます。 たとえば、Azure SQL Database から共有されているデータは、Azure Data Lake Storage Gen2、Azure SQL Database、または Azure Synapse Analytics で受信できます。 顧客は、受信先のデータ共有を設定するときに、データを受信する形式を選択できます。 

次の表では、データ コンシューマーがデータ共有の受け入れと構成を行うときに選択できる組み合わせとオプションについて説明します。 詳細については、[データ セットのマッピングの構成](how-to-configure-mapping.md)に関するページを参照してください。

| データ ストア | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (旧称 SQL Data Warehouse) | Synapse Analytics (ワークスペース) の専用 SQL プール | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (旧称 SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (ワークスペース) の専用 SQL プール | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>ストレージ アカウントからの共有
Azure Data Share では、Azure Data Lake Storage Gen1 および Azure Data Lake Storage Gen2 からのファイル、フォルダー、ファイル システムの共有がサポートされています。 また、Azure Blob Storage からの BLOB、フォルダー、コンテナーの共有もサポートされています。 現時点では、ブロック BLOB のみがサポートされています。 

ファイル システム、コンテナー、またはフォルダーがスナップショットベースの共有で共有されている場合、データ コンシューマーは共有データの完全なコピーを作成することを選択できます。 または、増分スナップショット機能を使用して、新しいファイルまたは更新されたファイルのみをコピーすることもできます。 

増分スナップショットは、ファイルの最終更新時刻に基づいています。 受信したデータ内のファイルと同じ名前を持つ既存のファイルは、スナップショットで上書きされます。 ソースから削除されたファイルは、ターゲットで削除されません。 

詳しくは、「[Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する](how-to-share-from-storage.md)」を参照してください。

## <a name="share-from-a-sql-based-source"></a>SQL ベースのソースからの共有
Azure Data Share では、Azure SQL Database および Azure Synapse Analytics (旧称 Azure SQL Data Warehouse) からのテーブルとビューの両方の共有がサポートされています。 Azure Synapse Analytics (ワークスペース) の専用 SQL プールからのテーブルの共有がサポートされています。 Azure Synapse Analytics (ワークスペース) サーバーレス SQL プールからの共有は、現在サポートされていません。 

データ コンシューマーは、データを Azure Data Lake Storage Gen2 または Azure Blob Storage に、CSV ファイルまたは parquet ファイルとして受け入れることができます。 また、データをテーブルとして Azure SQL Database と Azure Synapse Analytics に受け入れることもできます。

コンシューマーがデータを Azure Data Lake Storage Gen2 または Azure Blob Storage に受け入れると、完全なスナップショットによってターゲット ファイル (存在する場合) の内容が上書きされます。 データがテーブルに受け入れられるときにターゲット テーブルがまだ存在しない場合は、Azure Data Share によってソース スキーマを使用して SQL テーブルが作成されます。 ターゲット テーブルが既に存在して同じ名前を持つ場合、それは削除され、最新の完全なスナップショットで上書きされます。 増分スナップショットは現在はサポートされていません。

詳細については、「[Azure SQL Database と Azure Synapse Analytics からのデータの共有と受信](how-to-share-from-sql.md)」を参照してください。

## <a name="share-from-data-explorer"></a>Data Explorer からの共有
Azure Data Share では、Azure Data Explorer クラスターからデータベースをインプレース共有する機能がサポートされています。 データ プロバイダーは、データベースまたはクラスターのレベルで共有することができます。 

データがデータベース レベルで共有されている場合、データ コンシューマーは、データ プロバイダーが共有したデータベースにのみアクセスできます。 プロバイダーがデータをクラスター レベルで共有した場合、データ コンシューマーは、データ プロバイダーが今後作成するデータベースも含めて、プロバイダーのクラスターからすべてのデータベースにアクセスできます。

共有データベースにアクセスするには、データ コンシューマーには独自の Azure Data Explorer クラスターが必要です。 これらのクラスターは、データ プロバイダーの Azure Data Explorer クラスターと同じ Azure データ センター内に存在する必要があります。 

共有関係が確立されると、Azure Data Share によって、プロバイダーのクラスターとコンシューマーのクラスターの間にシンボリック リンクが作成されます。 バッチ モードを使用してソース クラスターに取り込まれたデータは、数分以内にターゲット クラスターに表示されます。

詳細については、[Azure Data Explorer からのデータの共有および受信](/azure/data-explorer/data-share)に関する記事をご覧ください。 

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
