---
title: Azure Media Services API にアクセスする Azure AD アプリを PowerShell で作成する | Microsoft Docs
description: PowerShell で Azure Active Directory (Azure AD) アプリを作成し、Azure Media Services API にアクセスするよう設定する方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680135"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Azure Media Services API と共に使用する Azure AD アプリを PowerShell で作成する

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

PowerShell スクリプトを使用して、Azure Media Services リソースにアクセスする Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成する方法について説明します。  

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)で作業を開始してください。 
- Media Services アカウント。 詳細については、[Azure Portal での Azure Media Services アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。

- Azure PowerShell。 詳細については、[Azure PowerShell の使用方法](https://docs.microsoft.com/powershell/azure/overview)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>PowerShell を使用した Azure AD アプリの作成  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

詳細については、次の記事を参照してください。

- [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Azure PowerShell を使用したロールベースのアクセス制御の管理](../../role-based-access-control/role-assignments-powershell.md)
- [証明書を使用してデーモン アプリを手動で構成する方法](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>次の手順

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
