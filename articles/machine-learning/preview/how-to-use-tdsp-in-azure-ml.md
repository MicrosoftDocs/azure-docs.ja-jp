---
title: "Team Data Science Process テンプレートでのプロジェクトの体系化 | Microsoft Docs"
description: "コラボレーションのためにプロジェクトを体系化する Team Data Science Process (TDSP) テンプレートを Azure ML でインスタンス化する方法。"
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
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>Team Data Science Process テンプレートでのプロジェクトの体系化

このドキュメントでは、コラボレーションと再現性のためにプロジェクトを体系化する Team Data Science Process (TDSP) テンプレートを使用して Azure Machine Learning でデータ サイエンス プロジェクトを作成する手順について説明します。 


## <a name="what-is-team-data-science-process"></a>Team Data Science Process とは
Team Data Science Process は、高度な分析ソリューションを実行および配信するアジャイルで反復的なデータ サイエンス プロセスです。 エンタープライズ組織のデータ サイエンス チームのコラボレーションと効率を向上させるように設計されています。 次の 4 つの主要コンポーネントでこれらの目標をサポートします。

1. 標準の[データ サイエンス ライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)の定義。
2. 標準化されたプロジェクト構造の[プロジェクト ドキュメントおよびレポート テンプレート](https://github.com/Azure/Azure-TDSP-ProjectTemplate)
3. 計算とストレージのインフラストラクチャやコード リポジトリなど、プロジェクトを実行するためのインフラストラクチャとリソース。
4. 協調的なバージョン コントロールとコード レビュー、データの探索とモデリング、作業計画など、データ サイエンス プロジェクト タスク用の[ツールとユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities)。

TDSP の詳細については、[Team Data Science Process の概要](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md)に関する記事をご覧ください。

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>TDSP の構造とテンプレートを使用する理由
データ サイエンス プロジェクトの構造、ライフサイクル、ドキュメントの標準化は、データ サイエンス チームの効果的なコラボレーションを促進する鍵です。 TDSP テンプレートを使用して Azure Machine Learning プロジェクトを作成すると、調和のとれたチームワークのフレームワークが提供されます。

以前に、こうした目標の実現に役立つ [TDSP プロジェクトの構造とテンプレートの GitHub リポジトリ](https://github.com/Azure/Azure-TDSP-ProjectTemplate)をリリースしました。 しかしこれまでは、データ サイエンス ツール内で TDSP の構造とテンプレートをインスタンス化することはできませんでした。 現在は、TDSP の構造とドキュメント テンプレートをインスタンス化する Azure Machine Learning プロジェクトを作成できるようになりました。 

## <a name="things-to-note-before-creating-a-new-project"></a>新規プロジェクトを作成する "*前*" に注意すべきこと
新規プロジェクトを作成する "*前*" に注意またはレビューする必要があることを以下に示します。
* TDSP Azure Machine Learning [テンプレート](https://aka.ms/tdspamlgithubrepo)。
* コンテンツ ('docs' フォルダーにあるもの以外) のサイズは 25 Mb 未満である必要があります。 下記の「**注**」をご覧ください。
* sample\_data フォルダーは、コードのテストや開発の初期段階に使用できる小さなデータ ファイル (5 Mb 未満) 専用です。
* Office Word や PowerPoint などのファイルを格納すると、'docs' フォルダーのサイズが大幅に増加する可能性があります。 このようなファイルを格納するには、コラボレーション Wiki、[SharePoint](https://products.office.com/en-us/sharepoint/collaboration)、またはその他のコラボレーション リソースを探すことをお勧めします。
* Azure Machine Learning でのサイズの大きいファイルと出力の処理については、[こちら](http://aka.ms/aml-largefiles)をお読みください。

> [!NOTE]
> readme.md ファイルを除き、プロジェクトの実行中には使用されないすべてのドキュメント関連コンテンツ (テキスト、マークダウン、イメージ、その他のドキュメント ファイル) が 'docs' (すべて小文字) という名前のフォルダーに存在することをご確認ください。 これは Azure Machine Learning の実行で無視される特殊なフォルダーであるため、このフォルダー内のコンテンツは計算のターゲットに不必要にコピーされません。 このフォルダー内のオブジェクトは、25 MB のプロジェクト サイズ上限でもカウントされないため、たとえばドキュメントで必要となる大きなイメージ ファイルを格納することができます。 これらは実行履歴を通じて Git によって追跡されます。 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>Azure Machine Learning テンプレート ギャラリーから TDSP の構造とテンプレートをインスタンス化する
Team Data Science Process の構造とドキュメント テンプレートで新しいプロジェクトを作成するには、次の手順を実行します。 

### <a name="click-on-new-project"></a>[新しいプロジェクト] をクリックする
Azure Machine Learning を開きます。 左上の **[プロジェクト]** で、**[+]** をクリックし、**[新しいプロジェクト]** を選択して新しいプロジェクトを作成します。

![新しいプロジェクト](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>新しい TDSP 体系化プロジェクトの作成
適切なボックスで、パラメーターと情報を指定します。

- プロジェクト名
- プロジェクト ディレクトリ
- プロジェクトの説明
- 空の Git リポジトリ パス
- ワークスペース名

**[検索]** ボックスに、"*「TDSP」*" と入力します。 **[Structure a project with TDSP (TDSP 使用したプロジェクトの体系化)]** が表示されたら、クリックしてそのテンプレートを選択します。 次に、**[作成]** をクリックして、TDSP 構造を持つ新しいプロジェクトを作成します。 プロジェクトの作成中に (適切なボックスで) 空の Git リポジトリを指定した場合は、プロジェクトの作成後、そのリポジトリにプロジェクトの構造とコンテンツが設定されます。

![TDSP プロジェクトの作成](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>TDSP プロジェクト構造の確認
新しいプロジェクトを作成したら、その構造 (次の図の左側のパネル) を確認できます。 ビジネスについて理解するための標準化されたドキュメントのすべての側面、TDSP ライフサイクルのステージ、データの場所、定義、このドキュメント テンプレートのアーキテクチャが含まれています。 この構造は、[こちら](https://github.com/Azure/Azure-TDSP-ProjectTemplate)で公開されている TDSP 構造から派生したもので、一部が変更されています。 たとえば、いくつかのドキュメント テンプレートは、[ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) という 1 つのマークダウンにマージされています。 

### <a name="project-folder-structure"></a>プロジェクト フォルダー構造
TDSP プロジェクト テンプレートには、次の最上位フォルダーが含まれています。
1. **code**: コードが含まれています
2. **docs**: プロジェクトに関する必要なドキュメント (たとえば、マークダウン ファイル、関連するメディアなど) が含まれています
3. **sample_data**: 開発の初期段階またはテストに使用できる**サンプル (小)** データが含まれています。 通常は、数 Mb (5 Mb) 未満です。 完全または大きなデータ セット用ではありません。

![サンプル データ](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>TDSP の構造とテンプレートの使用
構造とテンプレートには、プロジェクト固有の情報を設定する必要があります。 これらには、プロジェクトの実行と配信に必要なコードと情報を設定する必要があります。 [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ファイルは、プロジェクトに関連する情報で直接変更する必要のあるテンプレートです。 これには、[Team Data Science Process ライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)の 4 つの各ステージに関する情報を入力するのに役立つ質問のセットが付属しています。

プロジェクト構造が実行中または完了後にどのように表示されるかの例を次に示します (次の図の左側のパネル)。 これは、[Team Data Science Process サンプル プロジェクト: Azure Machine Learning での米国国勢調査データからの所得の分類](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)のページから取得したものです。

![プロジェクト構造の例](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>プロジェクトの文書化
プロジェクトの文書化については、[TDSP ドキュメント テンプレート](https://github.com/Azure/Azure-TDSP-ProjectTemplate)に関するページをご覧ください。 現在の Azure Machine Learning TDSP ドキュメント テンプレートでは、すべての情報を [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) ファイルに含めることをお勧めします。 このテンプレートには、プロジェクトに固有の情報を入力する必要があります。 

プライマリ プロジェクト ドキュメントには含まれないが文書化に役立つ情報を含めるために、別の [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) テンプレートも用意しています。 

### <a name="example-project-report"></a>プロジェクト レポートの例
プロジェクト レポートの例については、[こちら](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md)をご覧ください。 これは、データ サイエンス プロジェクト用の TDSP テンプレートをインスタンス化して使用する方法を示す[米国所得分類サンプル プロジェクト](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)のプロジェクト レポートです。

## <a name="next-steps"></a>次のステップ
Azure Machine Learning プロジェクトで TDSP の構造とテンプレートを使用する方法についてよく理解できるように、Azure Machine Learning のドキュメントで計画済みのプロジェクト例をいくつか提供しています。

- Azure Machine Learning で TDSP プロジェクトを作成する方法を示すサンプルについては、[Team Data Science Process サンプル プロジェクト: Azure Machine Learning での米国国勢調査データからの所得の分類](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)に関するページをご覧ください 
- Azure Machine Learning で TDSP インスタンス化されたプロジェクトの NLP にディープ ラーニングを使用するサンプルについては、[ディープ ラーニングと共に自然言語処理を使用した生体エンティティの認識](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)に関するページをご覧ください

