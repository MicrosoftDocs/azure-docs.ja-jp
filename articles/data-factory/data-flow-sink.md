---
title: マッピング データ フローでのシンク変換
description: マッピング データ フローでのシンク変換を構成する方法について説明します。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 5548d82326ec4ac2306e2c8945bedc20236a4e54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009353"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>マッピング データ フローでのシンク変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データの変換を完了したら、シンク変換を利用してそれを変換先ストアに書き込みます。 各データ フローには少なくとも 1 つのシンク変換が必要ですが、変換フローを完了するために必要な数だけのシンクに書き込むことができます。 追加のシンクに書き込むには、新しい分岐と条件分割によって新しいストリームを作成します。

各シンク変換は、厳密に 1 つの Azure Data Factory のデータセット オブジェクトまたはリンクされたサービスに関連付けられます。 シンク変換では、データの形状と書き込みを行う場所が決定されます。

## <a name="inline-datasets"></a>インライン データセット

シンク変換を作成するとき、シンク情報をデータセット オブジェクト内で定義するのか、シンク変換内で定義するのか選択します。 ほとんどの形式はどちらか一方しかありません。 特定のコネクタの使用方法については、該当するコネクタ ドキュメントを参照してください。

形式がインラインとデータセット オブジェクトの両方でサポートされているとき、両方に利点があります。 データセット オブジェクトは、他のデータ フローと、コピーなどのアクティビティとで使用できる再利用可能なエンティティです。 これらの再利用可能なエンティティは、強化されたスキーマを使用する場合に特に役立ちます。 データセットは Spark を基盤としていません。 場合によっては、シンク変換で特定の設定またはスキーマ プロジェクションをオーバーライドすることが必要となることがあります。

柔軟なスキーマ、1 回限りのシンク インスタンス、またはパラメーター化されたシンクを使用する際は、インライン データセットが推奨されます。 シンクが大きくパラメーター化されている場合、インライン データセットを使用すると、"ダミー" オブジェクトを作成できません。 インライン データセットは Spark を基盤とし、そのプロパティはデータ フローにネイティブです。

インライン データセットを使用するには、 **[シンクの種類]** セレクターで目的の形式を選択します。 シンク データセットを選択するのでなく、接続先にするリンクされたサービスを選択します。

![インラインが選択されていることを示すスクリーンショット。](media/data-flow/inline-selector.png "インラインが選択されていることを示すスクリーンショット。")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> サポートされているシンクの種類

マッピング データ フローは、抽出、読み込み、および変換 (ELT) のアプローチに従い、すべて Azure に存在する "*ステージング*" データセットを操作します。 現在は、次のデータセットをソース変換で使用できます。

