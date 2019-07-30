---
title: Excel からの分析の移行
titleSuffix: Azure Machine Learning Studio
description: Excel と Azure Machine Learning Studio での線形回帰モデルの比較
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7db66f6f4efa5e48f2af9380115de8bcfb75cb86
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786686"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Excel から Azure Machine Learning Studio に分析を移行する

> *Kate Baroni* 氏と *Ben Boatman* 氏は、マイクロソフトの Data Insights Center of Excellence のエンタープライズ ソリューション設計者です。 この記事では、Azure Machine Learning Studio を使用して、クラウド ベースのソリューションに既存の回帰分析スイートを移行する彼らの実験について説明します。

## <a name="goal"></a>目標

次の 2 つの目標を念頭においてプロジェクトを開始しました。 

1. 予測分析を使用して、組織の毎月の収益予測の精度を向上させる 
2. Azure Machine Learning Studio を使用して、結果の確認、最適化、高速化、拡張を行う 

多くの企業と同様に、組織は毎月の収益予測プロセスを通過しています。 小さなビジネス アナリスト チームが、Azure Machine Learning Studio を使用してプロセスをサポートし、予測の精度を向上させる任務に取り組みました。 チームは、数か月かけて複数のソースからデータを収集し、サービスの売上予測に関連するキーとなる属性を識別する統計分析を使用してデータ属性を実行しました。 次のステップは、Excel のデータで統計的回帰モデルのプロトタイプ作成を開始することでした。 数週間で、現在のフィールドや財務の予測プロセスを上回る Excel の回帰モデルを作成しました。 これが予測結果のベースラインになりました。 

この予測分析を Studio に推し進めて、Studio の予測パフォーマンスを向上させる方法を見つけるため、次の一歩を踏み出しました。

## <a name="achieving-predictive-performance-parity"></a>予測パフォーマンスのパリティの成功
最初の優先順位は、Studio と Excel の回帰モデル間でパリティを成功させることでした。 同じデータを使用して、トレーニング データとテスト データに均等に分割し、Excel と Studio 間で予測パフォーマンスのパリティを成功させようとしました。 最初は失敗しました。 Excel モデルは、Studio モデルよりもパフォーマンスが優れていました。 失敗の原因は、Studio のベース ツール設定の知識不足でした。 Studio 製品チームと協力することで、データ セットに必要な基本設定をよく理解し、2 つのモデル間のパリティに成功しました。 

### <a name="create-regression-model-in-excel"></a>Excel で回帰モデルを作成する
Excel 回帰は、Excel Analysis ToolPak で見つかった標準的な線形回帰モデルを使用しました。 

*平均絶対 % 誤差* を計算し、これをモデルのパフォーマンス測定に使用しました。 Excel を使用して作業モデルに到着するのに 3 か月かかりました。 多くの学習を Studio の実験に取り込み、これが最終的に要件の理解に役立ちました。

### <a name="create-comparable-experiment-in-studio"></a>Studio で同等の実験を作成する
次の手順に従って、Studio で実験を作成しました。 

1. データセットを csv ファイルとして Studio にアップロードしました (非常に小さいファイル)
2. Excel で使用したのと同じデータ特徴を選ぶために、新しい実験を作成し、[データセット内の列の選択][select-columns]モジュールを使用しました 
3. [データ分割][split]モジュール ("*相対式*" モードで) を使用して、Excel で実行されたのと同じトレーニング データセットにデータを分割しました 
4. [線形回帰][linear-regression]モジュール (既定のオプションのみ) で実験、文書化し、Excel の回帰モデルと結果を比較しました

### <a name="review-initial-results"></a>最初の結果を確認する
最初は、Excel モデルが Studio モデルよりも明らかにパフォーマンスが優れていました。 

|  | Excel | スタジオ |
| --- |:---:|:---:|
| パフォーマンス | | |
| <ul style="list-style-type: none;"><li>自由度調整済み決定係数</li></ul> |0.96 |該当なし |
| <ul style="list-style-type: none;"><li>決定 <br />係数</li></ul> |該当なし |0.78<br />(低精度) |
| 平均絶対誤差 |$9.5M |$ 19.4M |
| 平均絶対誤差 (%) |6.03% |12.2% |

Machine Learning チームの開発者とデータ サイエンティストによってプロセスと結果が実行されると、有益な情報がすぐに提供されました。 

* Studio で[線形回帰][linear-regression]モジュールを使用する場合は、次の 2 つの方法が用意されています。
  * オンライン勾配降下:大規模な問題に適しています。
  * 通常の最小二乗法:これは、線形回帰と聞くと多くの人が考える方法です。 小さなデータセットの場合は、通常の最小二乗法が最適な選択肢です。
* パフォーマンスを向上させるために、L2 正則化重みパラメーターを調整することを検討します。 既定では 0.001 に設定されていますが、小さなデータ セットでは、パフォーマンスを向上させるために 0.005 に設定しました。 

### <a name="mystery-solved"></a>問題の解決
Recommendations を適用した結果、Studio で Excel と同等のベースライン パフォーマンスを達成しました。 

