---
title: Azure CLI を使用してアクセス トークンを取得する - Azure API for FHIR
description: この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 7528f9d4e3b3043af1e4790c063eb6ddc6d9a828
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849031"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。 [Azure API for FHIR をプロビジョニングする](fhir-paas-portal-quickstart.md)場合は、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、Azure CLI を使用して取得したトークンを使用して、サービスにアクセスすることができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Azure CLI を使用してサインインする

トークンを取得するには、トークンを取得するユーザーとしてサインインする必要があります。

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>トークンを取得する

Azure API for FHIR は、FHIR サーバーの URI (`https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`) と同じ URI で `resource` または `Audience` を使用します。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Azure API for FHIR での使用

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法を学習しました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。

>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](access-fhir-postman-tutorial.md)
