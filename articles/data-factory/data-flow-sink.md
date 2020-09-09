---
title: マッピング データ フローでのシンク変換
description: マッピング データ フローでのシンク変換を構成する方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: b2a356f8ee6c8e3d11548a3c7c17a868e4c057c4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179183"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>マッピング データ フローでのシンク変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データの変換を完了したら、シンク変換を利用してそれを変換先ストアに書き込みます。 各データ フローには少なくとも 1 つのシンク変換が必要ですが、変換フローを完了するために必要な数だけのシンクに書き込むことができます。 追加のシンクに書き込むには、新しい分岐と条件分割によって新しいストリームを作成します。

各シンク変換は、厳密に 1 つの Azure Data Factory のデータセット オブジェクトまたはリンクされたサービスに関連付けられます。 シンク変換では、データの形状と書き込みを行う場所が決定されます。

## <a name="inline-datasets"></a>インライン データセット

シンク変換を作成するとき、シンク情報をデータセット オブジェクト内で定義するのか、シンク変換内で定義するのか選択します。 ほとんどの形式はどちらか一方しかありません。 特定のコネクタの使用方法については、該当するコネクタ ドキュメントを参照してください。

形式がインラインとデータセット オブジェクトの両方でサポートされているとき、両方に利点があります。 データセット オブジェクトは、他のデータ フローと、コピーなどのアクティビティで活用できる再利用可能なエンティティです。 これらは特に、強化されたスキーマの使用時に役立ちます。 データセットは Spark を基盤とせず、シンク変換で特定の設定やスキーマ プロジェクションをオーバーライドしなければならない場合があります。

インライン データセットは、柔軟なスキーマ、1 回限りのシンク インスタンス、またはパラメーター化されたシンクの使用時に推奨されます。 シンクが大きくパラメーター化されている場合、インライン データセットでは、"ダミー" オブジェクトを作成できません。 インライン データセットは Spark を基盤とし、そのプロパティはデータ フローにネイティブです。

インライン データセットを使用するには、 **[シンクの種類]** セレクターで目的の形式を選択します。 シンク データセットを選択せず、接続先にするリンクされたサービスを選択します。

![インライン データセット](media/data-flow/inline-selector.png "インライン データセット")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> サポートされているシンクの種類

Mapping Data Flow は、抽出、読み込み、変換 (ELT) のアプローチに従い、すべて Azure に存在する "*ステージング*" データセットを操作します。 現在は、次のデータセットをソース変換で使用できます。

| コネクタ | Format | データセット/インライン |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br> [差分 (プレビュー)](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br> [差分 (プレビュー)](format-delta.md) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (プレビュー)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

これらのコネクタに固有の設定は、 **[設定]** タブにあります。これらの設定に関する情報とデータ フロー スクリプトの例は、コネクタのドキュメントに記載されています。 

Azure Data Factory は、[90 を超えるネイティブ コネクタ](connector-overview.md)にアクセスできます。 それらの他のソースにデータ フローからデータを書き込むには、コピー アクティビティを使用し、サポートされているシンクからそのデータを読み込みます。

## <a name="sink-settings"></a>シンクの設定

シンクを追加したら、 **[シンク]** タブから構成を行います。ここでは、シンクを書き込むデータセットを選択して作成できます 以下の動画では、テキスト区切りのファイルの種類に対応するさまざまなシンク オプションについて説明します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![シンク設定](media/data-flow/sink-settings.png "シンクの設定")

**スキーマの誤差:** [スキーマの誤差](concepts-data-flow-schema-drift.md)は、データ フロー内の柔軟なスキーマをネイティブに処理するデータ ファクトリの機能であり、列の変更を明示的に定義する必要はありません。 シンク データ スキーマで定義されている内容の上に追加の列を記述するには、 **[Allow schema drift]\(スキーマの誤差を許可する\)** を有効にします。

**[Validate schema]:** [スキーマの検証] を選択すると、受信ソース スキーマの列がソース プロジェクションに見つからない場合、またはデータ型が一致しない場合にデータ フローは失敗します。 この設定を使用して、ソース データが定義済みのプロジェクションのコントラクトを満たすように強制します。 これは、列の名前または型が変更されたことを通知するために、データベース ソースのシナリオで非常に便利です。

## <a name="field-mapping"></a>フィールドのマッピング

シンクの **[マッピング]** タブ内では、Select 変換とほぼ同様に、受信列が書き込まれるかどうかを決定できます。 既定では、誤差のある列を含め、すべての入力列がマップされます。 これは、**自動マッピング**と呼ばれます。

自動マッピングを無効にすると、固定列ベースのマッピングまたはルールベースのマッピングのいずれかを追加するオプションを利用できます。 ルールベースのマッピングでは、パターン マッチングを使用した式を作成でき、固定マッピングでは、論理列名と物理列名がマップされます。 ルールベースのマッピングの詳細については、[マッピング データ フローの列パターン](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)に関する記事を参照してください。

## <a name="custom-sink-ordering"></a>カスタム シンクの順序付け

既定では、データが複数のシンクに書き込まれる順序は決まっていません。 変換ロジックは完了しており、シンクの順序は実行ごとに異なる場合があるので、実行エンジンではデータは並列に書き込まれます。 シンクの順序を正確に指定するには、データフローの全般タブで **[Custom sink ordering]\(カスタム シンクの順序付け\)** を有効にします。 有効にすると、シンクは昇順で連続して書き込まれます。

![カスタム シンクの順序付け](media/data-flow/custom-sink-ordering.png "カスタム シンクの順序付け")

## <a name="data-preview-in-sink"></a>シンクでのデータのプレビュー

デバッグ クラスター上でデータ プレビューをフェッチすると、データはシンクに書き込まれません。 データの外観を示すスナップショットが返されますが、指定した変換先には何も書き込まれません。 シンクへのデータの書き込みをテストするには、パイプライン キャンバスからパイプラインのデバッグを実行します。

## <a name="next-steps"></a>次のステップ
これでデータ フローが作成されたので、[Data Flow アクティビティをパイプラインに](concepts-data-flow-overview.md)追加します。
