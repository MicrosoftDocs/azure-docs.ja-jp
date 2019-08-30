---
title: Azure Front Door Service - バックエンドの正常性監視 | Microsoft Docs
description: この記事では、Azure Front Door Service によってバックエンドの正常性がどのように監視されるかを説明します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742386"
---
# <a name="health-probes"></a>正常性プローブ

それぞれの Front Door 環境では、各バックエンドの正常性を確認するために、構成されている各バックエンドに合成 HTTP/HTTPS 要求を定期的に送信します。 Front Door は、これらのプローブからの応答を使用して、実際のクライアント要求のルーティング先として "最適な" バックエンドを決定します。 Front Door は世界中に多くのエッジ環境があるので、バックエンドに対する正常性プローブ要求の量は、1 秒あたり複数の要求に達することがあり、構成された正常性プローブ頻度によって異なります。 



## <a name="supported-protocols"></a>サポートされるプロトコル

Front Door では、HTTP または HTTPS プロトコルを使用したプローブの送信をサポートしています。 これらのプローブは、クライアント要求のルーティング用に構成された同じ TCP ポートを介して送信され、オーバーライドすることはできません。

## <a name="health-probe-responses"></a>正常性プローブの応答

| Responses  | 説明 | 
| ------------- | ------------- |
| 正常性の特定  |  200 OK 状態コードは、バックエンドが正常であることを示します。 他のすべての状態コードは、エラーとみなされます。 (ネットワーク障害を含む) 何らかの理由で有効な HTTP 応答がプローブに対して受信されない場合、そのプローブはエラーとしてカウントされます。|
| 待ち時間の測定  | 待ち時間は、プローブ要求を送信する直前から応答の最後のバイトを受信した瞬間までの実時間です。 要求ごとに新しい TCP 接続が使用されるため、この測定値が既存のウォーム接続のあるバックエンドに偏ることはありません。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door によるバックエンドの正常性の判定方法

Azure Front Door Service では、すべてのアルゴリズムで次の同じ 3 段階のプロセスを使用して、正常性を判定します。

1. 無効なバックエンドを除外します。

2. 正常性プローブ エラーが発生したバックエンドを除外します。
    * この選択は、最後の _n_ 個の正常性プローブ応答を調べることによって行われます。 少なくとも _x_ 個以上が正常であれば、バックエンドは正常であると見なされます。

    * _n_ を構成するには、負荷分散の設定の SampleSize プロパティを変更します。

    * _x_ を構成するには、負荷分散の設定の SuccessfulSamplesRequired プロパティを変更します。

3. Front Door は、さらに、バックエンド プール内の正常なバックエンドのセットを対象に、各バックエンドの待ち時間 (ラウンドトリップ時間) を測定し、維持します。


## <a name="complete-health-probe-failure"></a>正常性プローブの完全なエラー

バックエンド プール内のすべてのバックエンドに対して正常性プローブが失敗した場合、Front Door はすべてのバックエンドが正常であると見なし、ラウンド ロビン ディストリビューションでバックエンド全体にトラフィックをルーティングします。

いずれかのバックエンドが正常な状態に戻った時点で、Front Door は通常の負荷分散アルゴリズムを再開します。

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