| コネクタ | Format | データセット/インライン |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Model](format-common-data-model.md#sink-properties)<br>[区切りテキスト](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Managed Instance (プレビュー)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

これらのコネクタに固有の設定は、 **[設定]** タブ上にあります。これらの設定に関する情報とデータ フロー スクリプトの例は、コネクタのドキュメントに記載されています。

Azure Data Factory から、[90 を超えるネイティブ コネクタ](connector-overview.md)にアクセスできます。 それらの他のソースにデータ フローからデータを書き込むには、コピー アクティビティを使用し、サポートされているシンクからそのデータを読み込みます。

## <a name="sink-settings"></a>シンクの設定

シンクを追加したら、 **[シンク]** タブを使用して構成を行います。ここでは、シンクを書き込むデータセットを選択して作成できます データセット パラメーターの開発値は、[デバッグの設定](concepts-data-flow-debug-mode.md)で構成できます (デバッグ モードをオンにする必要があります)。

以下のビデオでは、テキスト区切りのファイルの種類に対応するさまざまなシンク オプションについて説明します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![[シンク] の設定を示すスクリーンショット。](media/data-flow/sink-settings.png "[シンク] の設定を示すスクリーンショット。")

**スキーマの誤差:** [スキーマの誤差](concepts-data-flow-schema-drift.md)は、データ フロー内の柔軟なスキーマをネイティブに処理するデータ ファクトリの機能であり、列の変更を明示的に定義する必要はありません。 シンク データ スキーマで定義されている内容の上に追加の列を記述するには、 **[Allow schema drift]\(スキーマの誤差を許可する\)** を有効にします。

**スキーマの検証**: [スキーマの検証] を選択すると、受信ソース スキーマの列がソース プロジェクション内で見つからない場合、またはデータ型が一致しない場合にデータ フローは失敗します。 この設定を使用して、ソース データが定義済みのプロジェクションのコントラクトを満たすように強制します。 これは、列の名前または型が変更されたことを通知する、データベース ソースのシナリオにおいて便利です。

## <a name="cache-sink"></a>キャッシュ シンク

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

*キャッシュ シンク* は、データ フローによって、データ ストアではなく Spark キャッシュにデータが書き込まれる場合に使用されます。 マッピング データ フローでは、*キャッシュ参照* を使用して、同じフロー内でこのデータを何度も参照できます。 これは、式の一部としてデータを参照したいが、列を明示的に結合したくない場合に便利です。 キャッシュ シンクが有用である一般的な例としては、データ ストアで最大値を検索することや、エラー コードをエラー メッセージ データベースと照合することが挙げられます。 

キャッシュ シンクに書き込むには、シンク変換を追加し、シンクの種類として **[キャッシュ]** を選択します。 他のシンクの種類とは異なり、外部ストアに書き込むのではないため、データセットやリンクされたサービスを選択する必要はありません。 

![キャッシュ シンクの選択](media/data-flow/select-cache-sink.png "キャッシュ シンクの選択")

シンクの設定で、必要に応じてキャッシュ シンクのキー列を指定できます。 これは、キャッシュ参照で `lookup()` 関数を使用する場合に、一致条件として使用されます。 キー列を指定する場合は、キャッシュ参照で `outputs()` 関数は使用できません。 キャッシュ参照構文の詳細については、[キャッシュされた参照](concepts-data-flow-expression-builder.md#cached-lookup)に関する記事を参照してください。

![キャッシュ シンクのキー列](media/data-flow/cache-sink-key-columns.png "キャッシュ シンクのキー列")

たとえば、`cacheExample` というキャッシュ シンクに 1 つのキー列 `column1` を指定する場合、`cacheExample#lookup()` の呼び出しに、キャッシュ シンク内のどの行と照合するかを指定する 1 つのパラメーターを含めます。 この関数によって、マップされた各列のサブ列を含む 1 つの複合列が出力されます。

> [!NOTE]
> キャッシュ シンクは、キャッシュ参照を使用してそれを参照する変換から、完全に独立したデータ ストリームに存在する必要があります。 また、キャッシュ シンクは、最初に書き込まれるシンクである必要があります。 

## <a name="field-mapping"></a>フィールドのマッピング

シンクの **[マッピング]** タブ上では、Select 変換と同様に、受信列が書き込まれるかどうかを決定できます。 既定では、誤差のある列を含め、すべての入力列がマップされます。 この動作は "*自動マッピング*" として知られています。

自動マッピングを無効にすると、固定列ベースのマッピングまたはルールベースのマッピングのいずれかを追加することができます。 ルールベースのマッピングを使用すると、パターン マッチングを含む式を作成できます。 固定マッピングを使用すると、論理および物理列名がマップされます。 ルールベースのマッピングの詳細については、[マッピング データ フローの列パターン](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)に関するページを参照してください。

## <a name="custom-sink-ordering"></a>カスタム シンクの順序付け

既定では、データが複数のシンクに書き込まれる順序は決まっていません。 変換ロジックが完了すると、実行エンジンによってデータは並列に書き込まれます。シンクの順序は実行ごとに異なる場合があります。 シンクの順序を正確に指定するには、データフローの **[全般]** タブで **[カスタム シンクの順序付け]** を有効にします。 有効にすると、シンクは昇順で連続して書き込まれます。

![[カスタム シンクの順序付け] を示すスクリーンショット。](media/data-flow/custom-sink-ordering.png "[カスタム シンクの順序付け] を示すスクリーンショット。")

> [!NOTE]
> [キャッシュされた参照](./concepts-data-flow-expression-builder.md#cached-lookup)を使用する場合は、シンクの順序付けで、キャッシュされたシンクが順序付けの一番下 (または最初) である 1 に設定されるようにしてください。

![カスタム シンクの順序付け](media/data-flow/cache-2.png "カスタム シンクの順序付け")

### <a name="sink-groups"></a>シンク グループ

シンクをグループ化するには、一連のシンクに同じ順序番号を適用します。 ADF は、それらのシンクを並列に実行できるグループとして扱います。 並列実行のオプションは、パイプライン データ フロー アクティビティに表示されます。

## <a name="error-row-handling"></a>エラー行の処理

データベースに書き込む場合、書き込み先で設定されている制約によって、データの特定の行が失敗することがあります。 既定では、データ フローの実行は最初に発生したエラーで失敗します。 一部のコネクタでは、 **[エラーのまま続行する]** を選択すると、個々の行でエラーが発生した場合でもデータ フローを完了することができます。 現時点では、この機能は Azure SQL Database でのみ使用できます。 詳細については、[Azure SQL DB でのエラー行の処理](connector-azure-sql-database.md#error-row-handling)に関する記事を参照してください。

以下は、シンク変換でデータベースのエラー行の処理を自動的に使用する方法を説明するビデオ チュートリアルです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>シンクでのデータのプレビュー

デバッグ クラスター上でデータ プレビューをフェッチすると、データはシンクに書き込まれません。 データの外観を示すスナップショットが返されますが、指定した変換先には何も書き込まれません。 シンクへのデータの書き込みをテストするには、パイプライン キャンバスからパイプラインのデバッグを実行します。

## <a name="next-steps"></a>次のステップ

これでデータ フローが作成されたので、[データ フローのアクティビティをパイプラインに](concepts-data-flow-overview.md)追加します。