---
title: マッピング データ フローでの行の変更変換
description: マッピング データ フローで行の変更変換を使用してデータベースのターゲットを更新する方法
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982651"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>マッピング データ フローでの行の変更変換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

行の変更変換を使用して、行の挿入、削除、更新、アップサート ポリシーを設定します。 一対多の条件を式として追加できます。 各行は一致する最初の式に対応したポリシーでマークされるので、これらの条件は優先度の順に指定する必要があります。 これらの条件によってそれぞれ、行が挿入、更新、削除、アップサートされます。 行の変更では、ご利用のデータベースに対して DDL と DML の両方を生成できます。

![行の変更の設定](media/data-flow/alter-row1.png "行の変更の設定")

行の変更変換は、自分のデータ フローのデータベース シンクまたは Cosmos DB シンクでのみ動作します。 行に割り当てるアクション (挿入、更新、削除、upsert) は、デバッグ セッション中に発生することはありません。 データ フローの実行アクティビティをパイプラインで実行し、データベース テーブルに行の変更ポリシーを適用します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>既定の行ポリシーを指定する

行の変更変換を作成し、`true()` の条件を持つ行ポリシーを指定します。 以前に定義された式のいずれにも一致しない行はそれぞれ、指定された行ポリシー用にマークされます。 既定では、どの条件式にも一致しない行はそれぞれ、`Insert` 用にマークされます。

![行の変更ポリシー](media/data-flow/alter-row4.png "行の変更ポリシー")

> [!NOTE]
> すべての行を 1 つのポリシーでマークするには、そのポリシーの条件を作成し、条件を `true()` として指定します。

## <a name="view-policies-in-data-preview"></a>データのプレビューでポリシーを表示する

[デバッグ モード](concepts-data-flow-debug-mode.md)を使用して、[データのプレビュー] ペインで行の変更ポリシーの結果を表示します。 行の変更変換のデータのプレビューでは、ターゲットに対する DDL または DML アクションは生成されません。

![行の変更を行う複数のポリシー](media/data-flow/alter-row3.png "行の変更を行う複数のポリシー")

挿入、更新、upsert、または削除アクションが発生するかどうかを示すアイコンによって、それぞれの行の変更ポリシーが表されます。 上部ヘッダーは、プレビューの各ポリシーによって影響を受ける行数を示しています。

## <a name="allow-alter-row-policies-in-sink"></a>シンクで行の変更ポリシーを許可する

行の変更ポリシーを機能させるには、データ ストリームがデータベースまたは Cosmos シンクに書き込む必要があります。 シンクの **[設定]** タブで、そのシンクで許可する行の変更ポリシーを有効にします。

![行の変更のシンク](media/data-flow/alter-row2.png "行の変更のシンク")

既定の動作では、挿入のみが許可されます。 更新、upsert、または削除を許可するには、その条件に対応する、シンクのチェックボックスをオンにします。 更新、upsert、または削除が有効になっている場合は、シンク内のどのキー列を照合するかを指定する必要があります。

> [!NOTE]
> 挿入、更新、または upsert によりシンクのターゲット テーブルのスキーマが変更される場合、データ フローは失敗します。 データベース内のターゲット スキーマを変更するには、テーブル アクションとして **[Recreate table]\(テーブルの再作成\)** を選択します。 これにより、新しいスキーマ定義でご利用のテーブルがドロップされ、再作成されます。

シンク変換では、一意の行 ID を表す 1 つのキーまたは一連のキーがターゲット データベースに必要です。 SQL シンクの場合、それらのキーの設定は、シンク設定タブで行います。CosmosDB の場合は、それらの設定にパーティション キーを設定したうえで、CosmosDB のシステム フィールド "id" をシンクのマッピングで設定します。 CosmosDB で update、upsert、delete を行う場合は、システム列である "id" を含める必要があります。

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Azure SQL Database と Synapse を使用したマージと upsert

ADF データフローでは、upsert オプションを使用して、Azure SQL Database と Synapse データベース プール (データ ウェアハウス) に対するマージをサポートしています。

しかし、ターゲット データベース スキーマでキー列の ID プロパティが使用されているシナリオが発生する場合があります。 ADF では、更新と upsert の行の値を一致させるために使用するキーを識別する必要があります。 しかし、ターゲット列に ID プロパティが設定されていて、upsert ポリシーを使用している場合、ターゲット データベースでは列への書き込みが許可されません。 分散テーブルのディストリビューション列に対して upsert を実行しようとすると、エラーが発生する場合もあります。

これを修正する方法を次に示します。

1. シンク変換の設定に移動し、"キー列の書き込みのスキップ" を設定します。 これにより、ADF によってマッピングのキー値として選択した列が書き込まれないように指示されます。

2. このキー列が ID 列の問題の原因となっている列でない場合は、シンク変換の前処理の SQL オプション ```SET IDENTITY_INSERT tbl_content ON``` を使用できます。 次に、後処理の SQL プロパティ ```SET IDENTITY_INSERT tbl_content OFF``` を指定してこれをオフにします。

3. ID ケースとディストリビューション列ケースの両方について、条件分割変換を使用して別の更新条件と別の挿入条件を使用する Upsert からロジックを切り替えることができます。 この方法では、更新パスにマッピングを設定して、キー列のマッピングを無視できます。

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>例

以下の例は、受信ストリーム `SpecifyUpsertConditions` を受け取り、行の変更条件を 3 つ作成する、`CleanData` という行の変更変換です。 前の変換では、データベース内で行の挿入、更新、削除を実行するかどうかを決定する `alterRowCondition` という列が計算されます。 列の値に、行の変更ルールと一致する文字列値が含まれている場合、そのポリシーが割り当てられています。

Data Factory UX では、この変換は次の図のようになります。

![行の変更の例](media/data-flow/alter-row4.png "行の変更の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>次のステップ

行の変更変換の後に、[自分のデータと配布先データ ストアをシンク](data-flow-sink.md)する必要がある場合があります。
