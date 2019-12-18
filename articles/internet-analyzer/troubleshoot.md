---
title: Azure Internet Analyzer のトラブルシューティング | Microsoft Docs
description: Azure Internet Analyzer のトラブルシューティング リファレンス。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896897"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer のトラブルシューティング

この記事では、Internet Analyzer に関する一般的な問題のトラブルシューティング手順について説明します。

## <a name="azure-portal"></a>Azure Portal
**スコアカード セクションでの「スコア カードに対して十分な測定が収集されていない」**

以下の点に注意してください。
- 測定データは、インターネットアナライザー プロファイルのクライアント　スクリプトが、実際のユーザー トラフィックを受信するアプリケーションに埋め込まれている場合にのみ収集されます。 合成トラフィック (たとえば、Azure WebApp パフォーマンス テスト) では、通常、埋め込みの Javascript コードは実行されないため、この種類のトラフィックによって測定は生成されません。
- 時系列は、1時間に1回生成されるため、新しい測定データを表示するには、少なくともその時間を待つ必要があります。
- スコアカードは、毎日 (毎日、UTC 時間の最後に) 生成されます。
- スコアカードは選択したフィルターの組み合わせ (テスト、期間、国など) に対して100を超える測定値が収集された場合にのみ生成されます。

## <a name="next-steps"></a>次の手順
[Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む
