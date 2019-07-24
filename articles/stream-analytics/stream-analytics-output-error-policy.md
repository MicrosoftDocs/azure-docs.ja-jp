---
title: Azure Stream Analytics の出力エラー ポリシー
description: Azure Stream Analytics で利用できる出力エラー処理ポリシーについて説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111182"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics の出力エラー ポリシー
この記事では、Azure Stream Analytics で構成できる出力データ エラー処理ポリシーについて説明します。

出力データ エラー処理ポリシーは、Stream Analytics ジョブで生成された出力イベントがターゲット シンクのスキーマに準拠しないときに発生するデータ変換エラーにのみ適用されます。 **[再試行]** か **[ドロップ]** を選択することでこのポリシーを構成できます。 Azure Portal の Stream Analytics ジョブの **[構成]** の下で **[エラー ポリシー]** を選択して選択を行います。

![Azure Stream Analytics の出力エラー ポリシーの場所](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Retry
エラーが発生すると、Azure Stream Analytics により、書き込みが成功するまで無期限でイベントの書き込みが試行されます。 再試行にはタイムアウトがありません。 最終的には、再試行しているイベントによって後続のすべてのイベントの処理が妨げられます。 このオプションは既定の出力エラー処理ポリシーです。

## <a name="drop"></a>ドロップ
Azure Stream Analytics により、データ変換エラーを起こした出力イベントがドロップされます。 ドロップされたイベントを後で復元し、再処理することはできません。


(ネットワーク エラーなど) 一時的なエラーはすべて、出力エラー処理ポリシーの構成に関係なく、再試行されます。


## <a name="next-steps"></a>次の手順
[Azure Stream Analytics のトラブルシューティング ガイド](stream-analytics-troubleshooting-guide.md)
