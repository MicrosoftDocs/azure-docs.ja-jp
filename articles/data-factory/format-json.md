---
title: Azure Data Factory での JSON 形式
description: このトピックでは、Azure Data Factory で JSON 形式を処理する方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: ebac108388071ed3dc0eb2b7bfc0494f2f7bb481
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181365"
---
# <a name="json-format-in-azure-data-factory"></a>Azure Data Factory での JSON 形式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**JSON ファイルを解析する場合や、JSON 形式にデータを書き込む場合** は、この記事に従ってください。 

JSON 形式は、以下のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、JSON データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**Json** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| encodingName     | テスト ファイルの読み取り/書き込みに使用するエンコードの種類です。 <br>使用できる値は次のとおりです。"UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| いいえ       |
| compression | ファイル圧縮を構成するためのプロパティのグループ。 アクティビティの実行中に圧縮/圧縮解除を行う場合は、このセクションを構成します。 | いいえ |
| type<br/>( *`compression` の下にあります*) | JSON ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**TarGzip**、**Tar**、**snappy**、または **lz4** です。 既定では圧縮されません。<br>現在、Copy アクティビティでは "snappy" と "lz4" がサポートされておらず、マッピング データ フローでは "ZipDeflate"、"TarGzip"、"Tar" がサポートされていないことに **注意してください**。<br>コピー アクティビティを使用して **ZipDeflate**/**TarGzip**/**Tar** ファイルを圧縮解除し、ファイルベースのシンク データ ストアに書き込む場合、ファイルは既定で `<path specified in dataset>/<folder named as source compressed file>/` フォルダーに解凍されることに **注意** してください。圧縮ファイル名をフォルダー構造として保持するかどうかを制御するには、[コピー アクティビティのソース](#json-as-source)に対して `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` を使用します。| いいえ。  |
| level<br/>( *`compression` の下にあります*) | 圧縮率です。 <br>使用できる値は、**Optimal** または **Fastest** です。<br>- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、圧縮後のファイルが最適に圧縮されていない場合があります。<br>- **Optimal**: 圧縮操作で最適に圧縮されますが、操作が完了するまでに時間がかかる場合があります。 詳細については、 [圧縮レベル](/dotnet/api/system.io.compression.compressionlevel) に関するトピックをご覧ください。 | いいえ       |

Azure Blob Storage 上の JSON データセットの例を次に示します。

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、JSON のソースとシンクでサポートされるプロパティの一覧を示します。

JSON ファイルからデータを抽出してシンク データ ストアおよび形式にマップする方法、または[スキーマ マッピング](copy-activity-schema-and-type-mapping.md)からの逆の方法について説明します。

### <a name="json-as-source"></a>ソースとしての JSON

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティは、**JSONSource** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の **JSON の読み取り設定** に関する表を参照してください。 | いいえ       |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

`formatSettings` でサポートされている **JSON の読み取り設定**:

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings の type は、**JsonReadSettings** に設定する必要があります。 | はい      |
| compressionProperties | 特定の圧縮コーデックのデータを圧縮解除する方法のプロパティ グループ。 | いいえ       |
| preserveZipFileNameAsFolder<br>(" *`compressionProperties`->`type` の下に `ZipDeflateReadSettings` として*")  | **ZipDeflate** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの ZIP ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>/<folder named as source zip file>/` に書き込みます。<br>- **false** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>` に直接書き込みます。 競合または予期しない動作を避けるために、異なるソース ZIP ファイルに重複したファイル名がないことを確認します。  | いいえ |
| preserveCompressionFileNameAsFolder<br>(" *`compressionProperties`->`type` で `TarGZipReadSettings` または `TarReadSettings` として*") | **TarGzip**/**Tar** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの圧縮ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、Data Factory は圧縮解除されたファイルを `<path specified in dataset>/<folder named as source compressed file>/` に書き込みます。 <br>- **false** に設定した場合、Data Factory は圧縮解除されたファイルを `<path specified in dataset>` に直接書き込みます。 競合または予期しない動作を避けるために、異なるソース ファイルに重複したファイル名がないことを確認します。 | いいえ |

### <a name="json-as-sink"></a>シンクとしての JSON

コピー アクティビティの ***\* sink \**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティは、**JSONSink** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の **JSON の書き込み設定** に関する表を参照してください。 | いいえ       |
| storeSettings | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

`formatSettings` でサポートされている **JSON の書き込み設定**:

| プロパティ      | 説明                                                  | 必須                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings の type は、**JsonWriteSettings** に設定する必要があります。 | はい                                                   |
| filePattern |各 JSON ファイルに格納されたデータのパターンを示します。 使用できる値は、**setOfObjects** (JSON 行) と **arrayOfObjects** です。 **既定** 値は **setOfObjects** です。 これらのパターンの詳細については、「[JSON ファイルのパターン](#json-file-patterns)」セクションを参照してください。 |いいえ |

### <a name="json-file-patterns"></a>JSON ファイルのパターン

JSON ファイルからデータをコピーする場合、コピー アクティビティでは、JSON ファイルの以下のパターンを自動的に検出および解析することができます。 データを JSON ファイルに書き込む場合は、コピー アクティビティのシンクでファイル パターンを構成できます。

- **タイプ I: setOfObjects**

    各ファイルには、単一のオブジェクト、JSON 行、または連結されたオブジェクトが含まれます。

    * **単一オブジェクトの JSON の例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **JSON 行 (シンクの既定値)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **連結 JSON の例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **タイプ II: arrayOfObjects**

    各ファイルにはオブジェクトの配列が含まれます。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで JSON 形式での読み取りと書き込みを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>ソース プロパティ

次の表に、JSON ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| ワイルド カードのパス | ワイルドカードのパスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスはオーバーライドされます。 | no | String[] | wildcardPaths |
| パーティションのルート パス | パーティション分割されたファイル データについては、パーティション フォルダーを列として読み取るためにパーティションのルート パスを入力できます | no | String | partitionRootPath |
| ファイルの一覧 | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | no | `true` または `false` | fileList |
| ファイル名を格納する列 | ソース ファイル名とパスを使用して新しい列を作成します | no | String | rowUrlColumn |
| 完了後 | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | no | 削除: `true` または `false` <br> 移動: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no | Timestamp | modifiedAfter <br> modifiedBefore |
| 1 つのドキュメント | マッピング データ フローでは、各ファイルから 1 つの JSON ドキュメントが読み取られます | no | `true` または `false` | singleDocument |
| 引用符で囲まれていない列名 | **[Unquoted column names]\(引用符で囲まれていない列名\)** を選択した場合、マッピング データ フローでは、引用符で囲まれていない JSON 列が読み取られます。 | no | `true` または `false` |  unquotedColumnNames |
| コメントあり | JSON データに C または C++ スタイルのコメントが含まれている場合は、 **[コメントあり]** を選択します | no | `true` または `false` | asComments |
| 一重引用符付き | 引用符で囲まれていない JSON 列が読み取られます | no | `true` または `false` | singleQuoted |
| 円記号によるエスケープ | JSON データ内の文字をエスケープするためにバックスラッシュを使用している場合は、 **[円記号によるエスケープ]** を選択します | no | `true` または `false` | backslashEscape |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>ソース形式のオプション

データ フローでソースとして JSON データセットを使用すると、5 つの追加設定を行うことができます。 これらの設定は、 **[Source Options]\(ソース オプション\)** タブの **[JSON settings]\(JSON 設定\)** アコーディオンにあります。**ドキュメント フォーム** の設定では、 **[1 つのドキュメント]** 、 **[Document per line]\(行ごとのドキュメント\)** 、および **[Array of documents]\(ドキュメントの配列\)** のいずれかの種類を選択できます。

![JSON 設定](media/data-flow/json-settings.png "JSON 設定")

#### <a name="default"></a>Default

既定では、JSON データは次の形式で読み取られます。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>1 つのドキュメント

**[Single document]\(1 つのドキュメント\)** を選択した場合、マッピング データ フローでは、各ファイルから 1 つの JSON ドキュメントが読み取られます。 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
**[Document per line]\(行ごとのドキュメント\)** を選択した場合、マッピング データ フローでは、ファイルの各行から 1 つの JSON ドキュメントが読み取られます。 

``` json
File1.json
{"json": "record 1 }

File2.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}

File3.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
 {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
```
**[Array of documents]\(ドキュメントの配列\)** を選択した場合、マッピング データ フローでは、ファイルから 1 つのドキュメントが読み取られます。 

``` json
File.json
[
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
```
> [!NOTE]
> データ フローで JSON データのプレビュー時に "corrupt_record" というエラーがスローされた場合、JSON ファイル内の 1 つのドキュメントがデータに含まれている可能性があります。 "1 つのドキュメント" を設定すると、そのエラーがクリアされます。

#### <a name="unquoted-column-names"></a>引用符で囲まれていない列名

**[Unquoted column names]\(引用符で囲まれていない列名\)** を選択した場合、マッピング データ フローでは、引用符で囲まれていない JSON 列が読み取られます。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>コメントあり

JSON データに C または C++ スタイルのコメントが含まれている場合は、 **[コメントあり]** を選択します。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>一重引用符付き

JSON フィールドと値に二重引用符ではなく単一引用符を使用している場合は、 **[Single quoted]\(単一引用符付き\)** を選択します。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>円記号によるエスケープ

JSON データ内の文字をエスケープするためにバックスラッシュを使用している場合は、 **[円記号によるエスケープ]** を選択します。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、JSON シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| フォルダーのクリア | 書き込みの前に宛先フォルダーがクリアされるかどうか | no | `true` または `false` | truncate |
| ファイル名のオプション | 書き込まれたデータの名前付け形式です。 既定では、`part-#####-tid-<guid>` という形式で、パーティションごとに 1 ファイルです | no | パターン:String <br> [Per partition] (パーティションごと): String[] <br> [As data in column] (列内のデータとして): String <br> 1 つのファイルに出力する: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>派生列での JSON 構造の作成

派生列式エディターを使用して複合列をデータ フローに追加できます。 派生列変換に新しい列を追加し、青いボックスをクリックして式ビルダーを開きます。 列を複合にするには、手動で JSON 構造を入力するか、UX を使用してサブ列を対話的に追加します。

#### <a name="using-the-expression-builder-ux"></a>式ビルダー UX の使用

出力スキーマのサイド ウィンドウで、列の上にマウス ポインターを移動し、プラス記号のアイコンをクリックします。 列を複合型にするために、 **[Add subcolumn]\(サブ列の追加\)** を選択します。

![サブ列の追加](media/data-flow/derive-add-subcolumn.png "サブ列の追加")

同じ方法で、さらに列とサブ列を追加できます。 複合でない各フィールドについては、式エディターで右側に式を追加できます。

![複合列の追加](media/data-flow/derive-complex-column.png "列の追加")

#### <a name="entering-the-json-structure-manually"></a>手動による JSON 構造の入力

JSON 構造を手動で追加するには、新しい列を追加し、エディターで式を入力します。 式は、次の一般的な形式に従います。

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

"complexColumn" という名前の列に対してこの式が入力された場合、次の JSON としてシンクに書き込まれます。

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完全な階層定義のサンプル手動スクリプト
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
