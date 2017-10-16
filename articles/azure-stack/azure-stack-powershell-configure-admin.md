---
title: "Azure Stack オペレーターの PowerShell 環境の構成 | Microsoft Docs"
description: "Azure Stack オペレーターの PowerShell 環境の構成方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 7e912dcbfd1c745df2a0fc8717a075c0476e8d60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Azure Stack オペレーターの PowerShell 環境の構成

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack オペレーターの場合は、Azure Stack Development Kit の PowerShell 環境を構成できます。 構成後は、PowerShell を使用して、オファー、プラン、クォータの作成、アラートの管理などの Azure Stack リソースを管理できます。このトピックではクラウド オペレーター環境での使用についてのみ扱っています。ユーザー環境用に PowerShell を設定する場合は、[Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)のトピックを参照してください。 

## <a name="prerequisites"></a>前提条件

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。 

* [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  
* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>オペレーター環境の構成と Azure Stack へのサインイン

デプロイのタイプ (Azure AD または AD FS) に基づいて、次のいずれかのスクリプトを実行して PowerShell で Azure Stack オペレーター環境を構成します (環境の構成に従って、必ず AAD tenantName、GraphAudience エンドポイント、および ArmEndpoint の値を置き換えてください)。

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) ベースのデプロイ
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory フェデレーション サービス (AD FS) ベースのデプロイ
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

## <a name="test-the-connectivity"></a>接続をテストする

必要な設定がすべて整ったら、PowerShell を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>次のステップ
* [Azure Stack のテンプレートの開発](user/azure-stack-develop-templates.md)
* [PowerShell を使用したテンプレートのデプロイ](user/azure-stack-deploy-template-powershell.md)