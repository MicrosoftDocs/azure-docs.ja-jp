---
title: "Azure Stack に安全に格納されたパスワードで VM をデプロイする | Microsoft Docs"
description: "Azure Stack Key Vault に格納されているパスワードを使って VM をデプロイする方法を説明します"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: sngun
ms.openlocfilehash: 3292a2dfefc17e5034c66122a3eab24d6c03e694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Key Vault に格納されているパスワードを取得して仮想マシンを作成する

デプロイ時にセキュリティで保護されたパスワードなどの値を渡す必要があるときは、その値をシークレットとして Azure Stack Key Vault に格納し、Azure Resource Manager テンプレートで参照できます。 リソースをデプロイするたびにシークレットを手動で入力する必要はなく、シークレットにアクセスできるユーザーまたはサービス プリンシパルを指定することもできます。 

この記事では、Key Vault に格納されているパスワードを取得することによって Azure Stack に Windows 仮想マシンをデプロイするために必要な手順について説明します。 したがって、パスワードがテンプレート パラメーター ファイルにプレーン テキストで記述されることはありません。 この手順は、Azure Stack 開発キットから、または VPN 経由で接続している場合は外部クライアントから実行できます。

## <a name="prerequisites"></a>前提条件
 
* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。  
* [PowerShell for Azure Stack のインストール。](azure-stack-powershell-install.md)  
* [Azure Stack ユーザーの PowerShell 環境を構成します](azure-stack-powershell-configure-user.md)。

次の手順では、Key Vault に格納されているパスワードを取得することによって仮想マシンを作成するために必要なプロセスについて説明します。

1. Key Vault シークレットを作成します。
2. azuredeploy.parameters.json ファイルを更新します。
3. テンプレートをデプロイします。

## <a name="create-a-key-vault-secret"></a>Key Vault シークレットを作成する

次のスクリプトは、Key Vault を作成し、パスワードをシークレットとして Key Vault に格納します。 Key Vault を作成するときは、`-EnabledForDeployment` パラメーターを使います。 このパラメーターを指定すると、Azure Resource Manager テンプレートから Key Vault を参照できるようになります。

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

前のスクリプトを実行すると、出力にはシークレットの URI が含まれます。 この URI を書き留めておきます。 それを、[Key Vault のパスワードでの Windows 仮想マシンのデプロイ テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)で参照する必要があります。 開発用コンピューターに [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) フォルダーをダウンロードします。 このフォルダーには、次の手順で必要な `azuredeploy.json` ファイルと `azuredeploy.parameters.json` ファイルが含まれます。

環境の値に従って `azuredeploy.parameters.json` ファイルを変更します。 特に重要なパラメーターは、コンテナー名、コンテナー リソース グループ、およびシークレットの URI (前のスクリプトによって生成されたもの) です。 パラメーター ファイルの例を次に示します。

## <a name="update-the-azuredeployparametersjson-file"></a>azuredeploy.parameters.json ファイルを更新する

環境に従って仮想マシンの値の KeyVault URI、secretName、adminUsername で azuredeploy.parameters.json ファイルを更新します。 テンプレート パラメーター ファイルの JSON ファイルの例を次に示します。 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>テンプレートのデプロイ

次の PowerShell スクリプトを使用して、テンプレートをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
テンプレートが正常にデプロイされると、次の出力が表示されます。

![デプロイの出力](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>次のステップ
[Key Vault を使ってサンプル アプリをデプロイする](azure-stack-kv-sample-app.md)

[Key Vault 証明書を使って VM をデプロイする](azure-stack-kv-push-secret-into-vm.md)