|  | Excel | Studio (初期値) | 最小二乗法を使用した Studio |
| --- |:---:|:---:|:---:|
| ラベル付きの値 |実際の数値 |同じ |同じ |
| 学習者 |Excel -> データ分析 -> 回帰 |線形回帰。 |線形回帰 |
| 学習者のオプション |該当なし |既定値 |最小二乗法<br />L2 = 0.005 |
| データ セット |26 行、3 特徴、1 ラベル。 すべて数値。 |同じ |同じ |
| Split:トレーニング |Excel は最初の 18 行でトレーニングを行い、最後の 8 行でテストしました。 |同じ |同じ |
| Split:テスト |最後の 8 行に Excel の回帰式を適用しました |同じ |同じ |
| **パフォーマンス** | | | |
| 自由度調整済み決定係数 |0.96 |該当なし | |
| 決定係数 |該当なし |0.78 |0.952049 |
| 平均絶対誤差 |$9.5M |$ 19.4M |$9.5M |
| 平均絶対誤差 (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

さらに、Excel の係数を Azure のトレーニング済みモデルの特徴の重みと十分に比較しました。

|  | Excel の係数 | Azure の特徴の重み |
| --- |:---:|:---:|
| 切片/偏り |19470209.88 |19328500 |
| 特徴 A |0.832653063 |0.834156 |
| 特徴 B |11071967.08 |11007300 |
| 特徴 C |25383318.09 |25140800 |

## <a name="next-steps"></a>次の手順
Excel 内で Machine Learning Web サービスを使用したいと考えました。 ビジネス アナリストは Excel に依存しているため、Excel データの行で Machine Learning Web サービスを呼び出し、予測値を Excel に返す方法が必要でした。 

また、Studio で使用可能なオプションとアルゴリズムを使用して、モデルを最適化したいと考えました。

### <a name="integration-with-excel"></a>Excel との統合
ソリューションは、トレーニング済みのモデルから Web サービスを作成することで、Machine Learning の回帰モデルを運用することでした。 数分以内に、Web サービスを作成し、これを Excel から直接呼び出して予測収益値を返すことができます。 

*Web サービスのダッシュボード* のセクションには、ダウンロード可能な Excel ブックが含まれています。 ブックには、Web サービス API とスキーマ情報が埋め込まれて事前に書式設定されています。 *[Download Excel Workbook (Excel ブックのダウンロード)]* をクリックすると、Excel ブックが開き、ローカル コンピューターに保存できます。 

![Web サービスのダッシュボードから Excel ブックをダウンロードする](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

以下に示すように、ブックを開いた状態で、定義済みのパラメーターを青色の [パラメーター] セクションにコピーします。 パラメーターが入力されると、Excel は Machine Learning Web サービスを呼び出し、予測されたスコア付けラベルが緑色の [予測値] セクションに表示されます。 ブックは、[パラメーター] に入力されたすべての行項目に対して、トレーニング済みのモデルに基づいてパラメーターの予測を作成していきます。 この機能を使用する方法の詳細については、「 [Excel からの Azure Machine Learning Web サービスの使用](consuming-from-excel.md)」をご覧ください。 

![デプロイ済みの Web サービスに接続されているテンプレートの Excel ブック](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>最適化と今後の実験
これまで、Excel モデルを使用してベースラインを構築し、Machine Learning 線形回帰モデルの最適化を進めてきました。 [フィルターに基づく機能の選択][filter-based-feature-selection]モジュールを使用して初期データ要素の選択を改善することで、平均絶対誤差 4.6% のパフォーマンス向上の達成を実現しました。 今後のプロジェクトのために、データ属性に反復処理を行う数週間を節約できる可能性があるこの特徴を使用して、モデリングに使用する正しい特徴セットを検索します。 

次は、パフォーマンスを比較するために、[ベイジアン][bayesian-linear-regression] or [Boosted Decision Trees][boosted-decision-tree-regression] などのアルゴリズムを実験に含める予定です。 

回帰を試してみる場合に適切なデータセットは、多くの数値属性を持つエネルギー効率回帰のサンプル データセットです。 このデータセットは、Studio でサンプル データセットの一部として提供されています。 さまざまな学習モジュールを使用して、暖房負荷または冷房負荷のいずれかを予測できます。 次の表は、さまざまな回帰のパフォーマンスを比較したもので、エネルギー効率データセットに対する、ターゲット変数の冷房負荷の予測を示しています。 

| モデル | 平均絶対誤差 | 二乗平均平方根誤差 | 相対絶対誤差 | 相対二乗誤差 | 決定係数 |
| --- | --- | --- | --- | --- | --- |
| ブースト デシジョン ツリー |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| 線形回帰 (勾配降下) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| ニューラル ネットワーク回帰 |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| 線形回帰 (通常の最小二乗法) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>重要なポイント
Excel の回帰と Studio の実験を並列して実行することで、多くのことを学びました。 Excel でベースライン モデルを作成し、これを Machine Learning の[線形回帰][linear-regression]を使用してモデルと比較することは、Studio の学習に役立ち、データの選択とモデルのパフォーマンスを向上させることがわかりました。 

また、今後の予測プロジェクトを促進するために、[フィルターに基づく特徴選択][filter-based-feature-selection]の使用が望ましいことがわかりました。 データに特徴選択を適用することで、Studio で全体のパフォーマンスがさらに向上したモデルを作成できます。 

予測分析の予測を Studio から Excel に体系的に転送する機能を使用することで、さまざまなビジネス ユーザーに正常に結果を提供できる機能が大幅に向上します。 

## <a name="resources"></a>リソース
次のリソースは、回帰を使用する際に役立ちます。 

* Excel での回帰。 Excel で回帰を試したことがない場合は、このチュートリアル ([https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)) を利用すれば、簡単にできます。
* 回帰と予測。 Tyler Chessman 氏による、初心者向けの優れた線形回帰の説明が書かれた、Excel で時系列予測を実行する方法を説明するブログ記事。 [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* 通常の最小二乗法の線形回帰:欠点、問題、注意点。 回帰の概要とディスカッションについては、[https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/) を参照してください。

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

