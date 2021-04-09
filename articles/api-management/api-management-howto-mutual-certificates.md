---
title: クライアント証明書認証を使用して API Management バックエンドを保護する
titleSuffix: Azure API Management
description: Azure API Management でクライアント証明書認証を使用して証明書の管理とバックエンド サービスの保護を行う方法を説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492844"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する

API Management には、クライアント証明書を使用して API のバックエンド サービスへのアクセスを保護する機能があります。 このガイドでは、Azure portal を使用して Azure API Management サービス インスタンスで証明書を管理する方法について説明します。 また、証明書を使用してバックエンド サービスにアクセスするように API を構成する方法についても説明します。

[API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate)を使用して API Management 証明書を管理することもできます。

## <a name="certificate-options"></a>証明書のオプション

API Management には、バックエンド サービスへのアクセスをセキュリティで保護するために使用される証明書を管理するためのオプションが 2 つあります。

* [Azure Key Vault](../key-vault/general/overview.md) で管理される証明書を参照する 
* API Management で証明書ファイルを直接追加する

API Management のセキュリティ向上に役立つため、キー コンテナー証明書を使用することをお勧めします。

* キー コンテナーに格納されている証明書は、サービス間で再利用できます
* キー コンテナーに格納されている証明書には、きめ細かい[アクセス ポリシー](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies)を適用できます
* キー コンテナーで更新された証明書は、API Management で自動的にローテーションされます。 キー コンテナー内で更新が行われると、4 時間以内に API Management 内の証明書が更新されます。 また、Azure portal または管理 REST API を使用して、証明書を手動で更新することもできます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][Create an API Management service instance]に関するページを参照してください。
* バックエンド サービスがクライアント証明書の認証用にが構成されている必要があります。 Azure App Service で証明書認証を構成する場合は、[この記事][to configure certificate authentication in Azure WebSites refer to this article]を参照してください。 
* Azure キー コンテナーでの管理用の証明書とパスワードにアクセスするか、API Management サービスにアップロードする必要があります。 証明書は、**PFX** 形式でなければなりません。 自己署名証明書も許可されます。

### <a name="prerequisites-for-key-vault-integration"></a>キー コンテナー統合の前提条件

