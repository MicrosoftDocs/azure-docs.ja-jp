---
title: Azure Data Factory の Mapping Data Flow の行の変更変換
description: Azure Data Factory の Mapping Data Flow の行の変更変換を使用してデータベースのターゲットを更新する方法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: d842898ca700490ae99b46140be6609622a144df
ms.sourcegitcommit: 5f41e855d415cfa741d8f710792ea486480df5cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2019
ms.locfileid: "58133163"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory の行の変更変換

行の変更変換を使用して、行の挿入、削除、更新、アップサート ポリシーを設定します。 一対多の条件を式として追加できます。 これらの条件によってそれぞれ、行が挿入、削除、更新、アップサートされます。 行の変更では、ご利用のデータベースに対して DDL と DML の両方を生成できます。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![行設定の変更](media/data-flow/alter-row1.png "行設定の変更")

> [!NOTE]
> 行の変更変換は、自分のデータ フローのデータベース シンクでのみ動作します。 行に割り当てるアクション (挿入、更新、削除、アップサート) は、デバッグ セッション中に発生することはありません。 Execute Data Flow タスクをパイプラインに追加し、パイプラインのデバッグまたはトリガーを使用して、ご利用のデータベース テーブルで行ポリシーの変更を適用する必要があります。

## <a name="view-policies"></a>ポリシーの表示

Data Flow のデバッグ モードをオンに切り替えて、[データ プレビュー] ウィンドウで行ポリシーの変更の結果を表示します。 Data Flow のデバッグ モードで行の変更を実行しても、自分のターゲットに対して DDL アクションや DML アクションは生成されません。 このようなアクションを発生させるには、パイプライン内の Execute Data Flow アクティビティでデータ フローを実行します。

![行ポリシーの変更](media/data-flow/alter-row3.png "行ポリシーの変更")

これにより、条件に基づいて各行の状態を確認して表示できるようになります。 データ フローで発生する挿入、更新、削除、アップサート アクションごとに、それを表すアイコンがあり、パイプライン内でデータ フローを実行するときに発生しているアクションを示します。

## <a name="sink-settings"></a>シンクの設定

行の変更を動作させるために、データベース シンクの種類を設定する必要があります。 シンクの設定では、各アクションを許可するように設定する必要があります。

![行シンクの変更](media/data-flow/alter-row2.png "行シンクの変更")

データベース シンクを使用した ADF Data Flow 内の既定の動作は、行を挿入するためのものです。 更新、アップサート、削除も許可する場合、アクションを許可するシンクでこれらのチェック ボックスもオンにする必要があります。

> [!NOTE]
> 挿入、更新、またはアップサートでシンクのターゲット テーブルのスキーマが変更される場合、データ フローは失敗します。 データベースでターゲット スキーマを変更するために、シンクで "テーブルの再作成" オプションを選ぶ必要があります。 これにより、新しいスキーマ定義でご利用のテーブルがドロップされ、再作成されます。

## <a name="next-steps"></a>次の手順

行の変更変換の後に、[自分のデータと配布先データ ストアをシンク](data-flow-sink.md)する必要がある場合があります。
