---
title: Azure Data Factory を使用したファイル システムとの間でのデータのコピー
description: Azure Data Factory を使用して、オンプレミスのファイル システムとの間でデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 47dbd28f301009969ae02acfac6f4b272ef608f0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918646"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Azure Data Factory を使用してオンプレミスのファイル システムとの間でデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-onprem-file-system-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-file-system.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 のファイル システム コネクタ](../connector-file-system.md)に関するページを参照してください。


この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスのファイル システムとの間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>サポートされるシナリオ
**オンプレミスのファイル システムから**、以下のデータ ストアにデータをコピーできます。

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

以下のデータ ストアから、**オンプレミスのファイル システムに**データをコピーできます。

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> コピー アクティビティでは、コピー先にコピーされた後にソース ファイルが削除されることはありません。 コピー後にソース ファイルを削除する必要がある場合、カスタム アクティビティを作成してファイルを削除し、パイプラインのアクティビティを使用します。

## <a name="enabling-connectivity"></a>接続を有効にする
Data Factory は、**Data Management Gateway** を使用してオンプレミスのファイル システムとの間の接続をサポートします。 Data Factory サービスを、サポートされる任意のオンプレミスのデータ ストア (ファイル システムを含む) に接続するには、Data Management Gateway をオンプレミスの環境にインストールる必要があります。 Data Management Gateway の詳細およびゲートウェイの設定手順については、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」を参照してください。 Data Management Gateway 以外に、オンプレミスのファイル システムとの間で通信するために他のバイナリ ファイルをインストールする必要はありません。 ファイル システムが Azure IaaS VM にある場合でも、Data Management Gateway をインストールして使用する必要があります。 ゲートウェイの詳細については、「[Data Management Gateway](data-factory-data-management-gateway.md)」を参照してください。

