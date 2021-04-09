---
title: Azure SignalR Service でのメッセージと接続
description: Azure SignalR Service でのメッセージと接続に関する主要な概念の概要です。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491947"
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

たとえば、1 つのアプリケーション サーバーと 3 つのクライアントがあるとします。

アプリ サーバーは、接続されているすべてのクライアントに 1 KB のメッセージをブロードキャストします。アプリ サーバーからサービスへのメッセージは、無料の受信メッセージと見なされます。 サービスからそれぞれのクライアントに送信される 3 つのメッセージのみが、送信メッセージとして課金されます。

クライアント A は、アプリ サーバーを経由せずに、別のクライアント B に 1 KB のメッセージを送信します。 クライアント A からサービスへのメッセージは、無料の受信メッセージです。 サービスからクライアント B へのメッセージは、送信メッセージとして課金されます。

3 つのクライアントと 1 つのアプリケーション サーバーがあるとします。 1 つのクライアントが 4 KB のメッセージを 1 つ送信し、サーバーですべてのクライアントにブロードキャストします。 課金されるメッセージ数は 8 つです。サービスからアプリケーション サーバーへのメッセージが 1 つ、サービスからクライアントへのメッセージが 3 つです。 各メッセージは、2 つの 2 KB のメッセージとしてカウントされます。

## <a name="how-connections-are-counted"></a>接続のカウント方法

Azure SignalR Service にはサーバー接続とクライアント接続があります。 既定では、各アプリケーション サーバーの初期接続はハブあたり 5 個で、各クライアントのクライアント接続は 1 個です。

たとえば、2 つのアプリケーション サーバーがあり、コードで 5 つのハブを定義するとします。 サーバー接続の数は 50 になります:2 アプリ サーバー * 5 ハブ * 5 接続/ハブ。

Azure portal に表示される接続数には、サーバー接続、クライアント接続、診断接続、およびライブ トレース接続が含まれます。 接続の種類は以下の一覧で定義されています。

- **サーバー接続**: Azure SignalR Service とアプリ サーバーを接続します。
- **クライアント接続**: Azure SignalR Service とクライアント アプリを接続します。
- **診断接続**: より詳細なログを生成できる特殊なクライアント接続であり、パフォーマンスに影響する可能性があります。 この種類のクライアントは、トラブルシューティング用に設計されています。
- **ライブ トレース接続**: ライブ トレース エンドポイントに接続し、Azure SignalR Service のライブ トレースを受信します。 
 
ライブ トレース接続は、クライアント接続およびサーバー接続としてカウントされないことに注意してください。 

ASP.NET SignalR では、サーバー接続数の計算方法が異なります。 ユーザーが定義するハブに加えて、1 つの既定のハブが含まれます。 既定では、各アプリケーション サーバーにさらに 5 つの初期サーバー接続が必要になります。 既定のハブの初期接続数は、他のハブと一貫しています。

サービスとアプリケーション サーバーは、パフォーマンスとサービスの安定性を向上させるため、引き続き、接続状態を同期し、サーバー接続に対して調整を加えます。  そのため、サーバーの接続数がときどき変更される場合があります。

## <a name="how-inboundoutbound-traffic-is-counted"></a>受信/送信トラフィックのカウント方法

サービスに送信されるメッセージは、受信メッセージです。 サービスから送信されたメッセージは、送信メッセージです。 トラフィックは、バイト単位で計算されます。

## <a name="related-resources"></a>関連リソース

- [Azure Monitor での集計の種類](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR の構成](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
