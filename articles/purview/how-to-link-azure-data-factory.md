---
title: Azure Data Factory に接続する
description: この記事では、データ系列を追跡するために Azure Data Factory と Azure Purview を接続する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: 0e993cb1e53645f7081a20fc6a2785b8cfef1cce
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954186"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Azure Data Factory と Azure Purview を接続する方法

このドキュメントでは、データ系列を追跡するために Azure Data Factory アカウントと Azure Purview アカウントを接続するために必要な手順について説明します。 また、対象範囲スコープとサポートされる系列パターンの詳細についても説明します。

## <a name="view-existing-data-factory-connections"></a>既存の Data Factory 接続を表示する

系列情報をプッシュするために、複数の Azure Data Factory を 1 つの Azure Purview Data Catalog に接続できます。 現在の制限では、Purview の管理センターから一度に最大 10 個の Data Factory アカウントを接続できます。 Purview Data Catalog に接続されている Data Factory アカウントの一覧を表示するには、次の操作を行います。

1. 左側のナビゲーション ウィンドウで **[管理センター]** を選択します。
2. **[外部接続]** で **[Data Factory 接続]** を選択します。
3. Data Factory 接続の一覧が表示されます。

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Data Factory 接続の一覧を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 次の接続の **[状態]** のさまざまな値に注意してください。

    - **接続済み**: Data Factory はデータ カタログに接続されています。
    - **切断**: Data Factory はカタログにアクセスできますが、別のカタログに接続されています。 その結果、データ系列がこのカタログに自動的には報告されません。
    - **アクセス不可**: 現在のユーザーは Data Factory にアクセスできないため、接続の状態は不明です。
 >[!Note]
 >Data Factory 接続を表示するには、次の Purview ロールのいずれかが割り当てられている必要があります。
 >- Contributor
 >- 所有者
 >- Reader
 >- User Access Administrator

## <a name="create-new-data-factory-connection"></a>新しい Data Factory 接続を作成する

>[!Note]
>Data Factory 接続を追加または削除するには、次の Purview ロールのいずれかが割り当てられている必要があります。
>- 所有者
>- User Access Administrator
>
> さらに、ユーザーはデータ ファクトリの "所有者" または "共同作成者" である必要があります。 

既存の Data Factory アカウントを Purview Data Catalog に接続するには、次の手順に従います。

1. 左側のナビゲーション ウィンドウで **[管理センター]** を選択します。
2. **[外部接続]** で **[Data Factory 接続]** を選択します。
3. **[Data Factory connection] (Data Factory 接続)** ページで **[新規]** を選択します。

4. 一覧から Data Factory アカウントを選択し、 **[OK]** を選択します。 この一覧を制限するために、サブスクリプション名でフィルター処理することもできます。

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Azure Data Factory を接続する方法を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    Data Factory が現在の Purview アカウントに既に接続されているか、または Data Factory にマネージド ID がない場合は、一部の Data Factory インスタンスが無効になっている可能性があります。

    選択されている Data Factory のいずれかが他の Purview アカウントに既に接続されている場合は、警告メッセージが表示されます。 [OK] を選択すると、他の Purview アカウントとの Data Factory 接続は切断されます。 追加の確認は必要ありません。


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory を切断するという警告を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>一度に 10 個までの Data Factory の追加がサポートされるようになりました。 一度に 10 個を超える Data Factory を追加する場合は、サポート チケットを提出してください。

### <a name="how-does-the-authentication-work"></a>認証のしくみ

Purview ユーザーがアクセス権を持つ Data Factory を登録すると、バックエンドで以下が実行されます。

1. **Data Factory MSI** が次の Purview RBAC ロールに追加されます: **Purview データ キュレーター**。

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="Azure Data Factory MSI を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. 系列メタデータを Purview にプッシュバックできるようにするために、Data Factory パイプラインを再実行する必要があります。
3. 実行後に Data Factory メタデータが Purview にプッシュされます。

### <a name="remove-data-factory-connections"></a>Data Factory 接続を削除する
Data Factory 接続を削除するには、次の操作を行います。

1. **[Data Factory connection] (Data Factory 接続)** ページで、1 つ以上の Data Factory 接続の横にある **[削除]** ボタンを選択します。
2. ポップアップの **[確認]** を選択して、選択されている Data Factory 接続を削除します。

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="接続を削除する Data Factory を選択する方法を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-ir-to-collect-lineage-from-on-prem-sql"></a>オンプレミスの SQL から系列を収集するようにセルフホステッド IR を構成する

