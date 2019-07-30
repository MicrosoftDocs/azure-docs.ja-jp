---
title: チュートリアル:カスタム検索 Web ページを作成する - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: カスタム検索インスタンスを構成して、Web ページに統合する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 3a8d6a831adf24212191a103dcf356bbe74d7962
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405217"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>チュートリアル:Custom Search Web ページの作成

Bing Custom Search を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。 たとえば、検索エクスペリエンスを提供する武術に関する Web サイトを所有している場合は、Bing が検索するドメイン、サブサイト、および Web ページを指定できます。 ユーザーは、関係のないコンテンツが含まれる可能性がある通常の検索結果のページを移動し続けるのではなく、関心のあるコンテンツに合わせてカスタマイズされた検索結果を参照できます。 

このチュートリアルでは、カスタム検索インスタンスを構成して、新しい Web ページに統合する方法について説明します。

説明するタスクは次のとおりです。

> [!div class="checklist"]
> - カスタム検索インスタンスの作成
> - アクティブなエントリの追加
> - ブロックするエントリの追加
> - ピン留めするエントリの追加
> - カスタム検索の Web ページへの統合

## <a name="prerequisites"></a>前提条件

- チュートリアルを進めるには、Bing Custom Search API のサブスクリプション キーが必要です。  キーを取得するには、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)」を参照してください。
- まだ Visual Studio 2017 以降をインストールしていない場合は、**無料**の [Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。

## <a name="create-a-custom-search-instance"></a>カスタム検索インスタンスの作成

Bing Custom Search インスタンスを作成するには:

1. インターネット ブラウザーを開きます。  
  
2. カスタム検索[ポータル](https://customsearch.ai)に移動します。  
  
3. Microsoft アカウント (MSA) を使用して、ポータルにサインインします。 MSA を持っていない場合は、 **[Microsoft アカウントの作成]** をクリックします。 初めてポータルを使用する場合、データにアクセスするための権限を求められます。 **[はい]** をクリックします。  
  
4. サインイン後、 **[New custom search]** (新しいカスタム検索) をクリックします。 **[Create a new custom search instance]** (新しいカスタム検索インスタンスの作成) ウィンドウで、検索で返されるコンテンツの種類を説明するわかりやすい名前を入力します。 名前はいつでも変更できます。  
  
   ![新しいカスタム検索インスタンスの作成ボックスのスクリーンショット](../media/newCustomSrch.png)  
  
5. [OK] をクリックして、URL と、URL のサブページを含めるかどうかを指定します。  
  
   ![URL 定義ページのスクリーンショット](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>アクティブなエントリの追加

特定の Web サイトや URL からの結果を含めるには、それらを **[アクティブ]** タブに追加します。

1.  **[構成]** ページで **[アクティブ]** タブをクリックし、検索に含める 1 つまたは複数の Web サイトの URL を入力します。

    ![定義エディターのアクティブ タブのスクリーンショット](../media/customSrchEditor.png)

2.  インスタンスが結果を返すことを確認するには、右側のプレビュー ウィンドウにクエリを入力します。 Bing は、インデックスが付けられている公開 Web サイトの結果のみを返します。

## <a name="add-blocked-entries"></a>ブロックするエントリの追加

特定の Web サイトや URL からの結果を除外するには、それらを **[ブロック]** タブに追加します。

1. **[構成]** ページで **[ブロック]** タブをクリックし、検索から除外する 1 つまたは複数の Web サイトの URL を入力します。

    ![定義エディターのブロック タブのスクリーンショット](../media/blockedCustomSrch.png)


2. インスタンスがブロックする Web サイトの結果を返さないことを確認するには、右側のプレビュー ウィンドウにクエリを入力します。 

## <a name="add-pinned-entries"></a>ピン留めするエントリの追加

特定の Web ページを検索結果の最上位にピン留めするには、 **[Pinned]** (ピン留め) タブに Web ページと検索語を追加します。 **[Pinned]** (ピン留め) タブには、特定のクエリの最上位の結果として表示される Web ページを指定する Web ページと検索語のペアの一覧が含まれています。 ユーザーのクエリ文字列が、暗証番号 (pin) の一致条件に基づいて pin のクエリ文字列と一致する場合にのみ、Web ページがピン留めされます。 検索で表示されるのは、インデックスが作成された Web ページだけです。 詳細については、[カスタム ビューの定義](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results)に関するページを参照してください。

1. **[構成]** ページで **[ピン留め]** をクリックし、上位の検索結果として返される Web ページと検索語を入力します。  
  
2. 既定では、Bing が上位の検索結果として Web ページを返すには、ユーザーのクエリ文字列が pin のクエリ文字列と正確に一致する必要があります。 一致条件を変更するには、pin を編集 (鉛筆のアイコンをクリック) し、 **[Query match condition]\(クエリの一致条件\)** 列で [完全一致] をクリックして、アプリケーションに適切な一致条件を選択します。  
  
    ![定義エディターのピン留めタブのスクリーンショット](../media/pinnedCustomSrch.png)
  
3. インスタンスが上位の検索結果として指定された Web ページを返すことを確認するには、右側のプレビュー ウィンドウにピン留めした検索語を入力します。

## <a name="configure-hosted-ui"></a>ホステッド UI の構成

カスタム検索は、カスタム検索インスタンスの JSON 応答を表示するためにホステッド UI を提供します。 UI エクスペリエンスを定義するには:

1. **[Hosted UI]** (ホステッド UI) タブをクリックします。  
  
2. レイアウトを選択します。  
  
   ![ホステッド UI のレイアウト選択手順のスクリーンショット](./media/custom-search-hosted-ui-select-layout.png)  
  
3. 配色テーマを選択します。  
  
   ![ホステッド UI の配色テーマ選択のスクリーンショット](./media/custom-search-hosted-ui-select-color-theme.png)  

   より適切に Web アプリと統合するために配色テーマを微調整する必要がある場合は、 **[テーマのカスタマイズ]** をクリックします。 すべての配色構成がすべてのレイアウト テーマに適用されるわけではありません。 色を変更するには、対応するテキスト ボックスに色の RGB 16 進数 (たとえば、#366eb8) を入力します。 または、色のボタンをクリックし、適している網掛けをクリックします。 色を選択するときは必ずアクセシビリティを考慮します。
  
   ![ホステッド UI の配色テーマのカスタマイズのスクリーンショット](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. その他の構成オプションを指定します。  
  
   ![ホステッド UI のその他の構成手順のスクリーンショット](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   詳細な構成を取得するには、 **[Show advanced configurations]\(構成の詳細を表示\)** をクリックします。 これにより、Web 検索オプションに *[リンク先]* などの構成が、[イメージ] および [ビデオ] オプションに *[フィルターを有効にする]* が、[その他] オプションに *[Search box text placeholder]\(検索ボックスのテキスト プレースホルダー\)* が追加されます。

   ![ホステッド UI の詳細な構成手順のスクリーンショット](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. ドロップダウン リストからサブスクリプション キーを選択します。 または、サブスクリプション キーを手動で入力できます。 キーの取得方法については、「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api)」を参照してください。  
  
   ![ホステッド UI のその他の構成手順のスクリーンショット](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>ホステッド UI の使用

ホステッド UI は、2 つの方法で使用することができます。  

- オプション 1:提供される JavaScript スニペットをアプリケーションに統合する。
- オプション 2:提供される HTML エンドポイントを使用する。

このチュートリアルの残りの部分では、**オプション 1: Javascript スニペット**について説明します。  

## <a name="set-up-your-visual-studio-solution"></a>Visual Studio ソリューションのセットアップ

1. コンピューターで **Visual Studio** を開きます。  
  
2. **[ファイル]** メニューで、 **[新規]** 、 **[プロジェクト]** の順に選択します。  
  
3. **[新しいプロジェクト]** ウィンドウで、 **[Visual C#] / [Web] / [ASP.NET Core Web アプリケーション]** の順に選択し、プロジェクトに名前を付けて **[OK]** をクリックします。  
  
   ![新しいプロジェクト ウィンドウのスクリーンショット](./media/custom-search-new-project.png)  
  
4. **[新しい ASP.NET Core Web アプリケーション]** ウィンドウで、 **[Web アプリケーション]** を選択して **[OK]** をクリックします。  
  
   ![新しいプロジェクト ウィンドウのスクリーンショット](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>index.cshtml の編集

1. **[ソリューション エクスプローラー]** で **[ページ]** を展開し、**index.cshtml** をダブルクリックしてこのファイルを開きます。  
  
   ![ページを展開して index.cshtml を選択したソリューション エクスプローラーのスクリーンショット](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. index.cshtml の 7 行目以降をすべてを削除します。  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. 改行要素とコンテナーとして機能する div を追加します。  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. **[Hosted UI]\(ホステッド UI\)** タブで、 **[Consuming the UI]\(UI の使用\)** セクションまで下方向にスクロールします。 *[エンドポイント]* をクリックして、JavaScript スニペットにアクセスします。 また、 **[運用]** 、 **[Hosted UI]\(ホステッド UI\)** タブの順にクリックしてスニペットにアクセスすることもできます。
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. script 要素を追加したコンテナーに貼り付けます。  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. **[ソリューション エクスプローラー]** で **wwwroot** を右クリックし、 **[ブラウザーで表示]** をクリックします。  
  
   ![wwwroot コンテキスト メニューのブラウザーで表示を選択したソリューション エクスプローラーのスクリーンショット](./media/custom-search-webapp-view-in-browser.png)  

新しいカスタム検索 Web ページは、次のようになります。

![カスタム検索 Web ページのスクリーンショット](./media/custom-search-webapp-browse-index.png)

検索を実行すると、次のような結果が表示されます。

![カスタム検索結果のスクリーンショット](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Custom Search エンドポイントを呼び出す (C#)](../call-endpoint-csharp.md)
