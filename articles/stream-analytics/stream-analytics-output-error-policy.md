---
title: Azure Stream Analytics の出力エラー ポリシー
description: Azure Stream Analytics で利用できる出力エラー処理ポリシーについて説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19d762a55127af34e84185b11518aa6584acb5bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012412"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics の出力エラー ポリシー
この記事では、Azure Stream Analytics で構成できる出力データ エラー処理ポリシーについて説明します。

出力データ エラー処理ポリシーは、Stream Analytics ジョブで生成された出力イベントがターゲット シンクのスキーマに準拠しないときに発生するデータ変換エラーにのみ適用されます。 **[再試行]** か **[ドロップ]** を選択することでこのポリシーを構成できます。 Azure Portal の Stream Analytics ジョブの **[構成]** の下で **[エラー ポリシー]** を選択して選択を行います。

![Azure Stream Analytics の出力エラー ポリシーの場所](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>[再試行]
エラーが発生すると、Azure Stream Analytics により、書き込みが成功するまで無期限でイベントの書き込みが試行されます。 再試行にはタイムアウトがありません。 最終的には、再試行しているイベントによって後続のすべてのイベントの処理が妨げられます。 このオプションは既定の出力エラー処理ポリシーです。

## <a name="drop"></a>Drop
Azure Stream Analytics により、データ変換エラーを起こした出力イベントがドロップされます。 ドロップされたイベントを後で復元し、再処理することはできません。


(ネットワーク エラーなど) 一時的なエラーはすべて、出力エラー処理ポリシーの構成に関係なく、再試行されます。


## <a name="next-steps"></a>次のステップ
[Azure Stream Analytics のトラブルシューティング ガイド](./stream-analytics-troubleshoot-query.md)