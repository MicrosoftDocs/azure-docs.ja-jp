---
title: マッピング データ フローのソース変換
description: マッピング データ フローのソース変換を設定する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 310a062a8600539750935c93c7d10a1cf17a885d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016391"
---
# <a name="source-transformation-in-mapping-data-flow"></a>マッピング データ フローのソース変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ソース変換は、データ フローのデータ ソースを構成します。 データ フローを設計する際、最初の手順では、常にソース変換を構成します。 ソースを追加するには、データ フローのキャンバスにある **[Add Source]\(ソースの追加\)** ボックスを選択します。

各データ フローには少なくとも 1 つのソース変換が必要ですが、データ変換を完了するために必要な数だけソースを追加できます。 これらのソースは、結合、参照、または和集合変換を使用して結合できます。

各ソース変換が関連付けられるデータセットまたはリンクされたサービスは 1 つだけです。 データセットは、書き込みまたは読み取りを行うデータの形状と場所を定義します。 ファイルベースのデータセットを使用する場合、ソース内でワイルドカードやファイル リストを使用すると、一度に複数のファイルを操作できます。

## <a name="inline-datasets"></a>インライン データセット

ソース変換を作成するときに最初に行う決定は、ソース情報がデータセット オブジェクト内またはソース変換内で定義されるかどうかです。 ほとんどの形式はどちらか一方しかありません。 特定のコネクタの使用方法については、該当するコネクタ ドキュメントを参照してください。

形式がインラインとデータセット オブジェクトの両方でサポートされているとき、両方に利点があります。 データセット オブジェクトは、他のデータ フローと、コピーなどのアクティビティとで使用できる再利用可能なエンティティです。 これらの再利用可能なエンティティは、強化されたスキーマを使用する場合に特に役立ちます。 データセットは Spark を基盤としていません。 場合によっては、ソース変換で特定の設定またはスキーマ プロジェクションをオーバーライドすることが必要になります。

インライン データセットは、柔軟なスキーマ、1 回限りのソース インスタンス、またはパラメーター化されたソースの使用時に推奨されます。 ソースが大きくパラメーター化されている場合、インライン データセットでは、"ダミー" オブジェクトを作成しなくて済みます。 インライン データセットは Spark を基盤とし、それらのプロパティはデータ フローにネイティブです。

インライン データセットを使用するには、 **[Source Type]\(ソースの種類\)** セレクターで目的の形式を選択します。 ソース データセットを選択せず、接続先にするリンクされたサービスを選択します。

![インラインが選択されていることを示すスクリーンショット。](media/data-flow/inline-selector.png "インラインが選択されていることを示すスクリーンショット。")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> サポートされるソースの種類

マッピング データ フローは、抽出、読み込み、変換 (ELT) のアプローチに従い、すべて Azure に存在する "*ステージング*" データセットを操作します。 現在は、次のデータセットをソース変換で使用できます。

