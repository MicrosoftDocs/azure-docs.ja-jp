---
title: Bing Custom Search 用のホステッド UI を構成する |Microsoft Docs
titleSuffix: Azure Cognitive Services
description: この記事を使用して、Bing Custom Search 用のホステッド UI を構成して統合します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: ae073e10331f07d9863da1d4ed97533f95b87c86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405050"
---
# <a name="configure-your-hosted-ui-experience"></a>ホステッド UI エクスペリエンスを構成する

Bing Custom Search には、ご自身の Web ページと Web アプリに JavaScript コード スニペットとして簡単に統合できるホステッド UI が用意されています。 Bing Custom Search ポータルを使用して、この UI のレイアウト、色、および検索オプションを構成できます。



## <a name="configure-the-custom-hosted-ui"></a>カスタム ホステッド UI を構成する

Web アプリ用にホステッド UI を構成するには、次の手順に従います。 変更を行うと、右側のウィンドウに UI のプレビューが表示されます。 表示される検索結果は、インスタンスの実際の結果ではありません。

1. Bing Custom Search の[ポータル](https://customsearch.ai)にサインインします。  
  
2. Bing Custom Search インスタンスを選択します。

3. **[Hosted UI]** (ホステッド UI) タブをクリックします。  
  
4. レイアウトを選択します。

    |  |  |
    |---------|---------|
    |[Search bar and results]\(検索バーと結果\) (既定値)    | 検索ボックスを表示し、その下に検索結果を表示します。         |
    |[Results only]\(結果のみ\)     | 検索結果のみを表示します。検索ボックスは表示されません。 このレイアウトを使用する場合は、検索クエリを指定する必要があります (`&q=<query string>`)。 JavaScript スニペット内の要求 URL にクエリ パラメーターを追加するか、HTML エンドポイント リンクを指定します。        |
    |[Pop-over]\(ポップオーバー\)     | 検索ボックスを表示し、検索結果をスライディング オーバーレイで表示します。        |
    
5. 配色テーマを選択します。 **[テーマのカスタマイズ]** をクリックして、ご自身のアプリに合うように色をカスタマイズできます。 色を変更するには、色の RGB 16 進値 (例: `#366eb8`) を入力するか、色プレビューをクリックします。

   ポータルの右側で、変更の結果をプレビューできます。 **[既定値にリセット]** をクリックすることで、選択したテーマの既定の色に戻すことができます。

   > [!NOTE]
   > 色を選択するときは、アクセシビリティを考慮してください。

6. **[追加の構成]** で、実際のアプリにとって適切な値を指定します。 これらの設定は省略可能です。 適用または削除の影響を確認するには、右側のプレビュー ウィンドウを参照します。 使用できる構成は次のとおりです。  

7. 検索サブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使用している Azure アカウントのサブスクリプションからのキーが表示されます。 [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)についてのページを参照してください。  

8. 自動提案を有効にした場合は、自動提案のサブスクリプション キーを入力するか、ドロップダウン リストから 1 つ選択します。 ドロップダウン リストには、使用している Azure アカウントのサブスクリプションからのキーが表示されます。 カスタム自動提案を利用するには、特定のサブスクリプション レベルが必要です。[価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページをご覧ください。

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>カスタム UI を使用する

ホステッド UI を使用するには、次のいずれかを実行します。 

- Web ページにスクリプトを含めます  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- または、Web ブラウザーで次の URL を使用します。   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > 必要に応じて、次のクエリ パラメーターを URL に追加します。 これらのパラメーターについては、[Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) のリファレンスをご覧ください。
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > このページには、プライバシーに関する声明や、その他の通知および条項を表示できません。 使用状況に適合するかどうかはさまざまです。  

カスタム構成 ID を含めて、詳細については、 **[Production]** (運用) タブの下にある **[エンドポイント]** に移動してください。

## <a name="configuration-options"></a>構成オプション

**[追加の構成]** をクリックし、値を指定することで、ホステッド UI の動作を構成できます。 これらの設定は省略可能です。 適用または削除の影響を確認するには、右側のプレビュー ウィンドウを参照します。 

### <a name="web-search-configurations"></a>Web 検索の構成

|  |  |
|---------|---------|
|[Web results enabled]\(Web の結果を有効化\)    | Web 検索を有効にするかどうかを決定します (ページの上部に [Web] タブが表示されます)。        |
|[Enable autosuggest]\(自動提案を有効にする\)     | カスタム自動提案を有効にするかどうかを指定します (追加コストについては、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)に関するページをご覧ください)。        |
|[Web results per page]\(ぺーじあたりの Web 結果数\)    | 一度に表示する Web 検索結果の数です (上限は 1 ページあたり 50 件です)。        |
|[画像キャプション]   | 検索結果と共に画像を表示するかどうかを指定します。|


**[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。


|  | |
|---------|---------|
|[語句を強調表示する]     | 検索用語を太字にして結果を表示するかどうかを指定します。         |
|[リンク先]    |  ユーザーが検索結果をクリックしたときに、新しいブラウザー タブ (空白) または同じブラウザー タブ (セルフ) のどちらで Web ページを開くかを決定します。        |

### <a name="image-search-configurations"></a>画像検索の構成

| | |
|---------|---------|
|[Image results enabled]\(画像の結果を有効化\)     | 画像検索を有効にするかどうかを決定します (ページの上部に [画像] タブが表示されます)。            |
|[Image results per page]\(ページあたりの画像結果数\)     | 一度に表示する画像検索結果の数です (上限は 1 ページあたり 150 件です)。          |

**[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
| | |
|---------|---------|
| [フィルターを有効にする]     | Bing が返す画像をフィルター処理するために使用できるフィルターを追加します。 たとえば、ユーザーはアニメーション GIF のみに結果をフィルター処理できます。|

### <a name="video-search-configurations"></a>動画検索の構成

|  | |
|---------|---------|
|[Video results enabled]\(動画の結果を有効化\)     | 動画検索を有効にするかどうかを決定します (ページの上部に [動画] タブが表示されます)。           |
|[Video results per page]\(ページあたりの動画結果数\)   | 一度に表示する動画検索結果の数です (上限は 1 ページあたり 150 件です)。        |

**[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  
  
|  | |
|---------|---------|
|[フィルターを有効にする]    | Bing が返す動画をフィルター処理するために使用できるフィルターを追加します。 たとえば、ユーザーは、結果をフィルター処理して、特定の解像度の動画や、過去 24 時間以内に検出された動画を検索できます。          |

### <a name="miscellaneous-configurations"></a>その他の構成


| |  |
|---------|---------|
|Page title (ページ タイトル)   | 検索結果ページのタイトル領域に表示されるテキストです (ポップオーバー レイアウトの場合は表示されません)。        |
|Toolbar theme (ツール バーのテーマ)    | 検索結果ページのタイトル領域の背景色を指定します。 |

**[Show advanced configurations]\(構成の詳細を表示\)** をクリックすると、次の構成が表示されます。  

|Column1  |Column2  |
|---------|---------|
|[Search box text placeholder]\(検索ボックスのテキスト プレース ホルダー\)   | 入力されるまで検索ボックスに表示されるテキスト。        |
|[Title link url]\(タイトル リンク URL\)    |タイトル リンクのターゲット。         |
|Logo URL (ロゴ URL)     | タイトルの横に表示される画像。         |
|[お気に入りアイコン]    | ブラウザーのタイトル バーに表示されるアイコン。          |

次の構成は、HTML エンドポイントを通してホステッド UI を使用する場合にのみ適用されます (JavaScript のスニペットを使用する場合は適用されません)。

- Page title (ページ タイトル)
- Toolbar theme (ツール バーのテーマ)
- Title link URL (タイトル リンク URL)
- Logo URL (ロゴ URL)
- Faviicon URL  

## <a name="next-steps"></a>次の手順

- [装飾マーカーを使用してテキストを強調表示する](./hit-highlighting.md)
- [Web ページのページング](./page-webpages.md)
