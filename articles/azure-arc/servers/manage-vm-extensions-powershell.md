---
title: Azure PowerShell を使用して VM 拡張機能を有効にする
description: この記事では、Azure PowerShell を使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 01/05/2021
ms.topic: conceptual
ms.openlocfilehash: 9b1f83ad976aa3471430a912280fac25dc5c5c0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97916186"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell を使用して Azure VM 拡張機能を有効にする

この記事では、Azure PowerShell を使用して、Azure Arc 対応サーバーでサポートされている Azure VM 拡張機能を Linux または Windows ハイブリッド マシンにデプロイおよびアンインストールする方法を示します。

## <a name="prerequisites"></a>前提条件

- Azure PowerShell を搭載するコンピューター 手順については、 [Azure PowerShell のインストールおよび構成](/powershell/azure/)を参照してください。

Azure PowerShell を使用して、Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に `Az.ConnectedMachine` モジュールをインストールする必要があります。 Arc 対応サーバーで次のコマンドを実行します。

`Install-Module -Name Az.ConnectedMachine`.

インストールが完了すると、次のメッセージが返されます。

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>拡張機能を有効にする

Arc 対応サーバーで VM 拡張機能を有効にするには、`-Name`、`-ResourceGroupName`、`-MachineName`、`-Location`、`-Publisher`、-`ExtensionType`、`-Settings` の各パラメーターと共に [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) を使用します。

次の例では、Arc 対応 Linux サーバーで Log Analytics VM 拡張機能を有効にします。

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Arc 対応 Windows サーバーで Log Analytics VM 拡張機能を有効にするには、前の例の `-ExtensionType` パラメーターの値を `"MicrosoftMonitoringAgent"` に変更します。

次の例では、Arc 対応 Linux サーバーでカスタム スクリプト拡張機能を有効にします。

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM 拡張機能 (プレビュー)

> [!WARNING]
> 多くの場合、PowerShell クライアントでは、`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.` エラーを伴って akvvm_service が失敗する原因となる settings.json で、`\` が `"` に追加されます。

次の例では、Arc 対応サーバーで Key Vault VM 拡張機能 (プレビュー) を有効にします。

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Arc 対応サーバー上の VM 拡張機能の一覧を取得するには、`-MachineName` および `-ResourceGroupName` パラメーターと共に [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) を使用します。

例:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>インストールされている拡張機能を削除する

Arc 対応サーバー上の VM 拡張機能を削除するには、`-Name`、`-MachineName`、`-ResourceGroupName` の各パラメーターと共に [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) を使用します。

たとえば、Linux 用の Log Analytics VM 拡張機能を削除するには、次のコマンドを実行します。

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>次のステップ

- [Azure CLI](manage-vm-extensions-cli.md)、[Azure portal](manage-vm-extensions-portal.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。