1. キー コンテナーを作成する手順については、「[クイックスタート: Azure portal を使用してキー コンテナーを作成する](../key-vault/general/quick-create-portal.md)」を参照してください。
1. API Management インスタンスで、システムによって割り当てられた、またはユーザーが割り当てた[マネージド ID](api-management-howto-use-managed-service-identity.md) を有効にします。
1. コンテナーから証明書を取得して一覧表示するアクセス許可を持つマネージド ID に、[キー コンテナー アクセス ポリシー](../key-vault/general/assign-access-policy-portal.md)を割り当てます。 ポリシーを追加するには、次の手順を実行します。
    1. ポータルで、キー コンテナーに移動します。
    1. **[設定] > [アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
    1. **[証明書のアクセス許可]** を選択し、次に **[取得]** と **[一覧表示]** を選択します。
    1. **[プリンシパルの選択]** で、マネージド ID のリソース名を選択します。 システムによって割り当てられた ID を使用している場合、プリンシパルは API Management インスタンスの名前です。
1. キー コンテナーに証明書を作成またはインポートします。 「[クイック スタート:Azure portal を使用して Azure Key Vault から証明書の設定と取得を行う](../key-vault/certificates/quick-create-portal.md)」を参照してください。

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>キー コンテナー証明書を追加する

「[キー コンテナー統合の前提条件](#prerequisites-for-key-vault-integration)」を参照してください。

> [!CAUTION]
> API Management でキー コンテナー証明書を使用する場合は、証明書、キー コンテナー、またはキー コンテナーにアクセスするために使用するマネージド ID を削除しないように注意してください。

API Management にキー コンテナー証明書を追加するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[セキュリティ]** の **[証明書]** を選択します。
1. **[証明書]**  >  **[+ 追加]** を選択します。
1. **[ID]** に、希望の名前を入力します。
1. **[証明書]** で、 **[キー コンテナー]** を選択します。
1. キー コンテナー証明書の識別子を入力するか、 **[選択]** を選択してキー コンテナーから証明書を選択します。
    > [!IMPORTANT]
    > キー コンテナー証明書の識別子を自分で入力する場合は、バージョン情報が含まれていないことを確認してください。 そうしないと、キー コンテナーで更新が行われた後に証明書が API Management で自動的にローテーションされません。
1. **[クライアント ID]** で、システムによって割り当てられた、または既存のユーザー割り当てのマネージド ID を選択します。 [API Management サービスでのマネージド ID の追加または変更方法については、こちらを参照してください](api-management-howto-use-managed-service-identity.md)。
    > [!NOTE]
    > ID には、キー コンテナーから証明書を取得および一覧表示するためのアクセス許可が必要です。 キー コンテナーへのアクセスをまだ構成していない場合は、必要なアクセス許可を使用して ID を自動的に構成できるように、API Management によってプロンプトが表示されます。
1. **[追加]** を選択します。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="キー コンテナー証明書の追加":::

## <a name="upload-a-certificate"></a>証明書のアップロード

API Management にクライアント証明書をアップロードするには、次の操作を行います。 

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[セキュリティ]** の **[証明書]** を選択します。
1. **[証明書]**  >  **[+ 追加]** を選択します。
1. **[ID]** に、希望の名前を入力します。
1. **[証明書]** で、 **[カスタム]** を選択します。
1. 証明書 .pfx ファイルを参照して選択し、そのパスワードを入力します。
1. **[追加]** を選択します。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="クライアント証明書のアップロード":::

証明書がアップロードされると、 **[証明書]** ウィンドウに表示されます。 証明書が多数ある場合は、[ゲートウェイ認証](#configure-an-api-to-use-client-certificate-for-gateway-authentication)にクライアント証明書を使用するように API を構成するために、必要な証明書の拇印をメモします。

> [!NOTE]
> 自己署名証明書などを使用するときに証明書チェーンの検証をオフにするには、この記事の後半の「[自己署名証明書](#self-signed-certificates)」に記載されている手順に従ってください。

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>ゲートウェイ認証にクライアント証明書を使用するように API を構成する

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. **[API]** で **[API]** を選択します。
1. 一覧から API を選びます。 
2. **[デザイン]** タブで、 **[バックエンド]** セクションのエディター アイコンを選択します。
3. **[ゲートウェイ資格情報]** で **[クライアント証明書]** を選択し、ドロップダウンから証明書を選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="ゲートウェイ認証にクライアント証明書を使用する":::

> [!CAUTION]
> この変更は即時に有効になり、その API の操作の呼び出しは、証明書を使用してバックエンド サーバーを認証するようになります。

> [!TIP]
> API のバックエンド サービスのゲートウェイ認証に証明書を指定すると、その API のポリシーに追加され、ポリシー エディターで表示できるようになります。

## <a name="self-signed-certificates"></a>自己署名証明書

自己署名証明書を使用している場合は、API Management がバックエンド システムと通信するために、証明書チェーン検証を無効にする必要があります。 そうしないと、エラー コード 500 が返されます。 これを構成するには、[`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (新しいバックエンド) または[`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (既存のバックエンド) PowerShell コマンドレットを使用して、`-SkipCertificateChainValidation` パラメーターを `True` に設定します。

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>クライアント証明書の削除

証明書を削除するには、それを選択して、コンテキスト メニュー ( **...** ) から **[削除]** を選択します。

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="証明書を削除します":::

> [!IMPORTANT]
> 証明書がポリシーによって参照されている場合、警告画面が表示されます。 証明書を削除するには、最初に、証明書を使用するように構成されたすべてのポリシーからそれを削除する必要があります。

## <a name="next-steps"></a>次のステップ

* [API Management でクライアント証明書認証を使用して API を保護する方法](api-management-howto-mutual-certificates-for-clients.md)
* [API Management のポリシー](api-management-howto-policies.md)の詳細


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

