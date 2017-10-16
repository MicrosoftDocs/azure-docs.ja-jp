---
title: "Azure Stack に安全に格納された証明書で仮想マシンをデプロイする | Microsoft Docs"
description: "Azure Stack の Key Vault を使って、仮想マシンをデプロイして証明書を仮想マシンにプッシュする方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>仮想マシンを作成して Key Vault から取得した証明書を含める

この記事では、Azure Stack に仮想マシンを作成して証明書をプッシュする方法を説明します。 

## <a name="prerequisites"></a>前提条件

* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。 
* [PowerShell for Azure Stack のインストール。](azure-stack-powershell-install.md)  
* [Azure Stack ユーザーの PowerShell 環境を構成します](azure-stack-powershell-configure-user.md)。

Azure Stack の Key Vault を使って証明書を格納します。 証明書はさまざまなシナリオで役立ちます。 たとえば、証明書が必要なアプリケーションを実行している仮想マシンが Azure Stack 内に存在するシナリオを考えてみてください。 この証明書は、暗号化、Active Directory の認証、Web サイトでの SSL に、使うことができます。 Key Vault に証明書を格納すると、証明書をセキュリティで保護できます。

この記事では、Azure Stack の Windows 仮想マシンに証明書をプッシュするために必要な手順を説明します。 この手順は、Azure Stack 開発キットから、または VPN 経由で接続している場合は Windows ベースの外部クライアントから実行できます。

次の手順では、仮想マシンに証明書をプッシュするために必要なプロセスについて説明します。

1. Key Vault シークレットを作成します。
2. azuredeploy.parameters.json ファイルを更新します。
3. テンプレートのデプロイ

## <a name="create-a-key-vault-secret"></a>Key Vault シークレットを作成する

次のスクリプトは、.pfx 形式で証明書を作成し、Key Vault を作成して、Key Vault にシークレットとして証明書を格納します。 Key Vault を作成するときは、`-EnabledForDeployment` パラメーターを使う必要があります。 このパラメーターを指定すると、Azure Resource Manager テンプレートから Key Vault を参照できるようになります。

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

前のスクリプトを実行すると、出力にはシークレットの URI が含まれます。 この URI を書き留めておきます。 [Windows Resource Manager に証明書をプッシュするテンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)でそれを参照する必要があります。 開発用コンピューターに [vm-push-certificate-windows template](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) フォルダーをダウンロードします。 このフォルダーには、次の手順で必要な `azuredeploy.json` ファイルと `azuredeploy.parameters.json` ファイルが含まれます。

環境の値に従って `azuredeploy.parameters.json` ファイルを変更します。 特に重要なパラメーターは、コンテナー名、コンテナー リソース グループ、およびシークレットの URI (前のスクリプトによって生成されたもの) です。 パラメーター ファイルの例を次に示します。

## <a name="update-the-azuredeployparametersjson-file"></a>azuredeploy.parameters.json ファイルを更新する

環境に従って、vaultName、シークレットの URI、VmName、他の値で azuredeploy.parameters.json ファイルを更新します。 テンプレート パラメーター ファイルの JSON ファイルの例を次に示します。 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次の PowerShell スクリプトを使用して、テンプレートをデプロイします。

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

テンプレートが正常にデプロイされると、次の出力が表示されます。

![デプロイの出力](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

この仮想マシンがデプロイされると、Azure Stack は仮想マシンに証明書をプッシュします。 Windows では、証明書はユーザー指定の証明書ストアで LocalMachine の証明書の場所に追加されます。 Linux では、証明書は、X509 証明書ファイルの場合は &lt;UppercaseThumbprint&gt;.crt、秘密キーの場合は &lt;UppercaseThumbprint&gt;.prv というファイル名で、/var/lib/waagent ディレクトリに配置されます。

## <a name="retire-certificates"></a>証明書の使用を終了する

前のセクションでは、仮想マシンに新しい証明書をプッシュする方法を説明しました。 古い証明書は仮想マシンにまだ存在しており、削除することはできません。 ただし、`Set-AzureKeyVaultSecretAttribute` コマンドレットを使って、古いバージョンのシークレットを無効にできます。 このコマンドレットの使用例を次に示します。 コンテナー名、シークレット名、およびバージョンの値を、環境に従って置き換えてください。

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>次のステップ

* [Key Vault パスワードを使用して VM をデプロイする](azure-stack-kv-deploy-vm-with-secret.md)
* [アプリケーションが Key Vault にアクセスできるようにする](azure-stack-kv-sample-app.md)


