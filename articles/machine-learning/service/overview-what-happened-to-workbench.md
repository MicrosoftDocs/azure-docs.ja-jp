---
title: Machine Learning Workbench の変更点
titleSuffix: Azure Machine Learning service
description: Machine Learning Workbench アプリケーションの変更点、Azure Machine Learning service の変更箇所、およびサポートのタイムラインの概要について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: a164276d41d182c5b439efc691d80693f1ac831e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360134"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench の変更点

Azure Machine Learning Workbench アプリケーションとその他のいくつかの初期機能は、2018 年 9 月のリリースで非推奨とされ、改善された[アーキテクチャ](concept-azure-machine-learning-architecture.md)への移行のために置き換えられました。 

エクスペリエンスの向上のため、このリリースにはお客様からのフィードバックによって促進された、多くの重要な更新が含まれています。 モデル デプロイに対する実験の実行からのコア機能は変更されていません。 ただし、堅牢な <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> と [Azure CLI](reference-azure-machine-learning-cli.md) を使用して、機械学習のタスクとパイプラインを実現できるようになっています。  

以前のバージョンの Azure Machine Learning service で作成された成果物のほとんどは、独自のローカル ストレージやクラウド ストレージに保管されています。 これらの成果物は消失することはありません。

この記事では、Azure Machine Learning Workbench とそのAPI の変更点と、既存の作業に与える影響について説明します。

>[!Warning]
>この記事は、Azure Machine Learning Studio のユーザーを対象にはしていません。 これはワークベンチ (プレビュー) アプリケーションがインストールされているか、実験とモデルの管理プレビュー アカウントを持っている Azure Machine Learning サービスのお客様向けです。


## <a name="what-changed"></a>変更箇所

Azure Machine Learning service の最終リリースには、次の機能が含まれています。
+ [簡略化された Azure リソースのモデル](concept-azure-machine-learning-architecture.md)。
+ 実験とコンピューティング先を管理するための[新しいポータル UI](how-to-track-experiments.md)。
+ 新しい、より包括的な Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>。
+ 機械学習のための拡張された新しい [Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)。

