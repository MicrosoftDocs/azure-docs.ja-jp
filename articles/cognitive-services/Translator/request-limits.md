---
title: 要求の制限 - Translator
titleSuffix: Azure Cognitive Services
description: この記事では、Translator に対する要求の制限を示します。 料金は、要求ごとに 5,000 文字に制限された要求の頻度ではなく、文字数に基づいて発生します。 文字の制限はサブスクリプションに基づき、F0 では 1 時間あたり 200 万文字に制限されます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2bc2c1361c7d2f73ff8a67e906a6db725f669d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895409"
---
# <a name="request-limits-for-translator"></a>Translator に対する要求の制限

この記事では、Translator に対する調整の制限を示します。 サービスには、翻訳、音訳、文の長さの検出、言語の検出、および代替の翻訳が含まれます。

## <a name="character-and-array-limits-per-request"></a>要求あたりの文字および配列制限

各翻訳要求は、翻訳先のすべての対象言語で合計 10,000 文字までに制限されています。 たとえば、3,000 文字を 3 つの異なる言語に翻訳する翻訳要求を送信すると、要求のサイズは 3,000 x 3 = 9,000 文字となり、要求の上限が満たされます。 要求の数ではなく、文字単位で課金されます。 送信する要求を短くすることをお勧めします。

次の表には、Translator の各操作に関する配列要素および文字の制限が一覧表示されています。

| 操作 | 配列要素の最大サイズ |    配列要素の最大数 |    最大要求サイズ (文字数) |
|:----|:----|:----|:----|
| Translate | 10,000    | 100   | 10,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 50,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| 辞書検索| 100 |  10  | 1,000 |
| 辞書の例 | テキストに 100 と翻訳に 100 (合計 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>時間あたりの文字制限

時間あたりの文字制限は、Translator のサブスクリプション レベルに基づきます。 

時間あたりのクォータは、時間全体で均等に使用する必要があります。 たとえば、200 万文字/時間の F0 レベルの制限では、1 分のスライディング ウィンドウあたり約 33,300 文字以下の速さで文字を消費する必要があります (200 万文字割る 60 分)。

これらの制限に到達または超過した場合、または短期間にクォータの大きすぎる一部を送信した場合、クォータ不足の応答を受け取る可能性があります。 同時要求に制限はありません。

| レベル | 文字数制限 |
|------|-----------------|
| F0 | 200 万文字/時間 |
| S1 | 4,000 万文字/時間 |
| S2 / C2 | 4,000 万文字/時間 |
| S3 / C3 | 12,000 万文字/時間 |
| S4 / C4 | 20,000 万文字/時間 |

[マルチ サービス サブスクリプション](./reference/v3-0-reference.md#authentication)の制限は S1 レベルと同じです。

これらの制限は、Microsoft の標準翻訳モデルに制限されます。 カスタム翻訳ツールを使用するカスタム翻訳モデルは、1 秒あたり 1,800 文字に制限されます。

## <a name="latency"></a>Latency

Translator の最大待ち時間は、標準モデルを使用した場合は 15 秒、カスタム モデルを使用した場合は 120 秒です。 通常、*100 文字以内のテキスト* の応答は 150 ミリ秒から 300 ミリ秒で返されます。 カスタム トランスレーター モデルの待機時間特性は、持続要求レートに似ており、要求レートが間欠的になると待機時間が長くなる可能性があります。 応答時間は、要求のサイズと言語ペアによって異なります。 その時間枠内に翻訳または[エラー応答](./reference/v3-0-reference.md#errors)が返されなかった場合、ご使用のコードおよびネットワーク接続を確認してから、再試行してください。 

## <a name="sentence-length-limits"></a>文の長さの制限

[BreakSentence](./reference/v3-0-break-sentence.md) 関数を使用する場合、文の長さは 275 文字に制限されます。 以下の言語については例外があります。

| Language | コード | 文字数制限 |
|----------|------|-----------------|
| Chinese | zh | 166 |
| ドイツ語 | de | 800 |
| イタリア語 | it | 800 |
| 日本語 | ja | 166 |
| Portuguese | pt | 800 |
| スペイン語 | es | 800 |
| タイ語 | th | 180 |

> [!NOTE]
> この制限は、翻訳には適用されません。

## <a name="next-steps"></a>次のステップ

* [料金](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [リージョン別の提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator リファレンス](./reference/v3-0-reference.md)