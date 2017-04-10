---
title: "Recommendations UI を使用したモデルのビルド | Microsoft Docs"
description: "Azure Machine Learning の推奨事項 - Recommendations UI を使用したモデルのビルド"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5f98395e2beba671192e50f8e53c6198e1efed29


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Recommendations UI を使用したモデルのビルド
このドキュメントはステップ バイ ステップ ガイドです。 このガイドは、[Recommendations UI](https://recommendations-portal.azurewebsites.net/) を使用したモデルのトレーニングに必要な手順を説明することを目的としています。
演習を進めることで、モデルをビルドするプロセスを、プログラムによってそれを行う前に理解することができます。 また、サービスの使用開始に役立つ UI を詳しく理解することもできます。

演習の所要時間は約 30 分です。

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>手順 1 - Recommendations UI にサインインする
1. まだサインインしていない場合は、新しい [Recommendations API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) サブスクリプションに[サインアップ](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)する必要があります。 **Azure** ([http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1)) でサービスにサインアップし、Azure アカウントでサインインします。 サインアップ プロセスの詳細な手順については、[ファースト ステップ ガイド](cognitive-services-recommendations-quick-start.md)の「*タスク 1*」を参照してください。 
2. Recommendations API サブスクリプションの**キー**を入手したら、[Recommendations UI](https://recommendations-portal.azurewebsites.net/) に移動します。 
3. **アカウント キー** フィールドにキーを入力してログインし、**[ログイン]** ボタンをクリックします。
   
    ![Recommendations UI: [サインイン] ダイアログ][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>手順 2 - トレーニング データを収集する
ビルドを作成する前に、エンジンに次の 2 つの情報が必要です: カタログ ファイルおよび使用状況ファイルのセット。 

カタログ ファイルには、顧客に提供している項目についての情報が含まれています。 使用状況ファイルには、それらの項目がどのように使用されているかやビジネスのトランザクションに関する情報が含まれています。

通常は、ストア データベースにこれらの情報を照会します。 Recommendations API を使用する方法を習得するためのサンプル データが用意されています。

データは [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData) からダウンロードできます。 **Books.Zip** ファイルをコピーし、ローカル コンピューター上のフォルダーに展開します  (**c:\data** など)。

カタログ ファイルと使用状況ファイルのスキーマについて詳しくは、「[モデルをトレーニングするためのデータの収集](cognitive-services-recommendations-collecting-data.md)」をご覧ください。

この演習では、トレーニングの待機時間を少なくするために小さいファイルを使用します。 より現実的なファイルを試す必要がある場合は、[同じ場所](http://aka.ms/RecoSampleData)に Microsoft ストアのサンプルトランザクションを含む **MsStoreData.zip** が用意されています。

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>手順 3: プロジェクトを作成してカタログ データと使用状況データをアップロードする
[Recommendations UI](https://recommendations-portal.azurewebsites.net/) にログインすると [Projects] ページが表示されます。 以前に作成したプロジェクトもここに表示されます。
プロジェクト (API リファレンスの*モデル*とも呼ばれます) は、カタログ データと使用状況データのコンテナーです。 プロジェクト内に複数の*ビルド*を作成することができます。 次の手順では、このプロセスについて説明します。

1. 新しいプロジェクトを作成するには、テキスト ボックスに名前を入力し ("MyFirstModel" などをお勧めします)、**[プロジェクトの追加]** をクリックします。
   
    ![Recommendations UI: [Projects] ページ。][reco_projects]
2. プロジェクトを作成したら、**[カタログ ファイルの追加]** セクションの **[ファイルの参照]** ボタンをクリックします。 
   手順 2. で取得したカタログをアップロードします。 *c:\data* に保存した場合は、このフォルダーに移動する必要があります。
   
     ![Recommendations UI: プロジェクトへのデータの追加。][reco_firstmodel]
3. カタログがアップロードされたら、**[使用状況ファイルの追加]** セクションの **[ファイルの参照]** ボタンをクリックします。 usage_large.txt ファイルを追加します。

> **サイズの大きい使用状況データのファイルがある場合**
> 
> アップロードできるのは、200 MB 未満の使用状況ファイルのみです。 ただし、システムは最大 2 GB 分のトランザクション データを保持できるため、必要に応じて複数のファイルをアップロードすることができます。
> 適切なモデルの生成にそれほど多くのデータを必要としないかもしれませんが、係わってくるのはデータのサイズだけでなく、データの品質も問題となります。 よく見られる使用状況データは、ほとんどのトランザクションがいくつかの人気のある項目のみに関するものであり、その他の項目には "信号がほとんどない" ものです。
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>手順 4 - トレーニングを行う
カタログ データと使用状況データをアップロードしたので、次に、データのパターンを覚えるようエンジンをトレーニングしてみましょう。

1. **[New Build (新しいビルド)]** ボタンをクリックします。
2. ビルド タイプとして **[Recommendations]** を選択します。 順位付けのビルドおよび FBT (よく一緒に購入されている品目) ビルド タイプもサポートされています。
   
   ![Recommendations UI: [ビルド] ダイアログ][reco_build_dialog.png]

    FBT ビルドを使用すると、同じトランザクションでよく購入/消費される製品のパターンを特定できます。
    順位付けのビルドを使用すると、関心のある機能を特定できます。 
    このワークショップでは FBT または順位付けビルドについて掘り下げませんが、興味をお持ちの場合は、「[ビルドの種類とモデルの品質](cognitive-services-recommendations-buildtypes.md)」を参照してください。

1. **[ビルド]** ボタンをクリックします。 ビルド プロセスはブック データを使用した場合、約 5 分かかります。 データセットが大きいほど、かかる時間も長くなります。

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>手順 5 - 機械が何を学習したかを確認する
ビルドが完了すると、ビルド一覧に新しいビルドが表示されます。 このビルドに項目やユーザーの推奨事項を照会できます。

1. ビルドが完了したら、**[スコア]** をクリックします。 これにより、モデルをいろいろ試したり、推奨されている項目を確認したりできます。
   
    ![Recommendations UI: [スコア] ボタン][reco_score_button]
2. 項目を選択して、その項目の推奨としてどのような項目が返されるかを確認します。 特定の項目の推奨を予測するのに十分なトランザクションがない場合、その項目の推奨が返されないことに注意してください。  何らかの理由で、項目で推奨が返されない場合は、他の項目のスコアリングをお試しください。

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>手順 6 - 次のステップ
ご利用ありがとうございます。 モデルのトレーニングが完了し、そのモデルから推奨を取得しました。  Recommendations UI は、プロジェクトやビルドの状態を確認するのに便利なツールです。 

モデルを作成したので、前記のすべての手順をプログラムによって実行する方法を習得しましょう。 API をプログラムによって呼び出す方法を学習するために、[Recommendations API リファレンス](http://go.microsoft.com/fwlink/?LinkId=759348)を確認し、[Recommendations サンプル アプリケーション](http://go.microsoft.com/fwlink/?LinkID=759344)をダウンロードすることをお勧めします。

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Dec16_HO2-->


