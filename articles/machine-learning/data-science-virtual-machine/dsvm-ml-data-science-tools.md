---
title: "機械学習ツールとデータ サイエンス ツール - Azure | Microsoft Docs"
description: "機械学習ツールとデータ サイエンス ツール"
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
ms.openlocfilehash: c772fa16b94d09e0fc0450ce86503553c26f8a24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-and-data-science-tools"></a>機械学習ツールとデータ サイエンス ツール
データ サイエンス仮想マシン (DSVM) には、Python、R、Julia などの一般的な言語で使用可能な、機械学習用のツールおよびライブラリの豊富なセットがあります。 

ここでは、DSVM の機械学習ツールおよびライブラリの一部を示します。 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 紹介   |    Python、R、Java、Scala、C++、その他言語用の、高速で、可搬性があり、分散型の勾配ブースティング (GBDT、GBRT、または GBM) ライブラリ。 1 台のコンピューター、Hadoop、Spark で動作します    |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | 一般的な ML ライブラリ      |
| DSVM での構成/インストール方法      |  GPU サポートと共にインストールされます   |
| 使用/実行方法      | Python ライブラリ (2.7 および 3.5)、R パッケージ、パスの通ったコマンド ライン ツール (Windows は `C:\dsvm\tools\xgboost\bin\xgboost.exe`、Linux は `/dsvm/tools/xgboost/xgboost`)    |
| サンプルへのリンク      | サンプルは VM 上に含まれています (Linux は `/dsvm/tools/xgboost/demo`、Windows は `C:\dsvm\tools\xgboost\demo`)   |
| DSVM 上の関連ツール      | LightGBM、MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 紹介   |   Vowpal Wabbit ("VW" とも呼ばれます) は、オープン ソースの高速 out-of-core 学習システム ライブラリです    |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | 一般的な ML ライブラリ      |
| DSVM での構成/インストール方法      |  Windows - msi installer、Linux - apt-get |
| 使用/実行方法      | パスの通ったコマンド ライン ツール (Windows は `C:\Program Files\VowpalWabbit\vw.exe`、Linux は `/usr/bin/vw`)    |
| サンプルへのリンク      | [VowPal Wabbit サンプル](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| DSVM 上の関連ツール      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 紹介   |  Weka は、データ マイニング タスクのための機械学習アルゴリズムのコレクションです。 アルゴリズムはデータセットに直接適用するか、または独自の Java コードから呼び出すことができます。 Weka には、データの前処理、分類、回帰、クラスタリング、アソシエーション ルール、および視覚化のためのツールが含まれています。 |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | 一般的な ML ツール     |
| 使用/実行方法      | Windows では、[スタート] メニューから Weka を探します。 Linux では、X2Go でログインした後、[Applications] (アプリケーション) -> [Development] (開発) -> [Weka] にアクセスします。 |
| サンプルへのリンク      | [Weka サンプル](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM 上の関連ツール      |LightGBM、Rattle、XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 紹介   |   R を使用したデータ マイニングのためのグラフィカル ユーザー インターフェイス   |
| サポートされている DSVM エディション     | Windows、Linux     |
| 一般的な用途      | R 用の一般的な UI データ マイニング ツール    |
| 使用/実行方法      | UI ツール。 Windows では、コマンド プロンプトを起動して R を実行した後、R 内で `rattle()` を実行します。 Linux では、X2Go を使用して接続し、ターミナルを起動して R を実行した後、R 内で `rattle()` を実行します。 |
| サンプルへのリンク      | [Rattle](https://togaware.com/onepager/) |
| DSVM 上の関連ツール      |LightGBM、Weka、XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| 紹介   | デシジョン ツリー アルゴリズムに基づいた、高速、分散型、高パフォーマンスの勾配ブースティング (GBDT、GBRT、GBM、または MART) フレームワークであり、順位付け、分類、その他多くの機械学習タスクに使用されます。    |
| サポートされている DSVM バージョン      | Windows、Linux    |
| 一般的な用途      | 汎用の勾配ブースティング フレームワーク      |
| DSVM での構成/インストール方法      | Windows では、LightGBM は Python パッケージとしてインストールされます。 Linux では、コマンド ラインの実行可能ファイルは `/opt/LightGBM/lightgbm` にあり、R パッケージがインストールされ、Python パッケージがインストールされます。     |
| サンプルへのリンク      | [LightGBM Guide (LightGBM ガイド)](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| DSVM 上の関連ツール      | MXNet、XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| 紹介   | インメモリ、分散型、高速、かつスケーラブルな機械学習をサポートするオープン ソースの AI プラットフォーム  |
| サポートされている DSVM バージョン      | Linux   |
| 一般的な用途      | 汎用の分散型スケーラブル ML   |
| DSVM での構成/インストール方法      | H2O は `/dsvm/tools/h2o` にインストールされます。      |
| 使用/実行方法      | X2Go を使用して VM に接続します。 新しいターミナルを起動し、`java -jar /dsvm/tools/h2o/current/h2o.jar` を実行します。 次に、Web ブラウザーを起動して `http://localhost:54321` に接続します。      |
| サンプルへのリンク      | Jupyter の VM で、サンプルは `h2o` ディレクトリにあります。      |
| DSVM 上の関連ツール      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

DSVM にインストールされる Anaconda Python ディストリビューションの一部として付属する人気の `scikit-learn` パッケージなど、その他多数の ML ライブラリが DSVM には存在します。 必ず、それぞれのパッケージ マネージャーを実行して、Python、R、および Julia で利用可能なパッケージの一覧を確認してください。 
