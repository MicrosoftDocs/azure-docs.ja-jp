---
title: チュートリアル - Azure API Management を使用して API の複数のバージョンを発行する
description: このチュートリアルの手順に従って、API Management で API の複数のバージョンを発行する方法を学びます。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: dc3d3b4658bd9a26363b6d41377d448059e30c0f
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889673"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>チュートリアル:API の複数のバージョンを発行する 

API のすべての呼び出し元がまったく同じバージョンを使用するのは実用的ではない場合があります。 呼び出し元は、新しいバージョンにアップグレードするときに、わかりやすいアプローチを求めています。 このチュートリアルで示すように、Azure API Management で複数の "*バージョン*" を提供できます。 

背景については、「[バージョンとリビジョン](https://azure.microsoft.com/blog/versions-revisions/)」をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 既存の API に新しいバージョンを追加する
> * バージョン スキームを選択する
> * バージョンを製品に追加する
> * 開発者ポータルを参照してバージョンを確認する

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Azure portal に表示されたバージョン":::

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、「[Import and publish your first API (最初の API をインポートして発行する)](import-and-publish.md)」のチュートリアルも完了します。

## <a name="add-a-new-version"></a>新しいバージョンを追加する

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[API]** を選択します。
1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。 
1. **[Demo Conference API]** の横のコンテキスト メニュー ( **[...]** ) を選択します。
1. **[バージョンの追加]** を選択します。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API コンテキスト メニュー - [バージョンの追加]":::


> [!TIP]
> 新しい API を作成するときに、複数のバージョンを有効にすることもできます。 **[API の追加]** 画面で、 **[この API をバージョン管理しますか?]** を選択します。

## <a name="choose-a-versioning-scheme"></a>バージョン管理スキームを選択する

Azure API Management では、"*バージョン管理スキーム*" (**パス、ヘッダー**、または **クエリ文字列**) を選択して、呼び出し元が API バージョンを指定する方法を選択します。 次の例では、バージョン管理スキームとして "*パス*" を使用しています。

以下の表の値を入力します。 次に、 **[作成]** を選択してバージョンを作成します。

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="バージョンの追加ウィンドウ":::



|設定   |値  |説明  |
|---------|---------|---------|
|**名前**     |  *demo-conference-api-v1*       |  API Management インスタンスでの一意の名前。<br/><br/>バージョンは、実際には API の[リビジョン](api-management-get-started-revise-api.md)に基づく新しい API であるため、この設定は新しい API の名前になります。   |
|**バージョン管理スキーム**     |  **パス**       |  呼び出し元が API バージョンを指定する方法。     |
|**バージョン識別子**     |  *v1*       |  バージョンのスキーム固有のインジケーター。 **[パス]** の場合、API の URL パスのサフィックスです。 <br/><br/> **[ヘッダー]** または **[クエリ文字列]** を選択した場合は、追加の値 (ヘッダーの名前またはクエリ文字列パラメーター) を入力します。<br/><br/> 使用例が表示されます。        |
|**成果物**     |  **無制限**       |  (省略可能) API バージョンが関連付けられている 1 つ以上の製品。 API を発行するには、API を成果物に関連付ける必要があります。 後で[バージョンを製品に追加](#add-the-version-to-a-product)することもできます。      |

バージョンを作成すると、API 一覧の **[Demo Conference API]** の下に表示されます。 2 つの API (**オリジナル** と **v1**) が表示されています。

![Azure Portal の API の下に表示された複数のバージョン](media/api-management-getstarted-publish-versions/version-list.png)

**オリジナル** とは別の API として、**v1** を編集し、構成できます。 あるバージョンの変更が別のバージョンに影響することはありません。

> [!Note]
> バージョン管理されていない API にバージョンを追加すると、**オリジナル** も自動的に作成されます。 このバージョンは既定の URL で応答します。 オリジナル バージョンを作成することで、バージョンを追加するプロセスによって、既存のどの呼び出し元も中断されなくなります。 最初に複数のバージョンを有効にして新しい API を作成した場合には、オリジナルは作成されません。

## <a name="add-the-version-to-a-product"></a>バージョンを製品に追加する

呼び出し元が新しいバージョンを表示するためには、それが *製品* に追加されている必要があります。 バージョンを製品にまだ追加していない場合は、いつでも製品に追加できます。

たとえば、バージョンを **無制限** の製品に追加するには、次の手順に従います。
1. Azure portal で、API Management インスタンスに移動します。
1. **[製品]**  >  **[無制限]**  >  **[API]**  >  **[+ 追加]** の順に選択します。
1. **Demo Conference API** のバージョン **v1** を選択します。
1. **[選択]** をクリックします。

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="バージョンを製品に追加する":::

## <a name="use-version-sets"></a>バージョン セットを使用する

複数のバージョンを作成すると、Azure portal によって "*バージョン セット*" が作成されます。これは、1 つの論理的な API の一連のバージョンを表します。 複数のバージョンがある API の名前を選択します。 Azure portal に、その **バージョン セット** が表示されます。 バージョン セットの **名前** と **説明** はカスタマイズできます。

Azure CLI を使用すると、バージョン セットを直接操作できます。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

すべてのバージョン セットを表示するには、[az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) コマンドを実行します。

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Azure portal によってバージョン セットが自動的に作成されると、英数字の名前が割り当てられ、一覧の **[名前]** 列に表示されます。 他の Azure CLI コマンドでこの名前を使用します。

バージョン セットの詳細を表示するには、[az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) コマンドを実行します。

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

バージョン セットの詳細については、「[Azure API Management のバージョン](api-management-versions.md#how-versions-are-represented)」をご覧ください。

## <a name="browse-the-developer-portal-to-see-the-version"></a>開発者ポータルを参照してバージョンを確認する

[開発者ポータル](api-management-howto-developer-portal-customize.md)を試したことがあれば、そこで API バージョンを確認できます。

1. 上部のメニューから **[開発者ポータル]** を選択します。
2. **[API]** を選択してから、**[Demo Conference API]\(デモ会議 API\)** を選択します。
3. API 名の横に複数のバージョンのドロップダウンが表示されます。
4. **[v1]** を選択します。
5. 一覧で最初の操作の **[要求 URL]** を確認します。 API の URL パスに "**v1**" が含まれています。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 既存の API に新しいバージョンを追加する
> * バージョン スキームを選択する 
> * バージョンを製品に追加する
> * 開発者ポータルを参照してバージョンを確認する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [開発者ポータルのページのスタイルをカスタマイズする](api-management-howto-developer-portal-customize.md)
