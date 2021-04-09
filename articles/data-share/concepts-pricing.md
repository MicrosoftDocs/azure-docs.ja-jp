---
title: Azure Data Share の料金について
description: Azure Data Share の料金体系について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88136686"
---
# <a name="understand-azure-data-share-pricing"></a>Azure Data Share の料金について

スナップショット ベースの共有の場合、Azure Data Share の料金は、データセットのスナップショットとスナップショットの実行に対して請求されます。 この記事では、スナップショットの履歴情報を使用して、データセットのスナップショットとスナップショットの実行を見積もる方法について説明します。 現在、データ プロバイダーはデータセットのスナップショットとスナップショットの実行に対して課金されます。

## <a name="dataset-snapshot"></a>データセットのスナップショット

データセットのスナップショットは、データセットをその送信元から送信先に移動する操作です。 共有のスナップショットが取得される場合は、その共有内の各データセットのスナップショットがデータセットのスナップショット操作になります。 次の手順に従って、データセットのスナップショットの一覧を表示します。 

1. Azure portal で、データ共有リソースに移動します。

1. [送信された共有] または [受信された共有] から共有を選択します。

1. **[履歴]** タブをクリックします。

1. スナップショットの開始時刻をクリックします。
 
    ![スナップショットの履歴](./media/concepts/concepts-pricing/pricing-snapshot-history.png "スナップショットの履歴") 

1. データセットのスナップショット操作の一覧を表示します。 各行項目がデータセットのスナップショット操作に対応します。 この例では、2 つのデータセットのスナップショットがあります。

    ![データセットのスナップショット操作](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "データセットのスナップショット操作")

## <a name="snapshot-execution"></a>スナップショットの実行

スナップショットの実行には、データセットを送信元から送信先に移動するために必要なリソースが含まれます。 データセットのスナップショット操作ごとに、スナップショットの実行は、仮想コアの数にスナップショットの期間を掛けた値として計算されます。 料金は分単位で比例配分された後、切り上げられます。 仮想コアの数は、ソースとターゲットのペアとデータのパターンに基づいて動的に選択されます。 次の手順に従って、スナップショットの開始時刻、終了時刻、データセットのスナップショット操作に使用された仮想コアの数を表示します。

1. Azure portal で、データ共有リソースに移動します。

1. [送信された共有] または [受信された共有] から共有を選択します。

1. **[履歴]** タブをクリックします。

1. スナップショットの開始時刻をクリックします。

    ![スナップショットの履歴](./media/concepts/concepts-pricing/pricing-snapshot-history.png "スナップショットの履歴") 

1. データセットのスナップショット操作の [状態] をクリックします。

    ![データセットのスナップショットの状態](./media/concepts/concepts-pricing/pricing-snapshot-status.png "データセットのスナップショットの状態")

1. 開始時刻、終了時刻、このデータセットのスナップショット操作に使用された仮想コアの数を表示します。 

    ![スナップショットの実行](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "スナップショットの実行")

## <a name="next-steps"></a>次の手順

- 最新の価格情報を入手する - [Azure Data Share の価格](https://azure.microsoft.com/pricing/details/data-share/)
- Azure 料金計算ツールを使用してコストを見積もる - [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
