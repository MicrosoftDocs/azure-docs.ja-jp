---
title: 既存の Azure Data Share にデータセットを追加する
description: Azure Data Share で既存のデータ共有にデータセットを追加し、同じ受信者と共有する方法について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 540cdc2948cbcd97bf7c8872f3aace2862280434
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513491"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Azure Data Share で既存の共有にデータセットを追加する方法

この記事では、Azure Data Share を使用して、既存のデータ共有にデータセットを追加する方法について説明します。 これにより、新しい共有を作成しなくても、同じ受信者とより多くのデータを共有できます。

共有の作成時にデータセットを追加する方法については、[データの共有](share-your-data.md)に関するチュートリアルを参照してください。

## <a name="navigate-to-a-sent-data-share"></a>送信されたデータ共有に移動する

Azure Data Share で、送信された共有に移動し、 **[データセット]** タブを選択します。さらに多くのデータセットを追加するには、 **[+ Add Datasets] (+ データセットの追加)** ボタンをクリックします。

![データセットを追加する](./media/how-to/how-to-add-datasets/add-datasets.png)

右側にあるパネルで、追加するデータセットの型を選択し、 **[次へ]** をクリックします。 追加するデータのサブスクリプションとリソース グループを選択します。 ドロップダウンの矢印を使用して、追加するデータの横にあるボックスを見つけてオンにします。

![データセットを追加する](./media/how-to/how-to-add-datasets/add-datasets-side.png)

**[Add Datasets] (データセットの追加)** をクリックすると、データセットが共有に追加されます。 注:コンシューマーに新しいデータセットが表示されるようにするには、コンシューマーがスナップショットをトリガーする必要があります。 スナップショットの設定が構成されている場合は、次回スケジュールされているスナップショットが完了すると、コンシューマーに新しいデータセットが表示されます。 スナップショットの設定が構成されていない場合、コンシューマーは、更新を受信するためにデータの完全または増分コピーを手動でトリガーする必要があります。 スナップショットの詳細については、[スナップショット](terminology.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
[既存のデータ共有に受信者を追加する](how-to-add-recipients.md)方法について学習します。