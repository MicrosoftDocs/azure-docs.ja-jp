---
title: 証明書の共通名を使用してクラスターを作成する
description: テンプレートから、証明書共通名を使用する Service Fabric クラスターを作成する方法を説明します。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86246437"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>拇印ではなく証明書共通名を使用する Service Fabric クラスターをデプロイする
2 つの証明書が同じ拇印を持つことはできず、そのことがクラスター証明書のロール オーバーや管理を困難にしています。 ただし、複数の証明書で同じ共通名や件名を持つことはできます。  証明書共通名を使用するクラスターにより、証明書の管理が大幅に単純化されます。 この記事では、Service Fabric クラスターを、証明書の拇印ではなく証明書共通名を使用するようにデプロイする方法について説明します。
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>証明書を取得する
最初に、[証明機関 (CA)](https://wikipedia.org/wiki/Certificate_authority) から証明書を取得します。  証明書の共通名は、貴社が所有するカスタム ドメインを対象とし、かつドメイン レジストラーから購入したものであることが必要です。 たとえば、"azureservicefabricbestpractices.com" とします。Microsoft の従業員以外は、MS ドメインの証明書をプロビジョニングできません。そのため、ご利用の LB や Traffic Manager の DNS 名を証明書の共通名として使用することはできません。また、カスタム ドメインを Azure で解決可能にするためには、[Azure DNS ゾーン](../dns/dns-delegate-domain-azure-dns.md)をプロビジョニングする必要があります。 また、ご利用のクラスターのカスタム ドメイン エイリアスをポータルに反映したい場合は、貴社が所有するカスタム ドメインをクラスターの "managementEndpoint" として宣言する必要があります。

テスト目的の場合は、無料またはオープンな証明機関から CA の署名証明書を取得できます。

> [!NOTE]
> Azure Portal で、Service Fabric クラスターをデプロイするときに生成されるものを含め、自己署名証明書はサポートされません。 

## <a name="upload-the-certificate-to-a-key-vault"></a>キー コンテナーに証明書をアップロードする
Azure 内で、Service Fabric クラスターは仮想マシン スケール セットにデプロイされます。  キー コンテナーに証明書をアップロードします。  クラスターがデプロイされると、クラスターが実行されている仮想マシン スケール セットに証明書がインストールされます。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>サンプル テンプレートをダウンロードして更新する
この記事では、[セキュリティで保護された 5 ノード クラスターの例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)にあるテンプレートとテンプレート パラメーターを使用します。 *azuredeploy.json* ファイルと *azuredeploy.parameters.json* ファイルをコンピューターにダウンロードします。

### <a name="update-parameters-file"></a>パラメーター ファイルを更新する
最初に、テキスト エディターで *azuredeploy.parameters.json* ファイルを開き、次のパラメーター値を追加します。
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

次に、*certificateCommonName*、*sourceVaultValue*、および *certificateUrlValue* のパラメーター値を、前のスクリプトから返された値に設定します。
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>テンプレート ファイルを更新する
次に、テキスト エディターで *azuredeploy.json* ファイルを開き、証明書共通名をサポートするために 3 つの更新を加えます。

1. **parameters** セクションに *certificateCommonName* パラメーターを追加します。
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    また、*certificateThumbprint* を削除することを検討します。これはもう不要の可能性があります。

2. *sfrpApiVersion* 変数の値を "2018-02-01" に設定します。
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. **Microsoft.Compute/virtualMachineScaleSets** リソースで、拇印ではなく証明書の設定の共通名を使用するように仮想マシン拡張機能を更新します。  **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate** に以下を追加します。 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    `"thumbprint": "[parameters('certificateThumbprint')]",` を削除します。

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. **Microsoft.ServiceFabric/clusters** リソースで、API バージョンを "2018-02-01" に更新します。  次の例のように、**certificateCommonNames** の設定 (**commonNames** プロパティを含む) の追加と、**certificate** の設定 (thumbprint プロパティを含む) の削除も行います。
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
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > 'certificateIssuerThumbprint' フィールドには、想定される証明書の発行者を、特定のサブジェクトの共通名を使用して指定できます。 このフィールドでは、SHA1 サムプリントのコンマ区切りの列挙を使用できます。 これは、証明書の検証の強化であることに注意してください。発行者が指定されていないか空の場合でも、証明書チェーンの構築が可能であり、検証者によって信頼されているルートに最終的に到達すれば、証明書は認証用に受け入れられます。 発行者が指定されている場合は、証明書の直接的な発行者のサムプリントがこのフィールドに指定されている値のいずれかと一致すれば、ルートが信頼されているかどうかに関係なく、証明書は受け入れられます。 PKI では、同じサブジェクトの証明書が異なる証明機関を使用して発行される場合があるため、特定のサブジェクトの想定される発行者のサムプリントをすべて指定することが重要であることに注意してください。
   >
   > 発行者の指定はベスト プラクティスとみなされています。省略しても、信頼されたルートに到達する証明書チェーンによって機能は続行されますが、この動作には制限があり、近い将来、段階的に廃止される可能性があります。 さらに、Azure にデプロイされたクラスターで、プライベート PKI によって発行され、サブジェクトによって宣言されている X509 証明書を使用してセキュリティ保護されているクラスターでは、PKI の証明書ポリシーの検出、使用、およびアクセスを実行できなければ、(クラスター対サービス間通信での) Azure Service Fabric サービスによる検証を実行できない可能性があることに注意してください。 

## <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
変更を加えた後、更新したテンプレートを再度デプロイします。

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>次のステップ
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [クラスター証明書のロールオーバー](service-fabric-cluster-rollover-cert-cn.md)について学習します
* [クラスター証明書の更新と管理を行います](service-fabric-cluster-security-update-certs-azure.md)
* [クラスターを証明書の拇印から共通名に変更する](service-fabric-cluster-change-cert-thumbprint-to-cn.md)ことで、証明書の管理を簡略化する

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
