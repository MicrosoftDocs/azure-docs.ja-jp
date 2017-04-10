---
title: "コードから SQL Database にアクセスするアプリケーションを認証するための必要な値を取得する | Microsoft Docs"
description: "コードから SQL Database にアクセスするためのサービス プリンシパルを作成します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
tags: 
ms.assetid: b43e43bb-6660-49e6-b069-abde97eb5770
ms.service: sql-database
ms.custom: development
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 6fb71859d0ba2e0f2b39d71edd6d518b7a03bfe9
ms.openlocfilehash: 321b1630680f8bd4271f863b2cbe39be1a00cb89


---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>コードから SQL Database にアクセスするアプリケーションを認証するための必要な値を取得する
コードから SQL Database を作成および管理するには、Azure リソースを作成したサブスクリプションの Azure Active Directory (AAD) ドメインにアプリを登録する必要があります。

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>アプリケーションからリソースにアクセスするためのサービス プリンシパルの作成
最新の [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) をインストールして実行する必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

次の PowerShell スクリプトを実行すると、Active Directory (AD) アプリケーションのほか、C# アプリの認証に必要なサービス プリンシパルが作成されます。 このスクリプトによって、上記の C# のサンプルに必要な値が出力されます。 詳細については、「 [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md)」を参照してください。

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>関連項目
* [C# を使用した SQL Database の作成](sql-database-get-started-csharp.md)
* [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)




<!--HONumber=Feb17_HO3-->


