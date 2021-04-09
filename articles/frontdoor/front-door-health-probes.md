---
title: Azure Front Door - バックエンドの正常性監視 | Microsoft Docs
description: この記事は、バックエンドの正常性が Azure Front Door によってどのように監視されるかを理解するのに役立ちます
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449235"
---
# <a name="health-probes"></a>正常性プローブ

それぞれの Front Door 環境では、特定の Front Door 環境の各バックエンドの正常性と近接性を確認するために、構成されている各バックエンドに合成 HTTP/HTTPS 要求を定期的に送信します。 Front Door は、プローブからのこれらの応答を使用して、クライアント要求のルーティング先として "最適な" バックエンドリソースを決定します。 

> [!WARNING]
> Front Door は世界中に多くのエッジ環境があるので、バックエンドに対する正常性プローブの量が極めて多くなる可能性があります。構成されている正常性プローブの頻度にもよりますが、毎分 25 回要求されることもあれば、多いときは毎分 1200 回要求されることもあります。 既定のプローブ頻度は 30 秒であり、バックエンドのプローブ量は毎分 200 要求程度になるはずです。

## <a name="supported-protocols"></a>サポートされるプロトコル

Front Door では、HTTP または HTTPS プロトコルを使用したプローブの送信をサポートしています。 これらのプローブは、クライアント要求のルーティング用に構成された同じ TCP ポートを介して送信され、オーバーライドすることはできません。

## <a name="supported-http-methods-for-health-probes"></a>正常性プローブでサポートされている HTTP メソッド

Front Door では、正常性プローブを送信するための次の HTTP メソッドがサポートされています。

1. **GET:** GET メソッドでは、Request-URI によって特定された情報 (エンティティ形式) がすべて取得されます。
2. **HEAD:** HEAD メソッドは、サーバーからは応答でメッセージ本文を返すことが禁止されるという点を除き、GET と同じです。 新しい Front Door プロファイルの場合、既定では、プローブ メソッドは HEAD に設定されます。

> [!NOTE]
> Front Door でバックエンドの負荷とコストを抑える場合、正常性プローブに HEAD 要求を推奨しています。

## <a name="health-probe-responses"></a>正常性プローブの応答

| Responses  | 説明 | 
| ------------- | ------------- |
| 正常性の特定  |  200 OK 状態コードは、バックエンドが正常であることを示します。 他のすべての状態コードは、エラーとみなされます。 (ネットワーク障害を含む) 何らかの理由で有効な HTTP 応答がプローブに対して受信されない場合、そのプローブはエラーとしてカウントされます。|
| 待ち時間の測定  | 待ち時間は、プローブ要求を送信する直前から応答の最後のバイトを受信した瞬間までの実時間です。 要求ごとに新しい TCP 接続が使用されるため、この測定値が既存のウォーム接続のあるバックエンドに偏ることはありません。  |

## <a name="how-front-door-determines-backend-health"></a>Front Door によるバックエンドの正常性の判定方法

Azure Front Door では、すべてのアルゴリズムで次の同じ 3 段階のプロセスを使用して、正常性を判定します。

1. 無効なバックエンドを除外します。

2. 正常性プローブ エラーが発生したバックエンドを除外します。
    * この選択は、最後の _n_ 個の正常性プローブ応答を調べることによって行われます。 少なくとも _x_ 個以上が正常であれば、バックエンドは正常であると見なされます。

    * _n_ を構成するには、負荷分散の設定の SampleSize プロパティを変更します。

    * _x_ を構成するには、負荷分散の設定の SuccessfulSamplesRequired プロパティを変更します。

3. Front Door は、さらに、バックエンド プール内の正常なバックエンドのセットを対象に、各バックエンドの待ち時間 (ラウンドトリップ時間) を測定し、維持します。


## <a name="complete-health-probe-failure"></a>正常性プローブの完全なエラー

バックエンド プール内のすべてのバックエンドに対して正常性プローブが失敗した場合、Front Door はすべてのバックエンドが正常であると見なし、ラウンド ロビン ディストリビューションでバックエンド全体にトラフィックをルーティングします。

いずれかのバックエンドが正常な状態に戻った時点で、Front Door は通常の負荷分散アルゴリズムを再開します。

## <a name="disabling-health-probes"></a>正常性プローブを無効にする

バックエンド プールにバックエンドが 1 つある場合、正常性プローブを無効にし、アプリケーション バックエンドの負荷を減らすことができます。 バックエンド プールに複数のバックエンドがあるが、そのうちの 1 つだけが有効な状態にある場合でも、正常性プローブを無効にできます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
