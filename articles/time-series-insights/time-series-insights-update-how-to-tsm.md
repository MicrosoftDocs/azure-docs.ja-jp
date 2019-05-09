---
title: Azure Time Series Insights プレビューでのデータ モデリング | Microsoft Docs
description: Azure Time Series Insights プレビューでのデータ モデリングについて説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555406"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューでのデータ モデリング

このドキュメントでは、Azure Time Series Insights プレビューに従ってタイム シリーズ モデルを操作する方法について説明します。 いくつかの一般的なデータ シナリオについて詳しく説明します。

更新を使用する方法の詳細については、「[Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md)」(Azure Time Series Insights プレビュー エクスプローラー) をご覧ください。

## <a name="types"></a>型

### <a name="create-a-single-type"></a>1 つの種類を作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[種類]** を選択します。 パネルを折りたたんで、タイム シリーズ モデルの種類に注目します。

    ![Portal_one][1]

1. **[追加]** を選択します。
1. 種類に関係するすべての詳細情報を入力して、**[作成]** を選択します。 この操作により、環境に種類が作成されます。

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>1 つまたは複数の種類を一括アップロードする

1. **[JSON をアップロードします]** を選択します。
1. 種類のペイロードを含むファイルを選択します。
1. **[アップロード]** を選択します。

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>1 つの種類を編集する

種類を選択して、**[編集]** を選択します。 必要な変更を行い、**[保存]** を選択します。

![Portal_four][4]

### <a name="delete-a-type"></a>種類を削除する

種類を選択して、**[削除]** を選択します。 種類にインスタンスが関連付けられていない場合は、削除されます。

![Portal_five][5]

## <a name="hierarchies"></a>階層

### <a name="create-a-single-hierarchy"></a>1 つの階層を作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[階層]** を選択します。 パネルを折りたたんで、タイム シリーズ モデルの階層に注目します。

    ![Portal_six][6]

1. **[追加]** を選択します。

    ![Portal_seven][7]

1. 右側のウィンドウで **[レベルの追加]** を選択します。

    ![Portal_eight][8]

1. 階層の詳細を入力して、**[作成]** を選択します。

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>1 つまたは複数の階層を一括アップロードする

1. **[JSON をアップロードします]** を選択します。
1. 階層のペイロードを含むファイルを選択します。
1. **[アップロード]** を選択します。

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>1 つの階層を編集する

階層を選択し、**[編集]** を選択します。 必要な変更を行い、**[保存]** を選択します。

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>階層を削除する

階層を選択し、**[削除]** を選択します。 階層にインスタンスが関連付けられていない場合は、削除されます。

![Portal_twelve][12]

## <a name="instances"></a>Instances

### <a name="create-a-single-instance"></a>1 つのインスタンスを作成する

1. タイム シリーズ モデル セレクター パネルに移動し、メニューから **[インスタンス]** を選択します。 パネルを折りたたんで、タイム シリーズ モデルのインスタンスに注目します。

    ![Portal_thirteen][13]

1. **[追加]** を選択します。

    ![Portal_fourteen][14]

1. インスタンスの詳細を入力し、種類と階層の関連付けを選択して、**[作成]** を選択します。

### <a name="bulk-upload-one-or-more-instances"></a>1 つまたは複数のインスタンスを一括アップロードする

1. **[JSON をアップロードします]** を選択します。
1. インスタンスのペイロードを含むファイルを選択します。

    ![Portal_fifteen][15]

1. **[アップロード]** を選択します。

### <a name="edit-a-single-instance"></a>1 つのインスタンスを編集する

インスタンスを選択して、**[編集]** を選択します。 必要な変更を行い、**[保存]** を選択します。

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>インスタンスを削除する

インスタンスを選択して、**[削除]** を選択します。 イベントがインスタンスに関連付けられていない場合は、削除されます。

## <a name="next-steps"></a>次の手順

- タイム シリーズ モデルの詳細については、「[Data modeling](./time-series-insights-update-tsm.md)」(データ モデリング) をご覧ください。
- プレビューの詳細については、「[Visualize data in the Azure Time Series Insights Preview explorer](./time-series-insights-update-explorer.md)」(Azure Time Series Insights プレビュー エクスプローラーでデータを視覚化する) をご覧ください。
- サポートされている JSON の構造の詳細については、「[サポートされている JSON 構造](./time-series-insights-send-events.md#json)」をご覧ください。

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png