Data Factory のコピー アクティビティの系列は、オンプレミスの SQL データベースで使用できます。 Azure Data Factory でのデータ移動のためにセルフホステッド統合ランタイムを実行しており、Azure Purview で系列をキャプチャしたい場合は、バージョンが 4.8.7418.1 以降であることを確認してください。 セルフホステッド統合ランタイムの詳細については、「[セルフホステッド統合ランタイムを作成して構成する](../data-factory/create-self-hosted-integration-runtime.md)」を参照してください。

## <a name="supported-azure-data-factory-activities"></a>サポートされる Azure Data Factory アクティビティ

Azure Purview は、次の Azure Data Factory アクティビティからランタイムの系列をキャプチャします。

- データをコピーする
- Data Flow
- SSIS パッケージの実行

> [!IMPORTANT]
> ソースまたは宛先でサポートされていないデータ ストレージ システムが使用されている場合、Azure Purview は系列を削除します。

以降のセクションで説明されているように、Data Factory と Purview の間の統合では、Data Factory がサポートするデータ システムのサブセットのみがサポートされています。

### <a name="data-factory-copy-data-support"></a>Data Factory のデータ コピーのサポート

| データ ストレージ システム | ソースとしてサポート | シンクとしてサポート |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | はい | ○ (非バイナリ コピーのみ) |
| ADLS Gen2 | はい | はい |
| Azure BLOB | はい | はい |
| Azure Cosmos DB (SQL API) | はい | はい |
| Azure Cosmos DB (Mongo API) | はい | はい |
| Azure Cognitive Search | はい | はい |
| Azure Data Explorer | はい | はい |
| Azure Database for Maria DB \* | はい | はい |
| Azure Database for MYSQL \* | はい | はい |
| Azure Database for PostgreSQL \* | はい | はい |
| Azure File Storage | はい | はい |
| Azure Table Storage | はい | はい |
| Azure SQL Database \* | はい | はい |
| Azure SQL MI \* | はい | はい |
| Azure Synapse Analytics (以前の SQL DW) \* | はい | はい |
| オンプレミスの SQL Server (SHIR が必要) \* | はい | はい |
| Amazon S3 | はい | はい |
| Teradata | はい | はい |
| SAP s4 Hana | はい | はい |
| SAP ECC | はい | はい |
| Hive | はい | はい |

> [!Note]
> Data Factory のコピー アクティビティでは、系列の機能で特定のパフォーマンスのオーバーヘッドが発生します。 Purview で Data Factory 接続を設定している場合は、特定のコピー ジョブが完了するまでの時間が長くなることがあります。 この影響は多くの場合、とても無視できません。 コピー ジョブが完了するまでの時間が通常より大幅に長くなっている場合は、その時間の比較をサポートに連絡してください。

### <a name="data-factory-data-flow-support"></a>Data Factory Data Flow のサポート

| データ ストレージ システム | サポートされています |
| ------------------- | ------------------- | ----------------- |
| ADLS Gen1 | はい |
| ADLS Gen2 | はい |
| Azure BLOB | はい |
| Azure SQL Database \* | はい |
| Azure Synapse Analytics (以前の SQL DW) \* | はい |

### <a name="data-factory-execute-ssis-package-support"></a>Data Factory の SSIS パッケージの実行のサポート

| データ ストレージ システム | サポートされています |
| ------------------- | ------------------- | ----------------- |
| Azure BLOB | はい |
| ADLS Gen1 | はい |
| ADLS Gen2 | はい |
| Azure SQL Database \* | はい |
| Azure SQL MI \*| はい |
| Azure Synapse Analytics (以前の SQL DW) \* | はい |
| オンプレミスの SQL Server \* | はい |
| Azure File Storage | はい |

*\* SQL (Azure およびオンプレミス) のシナリオの場合、Azure Purview は、系列またはスキャンのためのストアド プロシージャやスクリプトをサポートしていません。系列は、テーブルとビューのソースだけに制限されています。*

> [!Note]
> Azure Data Lake Storage Gen2 の一般提供が開始されました。 今すぐ使用を開始することをお勧めします。 詳細については、[製品に関するページ](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)を参照してください。

## <a name="supported-lineage-patterns"></a>サポートされる系列パターン

