---
title: "ポータルで初めて Azure Search インデックスを作成する | Microsoft Docs"
description: "Azure Portal で、定義済みのサンプル データを使用してインデックスを生成します。 フルテキスト検索、フィルター、ファセット、あいまい検索、地理空間検索などについて確認します。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 02/22/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 7945ee77be8a09dcac9ddd6b338bdd542ec18540
ms.lasthandoff: 03/08/2017


---
# <a name="build-and-query-your-first-azure-search-index-in-the-portal"></a>ポータルで初めて Azure Search インデックスを作成してクエリを実行する

Azure Portal で、**データのインポート** ウィザードを使用して、定義済みのサンプル データセットからインデックスをすばやく生成します。 **Search エクスプローラー**を使用して、フルテキスト検索、フィルター、ファセット、あいまい検索、地理空間検索について確認します。  

この概要ではコーディングはせず、定義済みのデータを使用するので、興味深いクエリをすぐに作成できます。 ポータルのツールはコードに代わるものではありませんが、次のようなタスクには便利です。

+ すぐに実行できるハンズオン トレーニング
+ **データのインポート**でコードを記述する前のインデックスのプロトタイプ作成
+ **Search エクスプローラー**でのクエリとパーサー構文のテスト
+ サービスに発行されている既存のインデックスの表示とその属性の確認

**予想所要時間:** 約 15 分 (アカウントまたはサービスのサインアップも必要な場合はもう少しかかります)。 

また、準備として、[.NET での Azure Search のプログラミングに関するコード ベースの概要](search-howto-dotnet-sdk.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)と [Azure Search サービス](search-create-service-portal.md)があることを前提としています。 

