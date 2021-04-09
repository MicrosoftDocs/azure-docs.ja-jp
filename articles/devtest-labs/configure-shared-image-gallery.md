---
title: Azure DevTest Labs での共有イメージ ギャラリーの構成 | Microsoft Docs
description: Azure DevTest Labs で、ラボのリソースの作成時に、共有の場所からユーザーがイメージにアクセスできるよう、共有イメージ ギャラリーを構成する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: febcff640efc29eb4916250366641635f9d8721e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788423"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs での共有イメージ ギャラリーの構成
DevTest Labs で[共有イメージ ギャラリー](../virtual-machines/shared-image-galleries.md)機能がサポートされるようになりました。 ラボ ユーザーは、ラボ リソースの作成中に共有の場所からイメージにアクセスできます。 また、カスタム マネージド VM イメージに関連する構造および組織を構築できます。 共有イメージ ギャラリー機能では、次がサポートされています。

- イメージのマネージド グローバル レプリケーション
- 容易な管理のためのイメージのバージョン管理とグループ化
- 可用性ゾーンをサポートするリージョンでゾーン冗長ストレージ (ZRS) アカウントを使用して、イメージの可用性を高めます。 ZRS では、ゾーンの障害に対する回復性の向上が提供されます。
- Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、サブスクリプション間やテナント間で共有します。

詳細については、[共有イメージ ギャラリーのドキュメント](../virtual-machines/shared-image-galleries.md)を参照してください。 
 
メンテナンスが必要なマネージド イメージや社内全体で利用可能にしたいマネージド イメージが多数ある場合は、イメージを簡単に更新して共有できるリポジトリとして、共有イメージ ギャラリーを使用できます。 ラボ所有者は、既存の共有イメージ ギャラリーをラボにアタッチできます。 このギャラリーがアタッチされると、ラボ ユーザーはこれらの最新のイメージからマシンを作成できます。 この機能の主な利点は、DevTest Labs で共有イメージのメリットをラボ間、サブスクリプション間、リージョン間で利用できることです。 

> [!NOTE]
> Shared Image Gallery サービスに関連するコストについては、[Shared Image Gallery の料金](../virtual-machines/shared-image-galleries.md#billing)に関するページを参照してください。

## <a name="considerations"></a>考慮事項
- ラボにアタッチできる共有イメージ ギャラリーは一度に 1 つだけです。 別のギャラリーをアタッチする場合は、既存のものをデタッチして別のものをアタッチする必要があります。 
- DevTest Labs は現在、ラボを介したギャラリーへのイメージのアップロードをサポートしていません。 
- 共有イメージ ギャラリーのイメージを使用して仮想マシンを作成している間、DevTest Labs は常に、このイメージの最も新しく公開されたバージョンを使用します。 ただし、イメージに複数のバージョンがある場合、ユーザーは、仮想マシンの作成時に [詳細設定] タブに移動して、以前のバージョンからコンピューターを作成することもできます。  
- ラボが存在するリージョンに共有イメージ ギャラリーがイメージをレプリケートすることを確実にするために、DevTest Labs は自動的に最善の試みをしますが、それが常に可能とは限りません。 これらのイメージから VM を作成するときにユーザーが問題に遭遇しないよう、イメージがラボのリージョンにレプリケート済みであることを確認してください。

## <a name="use-azure-portal"></a>Azure Portal の使用
1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。
1. 一覧で **[DevTest Labs]** を選択します。
1. ラボの一覧で、目的の **ラボ** を選択します。
1. 左側のメニューの **[設定]** セクションで、 **[構成とポリシー]** を選択します。
1. 左側のメニューで、 **[仮想マシンのベース]** の下にある **[Shared Image Galleries]\(共有イメージ ギャラリー\)** を選択します。

    ![[Shared Image Galleries]\(共有イメージ ギャラリー\) メニュー](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **[Attach]\(アタッチ\)** ボタンをクリックしてドロップダウンからギャラリーを選択することによって、既存の共有イメージ ギャラリーをラボにアタッチします。

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. イメージ ギャラリーがアタッチされたら、選択してアタッチされたギャラリーに移動します。 VM 作成用に共有イメージを **有効化または無効化** するようギャラリーを構成します。 リストからイメージ ギャラリーを選択して構成します。 

    既定では、 **[すべてのイメージを仮想マシン ベースとして使用できるようにする]** が **[はい]** に設定されています。 つまり、アタッチされた共有イメージ ギャラリーにあるすべてのイメージは、ラボ ユーザーが新しいラボ VM を作成する際に使用できるということです。 特定のイメージへのアクセスを制限する必要がある場合は、 **[すべてのイメージを仮想マシン ベースとして使用できるようにする]** を **[いいえ]** に変更し、VM の作成時に許可したいイメージを選択したうえで、 **[保存]** ボタンを選択してください。

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="イメージを有効または無効にする":::

    > [!NOTE]
    > 共有イメージ ギャラリーにある一般化されたイメージと特殊化されたイメージの両方がサポートされています。 
1. 以後、ラボ ユーザーは、 **[+ 追加]** をクリックすることで、有効化されたイメージを使用して仮想マシンを作成できます。また、 **[choose your base]\(ベースの選択\)** ページでイメージを検索できます。

    ![ラボ ユーザー](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>共有イメージ ギャラリーをラボにアタッチする
Azure Resource Manager テンプレートを使用して共有イメージ ギャラリーをラボにアタッチする場合、次の例に示すように、Resource Manager テンプレートの resources セクションの下にそれを追加する必要があります。

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

完全な Resource Manager テンプレートの例については、パブリックの GitHub リポジトリにある次の Resource Manager テンプレート サンプルを参照してください。[Configure a shared image gallery while creating a lab\(ラボの作成中に共有イメージ ギャラリーを構成する\)](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-rest-api"></a>REST API を使用する

### <a name="get-a-list-of-labs"></a>ラボの一覧を取得する 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>ラボに関連付けられている共有イメージ ギャラリーの一覧を取得する

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>共有イメージ ギャラリーを作成または更新する

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>共有イメージ ギャラリー内のイメージを一覧表示する

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>次のステップ
アタッチされている共有イメージ ギャラリーのイメージを使用して VM を作成する方法については、次の記事を参照してください。[ギャラリーから共有イメージを使用して VM を作成する](add-vm-use-shared-image.md)