| コネクタ | Format | データセット/インライン |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model](format-common-data-model.md#source-properties)<br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

これらのコネクタに固有の設定は、 **[Source options]\(ソース オプション\)** タブにあります。これらの設定に関する情報とデータ フロー スクリプトの例は、コネクタのドキュメントに記載されています。

Azure Data Factory から、[90 を超えるネイティブ コネクタ](connector-overview.md)にアクセスできます。 それらの他のソースからのデータをデータ フローに含めるには、コピー アクティビティを使用して、サポートされているステージング領域のいずれかにそのデータを読み込みます。

## <a name="source-settings"></a>ソースの設定

ソースを追加したら、 **[ソースの設定]** タブを使用して構成します。ここでは、ソースが指すデータセットを選択または作成できます。 また、データのスキーマとサンプリング オプションも選択できます。

データセット パラメーターの開発値は、[デバッグの設定](concepts-data-flow-debug-mode.md)で構成できます (デバッグ モードをオンにする必要があります)。

![[ソースの設定] タブを示すスクリーンショット。](media/data-flow/source1.png "[ソースの設定] タブを示すスクリーンショット。")

**[出力ストリーム名]** : ソース変換の名前です。

**[ソースの種類]** : インライン データセットまたは既存のデータセット オブジェクトのどちらを使用するかを選択します。

**[テスト接続]** : ソース データセットで使用されているリンクされたサービスにデータ フローの Spark サービスが正常に接続できるかどうかをテストします。 この機能を有効にするにはデバッグ モードをオンにする必要があります。

**[スキーマの誤差]** : [[スキーマの誤差]](concepts-data-flow-schema-drift.md) は、データ フロー内の柔軟なスキーマをネイティブに処理する Data Factory の機能であり、列の変更を明示的に定義する必要はありません。

* ソース列が頻繁に変更される場合は、 **[Allow schema drift]\(スキーマの誤差を許可\)** チェック ボックスをオンにします。 この設定により、すべての受信ソース フィールドが変換を通してシンクに流れることができます。

* **[Infer drifted column types]\(誤差のある列の型を推論\)** を選択すると、Data Factory は、検出された新しい列ごとにデータ型を検出して定義するよう指示されます。 この機能が無効になっている場合、誤差の列はすべて文字列型になります。

**[Validate schema]:** **[スキーマの検証]** を選択した場合、受信したソース データがデータセットの定義済みスキーマと一致しなければ、データ フローの実行は失敗します。

**[Skip line count]\(スキップ行数\)** : この **[Skip line count]\(スキップ行数\)** フィールドでは、データセットの先頭で無視する行数を指定します。

**サンプリング**:ソースからの行数を制限するには、**サンプリング** を有効にします。 デバッグの目的でソースのデータをテストまたはサンプリングする場合は、この設定を使用します。 これは、パイプラインからデバッグ モードでデータ フローを実行する場合に非常に便利です。

ソースが正しく構成されていることを確認するには、デバッグ モードを有効にし、データ プレビューを取り込みます。 詳細については、[デバッグ モード](concepts-data-flow-debug-mode.md)に関するページを参照してください。

> [!NOTE]
> デバッグ モードを有効にすると、データ プレビュー時に、デバッグの設定での行数上限の構成によってソースのサンプリング設定が上書きされます。

## <a name="source-options"></a>ソース オプション

**[Source options]\(ソース オプション\)** タブには、選択したコネクタおよび形式に固有の設定が含まれています。 詳細と例については、関連する[コネクタのドキュメント](#supported-sources)を参照してください。

## <a name="projection"></a>Projection

データセット内のスキーマと同様に、ソース内のプロジェクションでは、ソース データのデータの列、型、および形式が定義されます。 SQL や Parquet など、ほとんどのデータセットの種類では、ソース内のプロジェクトは、データセットで定義されたスキーマを反映するように固定されています。 ソース ファイルが厳密に型指定されていない場合 (たとえば、Parquet ファイルでなくフラットな .csv ファイル)、このソース変換では各フィールドのデータ型を定義できます。

![[プロジェクション] タブの設定を示すスクリーンショット。](media/data-flow/source3.png "[プロジェクション] タブの設定を示すスクリーンショット。")

テキスト ファイルが定義済みのスキーマを含まない場合は、Data Factory がデータ型をサンプリングして推論するように、 **[Detect data type]** を選択します。 **[Define default format]\(既定の形式の定義\)** を選択して、既定のデータ形式を自動検出します。

**スキーマをリセットする** と、参照されているデータベースにある定義にプロジェクションがリセットされます。

列のデータ型は、下流の派生列変換で変更できます。 選択変換を使用して、列の名前を変更します。

### <a name="import-schema"></a>Import schema

**[プロジェクション]** タブの **[スキーマのインポート]** ボタンを選択すると、アクティブなデバッグ クラスターを使用し、スキーマ プロジェクションを作成できます。 あらゆるソースの種類で利用できます。 スキーマをここでインポートすると、データセットに定義されているプロジェクションがオーバーライドされます。 データセット オブジェクトは変更されません。

スキーマのインポートは、複雑なデータ構造をサポートする Avro や Azure Cosmos DB のようなデータセットで、データセットにスキーマ定義の存在を必要としない場合に便利です。 インライン データセットの場合、スキーマのインポートは、スキーマの誤差なしで列のメタデータを参照する唯一の方法です。

## <a name="optimize-the-source-transformation"></a>ソース変換を最適化する

**[Optimize]\(最適化\)** タブでは、各変換手順でパーティション情報を編集できます。 ほとんどの場合 **[Use current partitioning]\(現在のパーティション分割を使用\)** を選択すると、ソースの理想的なパーティション構造に最適化されます。

Azure SQL Database ソースから読み取る場合、カスタムの **[ソース]** パーティション分割にすると、データが最速で読み取られる可能性があります。 Data Factory は、データベースへの接続を並列で行うことによって、大規模なクエリを読み取ります。 このソース パーティション分割は、列に対して、またはクエリを使用して行うことができます。

![ソース パーティション設定を示すスクリーンショット。](media/data-flow/sourcepart3.png "ソース パーティション設定を示すスクリーンショット。")

マッピング データ フロー内での最適化の詳細については、[[Optimize] タブ](concepts-data-flow-overview.md#optimize)に関する説明を参照してください。

## <a name="next-steps"></a>次のステップ

[派生列変換](data-flow-derived-column.md)と[選択変換](data-flow-select.md)を使用して、データ フローの構築を開始します。
