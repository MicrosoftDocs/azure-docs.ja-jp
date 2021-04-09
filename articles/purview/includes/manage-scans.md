---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: ac83f455d2af82c3f6970077fccb17f07e53cccc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98901049"
---
## <a name="creating-and-running-a-scan"></a>スキャンの作成と実行

> [!Note] 
> 次に示す手順とスクリーンショットは、さまざまな種類のデータ ソース間でスキャンを管理するための一般的なプロセスを示しています。 使用するデータ ソースの種類によっては、選択肢が多少異なる場合があります。

新しいスキャンを作成して実行するには、次の操作を行います。

1. **[ソース]** に移動します

1. 登録したデータ ソースを選択します。

1. **[+ 新しいスキャン]** を選択します

1. 対象のデータ ソースに接続するための資格情報を選択します。 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="スキャンを設定する":::

1. 一覧内の適切な項目にチェック マークを付けることによって、スキャンの対象を、フォルダー、コレクション、スキーマなど、データ ソースの特定の部分に限定することができます。

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. スキャンのスキャン ルール セットを選択します。 システムの既定のルール セットを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行の確認

既存のスキャンを確認するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。 

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans---edit-delete-or-cancel"></a>スキャンを管理する - 編集、削除、またはキャンセル

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。 
