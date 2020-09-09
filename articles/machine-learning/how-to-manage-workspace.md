---
title: ポータルでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure portal で Azure Machine Learning ワークスペースを作成、表示、削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e1f97fddb07e56946e37c04d9b9685412782c560
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659757"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure portal 内で Azure Machine Learning ワークスペースを作成および管理する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning ](overview-what-is-azure-ml.md)用の Azure portal で [**Azure Machine Learning ワークスペース**](concept-workspace.md)を作成、表示、削除します。  ワークスペースを初めて利用する際にはポータルが最も手軽ですが、ニーズに変化が生じたり自動化の要件が高まったりした場合は、[CLI](reference-azure-machine-learning-cli.md)、[Python コード](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [VS Code 拡張機能](tutorial-setup-vscode-extension.md)を使用して、ワークスペースを作成したり、削除したりすることもできます。

## <a name="create-a-workspace"></a>ワークスペースの作成

ワークスペースを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

1. ご利用の Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。

      ![新しいリソースを作成](./media/how-to-manage-workspace/create-workspace.gif)

1. 検索バーを使用して、**Machine Learning** を見つけます。

1. **[Machine Learning]** を選択します。

1. **[Machine Learning]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明 
   ---|---
   ワークスペース名 |ワークスペースを識別する一意の名前を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。 ワークスペース名では、大文字と小文字は区別されません。
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 既存のリソース グループを使用するには、*共同作成者*または*所有者*のロールが必要です。  アクセスの詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」の記事をご覧ください。
   場所 | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。
   場所 | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。
   ワークスペースのエディション | **[Basic]** または **[Enterprise]** を選択します。  このワークスペース エディションによって、アクセスでき、価格設定の対象となる機能が決まります。 詳細については、[Basic および Enterprise Edition のオファリング](overview-what-is-azure-ml.md#sku)に関するページを参照してください。 

    ![ワークスペースを構成する](./media/how-to-manage-workspace/select-edition.png)

1. ワークスペースの構成が完了したら、 **[確認および作成]** を選択します。
2. 設定を確認し、追加の変更または訂正があれば実行します。 設定に問題がなければ、 **[作成]** を選択します。

   > [!Warning] 
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。 
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。

### <a name="download-a-configuration-file"></a>構成ファイルをダウンロードする

1. [コンピューティング インスタンス](tutorial-1st-experiment-sdk-setup.md#azure)を作成する場合は、この手順をスキップしてください。

1. このワークスペースを参照するローカル環境でコードを使用する場合は、ワークスペースの **[概要]** セクションから **[config. json をダウンロード]** を選択します。  

   ![config.json をダウンロードする](./media/how-to-manage-workspace/configure.png)
   
   このファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内に置きます。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。 コンピューティング インスタンスを作成するとき、このファイルは VM 上の正しいディレクトリに自動的に追加されます。

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Enterprise Edition へのアップグレード

Basic Edition から Enterprise Edition にワークスペースをアップグレードすることで、ローコード エクスペリエンスや強化されたセキュリティ機能などの拡張機能を利用できます。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. アップグレードするワークスペースを選択します。

1. ページの右上部にある **[詳細]** を選択します。

   [![ワークスペースをアップグレードする](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. 表示されるウィンドウで **[アップグレード]** を選択します。


> [!IMPORTANT]
> Enterprise Edition ワークスペースを Basic Edition ワークスペースにダウングレードすることはできません。 

## <a name="find-a-workspace"></a><a name="view"></a>ワークスペースの検索

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 上部の検索フィールドに「**Machine Learning**」と入力します。  

1. **[Machine Learning]** を選択します。

   ![Azure Machine Learning ワークスペースの検索](./media/how-to-manage-workspace/find-workspaces.png)

1. 見つかったワークスペースの一覧にひととおり確認します。 サブスクリプション、リソース グループ、場所に基づいてフィルター処理できます。  

1. ワークスペースを選択して、そのプロパティを表示します。

## <a name="delete-a-workspace"></a>ワークスペースを削除する

[Azure portal](https://portal.azure.com/) で、削除するワークスペースの上部にある **[削除]** を選択します。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="ワークスペースの削除":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>次のステップ

ワークスペースを使用して、Azure Machine Learning でモデルをビルド、トレーニング、デプロイする方法については、完全版のチュートリアルをご利用ください。

> [!div class="nextstepaction"]
> [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
