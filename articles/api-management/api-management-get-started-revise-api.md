---
title: チュートリアル - API Management でリビジョンを使用して互換性に影響しない API の変更を安全に行う
titleSuffix: Azure API Management
description: このチュートリアルの手順に従って、API Management でリビジョンを使用して互換性に影響しない変更を行う方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: acb121bb00df481c926ebed9594bf0fe1b9b17ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546637"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>チュートリアル:リビジョンを使用して互換性に影響しない API の変更を安全に行う
API の準備が整って開発者に使用され始めると、その API の変更が必要になることがありますが、API の呼び出し元を混乱させないように注意する必要もあります。 また、行った変更内容を開発者に知らせると有効です。 

変更を安全にモデル化してテストできるように、Azure API Management で "*リビジョン*" を使用して、互換性に影響しない API の変更を行います。 準備ができたら、リビジョンを最新とし、現在の API を置き換えることができます。 

背景については、「[バージョンとリビジョン](https://azure.microsoft.com/blog/versions-revisions/)」および「[Azure API Management を使用した API のバージョン管理](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)」をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリビジョンの追加
> * リビジョンに互換性に影響しない変更を加える
> * リビジョンを最新にして変更ログ エントリを追加する
> * 開発者ポータルを参照して、変更内容と変更ログを確認します。

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Azure portal の API リビジョン":::

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

## <a name="add-a-new-revision"></a>新しいリビジョンの追加

1. [Azure portal](https://portal.azure.com) にサインインし、API Management インスタンスに移動します。
1. **[API]** を選択します。
2. API の一覧で **[Demo Conference API]** (または、リビジョンを追加する別の API) を選択します。
3. **[リビジョン]** タブを選択します。
4. **[+ リビジョンの追加]** を選択します。

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API リビジョンを追加する":::

    > [!TIP]
    > API のコンテキスト メニュー ( **[...]** ) で **[リビジョンの追加]** を選択することもできます。

5. 新しいリビジョンの使用目的を示す説明を入力します。
6. **[作成]** を選択します。
7. これで、新しいリビジョンが作成されました。

    > [!NOTE]
    > 元の API は **リビジョン 1** のままとなります。 別のリビジョンが最新にされない限り、ユーザーはこのリビジョンを呼び出し続けます。

## <a name="make-non-breaking-changes-to-your-revision"></a>リビジョンに互換性に影響しない変更を加える

1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。
1. 画面の上部付近の **[Design] \(デザイン\)** タブを選択します。
1. **リビジョン セレクター** (デザイン タブのすぐ上) に、現在の選択対象として **リビジョン 2** が表示されます。

    > [!TIP]
    > リビジョン セレクターを使用して、作業対象のリビジョンに切り替えます。
1. **[+ Add Operation] \(+ 操作の追加\)** を選択します。
1. 新しい操作を **POST** に設定し、操作の名前、表示名、および URL を **test** に設定します。
1. 新しい操作を **保存** します。

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="リビジョンを変更する":::
1. これで、**リビジョン 2** への変更が完了しました。 ページの上部付近にある **リビジョン セレクター** を使用して、**リビジョン 1** に切り替えます。
1. 新しい操作は **リビジョン 1** には表示さません。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>リビジョンを最新にして変更ログ エントリを追加する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ページの上部付近にあるメニューから **[リビジョン]** タブを選択します。
1. **リビジョン 2** のコンテキスト メニュー (**...**) を開きます。
1. **[これを最新とする]** を選択します。
1. この変更に関するメモを投稿する場合は、 **[Post to Public Change log for this API]\(この API のパブリック変更ログを投稿する\)** チェック ボックスをオンにします。 開発者が参照できるように、変更内容の説明を入力します。たとえば、「**テスト用リビジョン。新しい "test" 操作を追加しました。** 」と入力します。
1. これで **リビジョン 2** が最新となりました。

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="[リビジョン] ウィンドウのリビジョン メニュー":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の使用を開始するには:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

リリースを作成および更新するには、次の手順に従います。

1. [az apim api list](/cli/azure/apim/api#az_apim_api_list) コマンドを実行して、API ID を表示します。

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   次のコマンドで使用する API ID は `Name` 値です。 API リビジョンは `ApiRevision` 列にあります。

1. リリース ノートと共にリリースを作成するには、[az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create) コマンドを実行します。

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   リリースしたリビジョンが最新のリビジョンになります。

1. リリースを表示するには、[az apim api release list](/cli/azure/apim/api/release#az_apim_api_release_list) コマンドを使用します。

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   指定したメモは変更ログに表示されます。 これらは前のコマンドの出力で確認できます。

1. リリースを作成するときに、`--notes` パラメーターは省略可能です。 メモは、[az apim api release update](/cli/azure/apim/api/release#az_apim_api_release_update) コマンドを使用して後で追加または変更できます。

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   リリース ID の `Name` 列の値を使用します。

リリースを削除するには、[az apim api release delete](/cli/azure/apim/api/release#az_apim_api_release_delete) コマンドを実行します。

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>開発者ポータルを参照して、変更内容と変更ログを確認します。

[開発者ポータル](api-management-howto-developer-portal-customize.md)を試したことがあれば、そこで API の変更と変更ログを確認できます。

1. Azure portal で **[API]** を選択します。
1. 上部のメニューから **[開発者ポータル]** を選択します。
1. 開発者ポータルで、 **[API]** を選択し、 **[Demo Conference API]** を選択します。
1. 新しい **test** 操作が使用可能となっています。
1. API 名の近くにある **[変更ログ]** を選択します。
1. この一覧に、変更ログ エントリが表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 新しいリビジョンの追加
> * リビジョンに互換性に影響しない変更を加える
> * リビジョンを最新にして変更ログ エントリを追加する
> * 開発者ポータルを参照して、変更内容と変更ログを確認します。

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [API の複数のバージョンを発行する](api-management-get-started-publish-versions.md)
