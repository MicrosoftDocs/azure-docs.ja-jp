---
title: Gen2 環境でのデータ モデリング ‐ Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 でのデータ モデリングについて確認します。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016855"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 でのデータ モデリング

この記事では、Azure Time Series Insights Gen2 でタイム シリーズ モデルを操作する方法について説明します。 いくつかの一般的なデータ シナリオについて詳しく説明します。

> [!TIP]
>
> * [タイム シリーズ モデル](concepts-model-overview.md)の詳細を確認してください。
> * [Azure Time Series Insights Gen2 エクスプローラー](./concepts-ux-panels.md)での移動について参照してください。

## <a name="instances"></a>Instances

Azure Time Series Insights エクスプローラーでは、ブラウザー内でのインスタンスの **作成**、**読み取り**、**更新**、および **削除** 操作がサポートされています。

開始するには、Azure Time Series Insights エクスプローラーの **[分析]** ビューから **[モデル]** ビューを選択します。

### <a name="create-a-single-instance"></a>1 つのインスタンスを作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[インスタンス]** を選択します。 選択した Azure Time Series Insights 環境に関連付けられているすべてのインスタンスが表示されます。

    [![最初に [インスタンス] を選択して、1 つのインスタンスを作成します。](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. **[+ 追加]** を選択します。

    [![[+ 追加] ボタンを選択して、インスタンスを追加します。](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. インスタンスの詳細を入力し、種類と階層の関連付けを選択して、 **[作成]** を選択します。

### <a name="bulk-upload-one-or-more-instances"></a>1 つまたは複数のインスタンスを一括アップロードする

> [!TIP]
> インスタンスは、JSON でデスクトップに保存できます。ダウンロードした JSON ファイルは、次の手順でアップロードできます。

1. **[JSON をアップロードします]** を選択します。
1. インスタンスのペイロードを含むファイルを選択します。

    [![JSON を使用してインスタンスを一括アップロードします。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. **[アップロード]** を選択します。

### <a name="edit-a-single-instance"></a>1 つのインスタンスを編集する

1. インスタンスを選択し、 **[編集]** または **鉛筆アイコン** を選択します。
1. 必要な変更を行い、 **[保存]** を選択します。

    [![1 つのインスタンスを編集します。](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>インスタンスを削除する

1. インスタンスを選択し、 **[削除]** または **ごみ箱アイコン** を選択します。

   [![[削除] を選択して、インスタンスを削除します。](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. **[削除]** を選択して削除を確定します。

> [!NOTE]
> 削除対象のインスタンスは、フィールド検証チェックに合格する必要があります。

## <a name="hierarchies"></a>階層

Azure Time Series Insights エクスプローラーでは、ブラウザー内での階層の **作成**、**読み取り**、**更新**、および **削除** 操作がサポートされています。

開始するには、Azure Time Series Insights エクスプローラーの **[分析]** ビューから **[モデル]** ビューを選択します。

### <a name="create-a-single-hierarchy"></a>1 つの階層を作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[階層]** を選択します。 選択した Azure Time Series Insights 環境に関連付けられているすべての階層が表示されます。

    [![ウィンドウを使用して階層を作成します。](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. **[+ 追加]** を選択します。

    [![階層の [+ 追加] ボタン。](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. 右側のウィンドウで **[+ レベルの追加]** を選択します。

    [![階層にレベルを追加します。](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. 階層の詳細を入力して、 **[保存]** を選択します。

    [![階層の詳細を指定します。](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>1 つまたは複数の階層を一括アップロードする

> [!TIP]
> 階層は、JSON でデスクトップに保存できます。ダウンロードした JSON ファイルは、次の手順でアップロードできます。

1. **[JSON をアップロードします]** を選択します。
1. 階層のペイロードを含むファイルを選択します。
1. **[アップロード]** を選択します。

    [![階層を一括アップロードする場合の選択。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>1 つの階層を編集する

1. 階層を選択し、 **[編集]** または **鉛筆アイコン** を選択します。
1. 必要な変更を行い、 **[保存]** を選択します。

    [![1 つの階層を編集する場合の選択。](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>階層を削除する

1. 階層を選択し、 **[削除]** または **ごみ箱アイコン** を選択します。

    [![[削除] ボタンを選択して、階層を削除します。](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. **[削除]** を選択して削除を確定します。

## <a name="types"></a>型

Azure Time Series Insights エクスプローラーでは、ブラウザー内での型の **作成**、**読み取り**、**更新**、および **削除** 操作がサポートされています。

開始するには、Azure Time Series Insights エクスプローラーの **[分析]** ビューから **[モデル]** ビューを選択します。

### <a name="create-a-single-type"></a>1 つの種類を作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[種類]** を選択します。 選択した Azure Time Series Insights 環境に関連付けられているすべての型が表示されます。

    [![タイム シリーズ モデルの型ウィンドウ。](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. **[+ 追加]** を選択して **[新しい型の追加]** ポップアップ モーダルを表示します。
1. 型のプロパティと変数を入力します。 入力したら、 **[保存]** を選択します。

    [![型を追加するための構成設定。](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>1 つまたは複数の種類を一括アップロードする

> [!TIP]
> 型は、JSON でデスクトップに保存できます。ダウンロードした JSON ファイルは、次の手順でアップロードできます。

1. **[JSON をアップロードします]** を選択します。
1. 種類のペイロードを含むファイルを選択します。
1. **[アップロード]** を選択します。

    [![型の一括アップロード オプション。](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>1 つの種類を編集する

1. 型を選択し、 **[編集]** または **鉛筆アイコン** を選択します。
1. 必要な変更を行い、 **[保存]** を選択します。

    [![ウィンドウで型を編集します。](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>種類を削除する

1. 型を選択し、 **[削除]** または **ごみ箱アイコン** を選択します。

   [![[削除] を選択して、型を削除します。](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. **[削除]** を選択して削除を確定します。

## <a name="next-steps"></a>次のステップ

* タイム シリーズ モデルの詳細については、[データ モデリング](./concepts-model-overview.md)に関する記事を参照してください。

* Gen2 の詳細については、[Azure Time Series Insights Gen2 エクスプローラーでデータを視覚化する](./concepts-ux-panels.md)方法に関する記事を参照してください。

* サポートされている JSON の構造については、[サポートされている JSON 構造](./time-series-insights-send-events.md#supported-json-shapes)に関する記事を参照してください。