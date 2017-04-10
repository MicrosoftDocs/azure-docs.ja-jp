---
title: "クイック スタート ガイド: Machine Learning Recommendations API | Microsoft Docs"
description: "Azure Machine Learning Recommendations - クイック スタート ガイド"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 7f90fe5e-c0bf-4c16-9624-49118adf9069
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 2acb7a96b04ba457fefe811765b17ee869de52bc
ms.openlocfilehash: 86d297fd902eb1eb8af159bc684ed119feef2b30


---
# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Cognitive Services の Recommendations API のクイック スタート ガイド
このドキュメントでは、 [Recommendations API](http://go.microsoft.com/fwlink/?LinkId=759710)を使用できるようにサービスまたはアプリケーションをオンボードする方法について説明します。
Recommendations API と他の Cognitive Services の詳細については、 [こちら](http://go.microsoft.com/fwlink/?LinkId=759709)をご覧ください。 このガイド全体を通じて、[Recommendations API リファレンス](http://go.microsoft.com/fwlink/?LinkId=759348)と [Recommendations UI クイックスタート ガイド](cognitive-services-recommendations-ui-intro.md)をいつでも参照できます。

<a name="Overview"></a>

### <a name="general-overview"></a>概要
このドキュメントはステップ バイ ステップ ガイドです。 モデルのトレーニングに必要な手順を説明し、運用環境からモデルを使用できるようにするためのリソースを示すことを目的としています。

この演習の所要時間は約 30 分です。

[Recommendations API](http://go.microsoft.com/fwlink/?LinkId=759710)を使用するには、次の手順を実行する必要があります。

1. モデルの作成 – モデルは、使用状況データ、カタログ データ、推奨モデルのコンテナーです。
2. カタログ データのインポート - カタログには、各商品に関するメタデータ情報が含まれます。
3. 使用状況データのインポート – 使用状況データは、2 つの方法のいずれか (または両方) でアップロードできます。
   * 使用状況データを含むファイルをアップロードする。
   * データ取得イベントを送信する。
     通常は、使用状況ファイルをアップロードして初期の推奨モデル (ブートストラップ) を作成し、システムがデータ取得形式を使用して十分なデータを収集するまではそのモデルを使用します。
4. 推奨モデルの構築 – これは、推奨システムがすべての使用状況データを受け取って推奨モデルを作成する非同期操作です。 この操作は、データのサイズとビルド構成パラメーターによっては、数分または数時間かかることがあります。 ビルドをトリガーすると、ビルドの ID を取得します。 Recommendations の使用を開始する前に、ビルド ID を使用してビルド プロセスの終了を確認します。
5. 推奨の使用 – 特定の項目や項目一覧に対する推奨を取得します。

<a name="GetStarted"></a>

### <a name="lets-get-started"></a>作業開始
推奨モデルの構築を開始します。 次に、モデルによって生成される結果を使用して、電子商取引サイトで推奨を活用する方法について説明します。

<a name="Ex1Task1"></a>

#### <a name="task-1---signing-up-for-the-recommendations-api"></a>タスク 1 - Recommendations API にサインアップする
このタスクでは、Recommendations API サービスにサインアップし、推奨モデルを作成します。

1. **Azure ポータル** ( [http://portal.azure.com/](http://portal.azure.com/) ) に移動し、Azure アカウントでサインインします。
2. **[+ 新規]**をクリックします。
3. **[インテリジェンス]** オプションを選択します。
4. **[Cognitive Services API]** 製品を選択します。
   この製品を使用すると、Cognitive Services API (Face、Text Analytics、Computer Vision など) のサブスクリプションを開始できます。 ここでは、Recommendations API に焦点を合わせます。
5. Cognitive Services API のランディング ページで、Recommendations のサブスクリプションの**アカウント名**を入力します  (例: "MyRecommendations")。 この名前には、スペースを含めることはできません。
6. **[API type (API の種類)]** で **[Recommendations]** を選択します。
7. **[価格レベル]** では、プランを選択できます。 1 か月あたり 10,000 トランザクションの **Free** レベルを選択してもかまいません。 これは無料プランであるため、システムを試用する場合に適しています。 運用環境に移行したら、要求のボリュームを考慮し、それに応じてプランの種類を変更することをお勧めします。 (注: Free レベルのサブスクリプションは一度に 1 つだけ使用できます。)
8. **リソース グループ**を選択するか、新しいリソース グループを作成します (リソース グループがまだない場合)。
9. [作成] ダイアログで他の要素を変更することもできます。 現在、リソース プロバイダーは米国のデータ センターからのみサポートされることに注意してください。
10. 選択が終了したら、 **[作成]**をクリックします。
11. リソースがデプロイされるまで、数分待ちます。
    リソースがデプロイされたら、**[設定]** ブレードの **[キー]** セクションに移動できます。このセクションでは、API を使用するためのプライマリ キーとセカンダリ キーが提供されます。  プライマリ キーをコピーします。このキーは、最初のモデルの作成時に必要になります。

<a name="Ex1Task2"></a>

#### <a name="task-2---did-you-bring-your-data"></a>タスク 2 - データを提供する
Recommendations API は、適切に製品を推奨するために、カタログとトランザクションから学習します。 つまり、製品に関する適切なデータ (**カタログ** ファイル) と、対象となる使用パターン (**使用状況**) を API が見つけることができるだけの十分な規模の一連のトランザクションを提供する必要があります。

1. 通常は、トランザクション データベースにこれらの情報を照会します。
   情報をすぐに入手できない場合に備えて、Microsoft ストアのトランザクション データに基づくサンプル データが用意されています。
   
   このデータは、 [こちら](http://aka.ms/RecoSampleData)からダウンロードできます。 MsStoreData.Zip をコピーし、ローカル コンピューター上のフォルダーに展開します。
   
   > [!NOTE]
   > タスク 3 でダウンロードして実行するサンプル コードには、サンプル データが既に組み込まれているため、このタスクは省略可能です。  ただし、このタスクでは、より現実的なデータ セットをダウンロードできるので、Recommendations API への入力について理解を深めることができます。
   > 
   > 
2. 次に、カタログ ファイルを見てみましょう。 データをコピーした場所に移動します。
   **メモ帳**でカタログ ファイルを開きます。
   
   カタログ ファイルは非常に単純であることがわかります。 カタログ ファイルは、 `<itemid>,<item name>,<product category>`
   
   > AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office  <br>
   > AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office  <br>
   >  C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 - Camel,Accessories
   > 
   > 
   
   カタログ ファイルは、さらに充実した内容にすることができます。たとえば、製品に関するメタデータ ("*商品の特徴*") を追加できます。 カタログの形式の詳細については、API リファレンスの[カタログの形式](http://go.microsoft.com/fwlink/?LinkID=760716)に関するセクションをご覧ください。
3. 使用状況データでも同じ操作を行います。 使用日が `<User Id>,<Item Id>,<Time Stamp>,<Event>`の形式であることがわかります。
   
   > 00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase 0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase 0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase 00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase 0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase 00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase
   > 
   > 

最初の 3 つの要素は必須であることに注意してください。 イベントの種類は省略可能です。 このトピックの詳細については、 [使用状況の形式](http://go.microsoft.com/fwlink/?LinkID=760712) に関するページをご覧ください。

> **必要なデータ量**
> 
> <p>
> 必要なデータ量は、実際には使用状況データ自体によって異なります。 システムは、ユーザーがさまざまな商品をいつ購入したかを学習します。 FBT などの一部のビルドでは、同じトランザクションで購入された商品を把握することが重要となります  (これを "*同時発生*" と呼びます)。 したがって、カタログに 10,000 個の商品が含まれている場合は、少なくともその 20 倍の数のトランザクション (約 200,000 個のトランザクション) を保持することをお勧めします。
> 繰り返しますが、これはあくまで目安です。 実際のデータでテストする必要があります。
> </p>
> 
> 

<a name="Ex1Task3"></a>

#### <a name="task-3---creating-a-recommendations-model"></a>タスク 3 - 推奨モデルを作成する
アカウントとデータを用意したところで、最初のモデルを作成しましょう。

このタスクでは、サンプル アプリケーションを使用して最初のモデルを構築します。

1. まず、 [Recommendations API リファレンス](http://go.microsoft.com/fwlink/?LinkId=759348)を確認しておきます。
2. [サンプル アプリケーション](http://go.microsoft.com/fwlink/?LinkID=759344) をローカル フォルダーにダウンロードします。
3. Visual Studio で、**C#** フォルダーにある **RecommendationsSample.sln** ソリューションを開きます。
4. **SampleApp.cs** ファイルを開きます。 このファイルで次の手順を確認します。
   
   * モデルの作成
   * カタログ ファイルの追加
   * 使用状況ファイルの追加
   * モデルのビルドのトリガー
   * 商品の組み合わせに基づいた推奨の取得
     <p></p>
5. **AccountKey** フィールドの値をタスク 1 で入手したキーに置き換えます。
6. ソリューションをステップ実行し、モデルがどのように作成されるのかを確認します。
7. ダウンロードしたカタログ ファイルと使用状況ファイルを置き換えて、Microsoft ストアの新しいモデル (書籍の推奨) を作成してみます。 モデル名も変更し、推奨を要求する商品を変更する必要があります。
8. モデルが作成されたら、**モデル ID** をメモします。この ID は運用環境で推奨を要求する際に必要になります。

> ビルドの種類の詳細とビルドの品質を評価する方法については、 [こちら](cognitive-services-recommendations-buildtypes.md)を参照してください。
> 

<a name="Ex1Task4"></a>

### <a name="putting-your-model-in-production"></a>運用環境へのモデルの配置
モデルを作成し、推奨を使用する方法がわかりました。次に、モデルを Web サイトやモバイル アプリケーションの運用環境に配置するか、CRM または ERP システムに統合します。
言うまでもなく、これらの実装はそれぞれ異なります。 Recommendations API は Web サービスとして要求されるので、これらの各種環境から API を簡単に呼び出すことができる必要があります。

>  [!IMPORTANT]
>  パブリック クライアント (電子商取引サイトなど) から推奨を表示する場合は、推奨を提供するプロキシ サーバーを作成する必要があります。 API キーが (信頼されていない可能性のある) 外部エンティティに公開されないようにするために、これが重要となります。

推奨を使用できる場所として、次の場所が考えられます。

### <a name="product-page"></a>製品ページ
**商品の推奨**

<p>モデルが購入データでトレーニングされている場合、顧客は "*元商品を購入したユーザーの興味を引くと考えられる製品を見つける*" ことができます。</p>
<p>モデルがデータのクリックでトレーニングされている場合、顧客は "*元商品にアクセスしたユーザーがアクセスする可能性のある製品を見つける*" ことができます。 この種のモデルは、類似する商品を返す場合があります。</p>

**よく一緒に購入されている商品の推奨**

<p>この商品と一緒に購入される可能性のある商品のセットを取得できるように、よく一緒に購入されている商品ビルドをトレーニングできます。</p>

### <a name="check-out-page"></a>チェック アウト ページ
**商品の推奨**

<p>推奨モデルは、商品のリストを入力として受け取ることができます。 そのため、バスケット内のすべての商品を入力として渡して推奨を取得できます。
この場合、モデルはバスケット内のすべての商品を基に推奨を提供します。
</p>

### <a name="landing-page"></a>ランディング ページ
**ユーザーの推奨**

<p>
推奨モデルは、ユーザー ID を入力として受け取ることができます。  この場合、該当のユーザーによるトランザクションの履歴を使用して、個人に合わせた推奨が指定のユーザーに提供されます。
</p>

[商品の推奨の取得に関するドキュメント](http://go.microsoft.com/fwlink/?LinkID=760719)をご覧ください。

<a name="Ex1Task6"></a>

### <a name="whats-next"></a>次の手順
これで演習は終了です。 [詳細を確認する場合は、完全な Recommendations API リファレンス](http://go.microsoft.com/fwlink/?LinkId=759348)をご覧ください。不明な点があれば、mlapi@microsoft.com までお気軽にお問い合わせください。




<!--HONumber=Nov16_HO5-->


