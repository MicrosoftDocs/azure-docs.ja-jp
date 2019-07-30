---
title: Custom Autosuggest の検索候補を定義する - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: カスタム検索候補を使用して Custom Autosuggest を構成する方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: b08eb49b0c4f9655326d2ab09ce39210205e28a9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405102"
---
# <a name="configure-your-custom-autosuggest-experience"></a>カスタム自動提案エクスペリエンスを構成する

Custom Autosuggest は、検索エクスペリエンスに関連する提案された検索クエリ文字列の一覧を返します。 提案されるクエリ文字列は、ユーザーが検索ボックスに入力した部分クエリ文字列に基づいています。 一覧には、最大 10 個の検索候補が含まれます。 

カスタム検索候補のみを返すか、Bing の検索候補を含めるかも指定します。 Bing の検索候補を含める場合、カスタム検索候補は Bing の検索候補の前に表示されます。 十分な関連検索候補を提供した場合、返される検索候補の一覧に Bing の検索候補が含まれない可能性があります。 Bing の候補は、Custom Search インスタンスのコンテキスト内に常にあります。 

ユーザーのインスタンス用の検索クエリ候補を構成するには、 **[Autosuggest]** タブをクリックします。  

> [!NOTE]
> この機能を使用するには、適切なレベルで Custom Search をサブスクライブする必要があります ([価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページをご覧ください)。

提供するエンドポイント (API またはホステッド UI) に検索候補が反映されるまで、最大で 24 時間かかることがあります。

## <a name="enable-bing-suggestions"></a>Bing の検索候補を有効にする

Bing の検索候補を有効にするには、 **[Automatic Bing suggestions]** (Bing の自動検索候補) スライダーをオンの位置にします。 スライダーが青になります。

## <a name="add-your-own-suggestions"></a>独自の検索候補を追加する

独自のクエリ文字列候補を追加するには、 **[User-defined suggestions]\(ユーザー定義の検索候補\)** の下の一覧に追加します。 一覧に検索候補を追加した後、Enter キーを押すか、または **[+]** アイコンをクリックします。 任意の言語で検索候補を指定できます。 最大で 5,000 個のクエリ文字列候補を追加できます。

## <a name="upload-suggestions"></a>検索候補をアップロードする

必要に応じて、検索候補の一覧をファイルからアップロードできます。 ファイルでは、1 行に 1 つの検索クエリ文字列を含める必要があります。 ファイルをアップロードするには、アップロード アイコンをクリックして、アップロードするファイルを選択します。 サービスによって、ファイルから検索候補が抽出されて、一覧に追加されます。

## <a name="remove-suggestions"></a>検索候補を削除する

クエリ文字列検索候補を削除するには、削除する検索候補の横にある削除アイコンをクリックします。

## <a name="block-suggestions"></a>検索候補をブロックする

Bing の検索候補を含める場合、Bing から返されたくない検索クエリ文字列の一覧を追加できます。 ブロックするクエリ文字列を追加するには、 **[Show blocked suggestions]\(ブロックされている検索候補を表示する\)** をクリックします。 一覧にクエリ文字列を追加し、Enter キーを押すか、または **[+]** アイコンをクリックします。 最大で 50 個のブロックするクエリ文字列を追加できます。



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Custom Autosuggest の構成変更が有効になるまで、最大で 24 時間かかる場合があります。


## <a name="enabling-autosuggest-in-hosted-ui"></a>ホステッド UI で Autosuggest を有効にする

ホステッド UI でクエリ文字列の検索候補を有効にするには、 **[Hosted UI]\(ホステッド UI\)** をクリックします。 **[Additional Configuration]\(追加構成\)** セクションまで下にスクロールします。 **[Web search]\(Web 検索\)** で、 **[Enable autosuggest]\(Autosuggest を有効にする\)** を **[オン]** にします。 Autosuggest を有効にするには、検索ボックスを含むレイアウトを選択する必要があります。


## <a name="calling-the-autosuggest-api"></a>Autosuggest API を呼び出す

Bing Custom Search API を使用して検索候補のクエリ文字列を取得するには、次のエンドポイントに `GET` 要求を送信します。

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

応答には、候補となるクエリ文字列を含んだ `SearchAction` オブジェクトのリストが格納されます。

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

それぞれの候補には、`displayText` フィールドと `query` フィールドが含まれています。 `displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用する検索候補のクエリ文字列が含まれています。

ユーザーがドロップダウン リストから検索候補のクエリ文字列を選択する場合は、[Bing Custom Search API](overview.md) を呼び出すときに `query` フィールドのクエリ文字列を使用します。


## <a name="next-steps"></a>次の手順

- [カスタム検索候補を取得する](./get-custom-suggestions.md)
- [カスタム インスタンスを検索する](./search-your-custom-view.md)
- [カスタムのホステッド UI を構成して使用する](./hosted-ui.md)