[アーキテクチャ](concept-azure-machine-learning-architecture.md)は、使いやすいように再設計されました。 複数の Azure リソースやアカウントは不要で、必要なものは [Azure Machine Learning サービス ワークスペース](concept-workspace.md)だけです。 ワークスペースは、[Azure portal]((setup-create-workspace.md#portal) ですばやく作成することができます。 ワークスペースを使用することで、複数のユーザーが、トレーニングやデプロイのコンピューティング先、モデルの実験、Docker イメージ、デプロイされたモデルなどを格納できます。

現在のリリースには新しい強化された CLI と SDK クライアントがありますが、デスクトップ ワークベンチ アプリケーション自体は廃止されました。 実験は、[Azure portal のワークスペース ダッシュボード](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)で管理できます。 ダッシュボードを使用して、実験履歴の取得、ワークスペースに接続されているコンピューティング ターゲットの管理、モデルと Docker イメージの管理、さらには Web サービスのデプロイも行います。

<a name="timeline"></a>

## <a name="support-timeline"></a>サポートのタイムライン

Machine Learning Workbench、Azure Machine Learning 実験、Machine Learning モデル管理の各アカウントのサポート、およびそれらに関連する SDK と CLI のサポートは、2019 年 1 月 9 日に終了しました。 

最新の機能はすべて、この <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>、[CLI](reference-azure-machine-learning-cli.md)、[ポータル](setup-create-workspace.md#portal)を使用して利用可能です。

## <a name="what-about-run-histories"></a>実行履歴について教えてください。

最新のバージョンでお客様の実行がまだ表示されていても、以前の実行履歴にはもうアクセスできなくなっています。 

実行履歴は**実験**と呼ばれるようになりました。 SDK、CLI、または Azure portal を使用して、モデルの実験を収集し、それらを表示することができます。

ポータルのワークスペース ダッシュボードは、Microsoft Edge、Chrome、および Firefox ブラウザーでのみでサポートされます。

[![Office ポータル](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

モデルのトレーニングと、新しい CLI と SDK を使用した実行履歴の追跡を開始します。 方法については、「[チュートリアル: Azure Machine Learning サービスを使用したモデルのトレーニング](tutorial-train-models-with-aml.md)」から参照できます。

## <a name="can-i-still-prep-data"></a>データは引き続き準備できますか。

もう Machine Learning Workbench がないため、既存のデータ準備ファイルは最新のリリースに移植されません。 ただし、引き続き任意のサイズのデータ セットをモデリング用に準備できます。   

任意のサイズのデータセットを用意し、[Azure Machine Learning 用のデータ準備パッケージ](https://aka.ms/data-prep-sdk)を使用し、Python コードを記述してモデリングの前にデータを簡単に準備できます。 

Azure Machine Learning Data Prep SDK の使い方について詳しくは、[こちらのチュートリアル](tutorial-data-prep.md)をご覧ください。

## <a name="will-projects-persist"></a>プロジェクトは保持されますか。

コードや作業が失われることはありません。 以前のバージョンでは、プロジェクトは、ローカル ディレクトリのあるクラウド エンティティです。 最新バージョンでは、ローカルの構成ファイルを使用して、ローカル ディレクトリを Azure Machine Learning service ワークスペースに接続します。 [最新アーキテクチャの図](concept-azure-machine-learning-architecture.md)をご覧ください。

プロジェクトのコンテンツの多くは、ローカル コンピューター上に既にありました。 そのため、ワークスペースに接続するには、そのディレクトリ内に構成ファイルを作成し、それをコードで参照するだけで済みます。 ファイルとスクリプトを含むローカル ディレクトリを使用し続けるには、ディレクトリの名前を ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python コマンドで指定するか、または `az ml project attach` CLI コマンドを使用します。  例:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

作業を開始するには、[ワークスペースを作成](setup-create-workspace.md#portal)してください。

## <a name="what-about-my-registered-models-and-images"></a>登録されているモデルとイメージについて教えてください。

古いモデル レジストリに登録したモデルは、それらを引き続き使用したい場合は新しいワークスペースに移行する必要があります。 お客様のモデルを移行するには、新しいワークスペースでモデルをダウンロードして再登録します。 

古いイメージ レジストリに作成したイメージは、新しいワークスペースに直接移行できません。 ほとんどの場合、イメージを作成することなくモデルをデプロイできます。 必要な場合は、新しいワークスペースにモデルのイメージを作成できます。 詳細については、[機械学習モデルの管理、登録、デプロイ、および監視](concept-model-management-and-deployment.md)に関する記事を参照してください。

## <a name="what-about-deployed-web-services"></a>デプロイ済み Web サービスについて教えてください。

以前の CLI のサポートが終了したので、モデルを再デプロイしたり、お客様が最初にご自分のモデル管理アカウントを使用してデプロイした Web サービスを管理したりすることはできなくなりました。 ただし、これらの Web サービスは Azure Container Service (ACS) がサポートされている限りは引き続き機能します。

最新バージョンでは、モデルは Web サービスとして Azure Container Instances (ACI) または Azure Kubernetes Service (AKS) クラスターにデプロイされます。 また、FPGA や Azure IoT Edge にデプロイすることもできます。 

詳細については、次の記事を参照してください。
+ [モデルをデプロイする場所と方法](how-to-deploy-and-where.md)
+ [チュートリアル:Azure Machine Learning service を使用してモデルをデプロイする](tutorial-deploy-models-with-aml.md)

## <a name="what-about-the-old-sdk-and-cli"></a>古い SDK と CLI はどうなりますか。

1 月まで引き続き動作します。 前の[タイムライン](#timeline)をご覧ください。 最新の SDK や CLI を使用して、新しい実験とモデルの作成を開始することをお勧めします。

最新のリリースで新しい Python SDK を使用することにより、Python 環境で Azure Machine Learning service を操作することができます。 最新の <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> のインストール方法を確認してください。 また、更新された [Azure Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)と充実した `az ml` コマンドのセットを使用して、Azure Cloud Shell などのコマンドライン環境でサービスを操作することもできます。

## <a name="what-about-visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools for AI について

この最新リリースでは、拡張機能は Azure Machine Learning for Visual Studio Code に変更されました。また、上記の新しい機能と共に動作するよう拡張および強化されています。

[![Azure Machine Learning for Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)](./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>ドメイン パッケージについて教えてください。

コンピューター ビジョン、テキスト分析、予測用のドメイン パッケージは、最新バージョンの Azure Machine Learning では使用できません。 ただし、コンピューター ビジョン、テキスト、および予測のモデルは、最新の Azure Machine Learning Python <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> を使用して引き続き構築およびトレーニングできます。 コンピューター ビジョン、テキスト分析、予測のパッケージを使用して構築された既存のモデルに移行する方法については、[AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com) までお問い合わせください。

## <a name="next-steps"></a>次の手順

[Azure Machine Learning service の最新のアーキテクチャ](concept-azure-machine-learning-architecture.md)について確認してください。 

サービスの概要については、「[Azure Machine Learning service の概要](overview-what-is-azure-ml.md)」を参照してください。

スクリプトの実行および最新のバージョンの Azure Machine Learning サービスを使用したスクリプトの実行履歴の調査を行う方法を示すクイック スタートとして、[Azure Machine Learning サービスを開始](quickstart-run-cloud-notebook.md)してみてください。

このワークフローをさらに詳しく体験するには、Azure Machine Learning service を使用してモデルをトレーニングおよびデプロイするための詳細な手順が記載されている、[完全版のチュートリアル](tutorial-train-models-with-aml.md)に従ってください。 
