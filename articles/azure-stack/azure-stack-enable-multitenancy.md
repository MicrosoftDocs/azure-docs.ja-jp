---
title: "Azure Stack でのマルチテナントの有効化 | Microsoft Docs"
description: "Azure Stack で複数の Azure Active Directory ディレクトリをサポートする方法を説明します。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3a90057b43e3f2074e72f3d0f896b35b4884368b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Azure Stack でのマルチテナントの有効化

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

複数の Azure Active Directory (Azure AD) テナントのユーザーが Azure Stack のサービスを使用できるように Azure Stack を構成することができます。 ここでは、次のシナリオを例に説明します。

 - あなたは contoso.onmicrosoft.com のサービス管理者です。ここに Azure Stack がインストールされています。
 - メアリーは、ゲスト ユーザーがいる fabrikam.onmicrosoft.com のディレクトリ管理者です。 
 - メアリーの会社は、あなたの会社から IaaS および PaaS サービスを受けており、ゲスト ディレクトリ (fabrikam.onmicrosoft.com) のユーザーが、contoso.onmicrosoft.com にある Azure Stack のリソースにサインインして使用できるようにする必要があります。

このガイドでは、このシナリオに基づいて、Azure Stack でマルチテナントを構成するために必要な手順を説明します。  このシナリオでは、Fabrikam のユーザーが Contoso の Azure Stack デプロイのサービスにサインインして使用できるようにするための手順を、あなたとメアリーがそれぞれ完了する必要があります。  

## <a name="before-you-begin"></a>開始する前に
Azure Stack でマルチテナントを構成する前に、いくつかの前提条件を考慮する必要があります。
  
 - Azure Stack がインストールされているディレクトリ (Contoso) とゲスト ディレクトリ (Fabrikam) の両方に対して、あなたとメアリーが連携して管理上の手順を実行する必要があります。  
 - Azure Stack 用 PowerShell の[インストール](azure-stack-powershell-install.md)と[構成](azure-stack-powershell-configure-admin.md)が済んでいることを確認してください。
 - [Azure Stack Tools をダウンロード](azure-stack-powershell-download.md)して、Connect モジュールと Identity モジュールをインポートします。

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - メアリーは Azure Stack への [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) アクセスが必要です。 

## <a name="configure-azure-stack-directory"></a>Azure Stack ディレクトリの構成
このセクションでは、Fabrikam の Azure AD ディレクトリのテナントからのサインインを許可するように Azure Stack を構成します。

### <a name="onboard-guest-directory-tenant"></a>ゲスト ディレクトリ テナントのオンボード
次に、ゲスト ディレクトリ テナント (Fabrikam) を Azure Stack にオンボードします。  この手順では、ゲスト ディレクトリ テナントのユーザーとサービス プリンシパルを受け入れるように Azure Resource Manager を構成します。

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local"
````



## <a name="configure-guest-directory"></a>ゲスト ディレクトリの構成
あなたが Azure Stack ディレクトリでの手順を完了した後に、メアリーは Azure Stack がゲスト ディレクトリにアクセスすることに同意し、Azure Stack をゲスト ディレクトリに登録する必要があります。 

### <a name="registering-azure-stack-with-the-guest-directory"></a>ゲスト ディレクトリへの Azure Stack の登録
ゲスト ディレクトリの管理者は、Azure Stack が Fabrikam のディレクトリにアクセスすることに同意したら、Azure Stack を Fabrikam のディレクトリ テナントに登録する必要があります。

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName ` 
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>ユーザーをサインインに誘導する
あなたとメアリーの両方が Fabrikam ディレクトリのオンボード手順を完了したので、メアリーは Fabrikam のユーザーをサインインに誘導することができます。  Fabrikam のユーザー (つまり、fabrikam.onmicrosoft.com のサフィックスを持つユーザー) は、https://portal.local.azurestack.external にアクセスしてサインインします。  

メアリーは、Fabrikam ディレクトリの[外部プリンシパル](../active-directory/active-directory-understanding-resource-access.md) (つまり、fabrikam.onmicrosoft.com のサフィックスを持たない Fabrikam ディレクトリのユーザー) はすべて、https://portal.local.azurestack.external/fabrikam.onmicrosoft.com を使用してサインインするよう誘導します。この URL を使用しなかった場合、これらのユーザーは既定のディレクトリ (Fabrikam) に送られ、管理者による同意がないことを示すエラーが表示されます。

## <a name="next-steps"></a>次のステップ

- [委任されたプロバイダーの管理](azure-stack-delegated-provider.md)
- [Azure Stack の主要概念](azure-stack-key-features.md)