サービスをすぐにプロビジョニングしない場合は、「[Azure Search Overview (Azure Search の概要)](https://channel9.msdn.com/Events/Connect/2016/138)」ビデオで、このチュートリアルの手順のデモを見ることができます (約 3 分目から 6 分間)。

## <a name="find-your-service"></a>サービスの検索
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. Azure Search サービスのサービス ダッシュボードを開きます。 サービス タイルをダッシュボードにピン留めしていない場合、次の手順でサービスを検索します。 
   
   * ジャンプ バーで、左側のナビゲーション ウィンドウの下部にある **[その他のサービス]** をクリックします。
   * 検索ボックスに「*search*」と入力し、サブスクリプションの Search サービスの一覧を取得します。 サービスが一覧に表示されます。 

## <a name="check-for-space"></a>領域の確認
多くのユーザーが最初に利用するのは、無料版のサービスです。 このバージョンは、インデックス、データ ソース、インデクサーがそれぞれ&3; つに限定されています。 十分な空き領域があることを確認してから開始してください。 このチュートリアルでは、それぞれのオブジェクトを&1; つ作成します。 

> [!TIP] 
> サービス ダッシュボードのタイルには、既に存在するインデックス、インデクサー、データ ソースの数が表示されます。 インデクサーのタイルには、成功と失敗のインジケーターが表示されます。 インデクサーの数を表示するには、タイルをクリックします。 
>
> ![インデクサーとデータソースのタイル][1]
>

## <a name="create-index"></a>インデックスの作成とデータの読み込み
検索クエリは、*インデックス*を反復処理します。インデックスには、検索可能なデータやメタデータのほか、特定の検索の動作を最適化することを目的とした構造が含まれています。

このタスクをすべてポータル上で実行するために、**データのインポート** ウィザードからインデクサーを使用してクロールできる、組み込みのサンプル データセットを使用します。 

#### <a name="step-1-start-the-import-data-wizard"></a>手順 1: データのインポート ウィザードを開始する
1. インデックスの作成とデータ投入はどちらもウィザードで実行できます。Azure Search サービス ダッシュボードで、コマンド バーの **[データのインポート]** をクリックしてウィザードを開始してください。
   
    ![[データのインポート] コマンド][2]

2. ウィザードで、**[データ ソース]** > **[サンプル]** > **[realestate-us-sample]** の順にクリックします。 このデータ ソースは、名前、種類、接続情報が事前構成されています。 作成すると、他のインポート操作で再度使用できる "既存のデータ ソース" になります。

    ![サンプル データセットの選択][9]

3. **[OK]** をクリックして、このデータセットを使用します。

#### <a name="step-2-define-the-index"></a>手順 2: インデックスを定義する
インデックスは通常、コードベースで手動で作成しますが、ウィザードでは、クロール可能な任意のデータ ソースのインデックスを生成できます。 インデックスには少なくとも、名前とフィールド コレクションが必要です。さらに、各ドキュメントを一意に識別するためのドキュメント キーとして、1 つのフィールドがマークされている必要があります。

フィールドには、データ型と属性があります。 上部に並んだチェック ボックスは、フィールドがどのように使用されるかを制御する "*インデックスの属性*" です。 

* **[取得可能]** は、そのフィールドが検索結果のリストに現れることを意味します。 このチェック ボックスをオフにすることで、フィルター式でのみ使用されているフィールドなど、検索結果から抑制するフィールドを個別に指定できます。 
* **[フィルター可能]**、**[ソート可能]**、**[ファセット可能]** は、フィルタリング、並べ替え、ファセットのナビゲーション構造にフィールドを使用できるかどうかを決定します。 
* **[検索可能]** は、フィールドがフルテキスト検索の対象となることを意味します。 文字列は検索可能です。 数値フィールドとブール型フィールドは通常、検索対象外として指定されます。 

既定では、ウィザードは一意の識別子のデータ ソースをキー フィールドの基準としてスキャンします。 文字列は、取得可能かつ検索可能です。 整数は、取得可能、フィルター可能、ソート可能、ファセット可能です。

  ![生成された realestate インデックス][3]

**[OK]** をクリックすると、インデックスが作成されます。

#### <a name="step-3-define-the-indexer"></a>手順 3: インデクサーを定義する
引き続き、**データのインポート** ウィザードで、**[インデクサー]** > **[名前]** の順にクリックし、インデクサーの名前を入力します。 

このオブジェクトによって、実行可能なプロセスが定義されます。 定期的なスケジュールを設定することもできますが、ここでは、**[OK]** をクリックした直後にインデクサーを一度だけ実行する既定のオプションを使用します。  

  ![realestate インデクサー][8]

## <a name="check-progress"></a>進行状況の確認
データのインポートを監視するには、サービス ダッシュボードに戻って下へスクロールし、**[インデクサー]** タイルをダブルクリックしてインデクサーの一覧を開きます。 先ほど作成したインデクサーが一覧に表示されます。"進行中" または "成功" を示すステータスと、インデックスが作成されたドキュメントの数も表示されます。

   ![インデクサーの進行状況のメッセージ][4]

## <a name="query-index"></a>インデックスのクエリを実行する
以上で検索インデックスの設定は完了しました。インデックスを使ってクエリを実行することができます。 **Search エクスプローラー** は、ポータルに組み込まれたクエリ ツールです。 検索ボックスが備わっており、期待どおりの検索結果が返されるかどうかを確認できます。 

> [!TIP]
> 「[Azure Search Overview (Azure Search の概要)](https://channel9.msdn.com/Events/Connect/2016/138)」ビデオの 6 分 8 秒から、次の手順のデモを見ることができます。
>

1. コマンド バーの **[Search エクスプローラー]** をクリックします。

   ![[Search エクスプローラー] コマンド][5]

2. コマンド バーで **[インデックスの変更]** をクリックし、*realestate-us-sample* に切り替えます。

   ![インデックスと API のコマンド][6]

3. コマンド バーで **[API バージョンを設定]** をクリックし、使用できる REST API を確認します。 プレビュー API では、まだ一般にリリースされていない新しい機能を使用できます。 以下のクエリでは、特に指定がない限り、一般公開バージョン (2016-09-01) を使用します。 

    > [!NOTE]
    > [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) は [.NET ライブラリ](search-howto-dotnet-sdk.md#core-scenarios)とまったく同等ですが、REST 呼び出しを処理するために **Search エクスプローラー**を備えています。 Search エクスプローラーは、[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)と[完全な Lucene クエリ パーサー](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)の両方の構文に加え、[ドキュメント検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)操作で使用できるすべての検索パラメーターを受け取ります。
    > 

4. 検索バーで次のクエリ文字列を入力して、**[検索]** をクリックします。

  ![検索クエリの例][7]

**`search=seattle`**

+ `search` パラメーターは、フルテキスト検索用のキーワード検索の入力に使用します。この例では、ドキュメントの検索可能フィールドに *Seattle* を含むワシントン州キング郡内の項目の一覧が返されます。 

+ **Search エクスプローラー**は JSON で結果を返しますが、ドキュメント構造が高密度な場合は冗長で読みづらくなります。 ドキュメントによっては、検索結果から重要な要素を抽出するコードの作成が必要になる場合があります。 

+ ドキュメントは、インデックスで取得可能としてマークされているすべてのフィールドで構成されます。 ポータルでインデックスの属性を表示するには、**[インデックス]** タイルで *[realestate-us-sample]* をクリックします。

**`search=seattle&$count=true&$top=100`**

+ 検索パラメーターを追加するには `&` 記号を使用します。検索パラメーターは任意の順序で指定できます。 

+  `$count=true` パラメーターは、返されたすべてのドキュメントの合計数を返します。 `$count=true` で報告される数の変化を監視することで、フィルター クエリを確認できます。 

+ `$top=100` は、全体の中から上位 100 件のドキュメントを返します。 既定では、Azure Search によって上位 50 件が返されます。 `$top` を使用して、この数を増減できます。

**`search=*&facet=city&$top=2`**

+ `search=*` は、空の検索です。 空の検索は、すべてを検索します。 空のクエリを送信する理由の&1; つは、ドキュメントのすべてのセットをフィルターまたはファセットすることです。 たとえば、インデックス内のすべての都市から成るファセット ナビゲーション構造が必要な場合などです。

+  `facet` は、UI コントロールに渡せるナビゲーション構造を返します。 返されるのはカテゴリと数です。 この例では、カテゴリは都市の数に基づいています。 Azure Search には集計機能はありませんが、各カテゴリのドキュメントの数を示す `facet` により、大まかな集計を行うことができます。

+ `$top=2` では&2; 件のドキュメントが返されます。つまり、`top` を使用すると、結果を減らすことも増やすこともできます。

**`search=seattle&facet=beds`**

+ このクエリは、*Seattle* に対するテキスト検索をベッドルーム数でファセットするものです。 フィールドがインデックスで取得可能、フィルター可能、ファセット可能としてマークされているので、`"beds"` をファセットとして指定できます。また、含まれる値 (1 ～ 5 の数値) は一覧をグループ (3 ベッドルームと 4 ベッドルームの一覧) に分類するのに適しています。 

+ ファセットできるのは、フィルター可能なフィールドのみです。 結果として返されるのは、取得可能なフィールドのみです。

**`search=seattle&$filter=beds gt 3`**

+ `filter` パラメーターは、指定した条件に一致する結果を返します。 この例では、ベッドルーム数が 3 を超えるものが返されます。 

+ フィルター構文は、OData 構文です。 詳細については、[フィルターの OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)に関するページを参照してください。

**`search=granite countertops&highlight=description`**

+ 検索結果の強調表示は、特定のフィールドで一致があった場合の、キーワードに一致したテキストの書式設定を表します。 検索対象の用語が説明に深く埋もれている場合、検索結果の強調表示を追加してその用語を見つけやすくすることができます。 この例では、書式設定された語句 `"granite countertops"` が説明フィールドで見つけやすくなります。

**`search=mice&highlight=description`**

+ フルテキスト検索では、セマンティクスが似ている語形を検索します。 この例では、ネズミの侵入がある家についてのキーワード検索 "mice" に対して、強調表示された "mouse" が検索結果に含まれます。 語形変化していても、言語分析により同じ単語として結果に表示できます。 

+ Azure Search では、Lucene と Microsoft の 56 個のアナライザーをサポートしています。 Azure Search で既定で使用されるのは、Lucene の標準アナライザーです。 

**`search=samamish`**

+ シアトル地域の "Samammish" 高原を検索しようとして「samamish」と入力するなど、語句のスペルを間違うと、通常の検索では一致しません。 スペルミスに対応するため、次の例に示すようにあいまい検索を使用できます。

**`search=samamish~&queryType=full`**

+ `~` 記号を指定し、`~` 構文を解釈して適切に解析する完全なクエリ パーサーを使用すると、あいまい検索が有効になります。 

+ あいまい検索は、完全なクエリ パーサーを設定していると使用できます。そのためには、`queryType=full` を設定します。 完全なクエリ パーサーによって有効になるクエリのシナリオの詳細については、「[Lucene query syntax in Azure Search (Azure Search での Lucene クエリ構文)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)」を参照してください。

+ `queryType` が指定されていない場合、既定の単純なクエリ パーサーが使用されます。 単純なクエリ パーサーの方が高速ですが、あいまい検索、正規表現、近接検索などの高度な種類のクエリが必要な場合は、完全な構文が必要になります。 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ 地理空間検索は、座標を格納しているフィールドの [edm.GeographyPoint データ型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)によってサポートされます。 地理空間検索はフィルターの種類で、[フィルターの OData 構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)で指定します。 

+ この例のクエリでは、位置データのすべての結果から、(緯度と経度の座標で指定された) 特定の地点から 5 km 未満のものをフィルター処理します。 `$count` を追加すると、距離と座標のいずれかを変更した場合に返される結果の数を確認できます。 

+ 地理空間検索は、検索アプリケーションに "近くを検索" 機能がある場合、またはマップ ナビゲーションを使用している場合に有用です。 ただし、フルテキスト検索ではありません。 ユーザーの要件に都市名または国名での検索がある場合は、座標に加えて、都市名または国名を含むフィールドを追加します。

## <a name="next-steps"></a>次のステップ

+ 作成したオブジェクトのいずれかを変更します。 ウィザードを&1; 回実行したら、最初に戻って個々のコンポーネント (インデックス、インデクサー、データ ソース) を表示したり変更を加えたりすることができます。 インデックスでは一部の編集 (フィールドのデータ型の変更など) が禁止されていますが、ほとんどのプロパティおよび設定は変更を加えることができます。

  個々のコンポーネントを表示するには、**[インデックス]**、**[インデクサー]**、**[データ ソース]** のいずれかのタイルをダッシュボードでクリックしてください。既存のオブジェクトの一覧が表示されます。 再構築不要なインデックス編集の詳細については、「[Update Index (Azure Search REST API) (インデックスの更新 (Azure Search REST API))](https://docs.microsoft.com/rest/api/searchservice/update-index)」を参照してください。

+ ツールと手順を他のデータ ソースで試してみます。 サンプル データセット `realestate-us-sample` は、Azure Search がクロールできる Azure SQL Database のデータセットです。 Azure Search は Azure SQL Database 以外にも、Azure Table Storage、Blob Storage、Azure VM 上の SQL Server、DocumentDB のフラットなデータ構造のインデックスをクロールおよび推論できます。 これらのデータ ソースはすべて、ウィザードでサポートされます。 コードでは、"*インデクサー*" を使用して簡単にインデックスを作成できます。

+ インデクサーのない他のデータ ソースはすべて、プッシュ モデルを使用してサポートされます。プッシュ モデルでは、コードは新しい行セットと変更した行セットを JSON でインデックスにプッシュします。 詳細については、[Azure Search でのドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)に関するページを参照してください。

この記事で触れた他の機能の詳細については、以下のリンクをクリックしてください。

* [インデクサーの概要](search-indexer-overview.md)
* [Create Index (インデックスの作成)(インデックスの属性についての詳しい説明を含む)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Search エクスプローラー](search-explorer.md)
* [Search Documents (ドキュメントの検索)(クエリ構文の例を含む)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png
