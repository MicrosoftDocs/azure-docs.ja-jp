---
title: Windows 用の Azure Key Vault VM 拡張機能
description: 仮想マシンの拡張機能を使用して、仮想マシンに Key Vault シークレットの自動更新を実行するエージェントをデプロイします。
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a984d044134dbd775bacb653f8590ee78724f15b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563559"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows 用の Key Vault 仮想マシン拡張機能

Key Vault VM 拡張機能では、Azure キー コンテナーに保存されている証明書の自動更新が行われます。 この拡張機能は、具体的には、Key Vault に保存されている観察対象の証明書の一覧を監視し、変更の検出時に取得し、対応する証明書をインストールします。 このドキュメントでは、Windows 用の Key Vault VM 拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。 

### <a name="operating-system"></a>オペレーティング システム

Key Vault VM 拡張機能では、以下のバージョンの Windows がサポートされています。

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

Key Vault VM 拡張機能は、Azure で使用するために、Windows Server 2019 Core インストールを使用してアップロードされ、特殊化されたイメージに変換されたカスタム ローカル VM でもサポートされています。

### <a name="supported-certificate-content-types"></a>サポートされている証明書の内容の種類

- PKCS #12
- PEM

## <a name="prerequisites"></a>前提条件

  - 証明書を持つ Key Vault インスタンス。 [Key Vault の作成](../../key-vault/general/quick-create-portal.md)に関するページを参照してください
  - VM には [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) が割り当てられている必要があります
  - Key Vault アクセス ポリシーは、シークレットの `get` および `list` アクセス許可を使用して、VM/VMSS マネージド ID が証明書の秘密の部分を取得できるように設定する必要があります。 [Key Vault に対して認証を行う方法](../../key-vault/general/authentication.md)に関するページと「[Key Vault アクセス ポリシーを割り当てる](../../key-vault/general/assign-access-policy-cli.md)」を参照してください。
  -  Virtual Machine Scale Sets には、次の ID 設定が必要です。

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - AKV 拡張機能には、次の設定が必要です。

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Key Vault VM 拡張機能のスキーマを示しています。 この拡張機能では、保護された設定は必要ありません。そのすべての設定はパブリック情報と見なされます。 この拡張機能には、監視対象の証明書、ポーリング頻度、および宛先の証明書ストアの一覧が必要です。 具体的な内容は次のとおりです。  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
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
> "authenticationSettings" プロパティは、**ユーザー割り当て ID** を使用する VM の場合にのみ **必須** です。
> Key Vault への認証に使用する ID を指定します。


### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine または CurrentUser (大文字と小文字を区別する) | string |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | 文字列配列
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の証明書の更新が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 拡張機能は、個々の VM または仮想マシン スケール セットにデプロイできます。 スキーマと構成は、両方のテンプレートの種類に共通です。 

仮想マシン拡張機能の JSON の構成は、テンプレートの仮想マシン リソースのフラグメント内に入れ子にする必要があります (具体的には、仮想マシン テンプレートの場合は `"resources": []` オブジェクト、仮想マシン スケール セットの場合は `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` オブジェクト)。

 > [!NOTE]
> VM 拡張機能では、Key Vault に対する認証のために、システムまたはユーザーのマネージド ID が割り当てられている必要があります。  [Key Vault に対して認証を行う方法と Key Vault アクセス ポリシー](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)に関するページを参照してください。
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>拡張機能の依存関係の順序付け
Key Vault VM 拡張機能は、構成されている場合に拡張機能の順序付けをサポートします。 既定では、拡張機能は、正常に起動したことを、ポーリングの開始直後に報告します。 ただし、証明書の完全な一覧が正常にダウンロードされた後に、正常な起動を報告するように構成することができます。 他の拡張機能が、起動する前に一連の証明書がすべてインストールされていることに依存している場合は、この設定を有効にすると、これらの拡張機能によって Key Vault 拡張機能に対する依存関係が宣言されるようになります。 これにより、依存先であるすべての証明書がインストールされるまで、これらの拡張機能を起動できなくなります。 拡張機能によって、最初のダウンロードが無期限に再試行され、`Transitioning` 状態のままとなります。

これを有効にするには、次のように設定します。
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> システム割り当て ID を作成し、その ID を使用して Key Vault アクセス ポリシーを更新する ARM テンプレートに対しては、この機能を使用することはできません。 それを行うと、すべての拡張機能が起動するまでコンテナーのアクセス ポリシーが更新されなくなり、デッドロックが発生することになります。 そうではなく、"*単一ユーザー割り当て MSI ID*" を使用し、その ID でコンテナーに事前 ACL を設定してからデプロイする必要があります。

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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
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
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* 仮想マシン スケール セットに拡張機能をデプロイするには:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

次の制限/要件に注意してください。
- Key Vault の制限:
  - デプロイ時に存在している必要があります 
  - マネージド ID を使用して VM/VMSS ID に Key Vault アクセス ポリシーが設定されている必要があります。 [Key Vault に対して認証を行う方法](../../key-vault/general/authentication.md)に関するページと「[Key Vault アクセス ポリシーを割り当てる](../../key-vault/general/assign-access-policy-cli.md)」を参照してください。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="frequently-asked-questions"></a>よく寄せられる質問

* 設定できる observedCertificates の数に制限はありますか?
  いいえ。Key Vault VM 拡張機能には、observedCertificates の数に制限はありません。

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell を使用します。 特定の VM での拡張機能のデプロイ状態を確認するには、Azure PowerShell を使用して次のコマンドを実行します。

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>ログと構成

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
