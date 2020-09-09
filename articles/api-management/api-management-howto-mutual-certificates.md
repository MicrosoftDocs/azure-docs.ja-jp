---
title: クライアント証明書認証を使用してバックエンド サービスを保護する
titleSuffix: Azure API Management
description: Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法を説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 8e02a47cd6ae6e4883b5113b07d4049cd723232d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250194"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法

API Management には、クライアント証明書を使用して API のバックエンド サービスへのアクセスを保護する機能があります。 このガイドでは、Azure portal の Azure API Management サービス インスタンスで証明書を管理する方法について説明します。 また、証明書を使用してバックエンド サービスにアクセスするように API を構成する方法についても説明します。

API Management REST API を使用して証明書を管理する方法の詳細については、「 <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API 証明書エンティティ</a>」をご覧ください。

## <a name="prerequisites"></a><a name="prerequisites"> </a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このガイドは、クライアント証明書認証を使用して API のバックエンド サービスにアクセスするように、API Management サービス インスタンスを構成する方法を示しています。 この記事の手順を行う前に、クライアント証明書の認証用にバックエンド サービスを構成する必要があります ([Azure App Service で証明書の認証を構成するには、こちらの記事を参照してください][to configure certificate authentication in Azure WebSites refer to this article])。 証明書へのアクセスと、API Management サービスに証明書をアップロードするためのパスワードが必要です。

## <a name="upload-a-certificate"></a><a name="step1"> </a>証明書のアップロード

> [!NOTE]
> アップロードした証明書の代わりに、この[例](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)に示すように、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) サービスに格納されている証明書を使用できます。

![クライアント証明書を追加する](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

新しいクライアント証明書をアップロードするには、次の手順を実行します。 API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][Create an API Management service instance]に関するチュートリアルを参照してください。

1. Azure portal で Azure API Management サービス インスタンスに移動します。
2. 一覧から **証明書** を選択します。
3. **[+ 追加]** ボタンをクリックします。
    ![クライアント証明書を追加する](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. 証明書を参照し、ID とパスワードを入力します。
5. **Create** をクリックしてください。

> [!NOTE]
> 証明書は、 **.pfx** 形式でなければなりません。 自己署名証明書も許可されます。

証明書がアップロードされると、 **[証明書]** に表示されます。  証明書が多数ある場合は、[ゲートウェイ認証にクライアント証明書を使用するように API を構成する][Configure an API to use a client certificate for gateway authentication]ために、必要な証明書の拇印をメモします。

> [!NOTE]
> 自己署名証明書などを使用するときに証明書チェーンの検証をオフにするには、この FAQ の[項目](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)に記載されている手順に従ってください。

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>クライアント証明書の削除

証明書を削除するには、コンテキスト メニューの **[...]** をクリックし、証明書の横にある **[削除]** を選択します。

![クライアント証明書を削除する](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

証明書が API によって使用されている場合、警告画面が表示されます。 証明書を削除するには、最初に、証明書を使用するように構成されたすべての API からそれを削除する必要があります。

![クライアント証明書の削除のエラー](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>API を構成して、ゲートウェイ認証にクライアント証明書を使用する

1. 左側の **[API Management]** メニューの **[API]** をクリックし、API に移動します。
    ![クライアント証明書を有効にする](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. **[デザイン]** タブで、 **[バックエンド]** セクションの鉛筆アイコンをクリックします。
3. **[ゲートウェイ サーバーの資格情報]** を **[クライアント証明書]** に変更し、ドロップダウンから証明書を選択します。
    ![クライアント証明書を有効にする](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. **[保存]** をクリックします。

> [!WARNING]
> この変更は即時に有効になり、その API の操作の呼び出しは、証明書を使用してバックエンド サーバーを認証するようになります。


> [!TIP]
> API のバックエンド サービスのゲートウェイ認証に証明書を指定すると、その API のポリシーに追加され、ポリシー エディターで表示できるようになります。

## <a name="self-signed-certificates"></a>自己署名証明書

自己署名証明書を使用している場合は、API Management がバックエンド システムと通信するために、証明書チェーン検証を無効にする必要があります。 そうしないと、エラー コード 500 が返されます。 これを構成するには、[`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (新しいバック エンド) または[`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (既存のバック エンド) PowerShell コマンドレットを使用して、`-SkipCertificateChainValidation` パラメーターを `True` に設定します。

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
