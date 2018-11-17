---
title: Azure Machine Learning Workbench の変更点 | Microsoft Docs
description: Workbench アプリケーションの変更点、Azure Machine Learning の変更箇所、およびサポートのタイムラインの概要について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: b8263c399f287be79590860cce7036207ef2e3f7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243745"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-preview"></a>Azure Machine Learning の Workbench の変更点 (プレビュー)

Workbench アプリケーションとその他のいくつかの初期機能は、2018 年 9 月のリリースでリプレースされ、改善された[アーキテクチャ](concept-azure-machine-learning-architecture.md)に移行されました。 このリリースには、エクスペリエンスの向上のための顧客からのフィールドによって促進された、多くの重要な更新プログラムが含まれています。 実験の実行からモデルのデプロイまでのコア機能は変更されていませんが、機械学習のタスクとパイプラインを実現するために堅牢 <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> と [CLI](reference-azure-machine-learning-cli.md) を使用できるようになりました。  

この記事では、Azure Machine Learning サービスの変更点と、既存の作業に与える影響について説明します。

## <a name="what-changed"></a>変更箇所

Azure Machine Learning サービスの最終リリースには、次のものが含まれています。
+ [簡略化された Azure リソースのモデル](concept-azure-machine-learning-architecture.md)
+ [新しいポータル UI](how-to-track-experiments.md) による、実験とコンピューティング ターゲットの管理
+ 新しい、より包括的な Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>
+ 機械学習のための拡張された新しい [Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)

