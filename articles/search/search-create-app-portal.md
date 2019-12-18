---
title: Azure portal で検索アプリを作成する
titleSuffix: Azure Cognitive Search
description: アプリの作成 (プレビュー) ウィザードを実行して、運用 Web アプリ用の HTML ページとスクリプトを生成します。 このページは、検索バー、結果領域、サイド バーを備えているほか、先行入力に対応しています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 16748d9948518964d47ee9543765e3cb59ae2ec4
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74902080"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>クイック スタート:ポータルで検索アプリを作成する (Azure Cognitive Search)

ブラウザーで動作する "localhost" スタイルのダウンロード可能な Web アプリを生成するには、ポータルの**検索アプリの作成**ウィザードを使用します。 その構成にもよりますが、生成されたアプリはリモート インデックスへのライブ接続を備え、そのままで実用性があります。 既定のアプリは、検索バーや結果領域、サイド バーのフィルターを備えているほか、先行入力をサポートします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件

[Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。 

アプリケーションの基礎として使用する[インデックスを作成](search-create-index-portal.md)します。 

このクイックスタートでは、縮小版画像を含んだ組み込みの不動産サンプル データとインデックスを使用します。 この演習で使用するインデックスを作成するには、**データのインポート** ウィザードを実行します。その際、データ ソースとして *realestate-us-sample* を選択してください。

![サンプル データのデータ ソース ページ](media/search-create-app-portal/import-data-realestate.png)

インデックスを使用する準備が整ったら、次の手順に進みます。

## <a name="start-the-wizard"></a>ウィザードを起動する

1. [Azure portal](https://portal.azure.com) にサインインし、[ご利用の検索サービスを探し](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)ます。

1. 概要ページの中央のリンクから **[インデックス]** を選択します。 

1. 既存のインデックスの一覧から *[realestate-us-sample-index]* を選択します。

1. インデックス ページの上部にある **[検索アプリの作成 (プレビュー)]** を選択してウィザードを開始します。

1. 最初のウィザード ページで **[Enable Cross Origin Resource Sharing (CORS)]\(クロス オリジン リソース共有 (CORS) を有効にする\)** を選択して、CORS のサポートをインデックスの定義に追加します。 この手順は省略できますが、省略した場合は、ローカル Web アプリがリモート インデックスに接続できなくなります。

## <a name="configure-search-results"></a>検索結果を構成する

このウィザードには、縮小版画像やタイトル、説明のための領域など、検索結果のレンダリングに使用される基本的なレイアウトが用意されています。 そうした各要素の基礎となるのは、インデックス内のデータを提供するフィールドです。 

1. [縮小版] で、*realestate-us-sample* インデックスの *thumbnail* フィールドを選択します。 このサンプルにはたまたま、画像の縮小版が含まれています。画像は URL アドレスの形式で、*thumbnail* というフィールドに格納されています。 インデックスに画像が含まれていない場合は、このフィールドは空のままにしてください。

1. [タイトル] には、各ドキュメントを一意に識別するフィールドを選択します。 このサンプルでは、listingId を選択しています。

1. [説明] では、クリックしてその特定のドキュメントにアクセスするかどうかの判断に役立つような詳しい情報が格納されているフィールドを選択します。

![サンプル データのデータ ソース ページ](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>サイド バーを追加する

この検索サービスは、ファセット ナビゲーションをサポートしています。ファセット ナビゲーションは、多くの場合、サイド バーとしてレンダリングされます。 ファセットは、インデックスのスキーマで表現された、フィルター可能かつファセット可能なフィールドがベースとなっています。

Azure Cognitive Search のファセット ナビゲーションは、累積的なフィルタリング エクスペリエンスとなっています。 カテゴリ内で複数のフィルターを選択すると、その結果が展開されます (City 内の Seattle と Bellevue を選択するなど)。 異なるカテゴリにまたがって複数のフィルターを選択することで、結果が絞り込まれます。

> [!TIP]
> インデックス スキーマの全容はポータルで確認できます。 各インデックスの概要ページにある **[インデックス定義 (JSON)]** というリンクを探してください。 ファセット ナビゲーションの要件を満たしたフィールドには、"filterable: true" 属性と "facetable: true" 属性があります。

現在選択されているファセットを受け入れて、次のページに進みます。


## <a name="add-typeahead"></a>先行入力を追加する

先行入力機能は、オートコンプリートとクエリ候補の形式で提供されます。 このウィザードでサポートされているのはクエリ候補です。 ユーザーによるキーボード操作の入力に基づいて、検索サービスから "補完" されたクエリ文字列のリストが返され、それを入力として選択することができます。

候補は、特定のフィールド定義に対して有効になります。 どの程度の量の情報を候補に含めるかは、ウィザードで選択することができます。 

次のスクリーンショットは、ウィザードのオプションとアプリでレンダリングされるページとを並べて示したものです。 選択フィールドの使い方や、[フィールド名を表示する] を使用して候補内のラベルを追加したり除外したりする方法が確認できます。

![クエリ候補の構成](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>作成、ダウンロード、実行する

1. **[検索アプリの作成]** を選択して HTML ファイルを生成します。

1. 確認を求められたら、 **[Download your app]\(アプリのダウンロード\)** を選択してファイルをダウンロードします。

1. ファイル を開きます。 以下のスクリーンショットのようなページが表示されます。 結果を絞り込むには、何か語句を入力してからフィルターを使用します。 

基になるインデックスは、複数のドキュメントにまたがって複製された架空の生成データからできていて、説明と画像が一致しない場合があります。 独自のインデックスに基づいてアプリを作成すれば、両者の関連性を高めることができます。

![アプリの実行](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次の手順

既定のアプリは最初の探索や小規模なタスクに役立ちますが、API を早い段階で確認することは、概念とワークフローをより深いレベルで理解するのに役立ちます。

> [!div class="nextstepaction"]
> [.NET SDK を使用してインデックスを作成する](https://docs.microsoft.com/azure/search/search-create-index-dotnet)