---
title: "シナリオを特定し分析プロセスを計画する - Azure | Microsoft Docs"
description: "一連の重要な確認事項を考慮することにより、高度な分析を計画します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: d11f023f263b0bb504e2ecb0ff69bb0cc726618b
ms.lasthandoff: 01/24/2017


---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>高度な分析データ処理のためのシナリオとプランを特定する方法
データセットに対する高度な分析プロセスを実行する環境をセットアップしている場合、どのようなリソースを含めるように計画するべきでしょうか? この記事では、シナリオに関連するタスクおよびリソースの特定に役立つ一連の確認事項について説明しています。 予測分析の大まかな手順は、「 [Team Data Science Process (TDSP) について](data-science-process-overview.md)」に記載されています。 それらの各手順で、特定のシナリオに関連するタスクのための固有のリソースが必要になります。 シナリオを特定するための主要な確認事項は、データ ロジスティック、特性、データセットの品質、分析で使用するツールや言語などに関連しています。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>ロジスティックの確認事項: データの場所と移動
ロジスティックの確認事項は、**データ ソース**の場所、Azure 内の**宛先**、データ移動の要件 (スケジュール、量、関与するリソースなど) に関連しています。 データの移動は、分析プロセス中に何度も必要になる場合があります。 一般的なシナリオでは、ローカル データを Azure 上のなんらかの形式のストレージに移動し、次に Machine Learning Studio に移動します。

1. **データ ソースは何ですか。** それはローカルとクラウドのどちらにありますか。 次に例を示します。
   
   * HTTP アドレスに公開されていて、入手できるデータ。
   * ローカル/ネットワーク上のファイル位置に存在するデータ。
   * SQL Server データベースに格納されているデータ。
   * Azure ストレージ コンテナーに格納されているデータ。
2. **Azure の送信先は何ですか。** 処理またはモデリングをするためには、どこである必要があるでしょうか。 次に例を示します。
   
   * Azure Blob Storage
   * SQL Azure データベース
   * Azure VM 上の SQL Server
   * HDInsight (Azure 上の Hadoop) または Hive テーブル
   * Azure Machine Learning
   * マウント可能な Azure 仮想ハード ディスク
3. **データをどのように移動しますか。** データをさまざまなストレージや処理環境に取り込んだり読み込んだりするために使用できる手順とリソースが、以下の各トピックで説明されています。
   
   * [分析用のストレージ環境にデータを読み込む](machine-learning-data-science-ingest-data.md)
   * [さまざまなデータ ソースから Azure Machine Learning Studio にトレーニング データをインポートする](machine-learning-data-science-import-data.md)
4. **データを定期的なスケジュールで移動したり移行中に変更したりする必要がありますか。** 特にオンプレミスとクラウドの両方のリソースにアクセスするハイブリッド シナリオに関連して、継続的にデータを移行する必要がある場合、および移行の過程でデータを処理する場合や、データに変更を加えたりビジネス ロジックを付加したりする必要がある場合には、Azure Data Factory (ADF) の使用を検討してください。 詳細については、「 [Azure Data Factory を使用してオンプレミスの SQL Server から SQL Azure にデータを移動する](machine-learning-data-science-move-sql-azure-adf.md)
5. **どのくらいの量のデータが Azure に移動されますか。** 非常に大きいデータセットの場合、特定の環境のストレージ容量を超える可能性があります。 たとえば、次のセクションで、Machine Learning Studio のサイズ制限の説明を参照してください。 このような場合、分析中はデータのサンプルを使用できます。 さまざまな Azure 環境でデータセットをダウンサンプリングする方法の詳細については、 [Team Data Science Process のデータのサンプリング](machine-learning-data-science-sample-data.md)に関するページをご覧ください。

## <a name="data-characteristics-questions-type-format-and-size"></a>データの特性の確認事項: 型、形式、およびサイズ
これらの確認事項は、ストレージと処理環境を計画するうえで重要です。各ストレージと処理環境には、適切なデータの種類があり、特定の制限があります。

1. **データ型は何ですか。** 次に例を示します。
   
   * 数値
   * カテゴリ
   * 文字列
   * Binary
2. **データはどのような形式か?** 次に例を示します。
   
   * コンマ区切り (CSV) またはタブ区切り (TSV) のフラット ファイル
   * 圧縮されているまたは圧縮されていない
   * Azure BLOB
   * Hadoop Hive テーブル
   * SQL Server テーブル
3. **データのサイズはどの程度か?**
   
   * 小さい: 2 GB 未満
   * 中程度: 2 GB 超、10 GB 未満
   * 大きい: 10 GB 超

Azure Machine Learning 環境を例とします。

* Azure Machine Learning Studio でサポートされているデータ形式および型の一覧については、「 [サポートされるデータ形式とデータ型](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) 」セクションを参照してください。
* Azure Machine Learning Studio のデータ制限については、「 **Machine Learning 用データのインポートとエクスポート** 」の「 [モジュールのデータ セットの大きさはどの程度まで許容されますか](machine-learning-faq.md#machine-learning-studio-questions)

分析プロセスで使用されるその他の Azure サービスの制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>データ品質の確認事項: 探索と前処理
1. **データについて、何を知っていますか。** データの基本的な特性を理解する必要がある場合は、データを探索します。 どのようなパターンや傾向を示し、どのようなはずれ値を持ち、どれくらいの値が欠落しているかを調べます。 この手順は、必要な前処理の範囲を判断したり、分析の最も適切な機能や種類を提示するような仮説を立てたり、追加のデータ収集の計画を立てたりするうえで重要です。 説明的な統計の計算や、視覚化のためのプロットは、データを検査するための便利な技法です。 さまざまな Azure 環境でデータセットを探索する方法の詳細については、「 [Team Data Science Process のデータを探索する](machine-learning-data-science-explore-data.md)」を参照してください。
2. **データに前処理やクリーニングは必要ですか。**
   データの前処理とクリーニングは、通常は、機械学習でデータセットを効果的に使用する前に行う必要がある重要なタスクです。 未加工のデータは、多くの場合、ノイズが多く、信頼性が低く、値が欠落している可能性もあります。 このようなデータを使用してモデリングを行うと、誤解を招く結果が生成されることがあります。 詳細については、「 [機械学習を強化するためのデータを準備するタスク](machine-learning-data-science-prepare-data.md)」を参照してください。

## <a name="tools-and-languages-questions"></a>ツールと言語の確認事項
どの言語および開発環境やツールが必要であるかや、使いやすいかに応じて、多くのオプションがあります。

1. **分析にはどの言語を使用しますか。**  
   
   * R
   * Python
   * SQL
2. **データ分析にはどのツールを使用する必要がありますか。**
   
   * [Microsoft Azure Powershell](/powershell/azureps-cmdlets-docs) - Azure リソースを管理するために使用されるスクリプト言語。
   * [Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools for Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter notebooks](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>高度な分析シナリオの特定
前のセクションの質問に答えたら、現在の状況に最適なシナリオを決定する準備が整います。 サンプル シナリオは、「 [Azure Machine Learning の Advanced Analytics Process and Technology 用のシナリオ](machine-learning-data-science-plan-sample-scenarios.md)」に記載されています。


