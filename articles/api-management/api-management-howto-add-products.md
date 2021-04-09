---
title: チュートリアル - Azure API Management で製品を作成して発行する
description: このチュートリアルでは、Azure API Management で製品を作成して発行します。 製品が発行されると、開発者は製品の API の利用を開始できます。
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545158"
---
# <a name="tutorial-create-and-publish-a-product"></a>チュートリアル:製品を作成して発行する  

Azure API Management の "[*製品*](api-management-terminology.md#term-definitions)" には、少なくとも 1 つの API に加え、使用量クォータや利用規約が含まれます。 製品が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="ポータルにおける API Management の製品":::


## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>製品を作成して発行する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal にサインインして、API Management インスタンスに移動します。
1. 左側のナビゲーションで **[製品]**  >  **[+ 追加]** を選択します。
1.  **[製品の追加]** ウィンドウで、以下の表に記載されている値を入力して製品を作成します。

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="ポータルで製品を追加する":::

    | 名前                     | 説明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 表示名             | [開発者ポータル](api-management-howto-developer-portal.md)で表示する名前です。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 説明              | 製品の目的、アクセスできる API、その他の詳細など、製品に関する情報を入力します。                                                                                                                                               |
    | State                    | 製品を発行する場合は **[発行済み]** を選択します。 製品に含まれる API を呼び出すには、あらかじめ製品を発行しておく必要があります。 新しい製品は、既定では発行されず、**Administrators** グループのみに表示されます。                                                                                      |
    | サブスクリプションを要求する    | ユーザーが製品を使用するにはサブスクリプションが必要である場合はオンにします。                                                                                                                                                                                                                                   |
    | 承認が必要        | この製品に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合はオンにします。 オフの場合、サブスクリプションの申し込みは自動承認されます。                                                                                                                         |
    | サブスクリプション数の制限 | 必要に応じて、複数同時に利用できるサブスクリプションの数を制限します。                                                                                                                                                                                                                                |
    | 法的条項              | サブスクライバーが製品を使用するにあたって同意する必要がある製品の使用条件を入力できます。                                                                                                                                                                                                             |
    | API                     | 1 つまたは複数の API を選択します。 製品の作成後に API を追加することもできます。 詳細については、「[製品への API の追加](#add-apis-to-a-product)」を参照してください。 |

3. **[作成]** を選択して新しい製品を作成します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

製品を作成するには、[az apim product create](/cli/azure/apim/product#az_apim_product_create) コマンドを実行します。

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

作成した製品にさまざまな値を指定できます。

   | パラメーター | 説明 |
   |-----------|-------------|
   | `--product-name` | [開発者ポータル](api-management-howto-developer-portal.md)で表示する名前です。 |
   | `--description`  | 製品の目的、アクセスできる API、その他の詳細など、製品に関する情報を入力します。 |
   | `--state`        | 製品を発行する場合は **[発行済み]** を選択します。 製品に含まれる API を呼び出すには、あらかじめ製品を発行しておく必要があります。 新しい製品は、既定では発行されず、**Administrators** グループのみに表示されます。 |
   | `--subscription-required` | ユーザーが製品を使用するにはサブスクリプションが必要である場合はオンにします。 |
   | `--approval-required` | この製品に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合はオンにします。 オフの場合、サブスクリプションの申し込みは自動承認されます。 |
   | `--subscriptions-limit` | 必要に応じて、複数同時に利用できるサブスクリプションの数を制限します。|
   | `--legal-terms`         | サブスクライバーが製品を使用するにあたって同意する必要がある製品の使用条件を入力できます。 |

現在の製品を表示するには、[az apim product list](/cli/azure/apim/product#az_apim_product_list) コマンドを使用します。

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

製品を削除するには、[az apim product delete](/cli/azure/apim/product#az_apim_product_delete) コマンドを使用します。

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>構成をさらに追加する

製品を保存した後、続けてその構成を行います。 API Management インスタンスで、 **[製品]** ウィンドウから製品を選択します。 次の項目を追加または更新します。

|アイテム   |説明  |
|---------|---------|
|設定     |    製品のメタデータと状態     |
|API     |  製品に関連付けられている API       |
|[ポリシー](api-management-howto-policies.md)     |  製品の API に適用されるポリシー      |
|アクセス制御     |  開発者またはゲストに向けた製品の可視性       |
|[サブスクリプション](api-management-subscriptions.md)    |    製品のサブスクライバー     |

## <a name="add-apis-to-a-product"></a>製品への API の追加

製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 製品の作成中、既存の API を追加することができます。複数の API を追加することも可能です。 製品には後から API を追加できます。 **[設定]** ページを使用して追加することも、API の作成時に追加することもできます。

開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

### <a name="add-an-api-to-an-existing-product"></a>既存の製品に API を追加する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. API Management インスタンスの左側のナビゲーションで **[製品]** を選択します。
1. 製品を選択し、 **[API]** を選択します。
1. **[+ 追加]** を選択します。
1. 少なくとも 1 つの API を選択し、 **[選択]** を選択します。

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="既存の製品に API を追加する":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. マネージド API を表示するには、[az apim api list](/cli/azure/apim/api#az_apim_api_list) コマンドを使用します。

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. 製品に API を追加するには、[az apim product api add](/cli/azure/apim/product/api#az_apim_product_api_add) コマンドを実行します。

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. 追加を確認するには、[az apim product api list](/cli/azure/apim/product/api#az_apim_product_api_list) コマンドを使用します。

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

製品から API を削除するには、[az apim product api delete](/cli/azure/apim/product/api#az_apim_product_api_delete) コマンドを使用します。

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) または PowerShell コマンドを通じてカスタム サブスクリプション キーを使用して、製品へのユーザーのサブスクリプションを作成または更新できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [空の API とAPI のモック応答を作成する](mock-api-responses.md)
