---
title: "Azure Media Services API にアクセスする Azure AD アプリを PowerShell で作成する | Microsoft Docs"
description: "PowerShell で Azure Active Directory (Azure AD) アプリを作成し、Azure Media Services API にアクセスするよう設定する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: f1d7ad94686d633b6353f39cef91d1cb1efab773
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---

# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Azure Media Services API と共に使用する Azure AD アプリを PowerShell で作成する

PowerShell スクリプトを使用して、Azure Media Services リソースにアクセスする Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成する方法について説明します。  

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 アカウントをお持ちでない場合は、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)で作業を開始してください。 
- Media Services アカウント。 詳細については、[Azure Portal での Azure Media Services アカウントの作成](media-services-portal-create-account.md)に関するページを参照してください。
- Azure PowerShell バージョン 0.8.8 以降。 詳細については、[Azure PowerShell の使用方法](https://docs.microsoft.com/powershell/azure/overview)に関するページを参照してください。
- Azure Resource Manager コマンドレット。  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>PowerShell を使用した Azure AD アプリの作成  

```powershell
Login-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

詳細については、次の記事を参照してください。

- [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Azure PowerShell を使用したロールベースのアクセス制御の管理](../active-directory/role-based-access-control-manage-access-powershell.md)
- [証明書を使用してデーモン アプリを手動で構成する方法](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。

