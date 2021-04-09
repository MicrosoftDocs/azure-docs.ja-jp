---
title: Azure Service Fabric クラスター テンプレートを作成する
description: Service Fabric クラスター用の Resource Manager テンプレートの作成方法について説明します。 セキュリティ、Azure Key Vault、および Azure Active Directory (Azure AD) をクライアント認証のために構成します。
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: b028b5c1f32733146c071e50b24202e5bad945c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260493"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric クラスターの Resource Manager テンプレートを作成する

[Azure Service Fabric クラスター](service-fabric-deploy-anywhere.md)はネットワークで接続された一連の仮想マシンです。マイクロサービスは Service Fabric クラスターにデプロイされ、そこで管理されます。 Azure で動作する Service Fabric クラスターは Azure リソースであり、Resource Manager を使用してデプロイ、管理、および監視されます。  この記事では、Azure で動作する Service Fabric クラスター用の Resource Manager テンプレートの作成方法について説明します。  テンプレートが完成したら、[クラスターを Azure にデプロイ](service-fabric-cluster-creation-via-arm.md)できます。

クラスター セキュリティはクラスターの初回セットアップ時に構成され、後からの変更はできません。 クラスターをセットアップする前に、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」をお読みください。 Azure の Service Fabric では、x509 証明書を使用してクラスターとそのエンドポイントをセキュリティで保護し、クライアントを認証し、データを暗号化します。 管理エンドポイントへのアクセスをセキュリティで保護するために、Azure Active Directory も推奨されます。 Azure AD テナントとユーザーは、クラスターを作成する前に作成する必要があります。  詳しくは、「[Set up Azure AD to authenticate clients](service-fabric-cluster-creation-setup-aad.md)」(クライアントを認証するための Azure AD のセットアップ) をご覧ください。

運用環境のワークロードを実行するための運用環境クラスターをデプロイする前に、必ず「[運用環境の準備状況チェックリスト](service-fabric-production-readiness-checklist.md)」を最初にお読みください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成
サンプルの Resource Manager テンプレートは、[GitHub の Azure サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates)で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。

この記事では、[セキュリティで保護された 5 ノード クラスター][service-fabric-secure-cluster-5-node-1-nodetype]の例にあるテンプレートとテンプレート パラメーターを使用します。 *azuredeploy.json* および *azuredeploy.parameters.json* をコンピューターにダウンロードし、両方のファイルを任意のテキスト エディターで開きます。

> [!NOTE]
> また、国内のクラウド (Azure Government、Azure China、Azure Germany) については、次の `fabricSettings` をテンプレートに追加する必要もあります: `AADLoginEndpoint`、`AADTokenEndpointFormat`、`AADCertEndpointFormat`。

## <a name="add-certificates"></a>証明書の追加
証明書は、証明書キーを含む Key Vault を参照することによって、Cluster Resource Manager テンプレートに追加されます。 Resource Manager テンプレート パラメーター ファイル (*azuredeploy.parameters.json*) にこれらの Key Vault パラメーターと値を追加します。

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>仮想マシン スケール セットの osProfile にすべての証明書を追加する
クラスターにインストールするすべての証明書は、スケール セット リソース (Microsoft.Compute/virtualMachineScaleSets) の **[osProfile]** セクションで構成する必要があります。 このアクションにより、リソース プロバイダーに対して VM に証明書をインストールするよう指示されます。 このインストールには、クラスター証明書とアプリケーションで使用する予定のあらゆるアプリケーションのセキュリティ証明書が含まれます。

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書を構成する

クラスターの認証証明書は、Service Fabric クラスター リソース (Microsoft.ServiceFabric/clusters) と、仮想マシン スケール セット リソースの仮想マシン スケール セット用 Service Fabric 拡張機能で構成する必要があります。 これにより、Service Fabric のリソース プロバイダーを使用してクラスターの認証および管理エンドポイントのサーバー認証用に構成できます。

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>仮想マシン スケール セット リソースの証明書情報を追加する

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースの証明書情報を追加する

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>クライアント アクセスに Azure AD を使用する Azure AD の構成を追加する

Azure AD 構成は、証明書キーを含む Key Vault を参照することによって、Cluster Resource Manager テンプレートに追加されます。 Resource Manager テンプレート パラメーター ファイル (*azuredeploy.parameters.json*) にこれらの Azure AD パラメーターと値を追加します。 

> [!NOTE]
> Linux 上では、Azure AD テナントとユーザーは、クラスターを作成する前に作成する必要があります。  詳しくは、「[Set up Azure AD to authenticate clients](service-fabric-cluster-creation-setup-aad.md)」(クライアントを認証するための Azure AD のセットアップ) をご覧ください。

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>パラメーター ファイルに値を設定する

最後に、Key Vault と Azure AD PowerShell コマンドからの出力値を使用してパラメーター ファイルを作成します。

Azure Service Fabric RM PowerShell モジュールを使用する予定がある場合は、クラスター証明書情報を設定する必要はありません。 クラスター セキュリティの自己署名証明書を自動的に生成するには、それらの情報を null のままにしておきます。 

> [!NOTE]
> RM モジュールでこれらの空のパラメーター値を取得して設定するには、パラメーター名が以下の名前と一致する必要があります。

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

アプリケーション証明書を使用している場合、または Key Vault にアップロードした既存のクラスターを使用している場合は、その情報を取得して設定する必要があります。

RM モジュールには、Azure AD 構成を生成する機能がありません。そのため、Azure AD をクライアント アクセスに使用する場合は、それを設定する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>テンプレートのテスト
次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをテストします。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

問題が発生し、不明なメッセージを受け取った場合は、オプションに "-Debug" を使用します。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

次の図は、Key Vault と Azure AD の構成を Resource Manager テンプレートに適合させる場所を示しています。

![Resource Manager の依存関係マップ][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>次のステップ
クラスター用のテンプレートが完成したので、[クラスターを Azure にデプロイ](service-fabric-cluster-creation-via-arm.md)する方法を学びます。  「[運用環境の準備状況チェックリスト](service-fabric-production-readiness-checklist.md)」をまだお読みでない場合は、 運用環境クラスターをデプロイする前にお読みください。

この記事でデプロイしたリソースの JSON 構文およびプロパティについては、次をご覧ください。

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: ../azure-resource-manager/templates/deploy-powershell.md

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
