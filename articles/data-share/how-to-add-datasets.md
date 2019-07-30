---
title: Azure Data Share プレビューで既存のデータ共有にデータセットを追加する
description: 既存のデータ共有にデータセットを追加する
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: t-maadam
ms.openlocfilehash: 01197d91277c59b58c5ab841dfc2abfb78be71de
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877409"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Azure Data Share プレビューで既存の共有にデータセットを追加する方法

この記事では、Azure Data Share プレビューを使用して、既存のデータ共有にデータセットを追加する方法について説明します。 これにより、新しい共有を作成しなくても、同じ受信者とより多くのデータを共有できます。

共有の作成時にデータセットを追加する方法については、[データの共有](share-your-data.md)に関するチュートリアルを参照してください。

## <a name="navigate-to-a-sent-data-share"></a>送信されたデータ共有に移動する

Azure Data Share プレビューで、送信された共有に移動し、 **[データセット]** タブを選択します。さらに多くのデータセットを追加するには、 **[+ Add Datasets] (+ データセットの追加)** ボタンをクリックします。

![データセットを追加する](./media/how-to/how-to-add-datasets/add-datasets.png)

右側にあるパネルで、追加するデータセットの型を選択し、 **[次へ]** をクリックします。 追加するデータのサブスクリプションとリソース グループを選択します。 ドロップダウンの矢印を使用して、追加するデータの横にあるボックスを見つけてオンにします。

![データセットを追加する](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**[Add Datasets] (データセットの追加)** をクリックすると、データセットが共有に追加されます。 注:コンシューマーに新しいデータセットが表示されるようにするには、コンシューマーがスナップショットをトリガーする必要があります。 スナップショットの設定が構成されている場合は、次回スケジュールされているスナップショットが完了すると、コンシューマーに新しいデータセットが表示されます。 スナップショットの設定が構成されていない場合、コンシューマーは、更新を受信するためにデータの完全または増分コピーを手動でトリガーする必要があります。 スナップショットの詳細については、[スナップショット](terminology.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
[既存のデータ共有に受信者を追加する](how-to-add-recipients.md)方法について学習します。