---
title: Azure Front Door Standard/Premium (プレビュー) 正常性プローブの監視
description: この記事は、バックエンドの正常性が Azure Front Door によってどのように監視されるかを理解するのに役立ちます。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098042"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure Front Door Standard/Premium (プレビュー) 正常性プローブの監視

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door は、HTTP または HTTPS 要求を各バックエンドに定期的に送信します。 これらの要求によって、Azure Front Door は、バックエンド プール内の各エンドポイントの正常性を確認できます。 Front Door は、プローブからのこれらの応答を使用して、クライアント要求のルーティング先として "最適な" バックエンドリソースを決定します。 

> [!WARNING]
> Front Door は世界中に多くのエッジ環境があるので、バックエンドに対する正常性プローブの量が極めて多くなる可能性があります。構成されている正常性プローブの頻度にもよりますが、毎分 25 回要求されることもあれば、多いときは毎分 1200 回要求されることもあります。 既定のプローブ頻度は 30 秒であり、バックエンドのプローブ量は毎分 200 要求程度になるはずです。

## <a name="supported-protocols"></a>サポートされるプロトコル

Front Door では、HTTP または HTTPS プロトコルを使用したプローブの送信をサポートしています。 これらのプローブは、クライアント要求のルーティング用に構成された同じ TCP ポートを介して送信され、オーバーライドすることはできません。

## <a name="supported-http-methods-for-health-probes"></a>正常性プローブでサポートされている HTTP メソッド

Front Door では、正常性プローブを送信するための次の HTTP メソッドがサポートされています。

* **GET:** GET メソッドでは、Request-URI によって特定された情報 (エンティティ形式) がすべて取得されます。
* **HEAD:** HEAD メソッドは、サーバーからは応答でメッセージ本文を返すことが禁止されるという点を除き、GET と同じです。 新しい Front Door プロファイルの場合、既定では、プローブ メソッドは HEAD に設定されます。

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

1. 正常性プローブ エラーが発生したバックエンドを除外します。

    * この選択は、最後の _n_ 個の正常性プローブ応答を調べることによって行われます。 少なくとも _x_ 個以上が正常であれば、バックエンドは正常であると見なされます。

    * _n_ を構成するには、負荷分散の設定の SampleSize プロパティを変更します。

    * _x_ を構成するには、負荷分散の設定の SuccessfulSamplesRequired プロパティを変更します。

1. Front Door は、さらに、バックエンド プール内の正常なバックエンドのセットを対象に、各バックエンドの待ち時間 (ラウンドトリップ時間) を測定し、維持します。


## <a name="complete-health-probe-failure"></a>正常性プローブの完全なエラー

バックエンド プール内のすべてのバックエンドに対して正常性プローブが失敗した場合、Front Door はすべてのバックエンドが正常であると見なし、ラウンド ロビン ディストリビューションでバックエンド全体にトラフィックをルーティングします。

いずれかのバックエンドが正常な状態に戻った時点で、Front Door は通常の負荷分散アルゴリズムを再開します。

## <a name="disabling-health-probes"></a>正常性プローブを無効にする

バックエンド プールに 1 つのバックエンドがある場合、またはバックエンド プールでアクティブなバックエンドが 1 つだけの場合は、正常性プローブを無効にすることができます。 これにより、アプリケーション バックエンドの負荷が軽減されます。

## <a name="next-steps"></a>次のステップ

[Front Door Standard または Premium を作成する](create-front-door-portal.md)方法について学習します。
