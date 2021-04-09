---
title: Azure ネットワーク ラウンドトリップ待ち時間統計 |Microsoft Docs
description: Azure リージョン間のラウンドトリップ待ち時間統計について説明します。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: bc2d7bb7ba17a4a47fecf2144157f79f5367fca7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059173"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure ネットワーク ラウンドトリップ待ち時間統計

Azure は、内部監視ツールを使用してネットワークのコア領域の待ち時間 (速度) を継続的に監視するほか、サードパーティの総合監視サービス [ThousandEyes](https://thousandeyes.com) によって収集された測定値も監視します。

## <a name="how-are-the-measurements-collected"></a>測定値の収集方法

待ち時間の測定値は、世界中の Azure クラウド リージョンでホストされている ThousandEyes エージェントから収集されます。エージェントはネットワーク プローブを 1 分間隔で相互に継続的に送信します。 月ごとの待ち時間の統計は、その月に収集されたサンプルを平均して算出されます。

## <a name="december-2020-round-trip-latency-figures"></a>2020 年 12 月のラウンド トリップ待ち時間の数値

過去 30 日間の Azure リージョン間の毎月の平均ラウンド トリップ時間 (2020 年 12 月 31 日に終了) を次に示します。 次に示す測定値は [ThousandEyes](https://thousandeyes.com) から得られたものです。

[![Azure リージョン間の待ち時間統計](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>次のステップ

[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)について確認します。
