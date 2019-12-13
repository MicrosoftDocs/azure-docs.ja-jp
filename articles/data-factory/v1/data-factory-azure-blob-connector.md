---
title: Azure Blob Storage との間でデータをコピーする
description: Azure Data Factory で BLOB データをコピーする方法について説明します。 サンプルを使用して、Azure Blob Storage と Azure SQL Database の間でデータをコピーする方法を示します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cb78be4456864e28c5559febf9733d7dc9a5029f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930185"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Blob Storage との間でのデータのコピー
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-blob-storage.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure Blob Storage コネクタ](../connector-azure-blob-storage.md)に関するページを参照してください。


この記事では、Azure Data Factory のコピー アクティビティを使って、Azure Blob Storage との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

## <a name="overview"></a>概要
サポートされる任意のソース データ ストアのデータを、Azure Blob Storage にコピーしたり、Azure Blob Storage のデータを、サポートされる任意のシンク データ ストアにコピーしたりできます。 次の表は、コピー アクティビティによってコピー元またはシンクとしてサポートされているデータ ストアの一覧です。 たとえば、SQL Server データベースまたは Azure SQL データベース**から** Azure Blob Storage **に**データを移動できます。 また、Azure Blob Storage **から** Azure SQL Data Warehouse または Azure Cosmos DB コレクション**に**データをコピーできます。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>サポートされるシナリオ
**Azure Blob Storage から**以下のデータ ストアにデータをコピーできます。

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

以下のデータ ストアから **Azure Blob Storage に**データをコピーできます。

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> コピー アクティビティによって、汎用 Azure Strage アカウントとコールド/ホット Blob Storage との間でデータをコピーできるようになりました。 このアクティビティは、**ブロック BLOB、追加 BLOB、ページ BLOB** を読み取りますが、**書き込みはブロック BLOB のみ**をサポートしています。 Azure Premium Storage はページ BLOB によって提供されるため、シンクとしてはサポートされていません。
>
> コピー アクティビティでは、コピー先にデータが正常にコピーされた後に、ソースからデータが削除されることはありません。 コピーが成功した後、コピー元データを削除する必要がある場合は、ファイルを削除する[カスタム アクティビティ](data-factory-use-custom-activities.md)を作成し、パイプライン内でそのアクティビティを使用します。 例については、「[Delete blob or folder sample on GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity)」(GitHub の BLOB またはフォルダーのサンプルを削除する) を参照してください。

