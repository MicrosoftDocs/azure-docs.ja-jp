---
title: Linux 用の Azure Key Vault VM 拡張機能
description: 仮想マシンの拡張機能を使用して、仮想マシンに Key Vault 証明書の自動更新を実行するエージェントをデプロイします。
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: e6702ab3753604af50e21f931dd23f63de3c1451
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936199"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Linux 用の Key Vault 仮想マシン拡張機能

Key Vault VM 拡張機能では、Azure キー コンテナーに保存されている証明書の自動更新が行われます。 具体的には、拡張機能では、キー コンテナーに格納されている確認済みの証明書のリストが監視されます。  変更が検出されると、拡張によって取得され、対応する証明書がインストールされます。 現時点では、Key Vault VM 拡張機能は、Linux VM において、Microsoft により公開およびサポートされています。 このドキュメントでは、Linux 用の Key Vault VM 拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。 

### <a name="operating-system"></a>オペレーティング システム

Key Vault VM 拡張機能では、次の Linux ディストリビューションがサポートされています。

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>サポートされている証明書の内容の種類

- PKCS #12
- PEM

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Key Vault VM 拡張機能のスキーマを示しています。 この拡張機能では、保護された設定は必要ありません。すべての設定がセキュリティに影響を与えない情報と見なされます。 この拡張機能には、監視対象のシークレット、ポーリング頻度、および宛先の証明書ストアの一覧が必要です。 具体的な内容は次のとおりです。  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> 確認された証明書の URL は、`https://myVaultName.vault.azure.net/secrets/myCertName` の形式である必要があります。
> 
> これは、`/secrets` のパスでは秘密キーを含む完全な証明書が返されるのに対し、`/certificates` のパスでは返されないためです。 証明書について詳しくは、次の記事をご覧ください: 「[Key Vault 証明書](../../key-vault/general/about-keys-secrets-certificates.md)」

> [!IMPORTANT]
> "authenticationSettings" プロパティは、**ユーザー割り当て ID** を使用する VM の場合にのみ**必須**です。
> Key Vault への認証に使用する ID を指定します。


### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForLinux | string |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | Linux では無視されます | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 文字列配列
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の証明書の更新が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 拡張機能は、個々の VM または仮想マシン スケール セットにデプロイできます。 スキーマと構成は、両方のテンプレートの種類に共通です。 

仮想マシン拡張機能の JSON の構成は、テンプレートの仮想マシン リソースのフラグメント内に入れ子にする必要があります (具体的には、仮想マシン テンプレートの場合は `"resources": []` オブジェクト、仮想マシン スケール セットの場合は `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` オブジェクト)。

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell でのデプロイ
> [!WARNING]
> 多くの場合、PowerShell クライアントでは、`[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.` エラーを伴って akvvm_service が失敗する原因となる settings.json で、`\` が `"` に追加されます。

Azure PowerShell を使用すると、Key Vault VM 拡張機能を既存の仮想マシンまたは仮想マシンのスケールセットにデプロイすることができます。 

* VM 拡張機能をデプロイするには、次の手順を実行します。
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 仮想マシン スケール セットに拡張機能をデプロイするには:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用すると、Key Vault VM 拡張機能を既存の仮想マシンまたは仮想マシン スケール セットにデプロイすることができます。 
 
* VM 拡張機能をデプロイするには、次の手順を実行します。
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 仮想マシン スケール セットに拡張機能をデプロイするには:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

次の制限/要件に注意してください。
- Key Vault の制限:
  - デプロイ時に存在している必要があります 
  - マネージド ID を使用して VM/VMSS ID に Key Vault アクセス ポリシーが設定されている必要があります。 「[マネージド ID で Key Vault の認証を提供する](../../key-vault/general/managed-identity.md)」を参照してください


## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell を使用します。 特定の VM での拡張機能のデプロイ状態を確認するには、Azure PowerShell を使用して次のコマンドを実行します。

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
### <a name="logs-and-configuration"></a>ログと構成

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
