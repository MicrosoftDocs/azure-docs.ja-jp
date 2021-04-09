---
title: Azure CLI を使用して VM 拡張機能を有効にする
description: この記事では、Azure CLI を使用して、ハイブリッド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6edb7d55e542f963c75693d535fa3b50dc5b827b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916203"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI を使用して Azure VM 拡張機能を有効にする

この記事では、Azure CLI を使用して、Azure Arc 対応サーバーでサポートされている VM 拡張機能を Linux または Windows ハイブリッド マシンにデプロイおよびアンインストールする方法を示します。

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

ConnectedMachine コマンドは、Azure CLI の一部として付属していません。 Azure CLI を使用して、Arc 対応サーバーで管理されているハイブリッド サーバー上の VM 拡張機能を管理するには、事前に ConnectedMachine 拡張機能を読み込む必要があります。 次のコマンドを実行して取得します。

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>拡張機能を有効にする

Arc 対応サーバーで VM 拡張機能を有効にするには、`--machine-name`、`--extension-name`、`--location`、`--type`、`settings`、`--publisher` の各パラメーターと共に [az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) を使用します。

次の例では、Arc 対応サーバーで Log Analytics VM 拡張機能を有効にします。

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

次の例では、Arc 対応 Linux サーバーでカスタム スクリプト拡張機能を有効にします。

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

次の例では、Arc 対応サーバーで Key Vault VM 拡張機能 (プレビュー) を有効にします。

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Arc 対応サーバー上の VM 拡張機能の一覧を取得するには、`--machine-name` および `--resource-group` パラメーターと共に [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) を使用します。

例:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

既定では、Azure CLI コマンドの出力形式は JSON (JavaScript Object Notation) です。 既定の出力をリストまたはテーブルなどに変更するには、[az configure --output](/cli/azure/reference-index) を使用します。 出力形式で 1 回のみ変更するために `--output` を任意のコマンドに追加することもできます。

次の例に、`az connectedmachine extension -list` コマンドの JSON 形式の出力の一部を示します。

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>インストールされている拡張機能を削除する

Arc 対応サーバーにインストールされている VM 拡張機能を削除するには、`--extension-name`、`--machine-name`、`--resource-group` の各パラメーターと共に [az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) を使用します。

たとえば、Linux 用の Log Analytics VM 拡張機能を削除するには、次のコマンドを実行します。

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

- [Azure portal](manage-vm-extensions-portal.md) で [Azure PowerShell](manage-vm-extensions-powershell.md) を使用するか、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。

- コマンドの詳細については、Azure CLI VM 拡張機能の[概要](/cli/azure/ext/connectedmachine/connectedmachine/extension)の記事を参照してください。
