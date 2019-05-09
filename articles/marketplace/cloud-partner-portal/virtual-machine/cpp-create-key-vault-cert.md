---
title: Azure Key Vault 証明書の作成 | Microsoft Docs
description: Azure にデプロイされた VHD からの VM を登録する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: a25418f30225184424011527def468d0d3909563
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045698"
---
# <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault の証明書を作成する

この記事では、Azure でホストされる仮想マシン (VM) に Windows Remote Management (WinRM) を接続するために必要な自己署名証明書をプロビジョニングする方法について説明します。 このプロセスは 3 つのステップで構成されます。

1.  セキュリティ証明書を作成します。 
2.  この証明書を格納する Azure Key Vault を作成します。 
3.  このキー コンテナーに証明書を格納します。 

この作業には使用する Azure リソース グループは新しいものでも既存のものでも構いません。  以下の説明では新しい Azure リソース グループを使用しています。



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>証明書を作成する

次の Azure PowerShell スクリプトを編集して実行し、ローカル フォルダーに証明書ファイル (.pfx) を作成します。  次のパラメーターの値を置換する必要があります。

|  **パラメーター**        |   **説明**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | .pfx ファイルの保存先となるローカル フォルダー  |
| `$location`    | Azure の標準的な地理的位置の 1 つ  |
| `$vmName`      | 予定の Azure 仮想マシンの名前   |
| `$certname`    | 証明書の名前 (予定の VM の完全修飾ドメイン名に一致する必要あり)  |
| `$certpassword` | 証明書のパスワード (予定の VM に使用されるパスワードに一致する必要あり)  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> さまざまなパラメーターの値が保持されるように、以上の手順の間、同じ PowerShell セッションをアクティブな状態のまま維持してください。

> [!WARNING]
> このスクリプトを保存する場合、セキュリティ情報 (パスワード) が含まれているため、安全な場所に保存してください。


## <a name="create-the-key-vault"></a>キー コンテナーを作成する

[キー コンテナー デプロイ テンプレート](./cpp-key-vault-deploy-template.md)の内容をローカル マシン上のファイルにコピーします。 (下のサンプル スクリプトでは、このリソースは `C:\certLocation\keyvault.json` です。)次の Azure PowerShell スクリプトを編集して実行し、Azure Key Vault インスタンスと関連リソース グループを作成します。  次のパラメーターの値を置換する必要があります。

|  **パラメーター**        |   **説明**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | デプロイ識別子に付加される任意の数値文字列                     |
| `$rgName`             | 作成する Azure リソース グループ (RG) の名前                                        |
|  `$location`          | Azure の標準的な地理的位置の 1 つ                                  |
| `$kvTemplateJson`     | キー コンテナーの Resource Manager テンプレートを含むファイル (keyvault.json) のパス |
| `$kvname`             | 新しいキー コンテナーの名前                                                       |
|  |  |

```powershell
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
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
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
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
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
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
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
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>証明書ストアを保存する

これで、次のスクリプトを実行することで、.pfx ファイルに含まれる証明書を新しいキー コンテナーに保存できるようになりました。 

```powershell
    #push certificate to key vault secret

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>次の手順

次に、[ユーザー VM イメージから VM をデプロイします](./cpp-deploy-vm-user-image.md)。
