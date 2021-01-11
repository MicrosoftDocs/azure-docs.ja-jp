---
title: Azure CLI:オープンソースの FHIR Server for Azure をデプロイする - Azure API for Azure
description: このクイックスタートでは、オープンソースの Microsoft FHIR Server for Azure をデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843574"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>クイック スタート:Azure CLI を使用してオープンソースの FHIR サーバーをデプロイする

このクイックスタートでは、Azure CLI を使用して、オープンソースの FHIR&reg; サーバーを Azure にデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>リソース グループの作成

プロビジョニングしたリソースの追加先となるリソース グループの名前を選んで、リソース グループを作成します。

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>テンプレートのデプロイ

Microsoft FHIR Server for Azure の [GitHub リポジトリ](https://github.com/Microsoft/fhir-server)には、必要なリソースをすべてデプロイするテンプレートが存在します。 デプロイには、次のコマンドを使用します。

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>FHIR サーバーが実行されていることを確認する

次のコマンドを使用して FHIR サーバーから機能ステートメントを取得します。

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

サーバーから最初の応答が返されるまでに 1 分ほど時間がかかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ご利用のサブスクリプションに Microsoft Open Source FHIR Server for Azure をデプロイしました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](access-fhir-postman-tutorial.md)
