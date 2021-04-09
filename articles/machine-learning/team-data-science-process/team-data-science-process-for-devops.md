---
title: DevOps 向け Team Data Science Process
description: Advanced Analytics と Cognitive Services ソリューションを実装するための開発者の操作 (DevOps) の機能。
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
ms.openlocfilehash: ceac1e7b5e8efa7e87ecf6e1f4c9dd8286c87e98
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614073"
---
# <a name="team-data-science-process-for-developer-operations"></a>DevOps 向け Team Data Science Process

この記事では、Advanced Analytics と Cognitive Services ソリューションを実装するための開発者の操作 (DevOps) の機能について説明します。 これらのトレーニング資料には、Team Data Science Process (TDSP) と Microsoft およびオープンソースのソフトウェアやツールキットが実装され、データ サイエンス ソリューションの計画、遂行、および実現に役立ちます。 ここでは、データ サイエンス、AI プロジェクトおよびソリューションに固有の DevOps ツールチェーンに関するトピックを参照しています。

## <a name="lesson-path"></a>レッスン パス
次の表は、レベルに基づくガイダンスを示しており、Azure 上でデータ サイエンス ソリューションを実装する場合に DevOps の目標を達成するために役立ちます。

| 目的 | トピック | **リソース** | **テクノロジ** | **Level** | **前提条件** |
|--|--|--|--|--|--|
| Advanced Analytics を理解する | Team Data Science Process ライフサイクル | [このテクニカル チュートリアルでは Team Data Science Process について説明します](overview.md) | データ サイエンス | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、IT プロジェクトおよびソリューションの実装に関する知識 |
| Advanced Analytics 向けの Microsoft Azure Platform を理解する | 情報管理 |
| [このリファレンスでは、分析データ ソリューション用のパイプラインを作成するための、Azure Data Factory の概要について確認できます。](../../data-factory/v1/data-factory-introduction.md) | Microsoft Azure Data Factory | 上級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、IT プロジェクトおよびソリューションの実装に関する知識 |
|  |
| [このリファレンスでは、データ ソースのメタデータを記録および管理する Azure Data Catalog の概要を確認できます](../../data-catalog/overview.md) | Microsoft Azure Data Catalog | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、リレーショナル データベース管理システム (RDBMS) と NoSQL データ ソースに関する知識 |
|  |
| [このリファレンスでは、Azure Event Hubs システムの概要と、使用しているソリューションにデータを取り込み使用する方法を確認できます](../../event-hubs/event-hubs-about.md) | Azure Event Hubs | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、リレーショナル データベース管理システム (RDBMS) と NoSQL データ ソースに関する知識、モノのインターネット (IoT) の用語と使用に関する知識 |
|  | ビッグ データ ストア |
| [このリファレンスでは、Azure Synapse Analytics を使用して大量のデータを保存および処理する方法の概要を確認できます](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) | Azure Synapse Analytics | 上級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、リレーショナル データベース管理システム (RDBMS) と NoSQL データ ソースに関する知識、HDFS の用語と使用に関する知識 |
|  |  | [このリファレンスでは、Azure Data Lake を使用して運用分析や調査分析のためにデータを任意のサイズ、種類、取り込み速度で 1 か所でキャプチャする方法の概要を確認できます。](../../data-lake-store/data-lake-store-overview.md) | Azure Data Lake Store | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、NoSQL データ ソースに関する知識、HDFS に関する知識 |
|  | Machine Learning と分析 | [このリファレンスでは、機械学習、予測分析、人工知能システムの概要を確認できます](../classic/index.yml) | Azure Machine Learning | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、データ サイエンスの用語知識、機械学習と人工知能の用語知識 |
|  |  | [この記事では、Hadoop テクノロジ スタックのクラウド ディストリビューションである Azure HDInsight について概説します。さらに、Hadoop クラスターとは何であるかや、いつ Hadoop クラスターを使用するかについても説明します。](../../hdinsight/hadoop/apache-hadoop-introduction.md) | Azure HDInsight | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、NoSQL データ ソースに関する知識 |
|  |  | [このリファレンスでは、Azure Data Lake Analytics ジョブ サービスの概要を確認できます](../../data-lake-analytics/data-lake-analytics-overview.md) | Azure Data Lake Analytics | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、NoSQL データ ソースに関する知識 |
|  |  | [この概要では、完全に管理されたイベント処理エンジンである Azure Stream Analytics を使用した、ストリーミング データを対象としたリアルタイム分析の計算の設定方法を確認できます。](../../stream-analytics/stream-analytics-introduction.md) | Azure Stream Analytics | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、構造化データと非構造化データの概念に関する知識 |
|  | Intelligence | [このリファレンスでは、使用可能な Cognitive Services (ビジョン、テキスト、検索など) の概要と使用開始方法を確認できます](../../cognitive-services/what-are-cognitive-services.md) | Cognitive Services | 上級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識、ソフトウェア開発経験 |
|  |  | [このリファレンスでは、Microsoft Bot Framework の概要と使用開始方法を確認できます](/bot-framework/overview-introduction-bot-framework) | Bot Framework | 上級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識 |
|  | グラフ | [この自分のペースで進められるオンライン コースでは、Power BI システムの概要とレポートの作成および発行方法を確認できます](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-0-what-is-power-bi/) | Microsoft Power BI | 初級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識 |
|  | ソリューション | [このリソース ページでは、完全なソリューションのすべてを確認するために複数のアプリケーションを使用してレビュー、テスト、実装を行う方法を確認できます](https://gallery.cortanaintelligence.com/) | Microsoft Azure、Azure Machine Learning、Cognitive Services、Microsoft R、Azure Cognitive Search、Python、Azure Data Factory、Power BI、Azure Document DB、Application Insights、Azure SQL DB、Azure Synapse Analytics、Microsoft SQL Server、Azure Data Lake、Cognitive Services、Bot Framework、Azure Batch | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識 |
| DevOps プロセスを理解して実装する | DevOps の基礎 | [このビデオ シリーズでは、DevOps の基礎について説明したうえで、DevOps のプラクティスにマップする方法を示します。](https://channel9.msdn.com/Series/DevOps-Fundamentals/Introduction-to-DevOps) | DevOps、Microsoft Azure Platform、Azure DevOps | 上級 | SDLC の使用経験、アジャイルなどの開発フレームワークに関する知識、IT 運用に関する知識 |
| データ サイエンス用に DevOps ツールチェーンを使用する | 構成 | [このリファレンスでは、Visio で適切な視覚エフェクトを選択してプロジェクト デザインに生かしていく方法の基礎を確認できます](https://support.office.com/article/Illustrate-business-processes-with-Visio-flowcharts-DAB16418-1FE6-4DE0-8F26-DBA44A26ED65) | Visio | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識 |
|  |  | [このリファレンスでは、Azure Resource Manager とその用語について説明し、サンプルや概要などの重要な参照情報を提供します](../../azure-resource-manager/management/overview.md) | Azure Resource Manager、Azure PowerShell、Azure CLI | 中級 | 一般的なテクノロジの背景知識、データ ソリューションに関する知識 |
|  |  | [このリファレンスでは、Linux および Windows 用の Azure データ サイエンス仮想マシンについて説明します](../data-science-virtual-machine/overview.md) | データ サイエンス仮想マシン | 上級 | データ サイエンス ワークロードと Linux に関する知識 |
|  |  | [このチュートリアルでは、Visual Studio を使用した Azure クラウド サービス ロールの構成について説明し、特にストレージ アカウント用の接続文字列について詳述します](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service#configure-an-azure-cloud-service) | Visual Studio | 中級 | ソフトウェア開発 |
|  |  | [このシリーズでは、Microsoft Project を使用した Advanced Analytics プロジェクトの時間、リソース、目標のスケジュール設定方法について説明します](https://support.office.com/article/Project-2013-videos-and-tutorials-af7d1e17-5fa7-421f-a452-9bbe2cd7b082) | Microsoft Project | 中級 | プロジェクト管理の基礎を理解する |
|  |  | [この Microsoft Project テンプレートを使用すれば、Advanced Analytics プロジェクトの時間、リソース、目標を追跡できます](https://buckwoody.wordpress.com/2017/08/17/a-data-science-microsoft-project-template-you-can-use-in-your-solutions/) | Microsoft Project | 中級 | プロジェクト管理の基礎を理解する |
|  |  | [この Azure Data Catalog チュートリアルでは、エンタープライズ データ資産の登録と検出のシステムについて説明します](../../data-catalog/data-catalog-get-started.md) | Azure Data Catalog | 初級 | データ ソースと構造に関する知識 |
|  |  | [この Microsoft Virtual Academy コースでは、Visual Studio Codespace と Microsoft Azure を使用して Dev/Test をセットアップする方法について説明します](https://mva.microsoft.com/training-courses/dev-test-with-visual-studio-online-and-microsoft-azure-8420?l=P7Ot1TKz_2104984382) | Visual Studio Codespace | 上級 | ソフトウェア開発経験、Dev/Test 環境に関する知識 |
|  |  | [この Microsoft System Center 用の管理パックのダウンロードには、Azure アセットの活用に役立つガイドライン ドキュメントが含まれています](https://www.microsoft.com/download/details.aspx?id=38414) | System Center | 中級 | System Center を使用した IT 管理経験 |
|  |  | [このドキュメントでは、開発者とオペレーション チーム向けに PowerShell Desired State Configuration について説明します](/powershell/scripting/dsc/overview/dscforengineers) | PowerShell DSC | 中級 | PowerShell コーディング経験、エンタープライズ アーキテクチャとスクリプトに関する知識 |
|  | コード | [このダウンロードには、Visual Studio Codespace コードを使用したデータ サイエンスと AI アプリケーションの作成に関するドキュメントが含まれています](https://code.visualstudio.com/) | Visual Studio Codespace | 中級 | ソフトウェア開発 |
|  |  | [この概要サイトでは、DevOps と Visual Studio について説明します](https://www.visualstudio.com/devops/) | Visual Studio | 初級 | ソフトウェア開発 |
|  |  | [App Service Editor を使用して Azure portal で直接コードを記述できます。このツールを使用した継続的インテグレーションの詳細については、こちらのリソースをご覧ください](https://github.com/projectkudu/kudu/wiki/App-Service-Editor) | Azure portal | 上級以上 | データ サイエンスの背景知識 (知識のあるなしに関わらずこちらをご覧ください) |
|  |  | [このリソースでは、Web ベースの Azure Machine Learning Studio (クラシック) ツールを使用して予測分析の実験のコードを書き、作成する方法について説明します](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) | Azure Machine Learning Studio (クラシック) | 上級 | ソフトウェア開発 |
|  |  | [このリファレンスには、Azure データ サイエンス仮想マシンのすべての開発ツールに関する一覧と学習用リンクが含まれています](../data-science-virtual-machine/overview.md) | データ サイエンス仮想マシン | 上級 | ソフトウェア開発経験、データ サイエンスに関する知識 |
|  |  | [こちらの Azure Security Trust Center のセキュリティ、プライバシー、コンプライアンスに関する各リファレンスをよく読み、理解することが非常に重要です](https://azure.microsoft.com/support/trust-center/) | Azure Security | 中級 | システム アーキテクチャの経験、セキュリティ開発の経験 |
|  | Build | [このコースでは、Visual Studio Codespace Build を使用した DevOps プラクティスの有効化について説明します](https://mva.microsoft.com/training-courses/enabling-devops-practices-with-visual-studio-online-build-12478?l=ipCj6MuNB_6305094681) | Visual Studio Codespace | 上級 | ソフトウェア開発経験、SDLC に関する知識 |
|  |  | [このリファレンスでは、Visual Studio を使用したコンパイルとビルドについて説明します](/previous-versions/visualstudio/visual-studio-2015/ide/compiling-and-building-in-visual-studio) | Visual Studio | 中級 | ソフトウェア開発経験、SDLC に関する知識 |
|  |  | [このリファレンスでは、Runbooks を使用したソフトウェア ビルドなど、プロセスの調整方法について説明します](/system-center/orchestrator/automate-runbooks) | System Center | 上級 | System Center Orchestrator の使用経験 |
|  | テスト | [このリファレンスでは、Visual Studio Codespace を使用したテスト ケースの管理方法について説明します](http://www.almguide.com/2014/07/visual-studio-online-test-case-management/) | Visual Studio Codespace | 上級 | ソフトウェア開発経験、SDLC に関する知識 |
|  |  | [Runbooks に関するこの過去のリファレンスを使用して、System Center でテストを自動化できます](/system-center/orchestrator/automate-runbooks) | System Center | 上級 | System Center Orchestrator の使用経験 |
|  |  | [テストだけでなく開発においても、セキュリティを考えて構築する必要があります。すべてのフェーズで Microsoft SDL Threat Modeling Tool が役に立ちます。詳細とダウンロードについてはこちらをご覧ください](https://www.microsoft.com/SDL/adopt/threatmodeling.aspx) | Threat Monitoring Tool | 上級 | セキュリティの概念に関する知識、ソフトウェア開発経験 |
|  |  | [この記事では、Microsoft Attack Surface Analyzer を使用してご利用の Advanced Analytics ソリューションをテストする方法について説明します](https://technet.microsoft.com/security/gg749821.aspx) | Attack Surface Analyzer | 上級 | セキュリティの概念に関する知識、ソフトウェア開発経験 |
|  | Package | [このリファレンスでは、TFS と Visual Studio Codespace でのパッケージ使用の概念について説明します](https://www.visualstudio.com/docs/package/collaborate-with-packages) | Visual Studio Codespace | 上級 | ソフトウェア開発経験、SDLC に関する知識 |
|  |  | [Runbooks に関するこの過去のリファレンスを使用して、System Center でパッケージングを自動化できます](/system-center/orchestrator/automate-runbooks) | System Center | 上級 | System Center Orchestrator の使用経験 |
|  |  | [このリファレンスでは、ご利用のソリューション用のデータ パイプラインを作成する方法と、それを JSON テンプレートの "パッケージ" として保存できることについて説明します](../../data-factory/v1/data-factory-introduction.md) | Azure Data Factory | 中級 | 一般的なコンピューティングの背景知識、データ プロジェクト経験 |
|  |  | [このトピックでは、Azure Resource Manager テンプレートの構造について説明します](../../azure-resource-manager/templates/template-syntax.md) | Azure Resource Manager | 中級 | Microsoft Azure Platform に関する知識 |
|  |  | [DSC は、IT と開発インフラストラクチャをコードとして構成し、パッケージとして保存できる PowerShell の管理プラットフォームです。このリファレンスはそのトピックの概要です](/powershell/scripting/dsc/overview/overview) | PowerShell Desired State Configuration | 中級 | PowerShell コーディング経験、エンタープライズ アーキテクチャとスクリプトに関する知識 |
|  | Release | [このヘッド リファレンス記事では、CI/CD 環境でのビルド、テスト、リリースの概念について説明します](/azure/devops/pipelines/) | Visual Studio Codespace | 上級 | ソフトウェア開発経験、CI/CD 環境に関する知識、SDLC に関する知識 |
|  |  | [Runbooks に関するこの過去のリファレンスを使用して、System Center でリリース管理を自動化できます](/system-center/orchestrator/automate-runbooks) | System Center | 上級 | System Center Orchestrator の使用経験 |
|  |  | [この記事は、Web アプリ、モバイル アプリ バックエンド、または API アプリのファイルを Azure App Service にデプロイする際の最適なオプションを判断するのに役立ちます。また、選択したオプションの固有の手順が記載された適切なリソースも紹介しています](../../app-service/deploy-local-git.md) | Microsoft Azure のデプロイ | 中級 | ソフトウェア開発経験、Microsoft Azure Platform の使用経験 |
|  | モニター | [このリファレンスでは、Application Insights と、それを Advanced Analytics Solutions に追加する方法について説明します](../../azure-monitor/app/app-insights-overview.md) | Application Insights | 中級 | ソフトウェア開発経験、Microsoft Azure Platform に関する知識 |
|  |  | [このトピックでは、Operations Manager インフラストラクチャを管理する管理者と Advanced Analytics Solution を監視およびサポートする運用担当者向けに、Operations Manager の基本概念について説明します](/previous-versions/system-center/system-center-2012-R2/hh230741(v=sc.12)) | System Center | 上級 | エンタープライズ監視に関する知識、 System Center Operations Manager の使用経験 |
|  |  | [このブログ エントリでは、Azure Data Factory を使用して Advanced Analytics パイプラインを監視および管理する方法について説明します](https://azure.microsoft.com/blog/azure-data-factory-updates-monitoring-and-management-enhancements/) | Azure Data Factory | 中級 | Azure Data Factory に関する知識 |
|  |  | [このビデオでは、Azure Monitor ログを使用してログを監視する方法について説明します](https://channel9.msdn.com/Shows/Data-Exposed/Enterprise-HDInsight-Monitoring-with-Operations-Management-Suite) | Azure ログと PowerShell の使用経験 | 上級 | Azure Platform に関する知識 |
| Azure で DevOps を使用したオープン ソース ツールの使用方法を理解する | オープン ソースの DevOps ツールと Azure | [このリファレンス ページには、Chef を使用した Azure デプロイに関する 2 つのビデオとホワイトペーパーがあります](https://www.chef.io/) | Chef | 上級 | Azure Platform に関する知識、DevOps に関する知識 |
|  |  | [このサイトには、ツールチェーン選択パスがあります](https://azure.microsoft.com/try/devops/) | DevOps、Microsoft Azure Platform、Azure DevOps、オープン ソース ソフトウェア | 上級 | SDLC の使用経験、アジャイルなどの開発フレームワークに関する知識、IT 運用に関する知識 |
|  |  | [このチュートリアルでは、継続的インテグレーションとデプロイの CI/CD パイプラインを使用して、アプリケーション開発のビルドおよびテスト フェーズを自動化します](/azure/developer/jenkins/pipeline-with-github-and-docker) | Jenkins | 上級 | Azure Platform に関する知識、DevOps に関する知識、Jenkins に関する知識 |
|  |  | [こちらで、Docker と Azure の概要と、データ サイエンス アプリケーションの実装に関する追加リファレンスを確認できます](https://www.docker.com/docker-azure#/overview) | Docker | 中級 | Azure Platform に関する知識、サーバーのオペレーティング システムに関する知識 |
|  |  | [こちらのインストールと説明では、Azure アセットを使用した Visual Studio Code の使用方法について説明します](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.OpeninVisualStudioCode) | VSCODE | 中級 | ソフトウェア開発経験、Microsoft Azure Platform に関する知識 |
|  |  | [このブログ エントリでは、Microsoft R を使用した R Studio の使用方法について説明します](https://www.r-bloggers.com/using-microsoft-r-open-with-rstudio/) | R Studio | 中級 | R 言語の使用経験 |
|  |  | [このブログ エントリでは、Azure と GitHub を使用した継続的インテグレーションの使用方法について説明します](https://blogs.msdn.microsoft.com/microsoftimagine/2015/09/01/using-continuous-integration-with-azure-github/) | Git、GitHub | 中級 | ソフトウェア開発 |

## <a name="next-steps"></a>次のステップ

「[データ サイエンティスト向け Team Data Science Process](team-data-science-process-for-data-scientists.md)」。この記事では、Azure を使用してデータ サイエンス ソリューションを実装するためのガイダンスを提供します。
