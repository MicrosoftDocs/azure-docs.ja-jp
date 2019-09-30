---
title: データの探索と視覚化のツール
titleSuffix: Azure Data Science Virtual Machine
description: データ サイエンス仮想マシン用の、データの探索と視覚化のツール。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191966"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine のデータの探索と視覚化のツール

データ サイエンスで最も重要なことは、データを理解することです。 視覚化およびデータ探索ツールは、データ理解の加速に役立ちます。 Data Science Virtual Machine (DSVM) に用意されている次のツールを使用すると、この重要な手順を簡単に行うことができます。

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 紹介   | ビッグ データに対するオープン ソースの SQL クエリ エンジン    |
| サポートされている DSVM バージョン      | Windows、Linux  |
| DSVM での構成とインストール方法      |  埋め込みモードのみで `/dsvm/tools/drill*` にインストールされます   |
| 標準的な使用      |  抽出、変換、読み込み (ETL) を必要としない、インプレース データ探索用。 CSV、JSON、リレーショナル テーブル、および Hadoop などのさまざまなデータ ソースとフォーマットのクエリ。     |
| 使用と実行方法      | デスクトップ ショートカット  <br/> [10 分で Drill を使ってみる](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上の関連ツール      |   Rattle、Weka、SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 紹介   |  データ マイニング タスクのための機械学習アルゴリズムのコレクション。 これらのアルゴリズムをデータセットに直接適用するか、独自の Java コードから呼び出すことができます。 Weka には、データの前処理、分類、回帰、クラスタリング、アソシエーション ルール、および視覚化のためのツールが含まれています。 |
| サポートされている DSVM エディション     | Windows、Linux     |
| 標準的な使用      | 一般的な機械学習ツール     |
| 使用と実行方法      | Windows では、[スタート] メニューで Weka を探します。 Linux では、X2Go でサインインし、[アプリケーション] > [開発] > [Weka] に移動します。 |
| サンプルへのリンク      | [Weka サンプル](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 上の関連ツール      |LightGBM、Rattle、Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 紹介   |   R を使用したデータ マイニングのためのグラフィカル ユーザー インターフェイス (GUI)   |
| サポートされている DSVM エディション     | Windows、Linux     |
| 標準的な使用      | R 用の一般的な UI データ マイニング ツール    |
| 使用と実行方法      | UI ツール。 Windows では、コマンド プロンプトを開き、R を実行してから、R 内で `rattle()` を実行します。 Linux では、X2Go で接続し、ターミナルを起動し、R を実行してから、R 内で `rattle()` を実行します。 |
| サンプルへのリンク      | [Rattle](https://togaware.com/onepager/) |
| DSVM 上の関連ツール      |LightGBM、Weka、Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| 紹介   | 対話型データ視覚化と BI ツール    |
| サポートされている DSVM バージョン      | Windows  |
| 標準的な使用      |  データ視覚化とダッシュボード構築   |
| 使用と実行方法      | デスクトップ ショートカット (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 上の関連ツール      |   Visual Studio 2019、Visual Studio Code、Juno      |

