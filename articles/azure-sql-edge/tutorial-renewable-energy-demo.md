---
title: Contoso 風力発電所のタービンに Azure SQL Edge をデプロイする
description: このチュートリアルでは、Azure SQL Edge を使用して、Contoso 風力発電所のタービンの後流を検出します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723475"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Azure SQL Edge を使用してよりスマートな再生可能リソースを構築する

この Azure SQL Edge のデモは、Contoso Renewable Energy に基づいています。これは、発電機に搭載されたデータ処理機能に SQL DB Edge を使用する風力タービン ファームです。 

このデモでは、デバイスで風の乱流が検出されたために発生したアラートを解決する方法について説明します。 モデルをトレーニングして SQL DB Edge にデプロイし、検出された風の後流を修正して、最終的に出力を最適化します。

Azure SQL Edge - Channel 9 の再生可能エネルギー デモ ビデオ:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>ローカル コンピューターでデモを設定する
Git を使用して、デモのすべてのファイルをローカル コンピューターにコピーします。 

1. [こちら](https://git-scm.com/download)から Git をインストールします。
2. コマンド プロンプトを開き、リポジトリをダウンロードするフォルダーに移動します。 
3. https://github.com/microsoft/sql-server-samples.git コマンドを実行します。
4. リポジトリがクローンされている場所で、**sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo** に移動します。
5. README.md の指示に従ってデモ環境を設定し、デモを実行します。