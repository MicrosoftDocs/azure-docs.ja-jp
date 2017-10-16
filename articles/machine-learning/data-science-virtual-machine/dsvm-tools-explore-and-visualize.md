---
title: "データの探索と視覚化のツール - Azure | Microsoft Docs"
description: "データ サイエンス仮想マシン用の、データの探索と視覚化のツール。"
keywords: "データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: d8f0616f17dbaa02082492cc1c68f1d989ea5aae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>データ サイエンス仮想マシンの、データの探索と視覚化のツール。

データ サイエンスの重要なステップは、データを理解することです。 視覚化およびデータ探索ツールは、データ理解の加速に役立ちます。 このキー ステップを促進する、DSVM で提供されるツールの一部をここで示します。 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 紹介   | ビッグ データに対するオープン ソースの SQL クエリ エンジン    |
| サポートされている DSVM バージョン      | Windows、Linux  |
| DSVM での構成/インストール方法      |  埋め込みモードのみで `/dsvm/tools/drill*` にインストールされます   |
| 一般的な用途      |  ETL を必要としない In-situ データ探索。 CSV、JSON、リレーショナル テーブル、Hadoop などのさまざまなデータ ソースおよびフォーマットをクエリします     |
| 使用/実行方法      | デスクトップ ショートカット  <br/> [10 分で Drill を使ってみる](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上の関連ツール      |   Rattle、Weka、SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 紹介   |  Weka は、データ マイニング タスクのための機械学習アルゴリズムのコレクションです。 アルゴリズムはデータセットに直接適用するか、または独自の Java コードから呼び出すことができます。 Weka には、データの前処理、分類、回帰、クラスタリング、アソシエーション ルール、および視覚化のためのツールが含まれています。 |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | 一般的な ML ツール     |
| 使用/実行方法      | Windows では、[スタート] メニューから Weka を探します。 Linux では、X2Go でログインした後、[Applications] (アプリケーション) -> [Development] (開発) -> [Weka] にアクセスします。 |
| サンプルへのリンク      | [Weka サンプル](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 上の関連ツール      |LightGBM、Rattle、Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 紹介   |   R を使用したデータ マイニングのためのグラフィカル ユーザー インターフェイス   |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | R 用の一般的な UI データ マイニング ツール    |
| 使用/実行方法      | UI ツール。 Windows では、コマンド プロンプトを起動して R を実行した後、R 内で `rattle()` を実行します。 Linux では、X2Go を使用して接続し、ターミナルを起動して R を実行した後、R 内で `rattle()` を実行します。 |
| サンプルへのリンク      | [Rattle](https://togaware.com/onepager/) |
| DSVM 上の関連ツール      |LightGBM、Weka、Xgboost   |

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| 紹介   | 対話型データ視覚化と BI ツール    |
| サポートされている DSVM バージョン      | Windows  |
| 一般的な用途      |  データ視覚化とダッシュボード構築   |
| 使用/実行方法      | デスクトップ ショートカット (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| DSVM 上の関連ツール      |   Visual Studio 2017、Visual Studio Code、Juno      |

