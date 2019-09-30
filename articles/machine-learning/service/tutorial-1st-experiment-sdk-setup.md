---
title: 'チュートリアル:初めての ML 実験を作成する: セットアップ'
titleSuffix: Azure Machine Learning
description: このチュートリアル シリーズでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。  パート 1 では、クラウド ノートブック サーバー環境の作成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: 9bc5b9688a8cd568b47fe2dad88d6d007ceca0c4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004060"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>チュートリアル:Python SDK で初めての ML 実験を作成する

このチュートリアルでは、Jupyter ノートブックで実行されている Azure Machine Learning Python SDK の使用を開始するためのエンドツーエンドの手順を完了します。 このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 1** です。Python 環境の設定と構成のほか、実験と機械学習モデルを管理するためのワークスペースの作成について説明します。 [**パート 2**](tutorial-1st-experiment-sdk-train.md) では、これを基に、複数の機械学習モデルをトレーニングし、Azure portal と SDK の両方を使用したモデル管理プロセスを紹介します。

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * 次のチュートリアルで使用する [Azure Machine Learning ワークスペース](concept-workspace.md)を作成します。
> * Azure Machine Learning Python SDK のインストールと事前構成が済んだクラウドベースの Jupyter Notebook VM を作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースは、機械学習モデルを実験、トレーニング、およびデプロイするために使用する、クラウドでの基本的なリソースです。 ワークスペースは、Azure サブスクリプションとリソース グループを、SDK 内の簡単に使用できるオブジェクトに結び付けます。 Azure Machine Learning ワークスペースが既にある場合は、[次のセクション](#azure)に進みます。 ワークスペースがない場合は、ここで作成します。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>クラウド ノートブック サーバーの作成

この例では、インストール不要であらかじめ構成されているエクスペリエンスを実現するために、お使いのワークスペースでクラウド ノートブック サーバーを使用します。 お使いの環境、パッケージ、および依存関係を制御したい場合は、[独自の環境](how-to-configure-environment.md#local)を使用してください。

ワークスペースで、Jupyter ノートブックの使用を開始するためのクラウド リソースを作成します。 このリソースは、Azure Machine Learning を実行するために必要なすべてのものがあらかじめ構成されているクラウドベースの Linux 仮想マシンです。

1. [Azure Portal](https://portal.azure.com/) でワークスペースを開きます。  ポータルでワークスペースを見つける方法がわからない場合は、[ワークスペースを探す](how-to-manage-workspace.md#view)方法に関するページを参照してください。

1. Azure portal のワークスペース ページで、左側の **[ノートブック VM]** を選択します。

1. **[+ 新規]** を選択して、ノートブック VM を作成します。

     ![新しい VM を選択する](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. VM の名前を指定します。 
   + Notebook VM 名は 2 文字から 16 文字にする必要があります。 英字、数字、- の文字が有効です。  
   + 名前は、Azure サブスクリプション内で一意である必要もあります。

1. **[作成]** を選択します。 VM の設定には少し時間がかかる場合があります。

1. 状態が **[実行中]** に変わるまで待機します。
   VM が実行中になった後、 **[ノートブック VM]** セクションを使用して、Jupyter の Web インターフェイスを起動します。

1. VM の **[URI]** 列で、 **[Jupyter]** を選択します。

    ![Jupyter ノートブック サーバーを開始する](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   リンクから、ノートブック サーバーが起動され、新しいブラウザー タブで Jupyter ノートブックの Web ページが開かれます。このリンクは、VM を作成するユーザーに対してのみ動作します。 ワークスペースの各ユーザーは、独自の VM を作成する必要があります。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のタスクを完了しました。

* Azure Machine Learning ワークスペースを作成しました。
* お使いのワークスペース内にクラウド ノートブック サーバーを作成して構成しました。

チュートリアルの**パート 2** では、`tutorial-1st-experiment-sdk-train.ipynb` のコードを実行して機械学習モデルをトレーニングします。 

> [!div class="nextstepaction"]
> [チュートリアル:最初のモデルをトレーニングする](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> このチュートリアルのパート 2 や他のチュートリアルに取り組む予定がない場合は、不使用時のコストを抑えるために、[クラウド ノートブック サーバー VM を停止](tutorial-1st-experiment-sdk-train.md#clean-up-resources)してください。


