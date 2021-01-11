---
title: VHD からデプロイされた仮想マシン (VM) のテスト - Azure Marketplace
description: 商業マーケットプレースで仮想マシン オファーをテストして送信する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/29/2020
ms.openlocfilehash: 36c497a180070358332997649e768999c78935cb
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433107"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>VHD からデプロイされた仮想マシン (VM) をテストする

この記事では、前のセクション ([Azure VM 技術資産を作成する](create-azure-vm-technical-asset.md)) で作成した一般化された VHD イメージから Azure 仮想マシン (VM) をデプロイしてテストし、VHD イメージが Azure Marketplace の公開要件を満たしていることを確認する方法について説明します。

これらの手順を実行して互換性レポートを作成し、VHD イメージが Azure Marketplace で使用できることを証明します。

1. リモート VM 管理に必要な証明書を作成して Azure Key Vault に展開します。
2. 「[Azure VM 技術資産を作成する](create-azure-vm-technical-asset.md)」で作成した一般化された VHD イメージから Azure VM をデプロイします。
3. デプロイした VM でテストを実行して、VHD イメージを公開して VM のデプロイに使用する準備ができていることを確認します。

## <a name="running-scripts"></a>スクリプトの実行

この記事には、PowerShell で実行する 3 つのスクリプトが含まれています。 最適なのはデスクトップ PowerShell ですが、PowerShell オプション (ウィンドウの左上) を選択して、Azure Cloud Shell を使用することもできます。

1. スクリプトを実行するように PowerShell が構成されていることを確認します。

    - PowerShell を起動する際は、常に **[管理者として実行]** オプションを使用します。
    - `Set-ExecutionPolicy` と `RemoteSigned` のスクリプトを実行できることを確認します。

