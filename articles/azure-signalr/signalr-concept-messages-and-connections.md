---
title: Azure SignalR Service でのメッセージと接続
description: Azure SignalR Service でのメッセージと接続に関する主要な概念の概要です。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556774"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR Service でのメッセージと接続

Azure SignalR Service の課金モデルは、接続の数とメッセージの数に基づいています。 この記事では、メッセージと接続を定義する方法、および課金のためにそれらをカウントする方法について説明します。


## <a name="message-formats"></a>メッセージ形式 

Azure SignalR Service では、ASP.NET Core SignalR と同じ形式がサポートされます:[JSON](https://www.json.org/) と [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)。

## <a name="message-size"></a>メッセージ サイズ

Azure SignalR Service では、メッセージのサイズに制限はありません。

大きいメッセージは各 2 KB 以下の小さなメッセージに分割され、個別のメッセージとして送信されます。 メッセージの分割と結合は SDK が処理します。 開発者が対処する必要はありません。

大きいメッセージでは、メッセージングのパフォーマンスに悪影響があります。 可能な限り小さいメッセージを使用し、各ユース ケース シナリオに最適なメッセージ サイズをテストして決定してください。

## <a name="how-messages-are-counted-for-billing"></a>課金に対してメッセージがカウントされる方法

課金に対しては、Azure SignalR Service からの送信メッセージのみがカウントされます。 クライアントとサーバー間の ping メッセージは無視されます。

2 KB を超えるメッセージは、各 2 KB の複数のメッセージとしてカウントされます。 Azure portal のメッセージ カウント グラフは、ハブあたり 100 件のメッセージごとに更新されます。

たとえば、3 つのクライアントと 1 つのアプリケーション サーバーがあるものとします。 1 つのクライアントが 4 KB のメッセージを 1 つ送信し、サーバーですべてのクライアントにブロードキャストします。 メッセージ数は 8 つになります。サービスからアプリケーション サーバーへの 1 つのメッセージと、サービスからクライアントへの 3 つのメッセージです。 各メッセージは、2 つの 2 KB のメッセージとしてカウントされます。

Azure portal に表示されるメッセージ数は、累計 100 件を超えるまでは 0 のままです。

## <a name="how-connections-are-counted"></a>接続のカウント方法

サーバー接続とクライアント接続があります。 既定では、各アプリケーション サーバーと Azure SignalR Service との接続はハブあたり 5 つで、各クライアントと Azure SignalR Service とのクライアント接続は 1 つです。

Azure portal に表示される接続数には、サーバー接続とクライアント接続の両方が含まれます。

たとえば、2 つのアプリケーション サーバーがあり、コードを使って 5 つのハブを定義するものとします。 サーバー接続の数は 50 になります:2 アプリ サーバー * 5 ハブ * 5 接続/ハブ。

ASP.NET SignalR では、サーバー接続数の計算方法が異なります。 ユーザーが定義するハブに加えて、1 つの既定のハブが含まれます。 既定では、各アプリケーション サーバーにさらに 5 つのサーバー接続が必要になります。 既定のハブの接続数は、他のハブの接続数と一貫しています。

## <a name="how-inboundoutbound-traffic-is-counted"></a>受信/送信トラフィックのカウント方法

受信トラフィックと送信トラフィックの区別は、Azure SignalR Service の観点に基づいています。 トラフィックは、バイト単位で計算されます。 メッセージ数と同様に、トラフィックにもサンプリング レートがあります。 Azure portal の受信/送信グラフは、ハブあたり 100 KB ごとに更新されます。

## <a name="related-resources"></a>関連リソース

- [Azure Monitor での集計の種類](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR の構成](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)