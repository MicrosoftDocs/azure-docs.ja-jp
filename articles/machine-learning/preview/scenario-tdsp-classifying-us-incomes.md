---
title: "収入分類 - Team Data Science Process - Azure Machine Learning | Microsoft Docs"
description: "Team Data Science Process テンプレートを使用して、米国の収入を分類するプロジェクトを Azure Machine Learning で作成します。"
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
ms.openlocfilehash: c0fbd9b3e6f9f9f4f7a5d3e6bda18ce1312650e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Team Data Science Process (TDSP) プロジェクトによる収入分類

## <a name="introduction"></a>はじめに

規定された[データ サイエンス ライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)に基づいたデータ サイエンス プロジェクトの構造とドキュメントの標準化は、データ サイエンス チームの効果的なコラボレーションを促進する鍵です。 [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) テンプレートを使用して Azure Machine Learning プロジェクトを作成すると、このような標準化のフレームワークが提供されます。

以前にも、[TDSP プロジェクトの構造とテンプレートの GitHub リポジトリ](https://github.com/Azure/Azure-TDSP-ProjectTemplate)はリリースされていました。 ただ、これまでは、データ サイエンス ツール内で TDSP の構造とテンプレートをインスタンス化することはできませんでした。 [Azure Machine Learning ための TDSP 構造とドキュメント テンプレート](https://github.com/amlsamples/tdsp)を使ってインスタンス化された Azure Machine Learning プロジェクト を作成できるようになりました。 Azure Machine Learning で TDSP 構造とテンプレートを使用する方法については[ここ](https://aka.ms/how-to-use-tdsp-in-aml)をご覧ください。 ここでは、TDSP 構造を使用し、プロジェクト固有のコード、成果物とドキュメントを含み、Azure Machine Learning 内で実行される、機械学習のプロジェクトを実際に作成する方法について説明しています。

## <a name="link-to-github-repository"></a>GitHub リポジトリへのリンク
サンプルに関する概要ドキュメントについては、[ここ](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)をご覧ください。 さらに詳細なドキュメントについては、GitHub サイトでご確認ください。

### <a name="purpose"></a>目的
このサンプルの主な目的は、[Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) 構造と Azure Machine Learning のテンプレートを使用して、機械学習プロジェクトをインスタンス化し実行する方法について説明することです。 このために、よく知られている [UCI Machine Learning Repository から取得された、1994 年の米国の国勢調査データ](https://archive.ics.uci.edu/ml/datasets/adult)を使用します。 モデリングのタスクは、米国国勢調査情報 (年齢、人種、学歴、出身国など) から米国の年間収入分類を予測することです。

### <a name="scope"></a>Scope
 * ユース ケースの概要で説明されている予測問題に対処する機械学習モデルのデータ探索、トレーニング、および配置。 
 * このプロジェクトのために、Azure Machine Learning から Team Data Science Process (TDSP) テンプレートを使用して Azure Machine Learning でプロジェクトを実行します。 プロジェクトの実行とレポートには、TDSP ライフサイクルを使用します。
 * ソリューションは、Azure Container Services の Azure Machine Learning から直接、運用化します。

 プロジェクトでは、TDSP 構造のインスタンス化と使用などの Azure Machine Learning の機能、Jupyter ノートブックや Python ファイルでのコードの実行、および Docker と Kubernetes を使用した Azure Container Services の簡単な運用化について説明します。


## <a name="team-data-science-process-tdsp-lifecycle"></a>Team Data Science Process (TDSP) のライフサイクル
[Team Data Science Process (TDSP) のライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)を参照してください。

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>前提条件
### <a name="required-subscription-hardware-software"></a>必要条件: サブスクリプション、ハードウェア、ソフトウェア
1. Azure [サブスクリプション](https://azure.microsoft.com)。 このサンプルを実行するために、[無料サブスクリプション](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg)を入手することもできます。
2. [Azure Data Science Virtual Machine (DSVM) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm)、(VM サイズ: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)、4 つの仮想 CPU と 14-Gb RAM を使用)。 Azure DSVM を使って検証されていますが、Windows 10 の任意のコンピューターで動作する可能性があります。
3. Azure Machine Learning およびその関連サービスに関するドキュメントを確認してください (以下のリンク参照)。
4. [クイック スタート インストール ガイド](quickstart-installation.md)に従って、Azure Machine Learning が正しくインストールされていることを確認してください。

このサンプルのデータセットは、UCI ML リポジトリ [[リンク]](https://archive.ics.uci.edu/ml/datasets/adult) から取得されています。 これは、1994 年の米国の国勢調査データベースから取得され、約 50,000 人の国勢調査および収入情報が含まれています。 これは数値と分類フィーチャー、および 2 つの収入カテゴリ ('> 50000' または '< = 50000') で構成されるカテゴリ ターゲット が含まれた構造化されたデータセットです。 

### <a name="optional-version-control-repository"></a>省略可能: バージョン コントロール リポジトリ
プロジェクトとそのコンテンツにバージョンを付けて保存するには、そのためのバージョン コントロール リポジトリが必要です。 Azure Machine Learning で TDSP テンプレートを使用して新しいプロジェクトを作成するときに、Git リポジトリの場所を入力できます。 詳細については、[Azure Machine Learning で Git を使用する](using-git-ml-project.md)を参照してください。

### <a name="informational-about-azure-machine-learning"></a>Azure Machine Learning の詳細
* [よくある質問 - ファースト ステップ](frequently-asked-questions.md)
* [概要](overview-what-is-azure-ml.md)
* [インストール](quickstart-installation.md)
* [実行](experiment-execution-configuration.md)
* [TDSP の使用](https://aka.ms/how-to-use-tdsp-in-aml)
* [ファイルの読み取りと書き込み](how-to-read-write-files.md)
* [Azure Machine Learning で Git を使用する](using-git-ml-project.md)
* [Web サービスとして ML モデルを展開する](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>新しいワークベンチ プロジェクトの作成

この例をテンプレートとして使用して新しいプロジェクトを作成します。
1.  Azure Machine Learning Workbench を開きます
2.  **[プロジェクト]** ページで **+** 記号をクリックし、**[新しいプロジェクト]** を選択します
3.  **[新しいプロジェクトの作成]** ウィンドウで、新しいプロジェクトの情報を入力します。
4.  **[プロジェクト テンプレートの検索]** 検索ボックスに「Classify US incomes - TDSP project」と入力し、テンプレートを選択します。
5.  **[作成]**

プロジェクトの作成中に (適切なボックスで) 空の Git リポジトリを指定した場合は、プロジェクトの作成後、そのリポジトリにプロジェクトの構造とコンテンツが設定されます。

## <a name="use-case-overview"></a>ユース ケースの概要
この問題は、米国国勢調査で収集された社会経済データを使って、どのように米国内の個人の年収を予測できるかを理解することです。 このような国勢調査フィーチャーに基づいた機械学習タスクは、個人の収入が 50,000 ドルを超えるかどうかを予測することです (二項分類タスク)。

## <a name="data-description"></a>データの説明
データの詳細については、UCI リポジトリの[説明](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names)を参照してください。 

このデータは、国勢調査局のデータベース (https://www.census.gov/en.html) から抽出されました。 


* 合計 48,842 インスタンス (フィルター処理前) があり、連続インスタンスと不連続インスタンスが混在しています (トレーニング = 32,561、テスト = 16,281)。
* '> 50,000' ラベルの確率: 23.93%/24.78% (不明を除く)。
* '<= 50,000' ラベルの確率: 76.07%/75.22% (不明を除く)。  

* **ターゲット**: 収入分類 '> 50,000'、'<= 50,000'。 データ準備フェーズで、1 と 0 にそれぞれ置き換えられます。
* **フィーチャー**: 年齢、職業分類、学歴、人種、性別、週あたりの就労時間など。


## <a name="project-structure-execution-and-reporting"></a>プロジェクトの構造、実行、およびレポート

### <a name="structure"></a>Structure
このプロジェクトでは、[TDSP のライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)に従った、TDSP フォルダー構造とドキュメント テンプレート (下記参照) を使用します。 

プロジェクトは、[こちら](https://aka.ms/how-to-use-tdsp-in-aml)の手順に従って作成されています。 プロジェクトのコードと成果物を含む構造は次のようになります (次の図で赤色の枠で囲まれたプロジェクトの構造を参照してください)。


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>実行
この例では、**ローカル コンピューティング環境**でコードを実行します。 [実行オプション](experiment-execution-configuration.md)の詳細については、Azure Machine Learningの ドキュメントを参照してください。

ローカルの Python ランタイムでは Python スクリプトを簡単に簡単できます。

    az ml experiment submit -c local my_script.py

Azure Machine Learning の UI の左側にあるプロジェクト構造から IPython ノートブック ファイルをダブルクリックして、Jypyter ノートブック サーバーで実行できます。


詳細なデータ サイエンス ワークフローは次のとおりです。

* [**データの取得と理解**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

データは、UCI ML リポジトリ [[リンク]](https://archive.ics.uci.edu/ml/datasets/adult) の URL から .csv 形式でダウンロードされました。 フィーチャー、ターゲット、およびその変換は ProjectReport.md ファイルで詳しく説明します。

データの取得および理解のためのコードは、/code/01_data_acquisition_and_understanding にあります。

データの探索は、データ サイエンス ツールの [TDSP スイート](https://github.com/Azure/Azure-TDSP-Utilities)の一部として発表された Python 3 [IDEAR (Interactive Data Exploration and Reporting) ユーティリティ](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python)を使用して実行されます。 このユーティリティは、数値、カテゴリのフィーチャーおよびターゲットを含むデータの標準化されたデータ探索レポートの生成に役立ちます。 Python 3 IDEAR ユーティリティの使用方法の詳細については、以下で説明します。 

最終的なデータ探索レポートの保存場所は [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs) です。 IDEAR レポートのビューを、次に示します。

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**モデリング**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

3 段階のクロス検証がある 2 つのモデル、Elastic Net とランダム フォレストを作成しました。 クロス検証とモデル パラメーターの最適化のための戦略として、ランダム グリッド検索用の [59 ポイント サンプリング](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf)を使用しました。 モデルの精度は、テスト データ セットに AUC (曲線の下の領域) を使用して測定されました。 

モデリングのためのコードは、/code/02_modeling にあります。

Elastic Net とランダム フォレスト モデルの両方の AUC は 0.85 を超えていました。 両方のモデルを pickled.pkl ファイルに保存し、両方のモデルの ROC プロットを出力します。 ランダム フォレスト モデルの AUC は 0.92 で、Elastic Net モデルの AUC は 0.90 でした。 また、モデルの解釈のための、ランダム フォレスト モデルのフィーチャーの重要度は、.csv ファイルに出力され、PDF (上位 20 位の予測フィーチャーのみ) にプロットされます。

テスト データ上の**ランダム フォレスト モデル**の ROC 曲線を次に示します。 これが、デプロイされたモデルです。

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

ランダム フォレスト モデルのフィーチャーの重要度 (上位 20 位) を、以下に示します。 これは、資本利得額、教育、配偶者の有無を示し、フィーチャーの重要性は最も高くなります。

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**デプロイ**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

ここでは、[Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/) のクラスター上に、ランダム フォレスト モデルを Web サービスとしてデプロイしました。 運用化環境では、クラスターで Docker と Kubernetes をプロビジョニングして、Web サービスのデプロイを管理します。 運用化のプロセスの詳細については、[こちら](model-management-service-deploy.md)を参照してください。 

展開のためのコードは、/code/03_deployment にあります。


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[プロジェクトの最終レポート](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
上記の各セクションの詳細については、プロジェクトの最終レポート [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md) にまとめられています。 プロジェクトのレポートには、ユース ケース、モデルのパフォーマンス指標、展開、およびプロジェクトが開発および配置されたインフラストラクチャの詳細情報も含まれています。

プロジェクト レポート、プロジェクト フォルダー全体のコンテンツ、およびバージョン管理リポジトリを、クライアントに配信できます。


## <a name="conclusion"></a>まとめ

このサンプルでは、Azure Machine Learning で TDSP 構造とテンプレートを使用する方法について説明しました。 ドキュメント、成果物のテンプレートを通して、次のことができます。
1. プロジェクトの目的とスコープを正しく定義する。
2. 分散された役割と責任でプロジェクト チームを作成する。
3. TDSP ライフ サイクルの段階に従ってプロジェクトを構成および実行する。
4. TDSP データ サイエンス ユーティリティ (IDEAR データ探索、視覚化レポートなど) を使用して、標準化されたレポートを作成する。
5. クライアントに納品する、データ科学プロジェクトの最終レポートを作成する。

データ サイエンス チーム内での標準化と共同作業を簡単に行うには、Azure Machine Learning のこの機能を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

作業を開始する前に以下の資料を参照してください。

[Azure Machine Learning での Team Data Science Process (TDSP) の使用](https://aka.ms/how-to-use-tdsp-in-aml)

[Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Azure Machine Learning の TDSP プロジェクト テンプレート](https://aka.ms/tdspamlgithubrepo)

[UCI ML リポジトリからの米国収入データセット](https://archive.ics.uci.edu/ml/datasets/adult)