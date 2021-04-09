---
title: Personalizer リソースを作成する
description: この記事では、フィードバック ループごとに Azure portal 内で Personalizer リソースを作成する方法を説明します。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303558"
---
# <a name="create-a-personalizer-resource"></a>Personalizer リソースを作成する

Personalizer リソースは、Personalizer の学習ループと同じものです。 単一リソース (学習ループ) は、存在する各サブジェクト ドメインまたはコンテンツ領域に対して作成されます。 同じループ内で複数のコンテンツ領域を使用しないでください。そのようにすると、学習ループを混乱させ、予測精度が低下するためです。

Personalizer で Web ページの複数のコンテンツ領域に最適なコンテンツを選択できるようにするには、それぞれに異なる学習ループを使用します。


## <a name="create-a-resource-in-the-azure-portal"></a>Azure portal でリソースを作成する

フィードバック ループごとに Personalizer リソースを作成します。

1. [Azure ポータル](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)にサインインします。 前のリンクから、Personalizer サービスの **作成** ページに移動できます。
1. サービス名を入力し、サブスクリプション、場所、価格レベル、リソース グループを選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure portal を使用して、学習ループとも呼ばれる、Personalizer リソースを作成する。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. **[作成]** を選択して、リソースを作成します。

1. リソースがデプロイされた後、 **[リソースに移動]** ボタンを選択して、Personalizer リソースに移動します。

1. リソースの **[クイック スタート]** ページを選択し、エンドポイントとキーの値をコピーします。 Rank API と Reward API を使用するには、リソース エンドポイントとキーの両方が必要です。

1. 新しいリソースの **[構成]** ページを選択し、[学習ループを構成](how-to-settings.md)します。

## <a name="create-a-resource-with-the-azure-cli"></a>Azure CLI を使用してリソースを作成する

1. 次のコマンドを使用して、Azure CLI にサインインします。

    ```azurecli-interactive
    az login
    ```

1. リソース グループを作成します。これは、Personalizer リソースで使用する予定のすべての Azure リソースを管理するための論理グループです。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 既存のリソース グループに対して次のコマンドを使用して、新しい Personalizer リソースである、"_学習ループ_" を作成します。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    これにより、**リソース エンドポイント** を含む JSON オブジェクトが返されます。

1. 次の Azure CLI コマンドを使用して、**リソース キー** を取得します。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Rank API と Reward API を使用するには、リソース エンドポイントとキーの両方が必要です。

## <a name="next-steps"></a>次のステップ

* Personalizer の学習ループを[構成する](how-to-settings.md)
