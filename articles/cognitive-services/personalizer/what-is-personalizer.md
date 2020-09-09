---
title: Personalizer とは
description: Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにするクラウドベースのサービスです。
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: Personalizer, Azure Personalizer, 機械学習
ms.openlocfilehash: 93b1f0487818cf783cc4a19f79618cfe2285cbd1
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055619"
---
# <a name="what-is-personalizer"></a>Personalizer とは

Azure Personalizer は、最適なコンテンツ項目をアプリケーションが選択してユーザーに表示できるよう支援するクラウドベースのサービスです。 Personalizer サービスを使用すると、買い物客に提示すべき製品を判断したり、広告の最適な表示位置を見つけ出したりすることができます。 そのコンテンツがユーザーに表示されると、システムは、リアルタイムでユーザーの行動を監視し、Personalizer サービスに報酬スコアをレポート バックします。 そうすることで機械学習モデルは継続的に改善され、また受け取ったコンテキスト情報に基づいて最適なコンテンツ項目を選択する Personalizer の能力も継続的に改善されます。

> [!TIP]
> コンテンツには、テキスト、画像、URL、メールなど、抽出してユーザーに表示するあらゆる情報単位が当てはまります。

先に進む前に、[こちらの対話型デモで Personalizer](https://personalizationdemo.azurewebsites.net/) を自由にお試しください。

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>最適なコンテンツ項目を Personalizer が選択するしくみ

Personalizer は**強化学習**を使用し、すべてのユーザーにわたって集めた行動と報酬スコアに基づいて最適な項目 ("_アクション_") を選択します。 アクションとは、ニュース記事、特定の映画、製品などのコンテンツ項目です。

**Rank** 呼び出しは、アクション項目、アクションの特徴、コンテキストの特徴を受け取って、最上位のアクション項目を選択します。

* **アクションと特徴** - コンテンツ項目と各項目に固有の特徴
* **コンテキストの特徴** - アプリ使用時のユーザーとそのコンテキスト、環境の特徴

Rank 呼び出しからは、ユーザーに表示するコンテンツ項目 (__アクション__) の ID が、**報酬アクション ID** フィールドで返されます。

ユーザーに表示される__アクション__は、一定期間にわたる報酬の総量が最大となるように機械学習モデルを使用して選択されます。

### <a name="sample-scenarios"></a>サンプル事例

ユーザーに表示する最適なコンテンツを選択する際に Personalizer が利用できるシナリオをいくつか見てみましょう。

|Content type|アクションと特徴|コンテキストの特徴|返される報酬アクション ID<br>(このコンテンツが表示されます)|
|--|--|--|--|
|ニュース リスト|a. `The president...` (国内, 政治, [テキスト])<br>b. `Premier League ...` (グローバル, スポーツ, [テキスト, 画像, ビデオ])<br> c. `Hurricane in the ...` (地域, 気象, [テキスト,画像]|ニュースの閲覧に使用するデバイス<br>月または季節<br>|a `The president...`|
|映画リスト|1.`Star Wars` (1977, [アクション, アドベンチャー, ファンタジー], ジョージ ルーカス)<br>2.`Hoop Dreams` (1994, [ドキュメンタリー, スポーツ], スティーブ ジェームズ<br>3.`Casablanca` (1942, [ロマンス, ドラマ, 戦争], マイケル カーティス)|映画の視聴に使用するデバイス<br>画面サイズ<br>ユーザーの種類<br>|3. `Casablanca`|
|Product 一覧|i. `Product A` (3 kg, $$$$, 24 時間以内に配送)<br>ii. `Product B` (20 kg, $$, 出荷と通関に 2 週間)<br>iii. `Product C` (3 kg, $$$, 48 時間以内に配送)|ショッピングの閲覧に使用するデバイス<br>ユーザーの支出階層<br>月または季節|ii. `Product B`|

Personalizer は、"_報酬アクション ID_" と呼ばれる最適なアクションを 1 つ選択するために強化学習を使用しました。 次の情報が機械学習モデルによって使用されます。 

* トレーニング済みのモデル - 過去にパーソナライズ サービスから受け取った情報。機械学習モデルの改善に使用されます。
* 最新のデータ - 特徴を伴う具体的なアクション、コンテキストの特徴。

## <a name="when-to-use-personalizer"></a>Personalizer が使用される状況

アプリケーションがコンテンツを表示するたびに、Personalizer の **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) が呼び出されます。 これは**イベント**と呼ばれ、"_イベント ID_" で表されます。

Personalizer の **Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) は、リアルタイムで呼び出すことも、実際のインフラストラクチャに合わせて時間差で呼び出すこともできます。 報酬スコアは、ビジネス ニーズに基づいて決めます。 報酬スコアは 0 と 1 の間です。 これは、適切な場合は 1、不適切な場合は 0 というように 1 つの値になる場合があるほか、ビジネス目標や指標を考慮して作成したアルゴリズムによって生成される場合もあります。

## <a name="content-requirements"></a>コンテンツの要件

Personalizer は、対象のコンテンツが次の条件を満たす場合に使用します。

* 選択肢となる一連の項目数が限られている (最大約 50 個)。 リストがそれよりも大きい場合は、[レコメンデーション エンジンを使用](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)して、項目を 50 個まで減らしてください。
* ランク付けしたいコンテンツを説明する情報がある ("_アクションと特徴_" および "_コンテキストの特徴_")。
* Personalizer で効果を発揮するために、コンテンツに関連したイベントが 1 日あたり少なくとも約 1,000 件発生する。 最低限必要なトラフィックが Personalizer に送信されなかった場合、このサービスでは、最適なコンテンツ項目を 1 つ割り出すために、さらに時間がかかります。

Personalizer は、集まった情報をほぼリアルタイムで使用して最適なコンテンツ項目を 1 つ返すため、このサービスでは次のことが行われません。
* ユーザー プロファイル情報を保持および管理する
* 個々のユーザーの好みや履歴を記録する
* クリーニング済みおよびラベル付きのコンテンツを要求する

## <a name="how-to-design-for-and-implement-personalizer"></a>Personalizer を設計して実装する方法

1. コンテンツ、"**_アクション_**"、"**_コンテキスト_**" を[設計](concepts-features.md)および計画します。 "**_報酬_**" スコア用の報酬アルゴリズムを決定します。
1. 作成した各 [Personalizer リソース](how-to-settings.md)は、1 学習ループと見なされます。 このループでは、そのコンテンツまたはユーザー エクスペリエンスに対する Rank と Reward 両方の呼び出しを受け取ります。

    |リソースの種類| 目的|
    |--|--|
    |[見習いモード](concept-apprentice-mode.md) `E0`|既存のアプリケーションに影響を与えずに Personalizer モデルをトレーニングしてから、運用環境にオンライン学習動作をデプロイします。|
    |標準、`S0`|運用環境でのオンライン学習動作|
    |無料、`F0`| 非運用環境でオンライン学習動作を試します。|

1. 対象のアプリケーション、Web サイトまたはシステムに Personalizer を追加します。
    1. ユーザーにコンテンツを表示する前に最適な "_コンテンツ_" 項目を 1 つ決定する **Rank** 呼び出しをアプリケーション、Web サイト、またはシステムの Personalizer に追加します。
    1. 最適な "_コンテンツ_" 項目を 1 つユーザーに表示します。これは、返された "_報酬アクション ID_" です。
    1. ユーザーの行動に関して収集された情報に "_ビジネス ロジック_" を適用して、次のように**報酬**スコアを決定します。

    |動作|算出された報酬スコア|
    |--|--|
    |ユーザーが最適な "_コンテンツ_" 項目 (報酬アクション ID) を 1 つ選択しました|**1**|
    |ユーザーがその他のコンテンツを選択しました|**0**|
    |ユーザーが一時停止し、漠然とした態度で前後にスクロールした後、最適な "_コンテンツ_" 項目 (報酬アクション ID) を 1 つ選択しました|**0.5**|

    1. 0 から 1 までの報酬スコアを送信する **Reward** 呼び出しを追加します。
        * コンテンツを表示した直後
        * またはオフライン システムで少し経ってから
    1. 一定期間使用した後、オフライン評価を使用して[ループを評価](concepts-offline-evaluation.md)します。 オフライン評価を使用すると、コードを変更したりユーザー エクスペリエンスに影響を与えたりすることなく、Personalizer サービスの有効性をテストして評価することができます。

## <a name="complete-a-quickstart"></a>クイックスタートに取り組む

クイックスタートは、C#、JavaScript、Python でご利用いただけます。 それぞれ基本的な設計パターンを学び、いずれも 10 分もかからずにコードを実行できるように作られています。 

* [クイック スタート: Personalizer クライアント ライブラリを使用する方法](sdk-learning-loop.md)

Personalizer サービスを使用する機会が得られたら、Web アプリケーションやチャット ボット、Azure Notebooks で Personalizer を使用する方法を、チュートリアルを通じて学びましょう。

* [チュートリアル:.NET Web アプリで Personalizer を使用する](tutorial-use-personalizer-web-app.md)
* [チュートリアル:.NET チャット ボットで Personalizer を使用する](tutorial-use-personalizer-chat-bot.md)
* [チュートリアル:Azure Notebook で Personalizer を使用する](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>関連項目 

* [Personalizer C#/.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Personalizer Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/personalizer/v1.0/personalizer)
* [Personalizer JavaScript SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Personalizer Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Personalizer のしくみ](how-personalizer-works.md)
> [強化学習とは](concepts-reinforcement-learning.md)
