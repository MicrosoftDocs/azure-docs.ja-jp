---
title: Azure API Management バックエンド | Microsoft Docs
description: API Management のカスタム バックエンドについて説明します
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99499427"
---
# <a name="backends-in-api-management"></a>API Management のバックエンド

API Management の "*バックエンド*" (つまり "*API バックエンド*") は、フロントエンド API とその操作を実装する HTTP サービスです。

特定の API をインポートすると、API Management によって API バックエンドが自動的に構成されます。 たとえば、[OpenAPI 仕様](import-api-from-oas.md)、[SOAP API](import-soap-api.md)、または Azure リソース (HTTP によってトリガーされた [Azure Functions アプリ](import-function-app-as-api.md)または [Logic Apps](import-logic-app-as-api.md) など) をインポートすると、API Management によってバックエンドが構成されます。

API Management では、[Service Fabric クラスター](how-to-configure-service-fabric-backend.md)やカスタム サービスなどの他の Azure リソースを API バックエンドとして使用することもサポートされています。 これらのカスタム バックエンドを使用するには、たとえば、バックエンド サービスへの要求の資格情報を承認したり、API 操作を定義したりするための追加の構成が必要になります。 これらのバックエンドは、Azure portal で、または Azure API やツールを使用して構成および管理します。

バックエンドを作成すると、お使いの API でバックエンド URL を参照できるようになります。 受信 API 要求をその API の既定のバックエンドではなく、カスタム バックエンドにリダイレクトするには、[`set-backend-service`](api-management-transformation-policies.md#SetBackendService) ポリシーを使用します。

## <a name="benefits-of-backends"></a>バックエンドの利点

カスタム バックエンドには、次のようないくつかの利点があります。

* バックエンド サービスに関する情報を抽象化し、API 間での再利用性を促進し、ガバナンスを強化します  
* 既存の API で変換ポリシーを構成することによって簡単に使用できます
* ヘッダーまたはクエリ パラメーターの認証用に[名前付きの値](api-management-howto-properties.md)が構成されている場合に、API Management の機能を利用して Azure Key Vault でシークレットを維持できます

## <a name="next-steps"></a>次のステップ

* Azure portal を使用して [Service Fabric バックエンド](how-to-configure-service-fabric-backend.md)を設定します。
* バックエンドは、API Management [REST API](/rest/api/apimanagement)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)、または [Azure Resource Manager テンプレート](../service-fabric/service-fabric-tutorial-deploy-api-management.md)を使用して構成することもできます。

