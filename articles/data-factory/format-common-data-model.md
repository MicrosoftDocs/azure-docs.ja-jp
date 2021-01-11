---
title: Common Data Model 形式
description: Common Data Model メタデータ システムを使用してデータを変換します
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: daperlov
ms.openlocfilehash: 483e26cf4044b909c8d7923cfd74bd6fcf871e2a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905299"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure Data Factory での Common Data Model 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model (CDM) メタデータ システムを使用すると、データとその意味をアプリケーションやビジネス プロセス間で簡単に共有できます。 詳しくは、「[Common Data Model](https://docs.microsoft.com/common-data-model/)」の概要を参照してください。

Azure Data Factory では、マッピング データ フローを使用して、[Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) に格納されている model.json およびマニフェスト形式の両方の CDM エンティティのデータの変換を行うことができます。 また、データをパーティション分割されたフォルダーに CSV または Parquet 形式で格納する CDM エンティティ参照を使用して、データを CDM 形式でシンクすることもできます。 

> [!NOTE]
> ADF データ フロー用の Common Data Model (CDM) 形式コネクタは、現在パブリック プレビューとして利用できます。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Common Data Model は、マッピング データ フローの[インライン データセット](data-flow-source.md#inline-datasets)として、ソースとシンクの両方として利用できます。

> [!NOTE]
> CDM エンティティを記述する際は、参照として使用する既存の CDM エンティティ定義 (メタデータ スキーマ) が既に定義されている必要があります。 ADF データ フロー シンクでは、その CDM エンティティ ファイルが読み取られ、フィールド マッピング用のシンクにスキーマがインポートされます。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、CDM ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | [形式] は必ず `cdm` | はい | `cdm` | format |
| メタデータ形式 | データへのエンティティ参照が置かれている場所。 CDM バージョン 1.0 を使用する場合は、manifest を選択します。 1\.0 より前のバージョンの CDM を使用している場合は、model.json を選択します。 | はい | `'manifest'` または `'model'` | manifestType |
| ルートの場所: コンテナー | CDM フォルダーのコンテナー名 | はい | String | fileSystem |
| ルートの場所: フォルダー パス | CDM フォルダーのルート フォルダーの場所 | はい | String | folderPath |
| マニフェスト ファイル: エンティティのパス | ルート フォルダー内のエンティティのフォルダー パス | no | String | entityPath |
| マニフェスト ファイル: マニフェスト名 | マニフェスト ファイルの名前。 既定値は 'default'  | いいえ | String | manifestName |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no | Timestamp | modifiedAfter <br> modifiedBefore | 
| スキーマのリンクされたサービス | コーパスが配置されているリンクされたサービス | はい (マニフェストを使用する場合) | `'adlsgen2'` または `'github'` | corpusStore | 
| エンティティ参照コンテナー | その中にコーパスがあるコンテナー | はい (ADLS Gen2 でマニフェストとコーパスを使用する場合) | String | adlsgen2_fileSystem |
| エンティティ参照リポジトリ | GitHub リポジトリ名 | はい (GitHub でマニフェストとコーパスを使用する場合) | String | github_repository |
| エンティティ参照ブランチ | GitHub リポジトリ ブランチ | はい (GitHub でマニフェストとコーパスを使用する場合) | String |  github_branch |
| コーパス フォルダー | コーパスのルートの場所 | はい (マニフェストを使用する場合) | String | corpusPath |
| コーパス エンティティ | エンティティ参照のパス | はい | String | エンティティ |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

### <a name="sink-settings"></a>シンクの設定

* 記述するエンティティの定義を含む CDM エンティティ参照ファイルをポイントします。

![エンティティの設定](media/data-flow/common-data-model-111.png "エンティティ参照")

* ADF でエンティティの記述に使用する出力ファイルのパーティション パスと形式を定義します。

![エンティティ形式](media/data-flow/common-data-model-222.png "エンティティ形式")

* 出力ファイルの場所とマニフェスト ファイルの場所と名前を設定します。

![CDM の場所](media/data-flow/common-data-model-333.png "CDM の場所")


#### <a name="import-schema"></a>Import schema

CDM はインライン データセットとしてのみ利用でき、既定では、関連付けられたスキーマはありません。 列メタデータを取得するには、 **[プロジェクション]** タブの **[スキーマのインポート]** ボタンをクリックします。これにより、コーパスによって指定されている列名とデータ型を参照できます。 スキーマをインポートするには、[データ フロー デバッグ セッション](concepts-data-flow-debug-mode.md)をアクティブにする必要があり、既存の CDM エンティティ定義ファイルをポイントする必要があります。

シンク変換でデータ フロー列をエンティティ プロパティにマップする場合は、"マッピング" タブをクリックし、"スキーマのインポート" を選択します。 ADF によって、シンク オプションでポイントしたエンティティ参照が読み取られ、これでターゲットの CDM スキーマにマップできるようになります。

![CDM シンクの設定](media/data-flow/common-data-model-444.png "CDM マッピング")

> [!NOTE]
>  Power BI または Power Platform データフローに由来する model.json ソース タイプを使用すると、ソース変換から "コーパスのパスが null または空である" というエラーが発生することがあります。 原因は、model.json ファイル内のパーティションの場所のパスで書式に問題がある可能性があります。 これを修正するには、次の手順に従います。 

1. model.json ファイルをテキスト エディターで開く
2. partitions.Location プロパティを検索する 
3. "blob.core.windows.net" を "dfs.core.windows.net" に変更する
4. URL の "% 2F" エンコードを "/" に修正する
 

### <a name="cdm-source-data-flow-script-example"></a>CDM ソース データ フロー スクリプトの例

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、CDM シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `cdm` である必要があります | はい | `cdm` | format |
| ルートの場所: コンテナー | CDM フォルダーのコンテナー名 | はい | String | fileSystem |
| ルートの場所: フォルダー パス | CDM フォルダーのルート フォルダーの場所 | はい | String | folderPath |
| マニフェスト ファイル: エンティティのパス | ルート フォルダー内のエンティティのフォルダー パス | no | String | entityPath |
| マニフェスト ファイル: マニフェスト名 | マニフェスト ファイルの名前。 既定値は 'default' | いいえ | String | manifestName |
| スキーマのリンクされたサービス | コーパスが配置されているリンクされたサービス | はい | `'adlsgen2'` または `'github'` | corpusStore | 
| エンティティ参照コンテナー | その中にコーパスがあるコンテナー | はい (コーパスが ADLS Gen2 にある場合) | String | adlsgen2_fileSystem |
| エンティティ参照リポジトリ | GitHub リポジトリ名 | はい (コーパスが GitHub にある場合) | String | github_repository |
| エンティティ参照ブランチ | GitHub リポジトリ ブランチ | はい (コーパスが GitHub にある場合) | String |  github_branch |
| コーパス フォルダー | コーパスのルートの場所 | はい | String | corpusPath |
| コーパス エンティティ | エンティティ参照のパス | はい | String | エンティティ |
| パーティションのパス | パーティションが書き込まれる場所 | no | String | partitionPath |
| フォルダーのクリア | 書き込みの前に宛先フォルダーがクリアされるかどうか | no | `true` または `false` | truncate |
| 形式の種類 | Parquet 形式を指定する場合に選択 | no | `parquet` (指定される場合) | subformat |
| 列区切り記号 | DelimitedText に書き込む場合に、列を区切る方法 | はい (DelimitedText に書き込む場合) | String | columnDelimiter |
| 先頭の行を見出しとして使用 | DelimitedText を使用する場合に、列名を見出しとして追加するかどうか | no | `true` または `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM シンク データ フロー スクリプトの例

関連付けられているデータ フロー スクリプトは次のとおりです。

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>次のステップ

マッピング データ フローの[ソース変換](data-flow-source.md)を作成します。
