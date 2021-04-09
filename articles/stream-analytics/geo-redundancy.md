---
title: Azure Stream Analytics ジョブの geo 冗長性を実現する
description: この記事では、geo フェールオーバーではなく Azure Stream Analytics ジョブの geo 冗長性を実現する方法について説明します。
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015523"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブの geo 冗長性を実現する

Azure Stream Analytics では自動的な geo フェールオーバーは提供されませんが、同じ Stream Analytics ジョブを複数の Azure リージョンにデプロイすることで、geo 冗長性を実現できます。 各ジョブは、ローカル入力ソースとローカル出力ソースに接続されます。 2 つのリージョン入力への入力データの送信と、2 つのリージョン出力の間の調整は、どちらもアプリケーションで行う必要があります。 Stream Analytics ジョブは、2 つの独立したエンティティです。

次の図は、イベント ハブを入力とし、Azure データベースを出力とする、geo 冗長 Stream Analytics ジョブのデプロイの例を示したものです。

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="geo 冗長 Stream Analytics ジョブの図":::

## <a name="primarysecondary-strategy"></a>プライマリとセカンダリの戦略

アプリケーションでは、どのリージョンの出力データベースをプライマリと見なし、どれをセカンダリと見なすかを、管理する必要があります。 プライマリ リージョンで障害が発生した場合、アプリケーションではセカンダリ データベースに切り替えて、そのデータベースからの更新の読み取りを開始します。 読み取りの重複が最小限になる実際のメカニズムは、アプリケーションによって異なります。 出力に追加情報を書き込むことで、このプロセスを簡略化できます。 たとえば、各出力にタイムスタンプまたはシーケンス ID を追加することで、重複する行のスキップを簡単な操作にできます。 プライマリ リージョンが復元された後、同様のメカニズムを使用してセカンダリ データベースに追いつくことができます。

入力と出力の種類が異なると、異なる geo レプリケーション オプションを使用できますが、この記事で説明されているパターンを使用して、geo 冗長性を実現することをお勧めします。このようにすれば、イベント プロデューサーとイベント コンシューマーの両方を柔軟に制御できます。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Azure Stream Analytics ジョブを監視および管理する](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Azure Stream Analytics でのデータ主導型デバッグ](stream-analytics-job-diagram-with-metrics.md)