この[アーキテクチャ](concept-azure-machine-learning-architecture.md)は、使いやすさを考慮して再設計されました。 複数の Azure リソースやアカウントは不要で、必要なものは [Azure Machine Learning サービス ワークスペース](concept-azure-machine-learning-architecture.md#workspace)だけです。  ワークスペースは、[Azure portal](quickstart-get-started.md) ですばやく作成することができます。  1 つのワークスペースを複数のユーザーが、トレーニングやデプロイのコンピューティングのターゲット、モデルの実験、Docker イメージ、デプロイしたモデルなどを格納するために使用することができます。

現在のリリースには新しい強化された CLI と SDK クライアントがありますが、デスクトップ Workbench アプリケーション自体は非推奨です。 現在は、[Azure Web ポータルのワークスペース ダッシュボード](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)で実験を監視できるようになっています。 ダッシュボードを使用して、実験履歴の取得、ワークスペースに接続されているコンピューティング ターゲットの管理、モデルと Docker イメージの管理、さらには Web サービスのデプロイも行います。

## <a name="how-do-i-migrate"></a>移行する方法

以前のバージョンの Azure Machine Learning サービスで作成された成果物のほとんどは、独自のローカル ストレージやクラウド ストレージに保管されています。 これらの成果物は消失することはありません。 移行するには、成果物を、更新された Azure Machine Learning サービスに再登録する必要があります。 移行できるものとその方法については、[移行に関する記事](how-to-migrate.md)を参照してください。

<a name="timeline"></a>

## <a name="support-timeline"></a>サポートのタイムライン

2018 年 9 月以降しばらくの間、Workbench アプリケーションとともに、ご使用中の実験アカウントとモデル管理アカウントを引き続きご利用いただけます。 そのリリースから 3 - 4 か月後に、次のリソースのサポートが段階的に削除されます。 目次の下にある [[リソース]](../desktop-workbench/tutorial-classifying-iris-part-1.md) セクションで、古い機能に関するドキュメントを引き続きお探しいただけます。

|段階|以前の機能のサポート詳細|
|:---:|----------------|
|1|"_Azure Machine Learning 実験アカウント_" と "_モデル管理アカウント_" を Azure portal と CLI で作成する機能は終了となります。 CLI から ML コンピューティング環境を作成する機能も終了となります。 既存のアカウントをお持ちの場合、CLI とデスクトップ Workbench はこの段階では引き続き機能します。|
|2|残りの API やデスクトップ Workbench など、その他すべてのサポートはこの段階で終了します。|

今すぐ[移行を始めてください](how-to-migrate.md)。 最新の機能はすべて、新しい <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>、[CLI](reference-azure-machine-learning-cli.md)、[Portal](quickstart-get-started.md) でご利用いただけます。

## <a name="what-about-run-histories"></a>実行履歴について教えてください。

実行履歴は、しばらくの間はアクセスできる状態が維持されます。 更新されたバージョンの Azure Machine Learning サービスに移行する準備ができたら、コピーを保存しておきたい場合はこれらの実行履歴をエクスポートすることができます。

実行履歴は、現在のリリースでは __実験__ と呼ばれるようになりました。 SDK、CLI、または Web ポータルを使用して、モデルの実験を収集し、それらを表示することができます。

ポータルのワークスペース ダッシュボードは、Edge、Chrome、および Firefox ブラウザーでのみでサポートされます。

![オンライン ポータル](./media/overview-what-happened-to-workbench/image001.png)


## <a name="can-i-still-prep-data"></a>データは引き続き準備できますか。

もう Workbench がないため、既存のデータ準備ファイルは最新のリリースに移植されません。 ただし、引き続きモデリング用にデータを準備することができます。  

小さいデータセットでは、<a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Data Prep SDK</a> を使用して、モデリングの前にデータをすばやく準備することができます。 

大きいデータセットでこれと同じ <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> を使用することも、Azure Databricks を使用して大きいデータセットを準備することもできます。 

## <a name="will-projects-persist"></a>プロジェクトは保持されますか。

コードや作業が失われることはありません。 以前のバージョンでは、プロジェクトは、ローカル ディレクトリのあるクラウド エンティティです。 最新バージョンでは、ローカルの構成ファイルを使用して、ローカル ディレクトリを Azure Machine Learning サービス ワークスペースに接続します。 [最新アーキテクチャの図をご覧ください](concept-azure-machine-learning-architecture.md)。

プロジェクト コンテンツのほとんどが既にローカル マシン上にあったため、ワークスペースに接続するには、そのディレクトリ内に構成ファイルを作成し、それをコードで参照するだけで済みます。 [既存のプロジェクトの移行方法を確認してください。](how-to-migrate.md#projects)

開始方法については、[メインの SDK を使用した Python](quickstart-get-started.md) に関するページを参照してください。

## <a name="what-about-my-registers-models-and-images"></a>登録されているモデルとイメージについて教えてください。
 
古いモデル レジストリに登録したモデルは、それらを引き続き使用したい場合は新しいワークスペースに移行する必要があります。 これは、[モデルをダウンロードして新しいワークスペースに再登録する](how-to-migrate.md)ことで実行できます。 

古いイメージ レジストリに作成したイメージは、引き続き使用するには新しいワークスペースに再作成する必要があります。 これは、[Docker イメージの作成](how-to-deploy-to-aci.md#configure-an-image)に関するセクションの説明に従うことで実行できます。 

## <a name="what-about-deployed-web-services"></a>デプロイ済み Web サービスについて教えてください。

モデル管理アカウントを使用して Web サービスとしてデプロイしたモデルは、Azure Container Service (ACS) がサポートされている限り、引き続き機能します。 それらの Web サービスは、モデル管理アカウントのサポートが終了した後でも機能します。 ただし、古い CLI のサポートが終了すると、それに合わせてそれらの Web サービスを管理する機能も終了します。

新しいバージョンでは、モデルは Web サービスとして [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) または [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS) クラスターにデプロイされます。 また、[FPGA や IoT エッジへのデプロイ](how-to-deploy-and-where.md)も可能です。 スコア付けファイル、依存関係、およびスキーマを変更しなくても、新しい SDK または CLI を使用してモデルを再デプロイすることができます。 

## <a name="what-about-the-old-sdk--cli"></a>以前の SDK と CLI について教えてください。

はい。それらは、しばらくの間は引き続き使用できます (上の[タイムライン](#timeline)を参照)。 最新の SDK や CLI を使用して、新しい実験とモデルの作成を開始することをお勧めします。

最新のリリースでは、新しい Python SDK を使用して、Python 環境で Azure Machine Learning サービスを操作することができます。 最新の <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> のインストール方法を確認してください。  また、[更新された Azure CLI 機械学習拡張機能](reference-azure-machine-learning-cli.md)と充実した `az ml` コマンドのセットを使用して、Azure portal クラウド シェルなどのコマンドライン環境でサービスを操作することもできます。

## <a name="what-about-vs-code-tools-for-ai"></a>VS Code Tools for AI について教えてください。

この最新のリリースでは、上記の新しい機能を使用するために、Visual Studio (VS) Code Tools for AI の拡張機能が展開され、強化されています。

[ ![Visual Studio Code Tools for AI](./media/overview-what-happened-to-workbench/vscode.png) ] (. media/overview-what-happened-to-workbench/vscode-big.png#lightbox/)

## <a name="what-about-domain-packages"></a>ドメイン パッケージについて教えてください。

[Computer Vision、Text Analytics、および Forecasting](../desktop-workbench/reference-python-package-overview.md) のドメイン パッケージは、最新バージョンの Azure Machine Learning では使用できません。 ただし、コンピューター ビジョン、テキスト、および予測のモデルは、最新の Azure Machine Learning Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> を使用して引き続き構築およびトレーニングできます。 Computer Vision、Text Analytics、および Forecasting のパッケージを使用して構築された既存のモデルに移行する方法については、[AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com) までお問い合わせください。

## <a name="next-steps"></a>次の手順

[Azure Machine Learning サービスの最新のアーキテクチャ](concept-azure-machine-learning-architecture.md)について確認し、クイック スタートまたはチュートリアルのいずれかを試してみます。

* [Azure Machine Learning サービスの概要](overview-what-is-azure-ml.md)
* [クイック スタート: Python でワークスペースを作成する](quickstart-get-started.md)
* [チュートリアル: モデルのトレーニング](tutorial-train-models-with-aml.md)
