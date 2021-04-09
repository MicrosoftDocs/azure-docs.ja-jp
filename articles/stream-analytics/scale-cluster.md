---
title: Azure Stream Analytics クラスターのサイズを変更する
description: Azure Stream Analytics クラスターのサイズをスケールアップおよびスケールダウンする方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020028"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Azure Stream Analytics クラスターのサイズを変更する

Stream Analytics クラスターの容量は、ストリーミング ユニット (SU) 単位で測定されます。 実行中のすべてのジョブに割り当てられている SU の合計がクラスターの容量を超えない限りは、同じクラスター内で複数のジョブを並列実行できます。

クラスターの容量は、ストリーミング ワークロードのサイズに合わせてスケールアップおよびスケールダウンできます。 クラスターのスケーリングには時間がかかり、頻繁なスケーリングは想定されていません。 使用を予定している正確な数の SU でクラスターを計画およびプロビジョニングすることをお勧めします。

## <a name="change-the-scale-of-your-cluster"></a>クラスターのスケールを変更する

1. Azure portal 内で、目的の Stream Analytics クラスターを見つけて選択します。

1. **[概要]** セクションで、 **[スケール]** を選択します。 クラスターに割り当てられている SU の数を確認できます。 必要に応じて、セレクターを使用して SU を増減させます。

   ![クラスターのスケーリング](./media/scale-cluster/scale-cluster.png)

スケーリング操作は、現在実行中のジョブには影響しません。

## <a name="next-steps"></a>次のステップ

これで、Stream Analytics クラスターをスケールアップおよびスケールダウンする方法がわかりました。 次は、プライベート エンドポイントを管理し、ジョブを自動スケーリングする方法を学習します。

* [Azure Stream Analytics クラスター内でプライベート エンドポイントを管理する](private-endpoints.md)
* [Azure Stream Analytics クラスター内でジョブを管理する](manage-jobs-cluster.md)
