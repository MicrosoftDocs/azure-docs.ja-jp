---
title: クイック スタート:Azure CLI を使用して Azure API for FHIR をデプロイする
description: このクイックスタートでは、Azure CLI を使用して Azure API for FHIR を Azure にデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: e1aa3362217a4edcfcf547c69b9effde00459f7e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850561"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure API for FHIR をデプロイする

このクイックスタートでは、Azure CLI を使用して Azure API for FHIR を Azure にデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>HealthcareAPIs 拡張機能を追加する

```azurecli-interactive
az extension add --name healthcareapis
```

HealthcareAPIs のコマンド一覧を取得します。

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Azure リソース グループを作成する

Azure API for FHIR の追加先となるリソース グループの名前を選んで、リソース グループを作成します。

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>Azure API for FHIR をデプロイする

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API の機能ステートメントをフェッチする

次のコマンドを使用して FHIR API から機能ステートメントを取得します。

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)
