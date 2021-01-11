---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dbdebb1a9fc31e225349cc7fca8366e8b8ed004
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378553"
---
クラウドベースのアプリケーションの認証を行うための最も簡単な方法は、マネージド ID を使用することです。詳細については、[Key Vault に対する認証](/azure/key-vault/general/authentication)に関するページを参照してください。

ただし簡潔にするために、このクイックスタートではデスクトップ アプリケーションを作成します。デスクトップ アプリケーションでは、サービス プリンシパルとアクセス制御ポリシーを使用する必要があります。 サービス プリンシパルは、"http://&lt;my-unique-service-principal-name&gt;" の形式で一意の名前にする必要があります。

Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name>" --sdk-auth
```

この操作では、一連のキーと値のペアが返されます。

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
