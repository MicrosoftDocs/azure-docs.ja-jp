---
title: Azure portal でデモ アプリを作成する
titleSuffix: Azure Cognitive Search
description: デモ アプリの作成 (プレビュー) ウィザードを実行して、運用 Web アプリ用の HTML ページとスクリプトを生成します。 このページは、検索バー、結果領域、サイド バーを備えているほか、先行入力に対応しています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745752"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>クイック スタート:ポータルでデモ アプリを作成する (Azure Cognitive Search)

ブラウザーで動作する "localhost" スタイルのダウンロード可能な Web アプリを生成するには、Azure portal の **デモ アプリの作成** ウィザードを使用します。 その構成にもよりますが、生成されたアプリは、リモート インデックスへのライブ読み取り専用接続を備えており、そのままで実用性があります。 既定のアプリは、検索バーや結果領域、サイド バーのフィルターを備えているほか、先行入力をサポートします。

デモ アプリは、インデックスがクライアント アプリでどのように機能するかを視覚化するのに役立ちますが、運用環境のシナリオ向けではありません。 クライアント アプリには、生成された HTML ページでは提供されないセキュリティ、エラー処理、およびホスティング ロジックを含める必要があります。 クライアント アプリを作成する準備ができたら、次の手順について、「[.NET SDK を使用して最初の検索アプリを作成する](tutorial-csharp-create-first-app.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ [Microsoft Edge (最新バージョン)](https://www.microsoft.com/edge) または Google Chrome。

+ 生成されるアプリケーションの基礎として使用する[検索インデックス](search-what-is-an-index.md)。 

  このクイックスタートでは、縮小版画像を含んだ組み込みの不動産サンプル データとインデックスを使用します (このウィザードでは、結果ページへの画像の追加がサポートされます)。 この演習で使用するインデックスを作成するには、**データのインポート** ウィザードを実行します。その際、データ ソースとして *realestate-us-sample* を選択してください。

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="サンプル データのデータ ソース ページ" border="false":::

インデックスを使用する準備が整ったら、次の手順に進みます。

## <a name="start-the-wizard"></a>ウィザードを起動する

1. Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

1. [自分の検索サービスを探し](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)、概要ページの中央のリンクから **[インデックス]** を選択します。 

1. 既存のインデックスの一覧から *[realestate-us-sample-index]* を選択します。

1. インデックス ページの上部にある、 **[Create demo app (preview)]\(デモ アプリの作成 (プレビュー)\)** を選択してウィザードを開始します。

1. 最初のウィザード ページで **[Enable Cross Origin Resource Sharing (CORS)]\(クロス オリジン リソース共有 (CORS) を有効にする\)** を選択して、CORS のサポートをインデックスの定義に追加します。 この手順は省略できますが、省略した場合は、ローカル Web アプリがリモート インデックスに接続できなくなります。

## <a name="configure-search-results"></a>検索結果を構成する

このウィザードには、縮小版画像やタイトル、説明のための領域など、検索結果のレンダリングに使用される基本的なレイアウトが用意されています。 そうした各要素の基礎となるのは、インデックス内のデータを提供するフィールドです。 

1. [縮小版] で、*realestate-us-sample* インデックスの *thumbnail* フィールドを選択します。 このサンプルにはたまたま、画像の縮小版が含まれています。画像は URL アドレスの形式で、*thumbnail* というフィールドに格納されています。 インデックスに画像が含まれていない場合は、このフィールドは空のままにしてください。

1. [タイトル] には、各ドキュメントを一意に識別するフィールドを選択します。 このサンプルでは、listingId を選択しています。

1. [説明] では、クリックしてその特定のドキュメントにアクセスするかどうかの判断に役立つような詳しい情報が格納されているフィールドを選択します。

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="サンプル データの結果を構成する" border="false":::

## <a name="add-a-sidebar"></a>サイド バーを追加する

この検索サービスは、ファセット ナビゲーションをサポートしています。ファセット ナビゲーションは、多くの場合、サイド バーとしてレンダリングされます。 ファセットは、インデックスのスキーマで表現された、フィルター可能かつファセット可能なフィールドがベースとなっています。

Azure Cognitive Search のファセット ナビゲーションは、累積的なフィルタリング エクスペリエンスとなっています。 カテゴリ内で複数のフィルターを選択すると、その結果が展開されます (City 内の Seattle と Bellevue を選択するなど)。 異なるカテゴリにまたがって複数のフィルターを選択することで、結果が絞り込まれます。

> [!TIP]
> インデックス スキーマの全容はポータルで確認できます。 各インデックスの概要ページにある **[インデックス定義 (JSON)]** というリンクを探してください。 ファセット ナビゲーションの要件を満たしたフィールドには、"filterable: true" 属性と "facetable: true" 属性があります。

1. ウィザードのページ上部にある **[サイド バー]** タブを選択します。 インデックスでフィルター可能とファセット可能の属性が付いたフィールドがすべて一覧表示されます。

1. 現在選択されているファセット フィールドを受け入れて、次のページに進みます。

## <a name="add-typeahead"></a>先行入力を追加する

先行入力機能は、オートコンプリートとクエリ候補の形式で提供されます。 このウィザードでサポートされているのはクエリ候補です。 ユーザーによるキーボード操作の入力に基づいて、検索サービスから "補完" されたクエリ文字列のリストが返され、それを入力として選択することができます。

候補は、特定のフィールド定義に対して有効になります。 どの程度の量の情報を候補に含めるかは、ウィザードで選択することができます。 

次のスクリーンショットは、ウィザードのオプションとアプリでレンダリングされるページとを並べて示したものです。 選択フィールドの使い方や、[フィールド名を表示する] を使用して候補内のラベルを追加したり除外したりする方法が確認できます。

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="クエリ候補の構成":::

## <a name="add-suggestions"></a>検索候補を追加する

検索候補は、検索ボックスにアタッチされる自動化されたクエリ プロンプトです。 Cognitive Search では、2 つの検索候補がサポートされます。検索語句を補完する "*オートコンプリート*" と、一致する可能性のあるドキュメントの選択元のドロップダウン リストに使用される "*候補*" です。

このウィザードでは候補がサポートされ、提示される結果の提供元フィールドが、インデックスの [`Suggesters`](index-add-suggesters.md) コンストラクトから得られます。

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. ウィザードのページ上部にある **[Suggestions]\(候補\)** タブを選択します。 候補プロバイダーとしてインデックス スキーマに指定されたフィールドがすべて一覧表示されます。

1. 現在選択されている項目を受け入れて、次のページに進みます。

## <a name="create-download-and-execute"></a>作成、ダウンロード、実行する

1. ページ下部にある **[Create demo app ]\(デモ アプリの作成\)** を選択して、HTML ファイルを生成します。

1. 確認を求められたら、 **[Download your app]\(アプリのダウンロード\)** を選択してファイルをダウンロードします。

1. ファイルを開いて、[検索] ボタンをクリックします。 このアクションによってクエリが実行されます。空のクエリ (`*`) を指定して任意の結果セットを取得することもできます。 実際のページは次のスクリーンショットのようになります。 結果を絞り込むには、何か語句を入力してからフィルターを使用します。 

基になるインデックスは、複数のドキュメントにまたがって複製された架空の生成データからできていて、説明と画像が一致しない場合があります。 独自のインデックスに基づいてアプリを作成すれば、両者の関連性を高めることができます。

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="アプリを実行する":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

デモ アプリはプロトタイプの作成に役立ちます。JavaScript やフロントエンド コードを書かなくてもエンドユーザーのエクスペリエンスをシミュレートすることができます。 フロントエンド機能について理解を深めるために、ファセット ナビゲーションに取り組んでみましょう。

> [!div class="nextstepaction"]
> [ファセット フィルターを作成する方法](search-filters-facets.md)