Linux ファイル共有を使用するには、Linux サーバーの場合は [Samba](https://www.samba.org/) を、Windows サーバーの場合は Data Management Gateway をインストールします。 Linux サーバーへの Data Management Gateway のインストールはサポートされていません。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、ファイル システムとの間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **Data Factory**を作成します。 データ ファクトリには、1 つまたは複数のパイプラインを設定できます。
2. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。 たとえば、Azure Blob Storage からオンプレミスのファイル システムにデータをコピーする場合、リンクされたサービスを 2 つ作成して、オンプレミスのファイル システムと Azure Storage アカウントをデータ ファクトリにリンクします。 オンプレミスのファイル システムに固有のリンクされたサービスのプロパティについては、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。
3. コピー操作用の入力データと出力データを表す**データセット**を作成します。 最後の手順で説明されている例では、データセットを作成して入力データを含む BLOB コンテナーとフォルダーを指定します。 また、別のデータセットを作成してファイル システムにフォルダーとファイル名 (省略可能) を指定します。 オンプレミスのファイル システムに固有のデータセットのプロパティについては、「[データセットのプロパティ](#dataset-properties)」セクションを参照してください。
4. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 前に説明した例では、コピー アクティビティのソースとして BlobSource を、シンクとして FileSystemSink を使います。 同様に、オンプレミスのファイル システムから Azure Blob Storage にコピーする場合は、FileSystemSource と BlobSink をコピー アクティビティで使います。 オンプレミスのファイル システムに固有のコピー アクティビティのプロパティについては、「[コピー アクティビティのプロパティ](#copy-activity-properties)」セクションを参照してください。 ソースまたはシンクとしてデータ ストアを使う方法について詳しくは、前のセクションのデータ ストアのリンクをクリックしてください。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  ファイル システムとの間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples-for-copying-data-to-and-from-file-system)」を参照してください。

次のセクションでは、ファイル システムに固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
**オンプレミスのファイル サーバー**のリンクされているサービスで、オンプレミスのファイル システムを Azure Data Factory にリンクできます。 次の表は、オンプレミスのファイル サーバーのリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティが **OnPremisesFileServer** に設定されていることを確認します。 |はい |
| host |コピーするフォルダーのルート パスを指定します。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「[サンプルのリンクされたサービスとデータセットの定義](#sample-linked-service-and-dataset-definitions)」をご覧ください。 |はい |
| userid |サーバーにアクセスするユーザーの ID を指定します。 |No (encryptedCredential を選択する場合) |
| password |ユーザー (userid) のパスワードを指定します。 |いいえ (encryptedCredential を選択する場合) |
| encryptedCredential |New-AzDataFactoryEncryptValue コマンドレットを実行して取得できる暗号化された資格情報を指定します。 |いいえ (プレーン テキストでユーザー ID とパスワードを指定する場合) |
| gatewayName |Data Factory が、オンプレミスのファイル サーバーへの接続に使用するゲートウェイの名前を指定します。 |はい |


### <a name="sample-linked-service-and-dataset-definitions"></a>サンプルのリンクされたサービスとデータセットの定義
| シナリオ | リンクされたサービス定義のホスト | データセット定義の folderPath |
| --- | --- | --- |
| Data Management Gateway コンピューター上のローカル フォルダー: <br/><br/>次に例を示します。D:\\\* または D:\folder\subfolder\\\\* |D:\\\\ (Data Management Gateway 2.0 以降のバージョンの場合) <br/><br/> localhost (Data Management Gateway 2.0 より以前のバージョン) |\\\\またはフォルダー\\\\サブフォルダー (Data Management Gateway 2.0 以降のバージョン) <br/><br/>D:\\\\ または D:\\\\フォルダー\\\\サブフォルダー (バージョン 2.0 より前のゲートウェイ) |
| リモート共有フォルダー: <br/><br/>例: \\\\myserver\\share\\\* または \\\\myserver\\share\\フォルダー\\サブフォルダー\\\\* |\\\\\\\\myserver\\\\share |.\\\\ またはフォルダー\\\\サブフォルダー |

>[!NOTE]
>UI を使用して作成する場合、JSON のように、エスケープするために二重バックスラッシュ (`\\`) を入力する必要はなく、単一のバックスラッシュを指定します。

### <a name="example-using-username-and-password-in-plain-text"></a>例:プレーンテキストでのユーザー名とパスワードの使用

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>例:encryptedcredential の使用

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。

typeProperties セクションは、データセットの型ごとに異なります。 データ ストアのデータの場所や書式などに関する情報を提供します。 **FileShare** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブパスを指定します。 文字列内の特殊文字にはエスケープ文字 '\' を使用します。 ワイルドカード フィルターはサポートされていません。 例については、「 [サンプルのリンクされたサービスとデータセットの定義](#sample-linked-service-and-dataset-definitions) 」ご覧ください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに **fileName** が指定されておらず、アクティビティ シンクで **preserveHierarchy** が指定されていない場合は、生成されるファイル名は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。 <br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: "fileFilter": "*.log"<br/>例 2: "fileFilter":2014-1-?.txt"<br/><br/>fileFilter は FileShare 入力データセットに適用されることに注意してください。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath/fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

### <a name="using-partitionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、**partitionedBy** プロパティ、[Data Factory 関数、およびシステム変数](data-factory-functions-variables.md)を使用して、時系列データに動的な folderPath および filename を指定できます。

時系列データセット、スケジュール作成、スライスに関する詳細を理解するには、[データセットの作成](data-factory-create-datasets.md)、[スケジュールと実行](data-factory-scheduling-and-execution.md)、および[パイプラインの作成](data-factory-create-pipelines.md)に関するページを参照してください。

#### <a name="sample-1"></a>サンプル 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

この例では、{Slice} は、形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2:

```JSON
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

この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティの使用する個別の変数に抽出されます。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 プロパティ (名前、説明、入力データセット、出力データセット、ポリシーなど) は、あらゆる種類のアクティビティで使用できます。 一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。

コピー アクティビティの場合、ソースとシンクの種類によって異なります。 オンプレミスのファイル システムからデータを移動する場合は、コピー アクティビティのソースの種類を **FileSystemSource**に設定します。 同様に、オンプレミスのファイル システムにデータを移動する場合は、コピー アクティビティのシンクの種類を **FileSystemSink**に設定します。 このセクションでは、FileSystemSource と FileSystemSink でサポートされるプロパティの一覧を示します。

**FileSystemSource** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

**FileSystemSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| copyBehavior |ソースが BlobSource または FileSystem である場合のコピー動作を定義します。 |**PreserveHierarchy:** ターゲット フォルダー内でファイル階層を保持します。 つまり、ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><br/>**FlattenHierarchy:** ソース フォルダーのすべてのファイルがターゲット フォルダーの最初のレベルに作成されます。 ターゲット ファイルは、自動生成された名前で作成されます。<br/><br/>**MergeFiles:** ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名/BLOB 名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 |いいえ |

### <a name="recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例
このセクションでは、recursive と copyBehavior のプロパティの値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive 値 | copyBehavior 値 | 行われる動作 |
| --- | --- | --- |
| true |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 はソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles |ソース フォルダー Folder1 が次のような構造の場合、<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="supported-file-and-compression-formats"></a>サポートされているファイル形式と圧縮形式
詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」に関する記事を参照してください。

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>ファイル システムとの間でのデータのコピーに関する JSON の例
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、オンプレミスのファイル システムと Azure Blob Storage の間でデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、[サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に記載されているいずれかのシンクに、任意のソースからデータを*直接*コピーできます。

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>例:オンプレミスのファイル システムから Azure Blob Storage へのデータのコピー
このサンプルは、オンプレミスのファイル システムから Azure Blob Storage にデータをコピーする方法を示しています。 このサンプルでは、次の Data Factory のエンティティがあります。

* [OnPremisesFileServer](#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [FileShare](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [FileSystemSource](#copy-activity-properties) および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

次のサンプルは、時系列データを 1 時間ごとに、オンプレミスのファイル システムから Azure Blob Storage にコピーします。 これらのサンプルで使用される JSON プロパティの説明は、サンプルの後のセクションにあります。

最初の手順として、「[Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md)」の指示に従って Data Management Gateway を設定します。

**オンプレミスのファイル サーバーのリンクされたサービス:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

**userid** や **password** プロパティを使用するのではなく、**encryptedCredential** プロパティを使用することをお勧めします。 このリンクされたサービスの詳細については、[ファイル サーバーのリンクされたサービス](#linked-service-properties)に関するページを参照してください。

**Azure Storage のリンクされたサービス:**

```JSON
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

**オンプレミスのファイル システムの入力データセット:**

データは新しいファイルから 1 時間おきに取得されます。 folderPath と fileName プロパティは、スライスの開始時間に基づいて決定されます。

`"external": "true"` の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory に通知されます。

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Azure Blob Storage の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔:1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**ファイル システム ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>例:Azure SQL Database からオンプレミスのファイル システムへのデータのコピー
次のサンプルは以下を示しています。

* [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 型のリンクされたサービス。
* [OnPremisesFileServer](#linked-service-properties)型のリンクされたサービス。
* [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 型の入力データセット。
* [FileShare](#dataset-properties) 型の出力データセット。
* [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) と [FileSystemSink](#copy-activity-properties) を使用するコピー アクティビティを含むパイプライン。

このサンプルは、Azure SQL テーブルから オンプレミスのファイル システムに時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルの後のセクションにあります。

**Azure SQL Database のリンクされたサービス:**

```JSON
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

**オンプレミスのファイル サーバーのリンクされたサービス:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

**userid** や **password** プロパティを使用するのではなく、**encryptedCredential** プロパティを使用することをお勧めします。 このリンクされたサービスの詳細については、 [ファイル システムのリンクされたサービス](#linked-service-properties) に関するページを参照してください。

**Azure SQL の入力データセット:**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

``“external”: ”true”`` の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory に通知されます。

```JSON
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

**オンプレミスのファイル システムの出力データセット:**

データが 1 時間おきに新しいファイルにコピーされます。 BLOB のフォルダー パスとファイル名は、スライスの開始時間に基づいて決定されます。

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**SQL ソースおよび ファイル システム シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **FileSystemSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

コピー アクティビティ定義で、ソース データセットの列をシンク データセットの列にマップすることもできます。 詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
 Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。
