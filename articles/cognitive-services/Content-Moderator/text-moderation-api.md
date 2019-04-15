---
title: テキストのモデレート - Content Moderator
description: 混入が考えられる不要なテキスト、PII、および条件のカスタム一覧に対して、テキストのモデレートを使用します。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5a1007f2408b48c96f5eeaf585b94c8caa7ceb45
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757754"
---
# <a name="learn-text-moderation-concepts"></a>テキスト モデレーションの概念を確認する

Content Moderator のコンピューター支援型のテキスト モデレーションと[目視レビュー](Review-Tool-User-Guide/human-in-the-loop.md)機能を使用して、テキスト コンテンツをモデレートします。

ポリシーとしきい値に基づいて、コンテンツをブロックするか、承認またはレビューします。 それを使用して、パートナー、従業員、およびコンシューマーがテキスト コンテンツを生成する環境のヒューマン モデレーションを拡張します。 これらには、チャット ルーム、ディスカッション掲示板、チャットボット、e コマース カタログ、およびドキュメントが含まれます。 

サービスの応答には次の情報が含まれています。

- 不適切な表現: さまざまな言語での不適切な用語の組み込みの一覧との用語ベースのマッチング
- 分類: 3 つのカテゴリへのコンピューター支援型の分類
- 個人データ
- 自動修正済みテキスト
- 元のテキスト
- 言語

## <a name="profanity"></a>不適切な表現

API が、いずれかの[サポートされる言語](Text-Moderation-API-Languages.md)で何らかの不適切な用語を検出した場合、それらの用語が応答に含められます。 また、応答には、元のテキストの場所 (`Index`) も含まれます。 以下のサンプル JSON にある `ListId` は、[カスタム用語一覧](try-terms-list-api.md)で検出された用語を参照しています (利用可能な場合)。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> **language** パラメーターについては、`eng` を割り当てるか、パラメーターを空のままにして、コンピューター支援による**分類**応答を表示します (プレビュー機能)。 **この機能でサポートされているのは英語のみです**。
>
> **不適切用語**の検出については、この記事の一覧に示したサポート対象言語の [ISO 639-3 コード](http://www-01.sil.org/iso639-3/codes.asp)を使用するか、空のままにします。

## <a name="classification"></a>分類

Content Moderator のコンピューター支援型**テキスト分類機能**では**英語のみ**をサポートし、潜在的に不要なコンテンツを検出します。 フラグが付いたコンテンツは、コンテキストによっては不適切として評価される可能性があります。 これは各カテゴリの可能性を伝達し、目視レビューを推奨する可能性があります。 この機能では、トレーニング済みのモデルを使用して、侮辱、軽蔑、差別と受け取られる可能性のある言葉を識別します。 これには、俗語、省略語、不快な言葉、およびレビュー備えて意図的にスペルミスを含めた用語が含まれます。 

JSON の次の抽出箇所は、出力例を示しています。

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>説明

- `Category1` は、特定の状況で性的に露骨か、または成人向けであると見なされる可能性のある言語の潜在的な存在を示します。
- `Category2` は、特定の状況で性的な連想を起こさせるか、または成熟した表現であると見なされる可能性のある言語の潜在的な存在を示します。
- `Category3` は、特定の状況で不快であると見なされる可能性のある言語の潜在的な存在を示します。
- `Score` は 0 から 1 の間です。 このモデルでは、スコアが高いほど、そのカテゴリに該当する可能性が高いと予測しています。 この機能は、人がコーディングした結果ではなく、統計モデルに依存しています。 独自のコンテンツを使用してテストを行い、実際の要件に合うように各カテゴリをどのように設定するかを決めることをお勧めします。
- `ReviewRecommended` は、内部スコアのしきい値に応じて true または false のどちらかになります。 ユーザーは、この値を使用するか、独自のコンテンツ ポリシーに基づいてカスタムしきい値を決めるかを見極める必要があります。

## <a name="personal-data"></a>個人データ

PII 機能は、この情報の潜在的な存在を検出します。

- 電子メール アドレス
- 米国の住所
- IP アドレス
- 米国の電話番号
- 英国の電話番号
- 社会保障番号 (SSN)

次の例は、サンプルの応答を示しています。

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>自動修正

次のような入力テキストについて考えます ('lzay' および 'f0x' は意図的です)。

    The qu!ck brown f0x jumps over the lzay dog.

自動修正を要求した場合、応答には修正されたバージョンのテキストが含まれます。

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>カスタム用語一覧の作成と管理

既定のグローバル用語一覧は、ほとんどのケースで有効に機能しますが、ビジネス ニーズに固有の用語に対してスクリーニングしたい場合があります。 たとえば、ユーザーの投稿から競合するブランドの名前を検出したい場合があります。

> [!NOTE]
> 上限は**用語の一覧が 5 つ**で、各一覧では**用語が 10,000 個を超えてはいけません**。
>

次の例では、一致するリスト ID を示しています。

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator は、[用語一覧の API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) にカスタム用語一覧を管理する操作を提供します。 [用語一覧の API コンソール](try-terms-list-api.md) を起動して、REST API コード サンプルを使用してください。 また、Visual Studio および C# に精通している場合は、[用語一覧に関する .NET のクイックスタート](term-lists-quickstart-dotnet.md)を確認してください。

## <a name="next-steps"></a>次の手順

[用語一覧の API コンソール](try-text-api.md)を試験運用して、REST API コード サンプルを使用してください。 また、Visual Studio と C# に精通している場合は、[テキスト モデレーションの .NET クイック スタート](text-moderation-quickstart-dotnet.md)も確認してください。
