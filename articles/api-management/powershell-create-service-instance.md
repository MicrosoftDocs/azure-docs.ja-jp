---
title: クイック スタート - PowerShell を使用した Azure API Management インスタンスの作成 | Microsoft Docs
description: Azure PowerShell を使用して新しい Azure API Management インスタンスを作成します。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90707067"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>クイック スタート」を参照してください。PowerShell を使用して新しい Azure API Management サービス インスタンスを作成する

API Management (APIM) が組織にもたらす利点は、外部、パートナー、社内の開発者に API を公開することによって、社内のデータやサービスの可能性を広げられることです。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 APIM を使用すると、任意の場所でホストされている既存のバックエンド サービスの最新の API ゲートウェイを作成し、管理できます。 詳細については、[概要](api-management-key-concepts.md)に関するページを参照してください。

このクイック スタートでは、Azure PowerShell コマンドレットを使用して新しい API Management インスタンスを作成する手順を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使う場合、このチュートリアルでは Azure PowerShell モジュールのバージョン 1.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次のコマンドは、*myResourceGroup* という名前のリソース グループを米国西部の場所に作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management サービスの作成

リソース グループを作成したので、API Management サービス インスタンスを作成できます。 [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) を使用して作成し、サービス名と発行者の詳細を指定します。 サービス名は Azure 内で一意になっている必要があります。

次の例では、*myapim* をサービス名として使用します。 名前を一意の値に更新します。 また、API パブリッシャーの組織名と、通知を受信するための管理者の電子メール アドレスを更新します。

既定では、このコマンドは、Azure API Management を評価するための経済的なオプションである Developer レベルにインスタンスを作成します。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。

> [!NOTE]
> この操作の実行には時間がかかります。 このサービス レベルに API Management サービスを作成してアクティブにするには、30 分から 40 分かかります。

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

コマンドが終了したら、[Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) を実行して、Azure API Management サービスのプロパティを表示します。 アクティブ化が完了すると、プロビジョニングの状態が [成功] になり、サービス インスタンスに複数の URL が関連付けられます。 次に例を示します。

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

出力例:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

API Management サービス インスタンスがデプロイされると、使用する準備ができています。 [最初の API をインポートして発行する](import-and-publish.md)チュートリアルを開始します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の API をインポートして発行する](import-and-publish.md)