2. [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

3. Azure PowerShell Az モジュールをインストールします。
    1. **オプション A**: モジュールがまだインストールされていない。
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        詳細については、「[Azure PowerShell モジュールをインストールする](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0)」を参照してください。

    2. **オプション B**: 現在、AzureRM モジュールを使用している。

        - Uninstall-AzureRM
        - Install-Module -Name Az -AllowClobber -Scope AllUsers
        - Enable-AzureRmAlias -Scope CurrentUser

        詳細については、「[AzureRM から Az への Azure PowerShell の移行](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0)」を参照してください。

4. セッション パラメーターを保存します。

このセクションのスクリプトでは、セッション変数/パラメーターを使用します。 セッションを閉じると、パラメーターは消去されます。 パラメーター値のエラーを回避するため、1 回のセッションですべてのスクリプトを実行することをお勧めします。 これが不可能な場合は、新しいセッションを開くときにパラメーターを再初期化する必要があります (特に後のスクリプトの場合)。

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Azure Key Vault の証明書を作成してデプロイする

このセクションでは、Azure でホストされる仮想マシンへの Windows リモート管理 (WinRM) 接続を設定するために必要な自己署名証明書を作成してデプロイする方法について説明します。

### <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault の証明書を作成する

このプロセスは 3 つのステップで構成されます。

1. セキュリティ証明書を作成します。
2. 証明書を格納する Azure Key Vault を作成します。
3. キー コンテナーに証明書を格納します。

この作業には使用する Azure リソース グループは新しいものでも既存のものでも構いません。

#### <a name="create-the-security-certificate"></a>セキュリティ証明書を作成する

このスクリプトを実行して、ローカル フォルダーに証明書ファイル (.pfx) を作成します。 証明書は、VHD イメージからデプロイされる予定の Azure VM に属しています。 VM には、スクリプト パラメーターで指定された名前、場所、およびパスワードが必要です。 次の Azure PowerShell **証明書作成スクリプト**を編集して、表に示されているスクリプト パラメーターの正しい値を指定します。

| **パラメーター** | **説明** |
| --- | --- |
| $certroopath | .pfx ファイルの保存先となるローカル フォルダー。 |
| $location | Azure の標準的な地理的場所の 1 つ。 |
| $vmName | 予定の Azure 仮想マシンの名前。 |
| $certname | 証明書の名前。予定の VM の完全修飾ドメイン名と一致する必要があります。 |
| $certpassword | 証明書のパスワード。予定の VM に使用されるパスワードと一致する必要があります。 |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> さまざまなパラメーターの値を保持するために、これらの手順の間、同じ Azure PowerShell コンソール セッションが開いて実行されている状態を維持してください。

> [!WARNING]
> このスクリプトを保存する場合、セキュリティ情報 (パスワード) が含まれているため、安全な場所にのみ保存してください。

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>証明書を格納する Azure キー コンテナーを作成する

以下のテンプレートの内容をローカル コンピューター上のファイルにコピーします。 (下のサンプル スクリプトでは、このリソースは `C:\certLocation\keyvault.json` です。)

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

次の Azure PowerShell スクリプトを編集して実行し、Azure Key Vault と関連リソース グループを作成します。 次の表に示すパラメーターの値を置き換えます

| **パラメーター** | **説明** |
| --- | --- |
| $postfix | デプロイ識別子に付加されるランダムな数値文字列。 |
| $rgName | 作成する Azure リソース グループ (RG) の名前。 |
| $location | Azure の標準的な地理的場所の 1 つ。 |
| $kvTemplateJson | キー コンテナー用の Resource Manager テンプレートを含むファイル (keyvault.json) のパス。 |
| $kvname | 新しいキー コンテナーの名前。|
|   |   |

```PowerShell
# Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzContext
        $accountNum = 0
        $accounts.Account | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Account

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id).Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].Name)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].Id
        $mysubName=$subslist[$selectedsub-1].Name
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>キー コンテナーに証明書を格納する

次のスクリプトを使用して、.pfx ファイルに含まれる証明書を新しいキー コンテナーに格納します。

```PowerShell
 $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>一般化された VHD イメージから Azure VM をデプロイする

このセクションでは、一般化された VHD イメージをデプロイして新しい Azure VM リソースを作成する方法について説明します。 このプロセスでは、指定された Azure Resource Manager テンプレートと Azure PowerShell スクリプトを使用します。

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを準備する

VHD デプロイ用の次の Azure Resource Manager テンプレートのいずれか (Windows または Linux 用) を、VHDtoImage.json という名前のローカル ファイルにコピーします。 次のスクリプトは、この JSON を使用するためにローカル コンピューター上の場所を要求します。

#### <a name="for-windows-based-vms"></a>Windows ベースの VM の場合

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

#### <a name="for-linux-based-vms"></a>Linux ベースの VM の場合

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "linux",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

次のスクリプトをコピーして編集し、これらのパラメーターの値を指定します。

| **パラメーター** | **説明** |
| --- | --- |
| ResourceGroupName | 既存の Azure リソース グループ名。 通常は、ご利用のキー コンテナーと同じ RG を使用します。 |
| TemplateFile | VHDtoImage.json ファイルの完全なパス名。 |
| userStorageAccountName | ストレージ アカウントの名前。 |
| sNameForPublicIP | パブリック IP の DNS 名。小文字にする必要があります。 |
| subscriptionId | Azure サブスクリプションの識別子。 |
| Location | リソース グループの標準的な Azure 地理的場所。 |
| vmName | 仮想マシンの名前。 |
| vaultName | キー コンテナーの名前。 |
| vaultResourceGroup | Key Vault のリソース グループ。 |
| certificateUrl | キー コンテナーに格納されているバージョンを含む、証明書の Web アドレス (URL)。例: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`。 |
| vhdUrl | 仮想ハード ディスクの Web アドレス。 |
| vmSize | 仮想マシン インスタンスのサイズ。 |
| publicIPAddressName | パブリック IP アドレスの名前。 |
| virtualNetworkName | 仮想ネットワークの名前。 |
| nicName | 仮想ネットワーク用のネットワーク インターフェイス カードの名前。 |
| adminUserName | 管理者アカウントのユーザー名。 |
| adminPassword | 管理者パスワード。 |
|   |   |

### <a name="deploy-an-azure-vm"></a>Azure VM をデプロイする

次のスクリプトをコピーして編集し、変数 `$storageaccount` と `$vhdUrl` の値を指定します。 それを実行して、汎用化された既存の VHD から Azure VM リソースを作成します。

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType""

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile $templateFile -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "$vmSize" -publicIPAddressName "$publicIPAddressName" -virtualNetworkName "$virtualNetworkName" -nicName "$nicName" -adminUserName "$adminUserName" -adminPassword $pwd -osType "$osType"

```

## <a name="run-tests-on-the-deployed-vm"></a>デプロイされた VM でテストを実行する

### <a name="download-and-run-the-certification-test-tool"></a>認定テスト ツールをダウンロードして実行する

Azure 認定用の認定テスト ツールはローカルの Windows マシン上で動作する自己テスト ツールですが、そのテスト対象は Azure ベースの Windows と Linux VM です。 これにより、ユーザーの VM イメージが Microsoft Azure で使用できることと、VHD を準備するにあたってのガイダンスと要件が満たされていることが認定されます。 このツールを使用すると、Azure Marketplace の要件に従って VM を公開する準備が整います。

1. 次のリンクから、[Azure 認定用の認定テスト ツール](https://www.microsoft.com/download/details.aspx?id=44299)の最新版をダウンロードしてインストールします。
2. 認定ツールを開き、 **[Start New Test]\(新規テストの開始\)** を選択します。
3. **[テスト情報]** 画面で、実行する**テストの名前**を入力します。
4. VM の**プラットフォーム**として、Windows Server または Linux を選択します。 ここでのプラットフォームの選択は、以降のオプションに影響します。
5. 該当するデータベース サービスを VM で使用している場合、 **[Test for Azure SQL Database]\(Azure SQL Database のテスト\)** チェック ボックスをオンにします。

### <a name="connect-the-certification-tool-to-a-vm-image"></a>VM イメージに認定ツールを接続する

このツールは、Windows ベースの VM には [Azure PowerShell](https://docs.microsoft.com/powershell/) で接続し、Linux VM には [SSH.Net](https://www.ssh.com/ssh/protocol/) で接続します。 次の 2 つのオプションのいずれかを選択します (Linux または Windows)。

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>オプション 1: Linux VM イメージに認定ツールを接続する

1. **[SSH Authentication]\(SSH 認証\)** モードを選択します:パスワード認証またはキー ファイル認証。
2. パスワード ベースの認証を使用する場合は、 **[VM DNS Name]\(VM の DNS 名\)** 、 **[User name]\(ユーザー名\)** 、 **[Password]\(パスワード\)** の値を入力します。 既定の **SSH ポート**番号を変更することもできます。

    ![Azure 認定テスト ツール、Linux VM イメージのパスワード認証](media/avm-cert2.png)

3. キー ファイル ベースの認証を使用する場合は、**VM の DNS 名**、**ユーザー名**、**秘密キー**の場所の値を入力します。 **パスフレーズ**を含めたり、既定の **SSH ポート**番号を変更したりすることもできます。

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>オプション 2:Windows ベースの VM イメージへの認定ツールの接続

1. 完全修飾 **VM DNS 名** (MyVMName.Cloudapp.net など) を入力します。
2. **ユーザー名**と**パスワード**の値を入力します。

    ![Azure 認定テスト ツール、Windows ベースの VM イメージのパスワード認証](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>認定テストを実行する

認定ツールで VM イメージのパラメーター値を入力したら、 **[Test Connection]\(接続のテスト\)** を選択して、VM への有効な接続を作成します。 接続の確認後、 **[次へ]** をクリックしてテストを開始します。 テストが完了すると、テスト結果が表に表示されます。 [Status]\(状態\) 列に、各テストについて Pass(合格)/Fail(不合格)/Warning(警告) が表示されます。 いずれかのテストが失敗した場合、イメージは認定 "_されません_"。 その場合は、要件と失敗のメッセージを確認して、提案された変更を行い、テストを再実行します。

自動テストが完了したら、 **[Questionnaire]\(アンケート\)** 画面の 2 つのタブ ( **[General Assessment]\(全般的な評価\)** と **[Kernel Customization]\(カーネルのカスタマイズ\)** ) で VM イメージに関する追加情報を入力し、 **[Next]\(次へ\)** を選択します。

最後の画面では、Linux VM イメージの SSH アクセス情報など、詳細情報を入力できるほか、例外を希望する場合は、 不合格となった評価の説明を入力できます。

最後に **[Generate Report]\(レポートの生成\)** を選択して、実行されたテスト ケースのテスト結果とログ ファイルを、アンケートに対する自分の回答と合わせてダウンロードします。 VHD と同じコンテナーに結果を保存します。

## <a name="next-step"></a>次のステップ

- [SAS URI に関する一般的な問題と解決策](common-sas-uri-issues.md)
