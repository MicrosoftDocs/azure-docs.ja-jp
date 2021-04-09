---
title: Azure Internet Analyzer のトラブルシューティング | Microsoft Docs
description: Azure Internet Analyzer のトラブルシューティング リファレンス。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: troubleshooting
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: fe173ec4d4f28444d43739ea2ed51e43021916b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744359"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer のトラブルシューティング

この記事では、Internet Analyzer に関する一般的な問題のトラブルシューティング手順について説明します。

## <a name="things-to-keep-in-mind"></a>留意事項
- クライアント スクリプトは、**HTTPS** Web サイトに埋め込む必要があります。 スクリプトがプレーンテキスト (**http://** ) またはローカル (**file://** ) の Web サイトで実行されている場合、測定値は収集されません。
- 測定データは、インターネットアナライザー プロファイルのクライアント　スクリプトが、実際のユーザー トラフィックを受信するアプリケーションに埋め込まれている場合にのみ収集されます。 合成トラフィック (たとえば、Azure WebApp パフォーマンス テスト) では、通常、埋め込みの Javascript コードは実行されないため、この種類のトラフィックによって測定は生成されません。

## <a name="azure-portal"></a>Azure portal
**スコアカード セクションで、"選択したフィルターの組み合わせに対してスコアカードが生成されない"**
- スコアカードは、毎日 (毎日、UTC 時間の最後に) 生成されます。
- スコアカードは選択したフィルターの組み合わせ (テスト、期間、国および地域など) に対して 100 を超える測定値が収集された場合にのみ生成されます。

**テストの 1 つまたは両方のエンドポイントに対して "合計測定数" が 0 である**
- 時系列と測定数は 1 時間に 1 回計算されるため、新しい測定データを表示するには、少なくともその時間だけ待つ必要があります。
- Internet Analyzer では、成功した測定 (つまり HTTP 200 応答) のみが分析にカウントされます。 テストの一方または両方のエンドポイントが到達不能の場合、または 200 以外の HTTP コードを返す場合は、合計測定数に 0 と表示されます。

## <a name="next-steps"></a>次のステップ
[Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む
