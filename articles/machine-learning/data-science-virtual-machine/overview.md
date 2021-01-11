---
title: Azure Data Science Virtual Machine とは
titleSuffix: Azure Data Science Virtual Machine
description: データ サイエンスを実行するためのツールとライブラリがプレインストール、構成されている、Azure クラウド プラットフォームで簡単に利用できる仮想マシン、Azure Data Science Virtual Machine の概要を紹介します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816339"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用の Azure Data Science Virtual Machine とは

Data Science Virtual Machine (DSVM) とは、データ サイエンス専用に構築された Azure クラウド プラットフォーム上のカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。

DSVM を利用できる OS:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Azure Machine Learning との比較

DSVM がデータ サイエンス向けにカスタマイズされた VM イメージであるのに対し、[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (AzureML) は、次の機能を含んだエンド ツー エンドのプラットフォームです。

+ フル マネージドのコンピューティング
  + コンピューティング インスタンス
  + 分散 ML タスク用のコンピューティング クラスター
  + リアルタイム スコアリング用の推論クラスター
+ データストア (例: Blob、ADLS Gen2、SQL DB)
+ 実験の追跡
+ モデル管理
+ ノートブック
+ 環境 (Conda と R の依存関係の管理)
+ ラベル付け
+ パイプライン (エンド ツー エンドのデータ サイエンス ワークフローの自動化)

### <a name="comparison-with-azureml-compute-instances"></a>AzureML コンピューティング インスタンスとの比較

[Azure Machine Learning コンピューティング インスタンス](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance)がフル構成の __マネージド__ VM イメージであるのに対し、DSVM は __アンマネージド__ VM です。

2 つの製品の主な違いは次のとおりです。


|特徴量 |データ サイエンス<br>VM |AzureML<br>コンピューティング インスタンス  | 
|---------|---------|---------|
| フル マネージド | いいえ        | はい        |
|言語サポート     |  Python、R、Julia、SQL、C#、<br> Java、Node.js、F#       | Python と R        |
|オペレーティング システム     | Ubuntu<br>Windows         |    Ubuntu     |
|事前構成済みの GPU オプション     |  はい       |    はい     |
|スケールアップ オプション | はい | はい |
|SSH アクセス    | はい        |    はい     |
|RDP アクセス    | はい        |     いいえ    |
|組み込み<br>ホスト ノートブック     |   いいえ<br>(別途構成が必要)      |      はい   |
|組み込み SSO     | いいえ <br>(別途構成が必要)         |    はい     |
|組み込みコラボレーション     | いいえ         | はい        |
|プレインストールされているツール     |  Jupyter(lab)、RStudio Server、VSCode、<br> Visual Studio、PyCharm、Juno、<br>Power BI Desktop、SSMS、 <br>Microsoft Office 365、Apache Drill       |     Jupyter(lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>サンプル ユース ケース

以下では、DSVM のお客様の一般的な使用例をいくつか紹介します。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価

DSVM を使用すると、特に公開されている[サンプルとチュートリアル](./dsvm-samples-and-walkthroughs.md)のいくつかを試すことで、新しいデータ サイエンス [ツール](./tools-included.md)を評価または学習できます。

### <a name="deep-learning-with-gpus"></a>GPU を使用したディープ ラーニング

DSVM では、トレーニング モデルはグラフィックス処理装置 (GPU) ベースのハードウェア上でディープ ラーニング アルゴリズムを使用できます。 Azure プラットフォームの VM スケーリング機能を利用することにより、DSVM はニーズに応じてクラウドで GPU ベースのハードウェアを使用するのに役立ちます。 大規模モデルをトレーニングするときや、同じ OS ディスクを保持しつつ高速計算が必要なときに、GPU ベースの VM に切り替えることができます。 DSVM を使用して、N シリーズ GPU 対応の任意の仮想マシン SKU を選択できます。 Azure 無料アカウントでは、GPU 対応の仮想マシン SKU がサポートされないので注意してください。

Windows エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング フレームワークが事前インストールされています。 Linux エディションでは、GPU のディープ ラーニングは、Ubuntu の DSVM で有効になります。 

Ubuntu または Windows エディションの DSVM を、GPU ベースでない Azure 仮想マシンにデプロイすることもできます。 この場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックはします。

[使用可能なディープ ラーニングと AI のフレームワークの詳細について学習](dsvm-tools-deep-learning-frameworks.md)してください。

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育

データ サイエンスのクラスを教える企業のトレーナーや教育担当者は、通常、仮想マシン イメージを提供します。 そのイメージにより、受講者が一貫したセットアップを行い、サンプルが予想どおりに動作するようにすることができます。

DSVM は、一貫したセットアップでオンデマンドの環境を作成するため、サポートと非互換性の問題を軽減できます。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。


## <a name="whats-included-on-the-dsvm"></a>DSVM に含まれているもの

Windows と Linux の両方の DSVM 上で使用できるツールの完全な一覧については、[こちら](tools-included.md)を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

+ Windows:
  + [Windows DSVM を設定する](provision-vm.md)
  + [Windows DSVM のデータ サイエンス](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM を設定する (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM のデータ サイエンス](linux-dsvm-walkthrough.md)
