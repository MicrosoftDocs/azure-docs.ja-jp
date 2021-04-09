---
title: Microsoft 製品で Jupyter Notebook を使用する
description: Microsoft 製品で Jupyter Notebook を使用する方法について説明します。
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507379"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Microsoft 製品で Jupyter Notebook を使用する

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

このクイックスタートでは、Jupyter Notebook をインポートして Microsoft の各種製品で使用する方法について説明します。 

既に Jupyter Notebook をご利用の場合、または新しいプロジェクトを開始したいと考えている場合、Microsoft のさまざまな製品でそれらを使用することができます。 いくつかのオプションについて以下のセクションで説明します。 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [GitHub Codespaces](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>ノートブック用の環境を作成する

廃止された Azure Notebooks プレビューの環境と一致する環境を作成する場合は、GitHub で提供されているスクリプト ファイルを使用できます。

1. Azure Notebooks の [GitHub リポジトリ](https://github.com/microsoft/AzureNotebooks)に移動するか、[環境フォルダーに直接アクセス](https://aka.ms/aznbrequirementstxt)します。
1. コマンド プロンプトで、プロジェクトに使用するディレクトリに移動します。
1. 環境フォルダーの内容をダウンロードし、README の指示に従って Azure Notebooks パッケージの依存関係をインストールします。


## <a name="use-notebooks-in-visual-studio-code"></a>Visual Studio Code でノートブックを使用する

[VS Code](https://code.visualstudio.com/) は無料のコード エディターであり、ローカルでも、リモート コンピューティングに接続しても使用できます。 それに Python 拡張機能を組み合わせることで、Jupyter Notebook を操作するための充実したネイティブ エクスペリエンスを含む、Python 開発用の完全な環境が提供されます。 

![VS Code による Jupyter Notebook のサポート](media/vs-code-jupyter-notebook.png)

既にプロジェクト ファイルがある場合、または新しいノートブックを作成したい場合は、ぜひ VS Code をご利用ください。 Jupyter Notebook と VS Code を使用する方法については、「[Visual Studio Code で Jupyter Notebook を使用する](https://code.visualstudio.com/docs/python/jupyter-support)」と「[Visual Studio Code でのデータ サイエンス](https://code.visualstudio.com/docs/python/data-science-tutorial)」のチュートリアルを参照してください。

また、Visual Studio Code で [Azure Notebooks 環境スクリプト](#create-an-environment-for-notebooks)を使用して、Azure Notebooks プレビューと一致する環境を作成することもできます。

## <a name="use-notebooks-in-github-codespaces"></a>GitHub Codespaces でノートブックを使用する

GitHub Codespaces によって、Visual Studio Code または Web ブラウザーを使用してノートブックを編集できる、クラウドでホストされる環境が提供されます。 VS Code と同じ優れた Jupyter エクスペリエンスが提供されますが、使用するデバイスに何もインストールする必要はありません。 ローカル環境を設定せずに、クラウドベースのソリューションを希望される場合は、コードスペースを作成することをお勧めします。 作業を開始するには:
1. (省略可) GitHub Codespaces で使用するプロジェクト ファイルを収集します。
1. ノートブックを格納するための [GitHub リポジトリを作成](https://help.github.com/github/getting-started-with-github/create-a-repo)します。   
1. リポジトリに[ファイルを追加](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)します。
1. [GitHub Codespaces プレビューへのアクセスを要求します](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Azure Machine Learning でノートブックを使用する

Azure Machine Learning によってエンドツーエンドの機械学習プラットフォームが提供され、ユーザーはそれを使用して、Azure でモデルの構築とデプロイをより迅速に行うことができます。 Azure ML では、VM または共有クラスターのコンピューティング環境で Jupyter Notebook を実行できます。 実験の追跡やデータセット管理などを含むクラウドベースのソリューションが ML ワークロード用に必要な場合は、Azure Machine Learning をお勧めします。 Azure ML の使用を開始するには:

1. (省略可) Azure ML で使用するプロジェクト ファイルを収集します。
1. Azure portal で[ワークスペースを作成](../machine-learning/how-to-manage-workspace.md)します。

   ![ワークスペースを作成する](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. [Azure Studio (プレビュー)](https://ml.azure.com/) を開きます。
1. 左側のナビゲーション バーを使用して、**[Notebooks]** を選択します。
1. **[ファイルのアップロード]** ボタンをクリックし、プロジェクト ファイルをアップロードします。

Azure ML および Jupyter Notebook の実行に関する追加情報については、[ドキュメント](../machine-learning/how-to-run-jupyter-notebooks.md)を参照するか、Microsoft Learn で [Machine Learning の概要](/learn/modules/intro-to-azure-machine-learning-service/)に関するモジュールをお試しください。


## <a name="use-azure-lab-services"></a>Azure Lab Services を使用する

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) を使用すると、教育機関では構成済み VM へのオンデマンド アクセスを簡単にセットアップし、教室全体に対して提供することができます。 カスタマイズされた教室の環境で Jupyter Notebook とクラウド コンピューティングを使用する方法をお探しの場合は、ラボ サービスが最適なオプションです。

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

既にプロジェクト ファイルがある場合、または新しいノートブックを作成したい場合は、ぜひ Azure Lab Services をご利用ください。 ラボの設定に関するガイダンスについては、「[Python と Jupyter Notebook を使用してデータ サイエンスを教えるためのラボを設定する](../lab-services/class-type-jupyter-notebook.md)」を参照してください。

## <a name="use-github"></a>GitHub を使用する

GitHub には、ノートブック (およびその他のファイル) を格納したり、ノートブックを他のユーザーと共有したり、共同作業したりするための、ソース管理に対応した無料の方法が用意されています。 プロジェクトを共有して他のユーザーと共同作業する方法を探している場合、GitHub は優れたオプションであり、優れた開発エクスペリエンスを実現するために [GitHub Codespaces](#use-notebooks-in-github-codespaces) と組み合わせることができます。 GitHub の使用を開始するには

1. (省略可) GitHub で使用するプロジェクト ファイルを収集します。
1. ノートブックを格納するための [GitHub リポジトリを作成](https://help.github.com/github/getting-started-with-github/create-a-repo)します。 
1. リポジトリに[ファイルを追加](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)します。

## <a name="next-steps"></a>次のステップ

- [Visual Studio Code での Python に関する詳細](https://code.visualstudio.com/docs/python/python-tutorial)
- [Jupyter Notebook を使用した Azure Machine Learning に関する詳細](../machine-learning/how-to-run-jupyter-notebooks.md)
- [GitHub Codespaces に関する詳細](https://github.com/features/codespaces)
- [Azure Lab Services に関する詳細](https://azure.microsoft.com/services/lab-services/)
- [GitHub に関する詳細](https://help.github.com/github/getting-started-with-github/)
