---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648917"
---
#### <a name="enable-app-service-authenticationauthorization"></a>App Service の認証/承認の有効化

App Service プラットフォームでは、Azure Active Directory (AAD) といくつかのサード パーティの ID プロバイダーを使用してクライアントを認証することができます。 この方法を使用して、関数のカスタム認可ルールを実装し、関数コードのユーザー情報を操作できます。 詳細については、「[Azure App Service での認証および認可](../articles/app-service/overview-authentication-authorization.md)」および「[クライアント ID の操作](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)」を参照してください。

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Azure API Management (APIM) を使用して要求を認証する

APIM では、受信要求用のさまざまな API のセキュリティ オプションを提供します。 詳細については、 [API Management の認証ポリシー](../articles/api-management/api-management-authentication-policies.md)を参照してください。 APIM を適切に配置すると、APIM インスタンスの IP アドレスからの要求のみを受け入れるように関数アプリを設定できます。 詳細については、[IP アドレス制限](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)を参照してください。
