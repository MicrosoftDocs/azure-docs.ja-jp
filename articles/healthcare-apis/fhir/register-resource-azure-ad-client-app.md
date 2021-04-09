---
title: Azure AD へのリソース アプリの登録 - Azure API for FHIR
description: リソース (つまり API) アプリを Azure Active Directory に登録して、クライアント アプリケーションが認証時にリソースへのアクセスを要求できるようにします。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8d70a7b44893ba9c9a0cc2d1d01c65e8e1584e0f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018993"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Azure Active Directory にリソース アプリケーションを登録する

この記事では、リソース (または API) アプリケーションを Azure Active Directory に登録する方法について説明します。 リソース アプリケーションは、FHIR サーバー API 自体を Azure Active Directory で表したもので、クライアント アプリケーションは、認証時にリソースへのアクセスを要求できます。 リソース アプリケーションは、OAuth の用語で *audience* とも呼ばれます。

## <a name="azure-api-for-fhir"></a>FHIR 用の Azure API

Azure API for FHIR を使用している場合、リソース アプリケーションはサービスのデプロイ時に自動的に作成されます。 アプリケーションをデプロイするときと同じ Azure Active Directory テナントで Azure API for FHIR を使用している限り、この攻略ガイドをスキップし、代わりに Azure API for FHIR をデプロイして開始することができます。

(サブスクリプションに関連付けられていない) 別の Azure Active Directory テナントを使用している場合は、PowerShell を使用して Azure API for FHIR リソース アプリケーションをテナントにインポートできます。

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

または、Azure CLI を使用することもできます。

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR Server for Azure

オープン ソースの FHIR Server for Azure を使用している場合は、[GitHub リポジトリ](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md)の手順に従って、リソース アプリケーションを登録します。 

## <a name="next-steps"></a>次のステップ

この記事では、リソース アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、機密クライアント アプリケーションを登録します。
 
>[!div class="nextstepaction"]
>[機密クライアント アプリケーションを登録する](register-confidential-azure-ad-client-app.md)