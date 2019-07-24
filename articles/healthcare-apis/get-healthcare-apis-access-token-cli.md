---
title: Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する
description: この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mihansen
ms.openlocfilehash: a847a3f7d8fbcc8c54b8f6ff4050e5c44f8beeef
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "58408450"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。 [Azure API for FHIR をプロビジョニングする](fhir-paas-portal-quickstart.md)場合は、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、Azure CLI を使用して取得したトークンを使用して、サービスにアクセスすることができます。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-with-azure-cli"></a>Azure CLI を使用してサインインする

トークンを取得するには、トークンを取得するユーザーとしてサインインする必要があります。

```azurecli-interactive
az login
```

## <a name="obtain-a-token"></a>トークンを取得する

Azure API for FHIR は、URI `https://azurehealthcareapis.com` で `resource` または `Audience` を使用します。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。

```azurecli-interactive
token=$(az account get-access-token --resource=https://azurehealthcareapis.com | jq -r .accessToken)
```

## <a name="use-with-azure-api-for-fhir"></a>Azure API for FHIR での使用

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>次の手順

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法を学習しました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。

>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](access-fhir-postman-tutorial.md)