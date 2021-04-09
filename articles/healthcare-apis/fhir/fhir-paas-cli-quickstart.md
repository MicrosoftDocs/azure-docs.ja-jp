---
title: クイック スタート:Azure CLI を使用して Azure API for FHIR をデプロイする
description: このクイックスタートでは、Azure CLI を使用して Azure API for FHIR を Azure にデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10bc0ac3a61ddba22dea46dfdd47a0305e2d9149
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018649"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure API for FHIR をデプロイする

このクイックスタートでは、Azure CLI を使用して Azure API for FHIR を Azure にデプロイする方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

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

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。 Azure API for FHIR を使い始める準備ができたら、アプリケーションの登録方法についての詳しい情報をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[アプリケーションの登録の概要](fhir-app-registration.md)
