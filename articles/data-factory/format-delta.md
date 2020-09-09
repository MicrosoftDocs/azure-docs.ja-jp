---
title: Azure Data Factory での差分形式
description: 差分形式を使用して Delta Lake のデータを変換したり、移動したりする
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: e9df7b00a384859fb29577be0ad05da233683f46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044529"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure Data Factory での差分形式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、差分形式を使用して、[Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) または [Azure Blob Storage](connector-azure-blob-storage.md) に保存されている Delta Lake との間でデータをコピーする方法について説明します。 このコネクタは、ソースとシンクの両方としてデータ フローをマッピングするとき、[インライン データセット](data-flow-source.md#inline-datasets)として利用できます。

> [!NOTE]
> データ フローをマッピングするための差分形式コネクタは現在、パブリック プレビューとして利用できます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

このコネクタは、ソースとシンクの両方としてデータ フローをマッピングするとき、[インライン データセット](data-flow-source.md#inline-datasets)として利用できます。

### <a name="source-properties"></a>ソース プロパティ

次の表に、差分ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `delta` である必要があります | はい | `delta` | format |
| ファイル システム | Delta Lake のコンテナーまたはファイル システム | はい | String | fileSystem |
| フォルダー パス | Delta Lake の直接 | はい | String | folderPath |
| [圧縮の種類] | 差分テーブルの圧縮の種類 | no | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 圧縮レベル | 圧縮を可能な限り短時間で完了させるか、圧縮後のファイルを最適に圧縮するかを選択します。 | `compressedType` を指定した場合は必須。 | `Optimal` または `Fastest` | compressionLevel |
| タイム トラベル | 差分テーブルの古いスナップショットにクエリを実行するかどうかを選択します | no | タイムスタンプ別のクエリ:Timestamp <br> バージョン別のクエリ:Integer | timestampAsOf <br> versionAsOf |

#### <a name="import-schema"></a>Import schema

差分はインライン データセットとしてのみ利用できます。既定では、スキーマは関連付けられていません。 列メタデータを取得するには、 **[プロジェクション]** タブの **[スキーマのインポート]** ボタンをクリックします。これにより、コーパスによって指定されている列名とデータ型を参照できます。 スキーマをインポートするには、[データ フロー デバッグ セッション](concepts-data-flow-debug-mode.md)をアクティブにする必要があり、既存の CDM エンティティ定義ファイルをポイントする必要があります。
 

### <a name="delta-source-script-example"></a>差分ソース スクリプトの例

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、差分シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `delta` である必要があります | はい | `delta` | format |
| ファイル システム | Delta Lake のコンテナーまたはファイル システム | はい | String | fileSystem |
| フォルダー パス | Delta Lake の直接 | はい | String | folderPath |
| [圧縮の種類] | 差分テーブルの圧縮の種類 | no | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 圧縮レベル | 圧縮を可能な限り短時間で完了させるか、圧縮後のファイルを最適に圧縮するかを選択します。 | `compressedType` を指定した場合は必須。 | `Optimal` または `Fastest` | compressionLevel |
| VACUUM | 古いバージョンのテーブルに保持しきい値を時間単位で指定します。 0 以下の既定値は 30 日です。 | はい | Integer | vacuum |
| 更新方法 | Delta Lake で許可される更新操作を指定します。 挿入ではない方法については、行にマークを付けるために、先行する行の変更変換が必要です。 | はい | `true` または `false` | deletable <br> insertable <br> updateable <br> upsertable |

### <a name="delta-sink-script-example"></a>差分シンク スクリプトの例

関連付けられているデータ フロー スクリプトは次のとおりです。

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>既知の制限事項

差分シンクに書き込まれるとき、書き込まれた行数が監視出力で返されないという既知の制限があります。

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[ソース変換](data-flow-source.md)を作成します。
* マッピング データ フローの[シンク変換](data-flow-sink.md)を作成します。
* [行の変更変換](data-flow-alter-row.md)を作成し、行に insert、update、upsert、または delete のマークを付けます。
