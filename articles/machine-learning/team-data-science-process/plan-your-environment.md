---
title: シナリオを特定し分析プロセスを計画する - Team Data Science Process | Azure Machine Learning
description: 一連の重要な確認事項を考慮することにより、高度な分析データ処理のためのシナリオとプランを特定します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322388"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>高度な分析データ処理のためのシナリオとプランを特定する方法

データセットで高度な分析処理を実行できる環境を作成するには、どのようなリソースが必要でしょうか。 この記事では、シナリオに関連するタスクおよびリソースの特定に役立つ一連の確認事項について説明しています。

予測分析の大まかな手順については、「[Team Data Science Process とは](overview.md)」をご覧ください。 各ステップでは、特定のシナリオに関連するタスクのための固有のリソースが必要になります。

シナリオを特定する次の領域における重要な質問に回答します。

* データ ロジスティック
* データの特性
* データセットの品質
* 推奨されるツールと言語

## <a name="logistic-questions-data-locations-and-movement"></a>ロジスティックの確認事項: データの場所と移動

ロジスティックの確認事項では次の項目がカバーされています。

* データ ソースの場所
* Azure でのターゲット移動先
* スケジュール、量、および関連するリソースを含む、データ移動の要件

データの移動は、分析プロセス中に何度も必要になる場合があります。 一般的なシナリオでは、ローカル データを Azure 上のなんらかの形式のストレージに移動し、次に Machine Learning Studio に移動します。

### <a name="what-is-your-data-source"></a>データ ソースは何ですか。

データはローカルとクラウドのどちらにありますか。 以下の場所が可能です。

* 公開されている HTTP アドレス
* ローカルまたはネットワーク ファイルの場所
* SQL Server データベース
* Azure Storage コンテナー

### <a name="what-is-the-azure-destination"></a>Azure の移動先は何ですか。

処理またはモデリングをするためには、データはどこである必要があるでしょうか。 

* Azure Blob Storage
* SQL Azure データベース
* Azure VM 上の SQL Server
* HDInsight (Azure 上の Hadoop) または Hive テーブル
* Azure Machine Learning
* マウント可能な Azure 仮想ハード ディスク

### <a name="how-are-you-going-to-move-the-data"></a>データをどのように移動しますか。

データをさまざまなストレージや処理環境に取り込んだり読み込んだりするために使用する手順とリソースについては、以下をご覧ください。

* [分析用のストレージ環境にデータを読み込む](ingest-data.md)
* [さまざまなデータ ソースから Azure Machine Learning Studio (クラシック) にトレーニング データをインポートする](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>データを定期的なスケジュールで移動したり移行中に変更したりする必要がありますか。

データを継続的に移行する必要がある場合は、Azure Data Factory (ADF) の使用を検討します。 ADF は次の場合に役立ちます。

* オンプレミスとクラウド両方のリソースが関連するハイブリッド シナリオ
* 移行の過程でビジネス ロジックによってデータがトランザクション処理、修正、または変更されるシナリオ

詳細については、「[Azure Data Factory を使用して SQL Server データベースから SQL Azure にデータを移動する](move-sql-azure-adf.md)」を参照してください。

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>どのくらいの量のデータが Azure に移動されますか。

大きいデータセットの場合、特定の環境のストレージ容量を超える可能性があります。 たとえば、次のセクションで、Machine Learning Studio (クラシック) のサイズ制限の説明を参照してください。 このような場合、分析中はデータのサンプルを使用できます。 さまざまな Azure 環境でデータセットをダウンサンプリングする方法の詳細については、 [Team Data Science Process のデータのサンプリング](sample-data.md)に関するページをご覧ください。

## <a name="data-characteristics-questions-type-format-and-size"></a>データの特性の確認事項: 型、形式、およびサイズ

これらの確認事項は、ストレージと処理環境の計画に重要です。 デーの種類に適切なシナリオを選択し、制限を理解するのに役立ちます。

### <a name="what-are-the-data-types"></a>データ型とは

* 数値
* Categorical
* 文字列
* Binary

### <a name="how-is-your-data-formatted"></a>データはどのような形式か?

* コンマ区切り (CSV) またはタブ区切り (TSV) のフラット ファイル
* 圧縮されているまたは圧縮されていない
* Azure BLOB
* Hadoop Hive テーブル
* SQL Server テーブル

### <a name="how-large-is-your-data"></a>データのサイズはどの程度か?

* 小さい:2 GB 未満
* 中程度:2 GB から 10 GB
* 大きい:10 GB 超

たとえば Azure Machine Learning Studio (クラシック) 環境を使用します。

* Azure Machine Learning Studio でサポートされているデータ形式および型の一覧については、「 [サポートされるデータ形式とデータ型](../classic/import-data.md#supported-data-formats-and-data-types) 」セクションを参照してください。
* 分析プロセスで使用されるその他の Azure サービスの制限事項については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>データ品質の確認事項: 探索と前処理

### <a name="what-do-you-know-about-your-data"></a>データについて、何を知っていますか。

データに関する基本的な特性を理解します。

* 示すパターンや傾向
* 含まれる外れ値
* 欠損値の数

このステップは次のために重要です。

* 必要な前処理の量を決定する
* 分析の最も適切な機能や種類を提案する仮説を作成する
* 追加のデータ コレクションの計画を作成する

データ検査の便利な技法としては、説明的な統計の計算や、視覚化のためのプロットなどがあります。 さまざまな Azure 環境でデータセットを探索する方法の詳細については、「 [Team Data Science Process のデータを探索する](explore-data.md)」を参照してください。

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>データに前処理やクリーニングは必要ですか。

機械学習にデータセットを効果的に使用する前に、データの前処理とクリーンアップが必要な場合があります。 生データはノイズが含まれ、信頼性が低いことがよくあります。 欠損値がある可能性があります。 このようなデータを使用してモデリングを行うと、誤解を招く結果が生成されることがあります。 詳細については、「 [機械学習を強化するためのデータを準備するタスク](prepare-data.md)」を参照してください。

## <a name="tools-and-languages-questions"></a>ツールと言語の確認事項

言語、開発環境、ツールには多くのオプションがあります。 ニーズと優先設定を把握します。

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>分析にはどの言語を使用しますか。

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>データ分析にはどのツールを使用する必要がありますか。

* [Microsoft Azure PowerShell](/powershell/azure/) - スクリプト言語で Azure リソースを管理するために使用されるスクリプト言語
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter notebooks](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>高度な分析シナリオの特定

前のセクションの質問に答えたら、現在の状況に最適なシナリオを決定する準備が整います。 サンプル シナリオは、「 [Azure Machine Learning の Advanced Analytics Process and Technology 用のシナリオ](plan-sample-scenarios.md)」に記載されています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Team Data Science Process (TDSP) とは](overview.md)