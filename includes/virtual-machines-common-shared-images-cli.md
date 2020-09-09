---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b49dc6ef2bfee311bc3ca524a5ccb0a4e4b5ca9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793633"
---
## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 

ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。   ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

[az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* という名前のリソース グループを "*米国東部*" に作成し、*myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>ギャラリーを共有する

ロールベースのアクセス制御 (RBAC) を使用して、サブスクリプション全体でイメージを共有できます。 イメージは、ギャラリー、イメージ定義、またはイメージ バージョンのレベルで共有できます。 イメージ バージョンへの読み取りアクセス許可があるユーザーは、サブスクリプション間でも、そのイメージ バージョンを使用して VM をデプロイできます。

他のユーザーとは、ギャラリー レベルで共有することをお勧めします。 ギャラリーのオブジェクト ID を取得するには、[az sig show](/cli/azure/sig#az-sig-show) を使用します。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

電子メール アドレスおよび [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) と共に、オブジェクト ID をスコープとして使用して、ユーザーに共有イメージ ギャラリーへのアクセスを付与します。 `<email-address>` と `<gallery iD>` は、実際の情報に置き換えてください。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC を使用してリソースを共有する方法の詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)」を参照してください。
