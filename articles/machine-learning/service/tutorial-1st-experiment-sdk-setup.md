---
title: 'チュートリアル:初めての ML 実験を作成する: セットアップ'
titleSuffix: Azure Machine Learning service
description: このチュートリアル シリーズでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。  パート 1 では、クラウド ノートブック サーバー環境の作成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: cc16f3c8ea287e78d7b7b4d9a56f5a2e82c26f01
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515329"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment"></a>チュートリアル:初めての ML 実験の作成を開始する

このチュートリアルでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1** です。Python 環境の設定と構成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。 [**パート 2**](tutorial-1st-experiment-sdk-train.md) では、これを基に、複数の機械学習モデルをトレーニングし、Azure portal と SDK の両方を使用したモデル管理プロセスを紹介します。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * 次のチュートリアルで使用する機械学習ワークスペースを作成します。
> * Azure Machine Learning Python SDK のインストールと事前構成が済んだクラウドベースの Jupyter Notebook VM を作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルの唯一の前提条件は、Azure サブスクリプションです。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

## <a name="create-a-workspace"></a>ワークスペースの作成

ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、SDK 内の簡単に使用できるオブジェクトに結び付けます。 Azure Machine Learning service ワークスペースが既にある場合は、[次のセクション](#azure)に進みます。 ワークスペースがない場合は、ここで作成します。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>クラウド ノートブック サーバーの作成

この例では、インストール不要であらかじめ構成されているエクスペリエンスを実現するために、お使いのワークスペースでクラウド ノートブック サーバーを使用します。 お使いの環境、パッケージ、および依存関係を制御したい場合は、[独自の環境](how-to-configure-environment.md#local)を使用してください。

ワークスペースで、Jupyter ノートブックの使用を開始するためのクラウド リソースを作成します。 このリソースは、Azure Machine Learning service を実行するために必要なすべてのものがあらかじめ構成されているクラウドベースの Linux 仮想マシンです。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  ポータルでワークスペースを見つける方法がわからない場合は、[ワークスペースを探す](how-to-manage-workspace.md#view)方法に関するページを参照してください。

1. Azure portal のワークスペース ページで、左側の **[ノートブック VM]** を選択します。

1. **[+ 新規]** を選択して、ノートブック VM を作成します。

     ![新しい VM を選択する](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. VM の名前を指定します。 **[作成]** を選択します。

    > [!NOTE]
    > Notebook VM 名は 2 文字から 16 文字にする必要があります。 英字、数字、- の文字が有効です。  名前は、Azure サブスクリプション内で一意である必要もあります。

1. 状態が **[実行中]** に変わるまで待機します。

### <a name="launch-jupyter-web-interface"></a>Jupyter の Web インターフェイスを起動する

VM が実行中になった後、 **[ノートブック VM]** セクションを使用して、Jupyter の Web インターフェイスを起動します。

1. VM の **[URI]** 列で、 **[Jupyter]** を選択します。

    ![Jupyter ノートブック サーバーを開始する](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    リンクから、ノートブック サーバーが起動され、新しいブラウザー タブで Jupyter ノートブックの Web ページが開かれます。このリンクは、VM を作成するユーザーに対してのみ動作します。 ワークスペースの各ユーザーは、独自の VM を作成する必要があります。

1. Jupyter ノートブックの Web ページで、最上位のフォルダー名はユーザー名です。  このフォルダーを選択します。

    > [!TIP]
    > このフォルダーは、ノートブック VM 自体ではなく、ワークスペース内の[ストレージ コンテナー](concept-workspace.md#resources)上にあります。  ノートブック VM を削除しても、すべての作業を続けることができます。  後で新しいノートブック VM を作成すると、この同じフォルダーが読み込まれます。 他のユーザーとワークスペースを共有すると、互いのフォルダーが表示されます。

1. チュートリアルの**パート 2** を実行するために、`samples-*` サブディレクトリを開き、次に `tutorials/tutorial-1st-experiment-sdk-train.ipynb` を開きます。

## <a name="end"></a>リソースのクリーンアップ

続けてチュートリアルの**パート 2** を行う予定の場合は、このセクションの操作を行わないでください。

### <a name="stop-the-notebook-vm"></a>ノートブック VM を停止する

クラウド ノートブック サーバーを使用していた場合は、使用していない VM を停止してコストを削減します。

1. ワークスペースで、 **[ノートブック VM]** を選択します。

   ![VM サーバーを停止する](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. 一覧から VM を選択します。

1. **[停止]** を選択します。

1. サーバーを再び使用する準備が整ったら、 **[開始]** を選択します。

### <a name="delete-everything"></a>すべてを削除する

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のタスクを完了しました。

* Azure Machine Learning service ワークスペースを作成しました。
* お使いのワークスペース内にクラウド ノートブック サーバーを作成して構成しました。

このチュートリアルの**パート 2** に進んで、単純な機械学習モデルをトレーニングします。

> [!div class="nextstepaction"]
> [チュートリアル:最初のモデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)