Azure Purview でサポートされる系列のパターンはいくつかあります。 生成される系列データは、Data Factory アクティビティで使用されるソースとシンクの種類に基づいています。 Data Factory は 80 を超えるソースとシンクをサポートしていますが、Azure Purview では、「[サポートされる Azure Data Factory アクティビティ](#supported-azure-data-factory-activities)」に示されているサブセットのみがサポートされています。

系列情報を送信するように Data Factory を構成するには、「[系列の使用を開始する](catalog-lineage-user-guide.md#get-started-with-lineage)」を参照してください。

系列ビューの情報を検索するための追加の方法には、次のようないくつかのものがあります。

- **[系列]** タブで、図形をマウスでポイントすると、そのアセットに関する追加情報がツールヒントにプレビューされます。
- ノードまたはエッジを選択すると、それが属するアセットの種類が表示されるか、またはアセットが切り替えられます。
- **[系列]** タブの左側には、データセットの列が表示されます。列レベルの系列の詳細については、「[列レベルの系列](catalog-lineage-user-guide.md#column-level-lineage)」を参照してください。

### <a name="data-lineage-for-11-operations"></a>1:1 の操作のデータ系列

データ系列をキャプチャするための最も一般的なパターンは、1 つの入力データセットから 1 つの出力データセットに、その間に 1 つのプロセスを使用してデータを移動することです。

このパターンの例には、次のものがあります。

- 1 つのソース/入力: *Customer* (SQL テーブル)
- 1 つのシンク/出力: *Customer1.csv* (Azure BLOB)
- 1 つのプロセス: *CopyCustomerInfo1\#Customer1.csv* (Data Factory のコピー アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="1:1 の Data Factory のコピー操作の系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 の系列とワイルドカードのサポートを使用したデータ移動

系列をキャプチャするためのもう 1 つの一般的なシナリオは、ワイルドカードを使用して、1 つの入力データセットから 1 つの出力データセットにファイルをコピーすることです。 ワイルドカードにより、コピー アクティビティはファイル名の共通の部分を使用して、コピーする複数のファイルを一致させることができます。 Azure Purview は、対応するコピー アクティビティによってコピーされた個々のファイルのファイル レベルの系列をキャプチャします。

このパターンの例には、次のものがあります。

* ソース/入力: *CustomerCall\*.csv* (ADLS Gen2 パス)
* シンク/出力: *CustomerCall\*.csv* (Azure BLOB ファイル)
* 1 つのプロセス: *CopyGen2ToBlob\#CustomerCall.csv* (Data Factory のコピー アクティビティ)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="ワイルドカードのサポートを使用した 1:1 のコピー操作の系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>n:1 の系列を使用したデータ移動

Data Flow アクティビティを使用してデータ操作 (merge や join など) を実行できます。 複数のソース データセットを使用してターゲット データセットを生成できます。 この例では、Azure Purview は、個々の入力ファイルのファイル レベルの系列を Data Flow アクティビティの一部である SQL テーブルにキャプチャします。

このパターンの例には、次のものがあります。

* 2 つのソース/入力: *Customer.csv*、*Sales.parquet* (ADLS Gen2 パス)
* 1 つのシンク/出力: *Company data* (Azure SQL テーブル)
* 1 つのプロセス: *DataFlowBlobsToSQL* (Data Factory Data Flow アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="n:1 の A D F Data Flow 操作の系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>リソース セットの系列

リソース セットは、基になるストレージ内の多数のパーティション ファイルを表す、カタログ内の論理オブジェクトです。 詳細については、「[リソース セットについて](concept-resource-sets.md)」を参照してください。 Azure Purview は、Azure Data Factory から系列をキャプチャするときに、個々のパーティション ファイルを正規化して 1 つの論理オブジェクトを作成するためのルールを適用します。

次の例では、Azure BLOB から Azure Data Lake Gen2 リソース セットが生成されます。

* 1 つのソース/入力: *Employee\_management.csv* (Azure BLOB)
* 1 つのシンク/出力: *Employee\_management.csv* (Azure Data Lake Gen 2)
* 1 つのプロセス: *CopyBlobToAdlsGen2\_RS* (Data Factory のコピー アクティビティ)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="リソース セットの系列を示すスクリーンショット。" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>次のステップ

- [カタログ系列ユーザー ガイド](catalog-lineage-user-guide.md)
- [系列のための Azure Data Share へのリンク](how-to-link-azure-data-share.md)
