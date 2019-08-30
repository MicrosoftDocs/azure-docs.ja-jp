---
title: Azure VM の WinRM アクセスの設定 | Microsoft Docs
description: Resource Manager デプロイ モデルに作成された Azure 仮想マシンで使用するために WinRM アクセスを設定します。
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: f7f57a43697a9376062bdd3baa2d5f7333bf4a7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100153"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager の仮想マシンの WinRM アクセスを設定する

ここでは WinRM 接続を備えた VM のセットアップに必要な手順を説明します。

1. Key Vault の作成
2. 自己署名証明書の作成
3. 自己署名証明書を Key Vault にアップロードする
4. Key Vault の自己署名証明書の URL を取得する
5. VM を作成するときに、自己署名証明書の URL を参照する

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>手順 1:Key Vault の作成
次のコマンドを使用して、Key Vault を作成します

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>手順 2:自己署名証明書の作成
この PowerShell スクリプトを使用して、自己署名証明書を作成します

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>手順 3:Key Vault に自己署名証明書をアップロードする
手順 1 で作成した Key Vault に証明書をアップロードする前に、Microsoft.Compute リソース プロバイダーが理解する形式への変換が必要です。 次の PowerShell スクリプトにより、実行が許可されます

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>手順 4:Key Vault の自己署名証明書の URL を取得する
VM をプロビジョニングするときに、Microsoft.Compute リソース プロバイダーには Key Vault 内部のシークレットへの URL が必要です。 これにより、Microsoft.Compute リソース プロバイダーがシークレットをダウンロードして、VM 上に同様の証明書を作成することができます。

> [!NOTE]
> シークレットの URL には、バージョンも含める必要があります。 URL の例を次に示します: https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>テンプレート
次のコードを使用して、テンプレートの URL へのリンクを取得する事ができます

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
次の PowerShell コマンドを使用して、この URL を取得することができます

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>手順 5:VM を作成するときに、自己署名証明書の URL を参照する
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート
テンプレートを使用して VM を作成する場合、"secrets" セクションと "WinRM" セクションで証明書を次のように参照します。

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

上記のサンプル テンプレートは、 [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

このテンプレートのソース コードは [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>手順 6:VM に接続する
VM に接続する前に、WinRM リモート管理のためにコンピューターが構成されていることを確認する必要があります。 管理者として PowerShell を開始し、次のコマンドを実行して設定を確認します。

    Enable-PSRemoting -Force

> [!NOTE]
> 上記が動作しない場合は、WinRM サービスが実行されていることを確認する必要があります。 `Get-Service WinRM`
> 
> 

設定が完了すると、次のコマンドを使用して VM に接続することができます。

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
