---
title: サンプル データセットの使用
titleSuffix: Azure Machine Learning Studio
description: Machine Learning Studio に含まれるサンプル モデルで使用されるデータセットの説明です。 実験には、これらのサンプル データセットを使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: f86ae4977621927a09d9b83287a00dfa3bc17196
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880995"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio におけるサンプル データセットの使用
[top]: #machine-learning-sample-datasets

Azure Machine Learning Studio で新しいワークスペースを作成する場合、既定でさまざまなサンプルのデータセットと実験が含まれています。 これらのサンプル データセットの多くは、[Azure AI ギャラリー](https://gallery.azure.ai/)のサンプル モデルで使用されます。 その他のデータセットは、機械学習で通常使用されるさまざまなデータのサンプルとして含まれています。

データセットの一部は、Azure Blob Storage で使用できます。 これらのデータセットでは、次の表に直接リンクが提供されています。 これらのデータセットは、[データのインポート][import-data] モジュールを使用して実験で使用できます。

これらのサンプル データセットの残りの部分は、**[Saved Datasets]/(保存されたデータセット/)** の下にあるワークスペースで使用できます。 これは、Microsoft Azure Machine Learning Studio の実験キャンバスの左側のモジュール パレットにあります。
実験キャンバスにこれらのデータセットのいずれかをドラッグすると、独自の実験で使用できます。

## <a name="datasets"></a>データセット

<table>

<tr>
  <th>データセットの名前</th>
  <th>データセットの説明</th>
</tr>

<tr>
  <td>米国国勢調査局提供の、成人収入に関する二項分類データセット</td>
  <td>
調整後の所得指数が 100 を超える就労成人男性 (16 歳以上) を対象とした、米国国勢調査局のデータベース (1994 年) のサブセットです。
<p></p>
<b>使用法:</b>人口統計データを使用して対象の人々を分類し、個人が 1 年間に 50,000 ドル以上の年収を得られるかどうかを予測します。
<p></p>
<b>関連の研究:</b>Kohavi, R.、Becker, B. (1996 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>空港コードのデータセット</td>
  <td>
米国の空港コード。
<p></p>
このデータセットは、米国の各空港ごとに 1 行という構成になっており、各行には、空港の ID 番号、名前、所在する都市および州が記載されています。
  </td>
</tr>

<tr>
  <td>自動車価格データ (生データ)</td>
  <td>
メーカー/モデル別にまとめた自動車の情報です。価格、シリンダー数、燃費、保険リスク スコアなどの情報が含まれます。
<p></p>
リスク スコアはまず車体価格に関連付けられます。 その後、アクチュアリー (保険数理士) の間でシンボリングと呼ばれるプロセスによって、実際のリスクに対して調整されます。 +3 は自動車のリスクが高く、-3 は高い安全性が見込まれることを示しています。
<p></p>
<b>使用法:</b>回帰または多変量分類を使用して、機能別のリスク スコアを予測します。 
<p></p>
<b>関連の研究:</b>Schlimmer, J.C. (1987 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>自転車レンタルの UCI データセット</td>
  <td>
UCI Bike Rental データセットを使用します。このデータセットは、ワシントン D.C. で自転車のレンタル ネットワークを管理している Capital Bikeshare 社の実際のデータに基づいています。
<p></p>
このデータセットは、2011 年と 2012 年の各日の 1 時間ごとに 1 行という構成になっており、全体で 17,379 行あります。 毎時間の自転車レンタル数の範囲は 1 ～ 977 です。

  </td>
</tr>

<tr>
  <td>Bill Gates の RGB 画像</td>
  <td>
CSV データに変換された、公開の画像ファイル。
<p></p>
画像を変換するコードは、<strong>K-平均法クラスタリングを使用した色の量子化</strong>モデルの詳細ページで提供されています。
  </td>
</tr>

<tr>
  <td>献血データ</td>
  <td>
台湾新竹市にある輸血サービス センターの献血者データベースから提供されたデータのサブセットです。
<p></p>
献血データには、最後の献血からの経過月数、頻度、献血の合計回数、最後の献血からの経過期間、献血量が含まれます。
<p></p>
<b>使用法:</b>目標は、2007 年 3 月に献血者が献血を実施したかどうかの分類に基づいて予測を実行することです。1 は対象期間に献血をした人、0 はしなかった人を示しています。 
<p></p>
<b>関連の研究:</b>Yeh, I. (2008 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部
<p></p>
Yeh, I-Cheng、Yang, King-Jang、Ting, Tao-Ming、「Knowledge discovery on RFM model using Bernoulli sequence」、Expert Systems with Applications、2008 年、<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>乳がんデータ</td>
  <td>
機械学習に関する文献で頻繁に登場する腫瘍学研究所が提供した、3 つのがん関連データセットの 1 つです。 診断情報と、研究所が分析した 300 個の細胞組織サンプルの特徴が組み合わせられています。
<p></p>
<b>使用法:</b>がんの種類を 9 つの属性 (線形、カテゴリなど) に基づいて分類します。 
<p></p>
<b>関連の研究:</b>Wohlberg, W.H.、Street, W.N.、Mangasarian, O.L. (1995 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>乳がん特徴 <td>
それぞれ 117 個の特徴によって説明される、約 102,000 件の疑わしい領域 (対象) を示す X 線画像に関する情報が含まれます。 特徴は独占所有されており、データセット作成元 (Siemens Healthcare) は特徴の意味を公開していません。 
  </td>
</tr>

<tr>
  <td>乳がん情報</td>
  <td>
データセットには、X 線画像の疑わしい領域それぞれについての追加情報が含まれています。 それぞれの例に、乳がんの特徴データセット内の対応する行番号について、ラベル、患者 ID、画像全体に対する患部の座標などの情報が提供されています。 1 人の患者に複数の例あります。 がんを発症している患者については、いくつかの例は陽性で、いくつかの例は陰性です。 がんでない患者の場合、すべての例は陰性です。 データセットには約 102,000 件の例が含まれています。 データセットには偏りがあり、陽性は全ポイントの 0.6% で、残りは陽性です。 データセットは Siemens Healthcare によって提供されています。
  </td>
</tr>

<tr>
  <td>CRM 強い欲求ラベルの共有</td>
  <td>
KDD Cup 2009 顧客間関係の予測に関する課題のラベル (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>)。
  </td>
</tr>

<tr>
  <td>CRM 離反ラベルの共有</td>
  <td>
KDD Cup 2009 顧客間関係の予測に関する課題のラベル (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>)。
  </td>
</tr>

<tr>
  <td>CRM データセットの共有</td>
  <td>
このデータは、KDD Cup 2009 顧客関係の予測に関する課題から取得しています (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>)。
<p></p>
データセットには、French Telecom company Orange の顧客 50,000 人のデータが含まれます。 各顧客には匿名化された特徴が 230 あり、その中の 190 が数値で、40 がカテゴリです。 特徴はきわめて疎です。
  </td>
</tr>

<tr>
  <td>CRM アップセリング ラベルの共有</td>
  <td>
KDD Cup 2009 顧客間関係の予測に関する課題のラベル (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>)。
  </td>
</tr>

<tr>
  <td>エネルギー効率回帰データ</td>
  <td>
12 種類の建物形状に基づいてシミュレーションされたエネルギー プロファイルのコレクションです。 建物は 8 種類の特徴によって区別されています。 これには、ガラス部分、ガラス部分の配置、方向が含まれます。
<p></p>
<b>使用法:</b>回帰または分類を使用して、2 つの実数値のうち 1 つを応答としてエネルギー効率を評価します。 マルチクラス分類の場合、応答変数が最も近い整数に丸められます。 
<p></p>
<b>関連の研究:</b>Xifara, A、Tsanas, A.(2012 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>フライト遅延データ</td>
  <td>
米国運輸省の TranStats データ コレクションから取得した旅客機の定時運航データ (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">定時</a>)。
<p></p>
データセットには、2013 年 4 月から 10 月までの期間のデータが含まれます。 Azure Machine Learning Studio にアップロードする前に、データセットは次のように処理されています。
<ul>
  <li>米国本土の混雑度が上位 70 位までの空港を含めるように、データセットをフィルター処理します。</li>
  <li>キャンセルされたフライトは 15 分超の遅延として分類します。</li>
  <li>迂回したフライトをフィルターで外します。</li>
  <li>次の列が選択されています。Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Cancelled</li>
</ul>
</td>
</tr>

<tr>
  <td>定刻フライト実績 (生データ)</td>
  <td>
2011 年 10 月の米国における航空機のフライトの発着記録です。
<p></p>
<b>使用法:</b>フライトの遅延を予測します。 
<p></p>
<b>関連の研究:</b>米国運輸省 <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> より。
  </td>
</tr>

<tr>
  <td>森林火災データ</td>
  <td>
不快指数や風速などの気象データが含まれます。 データはポルトガル北東地域から取得され、森林火災の記録と結合されます。
<p></p>
<b>使用法:</b>森林火災の被害地域を予測する回帰を実行するという、難しい作業となります。 
<p></p>
<b>関連の研究:</b>Cortez, P.、Morais, A.(2008 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部
<p></p>
[Cortez and Morais、2007 年] P. Cortez、A. Morais. 「A Data Mining Approach to Predict Forest Fires using Meteorological Data」 J. Neves、M. F. Santos and J. Machado Eds.、「New Trends in Artificial Intelligence」(第 13 回 EPIA 2007 - Portuguese Conference on Artificial Intelligence 議事録、512 から 523 ページ) 開催地: ギマランエス (ポルトガル)。2007 年。 APPIA,、SBN-13 978-989-95618-0-9。 <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> で入手可能。
  </td>
</tr>

<tr>
  <td>ドイツのクレジット カード UCI データセット</td>
  <td>
german.data ファイルを使用した、UCI Statlog (ドイツのクレジット カード) データセット (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>)。
<p></p>
データセットは、低信用リスクまたは高信用リスクとして属性のセットで表現された人々を分類します。 サンプルはそれぞれ人を表します。 20 の特徴があり、数値とカテゴリの両方と、二項のラベル (信用リスク値) で構成されます。 高信用リスクのエントリにはラベル 2、低信用リスクのエントリにはラベル 1 が付きます。 低信用リスクのサンプルを高信用リスクとして誤って分類した場合のコストは 1 ですが、高信用リスクのサンプルを低信用リスクとして誤って分類した場合のコストは 5 です。
  </td>
</tr>

<tr>
  <td>IMDB 映画のタイトル</td>
  <td>
このデータセットには、Twitter のツイートで評価された映画に関する情報として、IMDB 登録 ID、映画の名称、ジャンル、および制作年が含まれます。 約 17,000 件の映画の情報があります。 データセットは、S.  Dooms, T. De Pessemier and L. Martens による論文 「MovieTweetings: a Movie Rating Dataset Collected From Twitter。 Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013」で紹介されました。
  </td>
</tr>

<tr>
  <td>あやめの 2 クラス データ</td>
  <td>
パターン認識の文献でおそらく最も多く使用されているデータベースです。 3 種類のあやめの品種について 50 例の花弁を測定した、比較的小さなデータセットです。
<p></p>
<b>使用法:</b>あやめの品種を測定値から予測します。  
<p></p>
<b>関連の研究:</b>Fisher, R.A. (1988 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>ムービーのツイート</td>
  <td>
このデータセットは、ムービー ツイート データセットの拡張バージョンです。 Twitter で適切に構成されたツイートから抽出した、約 170,000 件の評価が含まれます。 各インスタンスは 1 つのツイートを表し、ユーザー ID、IMDB 登録 ID、評価、タイプスタンプ、このツイートのお気に入りの数、そして、このツイートのリツイートの数を組み合わせたタプルです。 データセットは、A. Said、S. Dooms、B. Loni、D. Tikk によって Recommender Systems Challenge 2014 のために提供されました。
  </td>
</tr>

<tr>
  <td>さまざまな自動車の燃費データ</td>
  <td>
カーネギーメロン大学 StatLib 研究所が提供したデータセットを多少変更したものです。 このデータセットは 1983 年に米国統計学会で使用された実績があります。
<p></p>
データには、さまざまな車体の燃料消費量がガロンあたりのマイル数で一覧表示されます。 これには、シリンダー数、エンジン排気量、馬力、総重量、加速度などの情報が含まれます。
<p></p>
<b>使用法:</b>3 つの多値離散属性と 5 つの連続属性に基づいて燃費効率を予測します。 
<p></p>
<b>関連の研究:</b>カーネギーメロン大学 StatLib (1993 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>ピマ族の糖尿病に関する二項分類データセット</td>
  <td>
国立糖尿病・消化器・腎疾病研究所のデータベースから提供されたデータのサブセットです。 データセットはフィルタリングされ、ピマ族の女性患者だけが対象とされています。 データには、血糖値やインスリン値などの医療データと、生活習慣要因が含まれています。
<p></p>
<b>使用法:</b>対象者が糖尿病にかかっているかどうかを予測します (二項分類)。 
<p></p>
<b>関連の研究:</b>Sigillito, V.(1990 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部 </td>
</tr>

<tr>
  <td>レストラン顧客データ</td>
  <td>
人口統計データや嗜好など、顧客に関するメタデータのセットです。
<p></p>
<b>使用法:</b>このデータセットを、レストランに関する他の 2 つのデータセットと組み合わせて使用して、レコメンダー システムの調整とテストを実施します。 
<p></p>
<b>関連の研究:</b>Bache, K.、Lichman, M.(2013 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部。
  </td>
</tr>

<tr>
  <td>レストラン特徴データ</td>
  <td>
料理の種類、食事スタイル、場所など、レストランとその特徴に関するメタデータのセットです。
<p></p>
<b>使用法:</b>このデータセットを、レストランに関する他の 2 つのデータセットと組み合わせて使用して、レコメンダー システムの調整とテストを実施します。 
<p></p>
<b>関連の研究:</b>Bache, K.、Lichman, M.(2013 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部。
  </td>
</tr>

<tr>
  <td>レストランの評価</td>
  <td>
ユーザーによるレストランの評価 (0 ～ 2) が含まれます。
<p></p>
<b>使用法:</b>このデータセットを、レストランに関する他の 2 つのデータセットと組み合わせて使用して、レコメンダー システムの調整とテストを実施します。 
<p></p>
<b>関連の研究:</b>Bache, K.、Lichman, M.(2013 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部。
  </td>
</tr>

<tr>
  <td>鉄鋼のアニール処理に関するマルチクラス データセット</td>
  <td>
このデータセットには、鉄鋼のアニール処理試験に関する一連の記録が含まれています。 幅、厚み、種類 (コイル、シートなど) など、処理後のさまざまな物理的属性も含まれます。
<p></p>
<b>使用法:</b>2 つの数値クラス属性 (硬度と強度) のいずれかを予測します。 属性間の相関関係を分析することも可能です。
<p></p>
鉄鋼のグレードは、SAE その他の機関が定義した標準に準拠します。 特定の "グレード"(クラス変数) を検索して、必要な値を把握する必要があります。 
<p></p>
<b>関連の研究:</b>Sterling, D.、Buntine, W.(年度不明)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部
<p></p>
鉄鋼グレードの有用なガイドは、<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
   にあります。</td>
</tr>

<tr>
  <td>望遠鏡データ</td>
  <td>
高エネルギー ガンマ線バーストとバックグラウンド ノイズをモンテカルロ法でシミュレーションした記録です。
<p></p>
シミュレーションの目的は大気地上チェレンコフ ガンマ望遠鏡の精度向上です。 これは、期待する信号 (チェレンコフ放射) と上層大気の宇宙線から発生するバックグラウンド ノイズ (ハドロン放射) を統計的手法によって区別することで実現します。
<p></p>
データを前処理することによって、長軸がカメラの中心を向く伸長クラスターが作成されています。 この楕円のさまざまな特性は通常 Hillas パラメーターと呼ばれ、これらが画像のパラメーターに表れることによって区別が可能になります。
<p></p>
<b>使用法:</b>放射の画像が信号とバックグラウンド ノイズのどちらを表しているかを予測します。
<p></p>
<b>注:</b>バックグラウンド イベントを信号に分類することは、シグナル イベントをバックグラウンドに分類することよりも大きな悪影響を及ぼすため、シンプルな分類の正確度は、このデータでは意味を持ちません。 異なる分類子の比較には ROC グラフを使用する必要があります。 バックグラウンド イベントを信号として受信する可能性は、次のしきい値のいずれかを下回る必要があります。0.01、0.02、0.05、0.1、または 0.2。
<p></p>
また、バックグラウンド イベントの数 (ハドロン放射の場合 h) は過小評価されます。 実際の測定では、h またはノイズ クラスがイベントの大部分を占めています。 
<p></p>
<b>関連の研究:</b>Bock, R.K. (1995 年)。 UCI Machine Learning リポジトリ <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 カリフォルニア州アーバイン:カリフォルニア大学、情報学部 </td>
</tr>

<tr>
  <td>天候データセット</td>
  <td>
NOAA の 1 時間ごとの陸上の気象観測 (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">2013 年 4 月から 2013 年 10 月までのデータをマージ</a>)。
<p></p>
気象データには、2013 年 4 月から 10 月までの期間の航空気象観測所での観測が含まれます。 Azure Machine Learning Studio にアップロードする前に、データセットは次のように処理されています。
<ul>
  <li>気象観測所 ID を対応する空港 ID にマッピングします。</li>
  <li>混雑度が上位 70 位の空港に関連付けられていない気象観測所をフィルターで外します。</li>
  <li>Date 列を Year、Month、Day の列に分割します。</li>
  <li>次の列が選択されています。AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 データセット</td>
  <td>
データは、S&P 500 企業それぞれの記事に基づいて Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) から取得され、XML データとして格納されています。
<p></p>
Azure Machine Learning Studio にアップロードする前に、データセットは次のように処理されています。
<ul>
  <li>特定の企業のテキスト コンテンツを抽出します。</li>
  <li>Wiki の書式設定を削除します。</li>
  <li>英数字以外の文字を削除します。</li>
  <li>すべてのテキストを小文字に変換します。</li>
  <li>既知の会社のカテゴリを追加します。</li>
</ul>
<p></p>
いくつかの企業の記事が見つからないため、レコード数は 500 未満であることに注意してください。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
顧客データと、ダイレクト メール キャンペーンに対する顧客の反応を示す指標が含まれます。 1 行につき 1 件の顧客を表します。 データセットには、ユーザーの人口統計と過去の行動に関する 9 個の特徴と、3 つのラベル列 (訪問、コンバージョン、支出) が含まれます。  訪問は、顧客がマーケティング キャンペーン後に訪問したことを示すバイナリ列です。 コンバージョンは、顧客が何かを購入したことを示します。 支出は、使われた金額です。  データセットは Kevin Hillstrom によって、MineThatData 電子メール分析とデータ マイニングの課題用に提供されています。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
RCV1-V2 Reuters ニュースのデータセットにあるテスト例の特徴。 データセットには約 781,000 本のニュース記事が、それぞれの記事の ID (データセットの最初の列) と共に含まれます。 各記事はトークン化され、ストップワード処理と語幹処理が行われています。 データセットは David D. Lewis によって提供されています。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
RCV1-V2 Reuters ニュースのデータセットにあるトレーニング例の特徴。 データセットには約 23,000 本のニュース記事が、それぞれの記事の ID (データセットの最初の列) と共に含まれます。 各記事はトークン化され、ストップワード処理と語幹処理が行われています。 データセットは David D. Lewis によって提供されています。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
KDD Cup 1999 ナレッジ検出とデータ マイニング ツールのコンペティションのデータセット (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>)。
<p></p>
データセットは、Azure Blob Storage にダウンロードされ、格納されています (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>)。データセットには、トレーニングとテストの両方のデータセットが含まれます。 トレーニング データセットには、ラベルも含めておよそ 126,000 個の行と 43 個の列があります。 ラベル情報の一部である 3 列と、数値、文字列、カテゴリによる特徴で構成される 40 列がモデルのトレーニングに使用できます。 テスト データには約 22,500 個のテスト例があります (トレーニング データと同じ 43 列が含まれます)。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
RCV1-V2 Reuters ニュース データセット内のニュース記事のトピック割り当てです。 1 つのニュース記事を複数のトピックに割り当てることができます。 各行の形式は "&lt;トピック名&gt; &lt;ドキュメント ID&gt; 1" です。 データセットには、260 万件のトピック割り当てが含まれます。 データセットは David D. Lewis によって提供されています。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
このデータは、KDD Cup 2010 学生の成績評価に関する課題 (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">学生の成績評価</a>) から取得しています。 使用されるデータは、Algebra_2008_2009 トレーニング セット (Stamper, J.、Niculescu-Mizil, A.、Ritter, S.、Gordon, G.J.、Koedinger, K.R. (2010)、 Algebra I 2008-2009 です。 KDD Cup 2010 データ マイニングの学習課題のデータセットは、 <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> にあります。
<p></p>
データセットは、Azure Blob Storage にダウンロードされ、格納されています (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>)。データセットには、学生のチュータリング システムのログ ファイルが含まれます。 提供される特徴には、問題 ID とその簡潔な説明、学生 ID、タイムスタンプ、学生が問題を正しい方法で解くまでにかかった回数があります。 元のデータセットには、8,900,000 件のレコードが含まれますが、このデータセットは、最初の 100,000 行にダウンサンプリングされています。 このデータセットには、23 個のタブで区切られた、さまざまなタイプ (数値、カテゴリ、およびタイムスタンプ) の列が含まれます。
  </td>
</tr>

</table>

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [例を使用して実験を開始する](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