## <a name="get-started"></a>作業開始
さまざまなツール/API を使用して、Azure BLOB ストレージとの間でデータを移動するコピー アクティビティでパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 この記事には、Azure Blob Storage の場所から別の Azure Blob Storage の場所にデータをコピーするためのパイプラインの作成に関する[チュートリアル](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage)が含まれます。 Azure Blob Storage から Azure SQL Database にデータをコピーするためのパイプラインの作成に関するチュートリアルについては、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **Data Factory**を作成します。 データ ファクトリには、1 つまたは複数のパイプラインを設定できます。
2. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。 たとえば、Azure Blob Storage から Azure SQL データベースにデータをコピーする場合、リンクされたサービスを 2 つ作成して、Azure ストレージ アカウントと Azure SQL データベースをデータ ファクトリにリンクします。 Azure Blob Storage に固有のリンクされたサービスのプロパティについては、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 最後の手順で説明されている例では、データセットを作成して入力データを含む BLOB コンテナーとフォルダーを指定します。 また、もう 1 つのデータセットを作成して、Blob Storage からコピーされたデータを保持する Azure SQL データベースの SQL テーブルを指定します。 Azure Blob Storage に固有のデータセットのプロパティについては、「[データセットのプロパティ](#dataset-properties)」セクションを参照してください。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 前に説明した例では、コピー アクティビティのソースとして BlobSource を、シンクとして SqlSink を使います。 同様に、Azure SQL Database から Azure Blob Storage にコピーする場合は、SqlSource と BlobSink をコピー アクティビティで使います。 Azure Blob Storage に固有のコピー アクティビティのプロパティについては、「[コピー アクティビティのプロパティ](#copy-activity-properties)」セクションを参照してください。 ソースまたはシンクとしてデータ ストアを使う方法について詳しくは、前のセクションのデータ ストアのリンクをクリックしてください。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  Azure Blob Storage との間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples-for-copying-data-to-and-from-blob-storage  )」を参照してください。

次のセクションでは、Azure Blob Storage に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
Azure Storage を Azure Data Factory にリンクするときに使用できるリンクされたサービスは 2 種類あります。 次に例を示します。それらは、**AzureStorage** のリンクされたサービスと **AzureStorageSas** のリンクされたサービスです。 Azure Storage のリンクされたサービスは、Azure Storage へのグローバル アクセスを Data Factory に提供します。 一方、Azure Storage SAS (Shared Access Signature) のリンクされたサービスは、Azure Storage への制限付き/期限付きアクセスを Data Factory に提供します。 これら 2 つのリンクされたサービスには、これ以外の相違点はありません。 ニーズに適したリンクされたサービスを選択します。 以下のセクションで、これら 2 つのリンクされたサービスについて詳しく説明します。

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>データセットのプロパティ
データセットを指定して Azure Blob Storage の入力データまたは出力データを表すには、そのデータセットの type プロパティを **AzureBlob** に設定します。 また、データセットの **linkedServiceName** プロパティは、Azure Storage または Azure Storage SAS のリンクされたサービスの名前に設定します。  データセットの type プロパティでは、Blob Storage の **BLOB コンテナー**と**フォルダー**を指定します。

データセットの定義に利用できる JSON のセクションとプロパティの完全一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

Data Factory は、Azure BLOB などの読み取りデータ ソースのスキーマに "structure" で型情報を提供する場合、次の CLS に準拠している .NET ベースの型値をサポートしています。Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Bool、String、Guid、Datetime、Datetimeoffset、Timespan。 また、ソース データ ストアのデータをシンク データ ストアにデータを移動するときに、型変換を自動的に実行します。

**typeProperties** セクションは、データセットの型ごとに異なり、データ ストアのデータの場所や書式などに関する情報を提供します。 **AzureBlob** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |BLOB ストレージのコンテナーとフォルダーのパス。 例: myblobcontainer\myblobfolder\ |はい |
| fileName |BLOB の名前です。 fileName は省略可能で、大文字と小文字を区別します。<br/><br/>fileName を指定すると、アクティビティ (コピーを含む) は特定の BLOB で動作します。<br/><br/>fileName が指定されていない場合、コピーには入力データセットの folderPath のすべての BLOB が含まれます。<br/><br/>出力データセットに **fileName** が指定されておらず、アクティビティ シンクで **preserveHierarchy** が指定されていない場合は、生成されるファイル名は次の形式になります。`Data.<Guid>.txt` (例: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| partitionedBy |partitionedBy は任意のプロパティです。 これを使用し、時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath をパラメーター化できます。 詳細と例については、「 [partitionedBy プロパティの使用](#using-partitionedby-property) 」をご覧ください。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

### <a name="using-partitionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、**partitionedBy** プロパティ、[Data Factory 関数、およびシステム変数](data-factory-functions-variables.md)を使用して、時系列データに動的な folderPath および filename を指定できます。

時系列データセット、スケジュール設定、およびスライスの詳細については、[データセットの作成](data-factory-create-datasets.md)に関する記事および[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

#### <a name="sample-1"></a>サンプル 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 プロパティ (名前、説明、入力データセット、出力データセット、ポリシーなど) は、あらゆる種類のアクティビティで使用できます。 一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。 Azure BLOB ストレージからデータを移動する場合は、コピー アクティビティのソースの種類を **BlobSource**に設定します。 同様に、Azure BLOB ストレージにデータを移動する場合は、コピー アクティビティのシンクの種類を **BlobSink**に設定します。 このセクションでは、BlobSource と BlobSink でサポートされるプロパティの一覧を示します。

**BlobSource** の **typeProperties** セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True (既定値)、False |いいえ |

**BlobSink** の **typeProperties** セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |ソースが BlobSource または FileSystem である場合のコピー動作を定義します。 |<b>PreserveHierarchy</b>: ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><br/><b>FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルになります。 ターゲット ファイルは、自動生成された名前になります。 <br/><br/><b>MergeFiles</b>: ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル/Blob の名前を指定した場合、マージされたファイル名は指定した名前になります。それ以外は自動生成されたファイル名になります。 |いいえ |

**BlobSource** は、下位互換性のために次の 2 つのプロパティもサポートしています。

* **treatEmptyAsNull**: null または空の文字列を null 値として処理するかどうかを指定します。
* **skipHeaderLineCount** - スキップする必要がある行数を指定します。 入力データセットで TextFormat を利用しているときにのみ適用されます。

同様に、 **BlobSink** も、下位互換性のために次のプロパティをサポートしています。

* **blobWriterAddHeader**: 出力データセットへの書き込み中に列定義のヘッダーを追加するかどうかを指定します。

データセットは、同じ機能を実装するプロパティとして、**treatEmptyAsNull**、**skipLineCount**、**firstRowAsHeader** をサポートするようになりました。

次の表は、BLOB ソース/シンク プロパティの代わりに新しいデータセットのプロパティを使用するためのガイドラインをまとめたものです。

| コピー アクティビティのプロパティ | データセットのプロパティ |
|:--- |:--- |
| BlobSource の skipHeaderLineCount |skipLineCount と firstRowAsHeader。 最初に行がスキップされ、その後、最初の行がヘッダーとして読み取られます。 |
| BlobSource の treatEmptyAsNull |入力データセットの treatEmptyAsNull |
| BlobSink の blobWriterAddHeader |出力データセットの firstRowAsHeader |

これらのプロパティの詳細については、「 [TextFormat の指定](data-factory-supported-file-and-compression-formats.md#text-format) 」セクションを参照してください。

### <a name="recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例
このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive | copyBehavior | 行われる動作 |
| --- | --- | --- |
| true |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 はソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます。 |
| false |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>チュートリアル:コピー ウィザードを使用して Blob Storage との間でデータをコピする
Azure Blob Storage との間でデータをすばやくコピーする方法を確認してみましょう。 このチュートリアルでは、コピー元データ ストアとコピー先データ ストアの両方の種類が Azure Blob Storage です。 このチュートリアルのパイプラインは、同じ BLOB コンテナー内のフォルダー間でデータをコピーします。 ここではチュートリアルを意図的にシンプルにして、Blob Storage をコピー元またはシンクとして使用するときの設定とプロパティを示しています。

### <a name="prerequisites"></a>前提条件
1. 汎用 **Azure ストレージ アカウント**を作成します (お持ちでない場合)。 このチュートリアルでは、**コピー元**データ ストアおよび**コピー先データ ストア**として Blob Storage を使用します。 Azure ストレージ アカウントがない場合、ストレージ アカウントの作成手順については、「 [ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md) 」をご覧ください。
2. ストレージ アカウントに **adfblobconnector** という名前の BLOB コンテナーを作成します。
4. **adfblobconnector** コンテナーに **input** という名前のフォルダーを作成します。
5. 次のコンテンツを含む **emp.txt** という名前のファイルを作成し、[Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/)などのツールを使って、**input** フォルダーにアップロードします
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Data Factory の作成
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上隅にある **[リソースの作成]** 、 **[インテリジェンス + 分析]** 、 **[データ ファクトリ]** の順にクリックします。
3. **[新しいデータ ファクトリ]** ウィンドウで、次の手順を実行します。  
    1. **[名前]** に「**ADFBlobConnectorDF**」と入力します。 Azure Data Factory の名前はグローバルに一意にする必要があります。 エラー `*Data factory name “ADFBlobConnectorDF” is not available` が発生した場合は、データ ファクトリの名前を変更して (yournameADFBlobConnectorDF など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](data-factory-naming-rules.md) に関するトピックを参照してください。
    2. Azure **サブスクリプション**を選択します。
    3. リソース グループについては、 **[Use existing (既存のものを使用)]** を選択して、既存のリソース グループを選択するか、 **[新規作成]** を選択して、リソース グループの名前を入力します。
    4. データ ファクトリの**場所**を選択します。
    5. ブレードの一番下にある **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。
    6. **Create** をクリックしてください。
3. 作成が完了すると、次の図に示すような **[Data Factory]** ブレードが表示されます。![データ ファクトリのホーム ページ](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>コピー ウィザード
1. Data Factory のホーム ページで **[データのコピー]** タイルをクリックして、新しいタブで**データのコピー ウィザード**を起動します。  
    
    > [!NOTE]
    > 承認中であることを示すメッセージが表示されたまま Web ブラウザーが停止してしまう場合は、**サード パーティの Cookie とサイト データをブロック**する設定を無効にしてください。または、有効な状態のまま **login.microsoftonline.com** に対する例外を作成し、そのうえで、もう一度ウィザードを起動してください。
2. **[プロパティ]** ページで次の操作を実行します。
    1. **[タスク名]** に「**CopyPipeline**」と入力します。 タスク名は、データ ファクトリのパイプラインの名前です。
    2. タスクの**説明**を入力します (省略可能)。
    3. **タスクの周期またはスケジュール**については、 **[Run regularly on schedule (スケジュールに従って定期的に実行する)]** オプションをそのままにします。 スケジュールに従って繰り返し実行するのではなく、1 回だけ実行する場合は、 **[Run once now (今すぐ 1 回実行する)]** を選択します。 **[Run once now (今すぐ 1 回実行する)]** オプションを選択すると、[ワンタイム パイプライン](data-factory-create-pipelines.md#onetime-pipeline)が作成されます。
    4. **[再実行のパターン]** の設定はそのままにします。 このタスクは、次の手順で指定する開始時間と終了時間の間に毎日実行されます。
    5. **開始日時**を **04/21/2017** に変更します。
    6. **終了日時**を **04/25/2017** に変更します。 日付はカレンダーから選択する代わりに、直接入力することもできます。
    8. **[次へ]** をクリックします。
        ![コピー ツール - [プロパティ] ページ](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. **[Source data store (ソース データ ストア)]** ページで、 **[Azure Blob Storage]** タイルをクリックします。 このページを使用して、コピー タスクのソース データ ストアを指定します。 既存のデータ ストアのリンクされたサービスを使用するか、新しいデータ ストアを指定できます。 既存のリンクされたサービスを使用するには、 **[FROM EXISTING LINKED SERVICES (既存のリンクされたサービスから)]** を選択し、適切なリンクされたサービスを選択します。
    ![コピー ツール - [ソース データ ストア] ページ](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. **[Specify the Azure Blob storage account (Azure BLOB ストレージ アカウントの指定)]** ページで次の操作を実行します。
    1. **[接続名]** は、自動生成された名前のままにします。 接続名は、リンクされたサービスの種類の名前である Azure Storage です。
    2. **[Account selection method (アカウントの選択方法)]** で **[From Azure subscriptions (Azure サブスクリプションから)]** オプションが選択されていることを確認します。
    3. 使用している Azure サブスクリプションを選択するか、 **[Azure サブスクリプション]** で設定されている **[すべて選択]** をそのまま使用します。
    4. 選択したサブスクリプションで利用できる Azure ストレージ アカウントの一覧から、使用する **Azure ストレージ アカウント**を選択します。 ストレージ アカウント設定を手動で入力することもできます。その場合は、 **[Account selection method (アカウントの選択方法)]** で **[手動で入力]** オプションを選択します。
    5. **[次へ]** をクリックします。  
        ![コピー ツール - Azure BLOB ストレージ アカウントの指定](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. **[Choose the input file or folder (入力ファイルまたはフォルダーの選択)]** ページで次の操作を実行します。
    1. **[adfblobcontainer]** をダブルクリックします。
    2. **[input]** を選択し、 **[選択]** をクリックします。 このチュートリアルでは、input フォルダーを選択します。 フォルダーではなく、emp.txt ファイルも選択することもできます。
        ![コピー ツール - 入力ファイルまたはフォルダーの選択](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. **[Choose the input file or folder (入力ファイルまたはフォルダーの選択)]** ページで次の操作を実行します。
    1. **[ファイルまたはフォルダー]** が **adfblobconnector/input** に設定されていることを確認します。 ファイルがサブフォルダー内にある場合は、ファイルまたはフォルダーの名前として、adfblobconnector/input/{年}/{月}/{日} (例: 2017/04/01、2017/04/02、...) と入力します。 テキスト ボックスの外で Tab キーを押すと、3 つのドロップダウン リストが表示され、年 (yyyy)、月 (MM)、および日 (dd) の形式を選択できます。
    2. **[Copy file recursively (再帰的にファイルをコピーする)]** は設定しないでください。 このオプションは、フォルダーを再帰的にスキャンして、ファイルをコピーするときに選択します。
    3. **[バイナリ コピー]** オプションは選択しないでください。 このオプションは、コピー元ファイルをバイナリ コピーするときに選択します。 次のページで他のオプションが表示されるように、このチュートリアルでは選択しません。
    4. **[圧縮の種類]** が **[なし]** に設定されていることを確認します。 サポートされている形式のいずれかでコピー元ファイルが圧縮されている場合は、このオプションで値を選択します。
    5. **[次へ]** をクリックします。
    ![コピー ツール - 入力ファイルまたはフォルダーの選択](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. **[File format settings (ファイル形式の設定)]** ページに、ウィザードがファイルを解析することによって自動的に検出した区切り記号とスキーマが表示されます。
    1. 次のオプションを選択します。  
        a. **[ファイル形式]** を **[テキスト形式]** に設定します。 ドロップダウン リストには、サポートされているすべての形式が表示されます。 例: JSON、Avro、ORC、Parquet。
       b. **[列区切り記号]** を `Comma (,)` に設定します。 ドロップダウン リストには、Data Factory でサポートされているその他の列区切り記号が表示されます。 また、独自の区切り記号を指定することもできます。
       c. **[行区切り記号]** を `Carriage Return + Line feed (\r\n)` に設定します。 ドロップダウン リストには、Data Factory でサポートされているその他の行区切り記号が表示されます。 また、独自の区切り記号を指定することもできます。
       d. **[skip line count]\(スキップ行数\)** を **0** に設定します。 ファイルの先頭で行をスキップする場合は、ここにその行数を入力します。
       e. **[The first data row contains column names]\(先頭データ行に列名を使用する\)** はオンにしません。 コピー元ファイルの先頭行に列名が使用されている場合は、このオプションを選択します。
       f. **[Treat empty column value as null ]\(空の列の値を null として処理する\)** オプションをオンにします。
    2. **[詳細設定]** を展開して、利用可能な詳細オプションを表示します。
    3. ページの下部で、emp.txt ファイルのデータの**プレビュー**を確認します。
    4. 下部の **[スキーマ]** タブをクリックし、コピー元ファイルのデータからコピー ウィザードが推測したスキーマを確認します。
    5. 区切り記号を確認し、データをプレビューしたら、 **[次へ]** をクリックします。
    ![コピー ツール - ファイル形式の設定](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. **[Destination data store (コピー先データ ストア)]** ページで **[Azure Blob Storage]** を選択し、 **[次へ]** をクリックします。 このチュートリアルでは、コピー元データ ストアおよびコピー先データ ストアとして Azure Blob Storage を使用しています。  
    ![コピー ツール - コピー先データ ストアの選択](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. **[Specify the Azure Blob storage account (Azure BLOB ストレージ アカウントの指定)]** ページで次の操作を実行します。  
    1. **[接続名]** フィールドに「**AzureStorageLinkedService**」と入力します。
    2. **[Account selection method (アカウントの選択方法)]** で **[From Azure subscriptions (Azure サブスクリプションから)]** オプションが選択されていることを確認します。
    3. Azure **サブスクリプション**を選択します。
    4. [Azure ストレージ アカウント] を選択します。
    5. **[次へ]** をクリックします。
10. **[Choose the output file or folder (出力ファイルまたはフォルダーの選択)]** ページで次の操作を実行します。  
    1. **[フォルダー パス]** で **adfblobconnector/output/{year}/{month}/{day}** を指定します。 **Tab** キーを押します。
    1. **[年]** では **[yyyy]** を選択します。
    1. **[月]** で、 **[MM]** が設定されていることを確認します。
    1. **[日]** で、 **[dd]** が設定されていることを確認します。
    1. **[圧縮の種類]** が **[なし]** に設定されていることを確認します。
    1. **[コピー動作]** が **[Merge files (ファイルを結合)]** に設定されていることを確認します。 同じ名前の出力ファイルが既に存在する場合は、最後にある同じファイルに新しいコンテンツが追加されます。
    1. **[次へ]** をクリックします。
       ![コピー ツール - 出力ファイルまたはフォルダーの選択](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. **[File format settings (ファイル形式設定)]** ページで設定を確認し、 **[次へ]** をクリックします。 ここで使用できる追加オプションの 1 つが、ヘッダーを出力ファイルに追加するオプションです。 このオプションを選択すると、ヘッダー行が、コピー元のスキーマの列名で追加されます。 既定の列名は、コピー元のスキーマを表示するときに変更できます。 たとえば、最初の列を "名" に、2 番目の列を "姓" に変更できます。 その後、出力ファイルが生成され、そのファイルのヘッダーでは、この名前が列名として使用されます。
    ![コピー ツール - コピー先のファイル形式の設定](media/data-factory-azure-blob-connector/file-format-destination.png)
12. **[パフォーマンス設定]** ページで、 **[Cloud units (クラウド単位)]** と **[Parallel copies (並列コピー)]** が **[自動]** に設定されていることを確認し、[次へ] をクリックします。 こうした設定の詳細については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md#parallel-copy)」を参照してください。
    ![コピー ツール - パフォーマンス設定](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. **[概要**] ページで、すべての設定 (タスクのプロパティ、コピー元とコピー先の設定、コピーの設定) を確認し、 **[次へ]** をクリックします。
    ![コピー ツール - [概要] ページ](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. **[概要]** ページの内容を確認し、 **[完了]** をクリックします。 これにより、2 つのリンクされたサービス、2 つのデータセット (入力と出力)、1 つのパイプラインが (コピー ウィザードを起動した場所から) データ ファクトリに作成されます。
    ![コピー ページ - [デプロイ] ページ](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>パイプラインの監視 (コピー タスク)

1. **[デプロイ]** ページで `Click here to monitor copy pipeline` リンクをクリックします。
2. 新しいタブで **[Monitor and Manage application (アプリケーションの監視および管理)]** が表示されます。![アプリの監視および管理](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. **開始**時間を `04/19/2017` に、**終了**時間を `04/27/2017` に変更し、 **[適用]** をクリックします。
4. **[ACTIVITY WINDOWS (アクティビティ ウィンドウ)]** リストに 5 つのアクティビティ ウィンドウが表示されます。 **[Window Start (ウィンドウの開始)]** 時間には、すべての日付のパイプライン開始時間からパイプライン終了時間が表示されます。
5. すべてのアクティビティ ウィンドウの状態が [準備完了] に設定されるまで、 **[ACTIVITY WINDOWS (アクティビティ ウィンドウ)]** リストの **[更新]** を何度かクリックします。
6. 次に、出力ファイルが adfblobconnector コンテナーの出力フォルダーに生成されていることを確認します。 出力フォルダーのフォルダー構造は次のとおりです。
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   データ ファクトリの監視と管理の詳細については、[Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事をご覧ください。

### <a name="data-factory-entities"></a>Data Factory のエンティティ
ここで、Data Factory のホーム ページのタブに戻ります。 現在データ ファクトリには、2 つのリンクされたサービス、2 つのデータセット、および 1 つのパイプラインがあります。

![Data Factory のホーム ページとそのエンティティ](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

**[作成およびデプロイ]** をクリックして、Data Factory エディターを起動します。

![Data Factory エディター](media/data-factory-azure-blob-connector/data-factory-editor.png)

データ ファクトリに次の Data Factory エンティティが表示されます。

- 2 つのリンクされたサービス。 1 つはコピー元を、もう 1 つはコピー先を対象としています。 このチュートリアルでは、両方のリンクされたサービスが同じ Azure ストレージ アカウントを参照しています。
- 2 つのデータセット。 入力データセットと出力データセット。 このチュートリアルでは、両方が同じ BLOB コンテナーを使用していますが、それぞれ異なるフォルダー (入力と出力) を参照しています。
- パイプライン。 パイプラインには、BLOB コピー元と BLOB シンクを使用して、Azure BLOB の場所から別の Azure BLOB の場所にデータをコピーするコピー アクティビティが含まれています。

以降のセクションでは、こうしたエンティティについて詳しく説明します。

#### <a name="linked-services"></a>リンクされたサービス
2 つのリンクされたサービスが表示されます。 1 つはコピー元を、もう 1 つはコピー先を対象としています。 このチュートリアルでは、両方の定義が同じに見えますが、名前が異なります。 リンクされたサービスの**種類**は **AzureStorage** に設定されています。 リンクされたサービス定義で最も重要なプロパティは **connectionString** です。このプロパティは、実行時に Azure ストレージ アカウントに接続するときに、Data Factory によって使用されます。 定義の hubName プロパティは無視します。

##### <a name="source-blob-storage-linked-service"></a>コピー元 Blob Storage のリンクされたサービス
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>コピー先 Blob Storage のリンクされたサービス

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Azure Storage のリンクされたサービスの詳細については、「[リンクされたサービスのプロパティ](#linked-service-properties)」を参照してください。

#### <a name="datasets"></a>データセット
データセットには、入力データセットと出力データセットの 2 つがあります。 データセットの型は両方とも **AzureBlob** に設定されています。

入力データセットは、**adfblobconnector** BLOB コンテナーの **input** フォルダーを指定します。 このデータセットについては、**external** プロパティは **true** に設定されています。データは、このデータセットを入力として使用するコピー アクティビティでは、パイプラインによって生成されないためです。

出力データセットは、同じ BLOB コンテナーの **output** フォルダーを指定します。 出力データセットも、**SliceStart** システム変数の年、月、および日を使用して、出力ファイルのパスを動的に評価します。 Data Factory でサポートされている関数とシステム変数の一覧については、[Data Factory の関数とシステム変数](data-factory-functions-variables.md)に関するページをご覧ください。 このデータセットはパイプラインによって生成されるため、**external** プロパティは **false** (既定値) に設定されています。

Azure BLOB データセットでサポートされるプロパティの詳細については、「[データセットのプロパティ](#dataset-properties)」を参照してください。

##### <a name="input-dataset"></a>入力データセット

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>出力データセット

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>パイプライン
パイプラインに含まれるアクティビティは 1 つだけです。 アクティビティの **type** は **Copy** に設定されています。 アクティビティの type プロパティには 2 つのセクションがあり、1 つはコピー元、もう 1 つはシンクを対象としています。 アクティビティは Blob Storage からデータをコピーするため、コピー元の種類は **BlobSource** に設定されています。 アクティビティは Blob Storage にデータをコピーするため、シンクの種類は **BlobSink** に設定されています。 コピー アクティビティは、入力として InputDataset-z4y を使用し、出力として OutputDataset-z4y を使用します。

BlobSource と BlobSink でサポートされるプロパティの詳細については、「[コピー アクティビティのプロパティ](#copy-activity-properties)」を参照してください。

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>Blob Storage との間でのデータのコピーに関する JSON の例
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、Azure Blob Storage と Azure SQL Database の間でデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることができます。

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON サンプル:Blob Storage から SQL Database にデータをコピーする
次のサンプルは以下を示しています。

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](#copy-activity-properties) と [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB から Azure SQL テーブルに時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage のリンクされたサービス:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory では、2 種類の Azure Storage のリンクされたサービス **AzureStorage** と **AzureStorageSas** をサポートしています。 前者ではアカウント キーを含む接続文字列を指定し、後者では Shared Access Signature (SAS) の URI を指定します。 詳細については、「 [リンクされたサービス](#linked-service-properties) 」をご覧ください。

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔:1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスでは開始時間の年、月、日の部分を使用し、ファイル名では開始時間の時刻部分を使用します。 "external": "true" の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory に通知されます。

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL の出力データセット:**

このサンプルは Azure SQL データベースの「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含まれることが予想される列数で Azure SQL データベースにテーブルを作成します。 新しい行は 1 時間ごとにテーブルに追加されます。

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**BLOB ソースおよび SQL シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlSink** に設定されています。

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON サンプル:Azure SQL から Azure BLOB にデータをコピーする
次のサンプルは以下を示しています。

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](#linked-service-properties)型のリンクされたサービス。
3. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) と [BlobSink](#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure SQL テーブルから Azure BLOB に時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage のリンクされたサービス:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory では、2 種類の Azure Storage のリンクされたサービス **AzureStorage** と **AzureStorageSas** をサポートしています。 前者ではアカウント キーを含む接続文字列を指定し、後者では Shared Access Signature (SAS) の URI を指定します。 詳細については、「 [リンクされたサービス](#linked-service-properties) 」をご覧ください。

**Azure SQL の入力データセット:**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

"external": "true" の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SQL ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
