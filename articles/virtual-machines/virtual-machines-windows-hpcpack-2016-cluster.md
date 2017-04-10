---
title: "Azure の HPC Pack 2016 クラスター | Microsoft Docs"
description: "Azure に HPC Pack 2016 クラスターをデプロイする方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/15/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: f4c29b292ecd97b51620b5e41cbcd6a096a7768e
ms.openlocfilehash: e96d26e458f3b8f88ba88a5839dff9c48a755ffa


---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Azure に HPC Pack 2016 クラスターをデプロイする

この記事の手順に従って、[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) クラスターを Azure Virtual Machines にデプロイします。 HPC Pack は、Microsoft Azure と Windows Server テクノロジに基づいて構築された、Microsoft の無料 HPC ソリューションであり、さまざまな HPC ワークロードをサポートしています。

[Azure Resource Manager のテンプレート](https://github.com/MsHpcPack/HPCPack2016)のいずれかを使用して、HPC Pack 2016 クラスターをデプロイします。 クラスター トポロジには、クラスター ヘッド ノードの数の違いや、Linux か Windows のどちらの計算ノードを使用するかによって、いくつかの選択肢が用意されています。

## <a name="prerequisites"></a>前提条件

### <a name="pfx-certificate"></a>PFX 証明書

Microsoft HPC Pack 2016 クラスターでは、HPC ノード間の通信をセキュリティで保護する Personal Information Exchange (PFX) 証明書が必要です。 証明書は次の要件を満たす必要があります。

* キー交換に対応する秘密キーであること
* キー使用法にデジタル署名とキーの暗号化が含まれていること
* 拡張キー使用法にクライアントの認証とサーバーの認証が含まれていること

これらの要件を満たす証明書がまだない場合は、証明機関に証明書を要求できます。 または、次のコマンドを使って、コマンドを実行するオペレーティング システムに基づく自己署名証明書を生成し、秘密キーと共に PFX 形式の証明書をエクスポートできます。

* **Windows 10 または Windows Server 2016 の場合は**、次のように組み込みの **New-SelfSignedCertificate** PowerShell コマンドレットを実行します。

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Windows 10 または Windows Server 2016 より前のオペレーティング システムの場合は**、Microsoft スクリプト センターから [self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) をダウンロードします。 その内容を抽出し、PowerShell プロンプトで次のコマンドを実行します。

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

### <a name="upload-certificate-to-an-azure-key-vault"></a>Azure Key Vault に証明書をアップロードします

HPC クラスターをデプロイする前に、証明書を [Azure Key Vault](../key-vault/index.md) に秘密証明書として追加し、デプロイ時に使用する**コンテナーの名前**、**コンテナーのリソース グループ**、**証明書の URL**、**証明書の拇印**などの情報を記録します。

証明書をアップロードする PowerShell のサンプルスクリプトが続きます。 Azure Key Vault に証明書をアップロードする方法について詳しくは、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>サポートされているトポロジ

[Azure Resource Manager のテンプレート](https://github.com/MsHpcPack/HPCPack2016)のいずれかを選び、HPC Pack 2016 クラスターをデプロイします。 サポートされている 3 つのクラスター トポロジのアーキテクチャの概要を次に示します。 高可用性トポロジには、複数のクラスター ヘッド ノードが含まれています。

1. Active Directory ドメインがある高可用性クラスター

    ![AD ドメイン内の HA クラスター](./media/virtual-machines-windows-hpcpack-2016-cluster/haad.png)



2. Active Directory ドメインがない高可用性クラスター

    ![AD ドメインのない HA クラスター](./media/virtual-machines-windows-hpcpack-2016-cluster/hanoad.png)

3. ヘッド ノードが単一のクラスター

   ![ヘッド ノードが単一のクラスター](./media/virtual-machines-windows-hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>クラスターのデプロイ

クラスターを作成するには、テンプレートを選び、**[Azure に配置する]** をクリックします。 [Azure Portal](https://portal.azure.com) で、次の手順に従ってテンプレートのパラメーターを指定します。 各テンプレートは、HPC クラスター インフラストラクチャに必要なすべての Azure リソースを作成します。 リソースには、Azure の仮想ネットワーク、パブリック IP アドレス、ロード バランサー (高可用性クラスターの場合のみ)、ネットワーク インターフェイス、可用性セット、ストレージ アカウント、仮想マシンなどが含まれます。

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>手順 1: サブスクリプション、場所、およびリソース グループを選択する

**サブスクリプション**と**場所**は、PFX 証明書をアップロードしたときに指定したものと同じである必要があります (「前提条件」をご覧ください)。 **リソース グループ**はデプロイ用に新しく作成することをお勧めします。

### <a name="step-2-specify-the-parameter-settings"></a>手順 2: パラメーター設定を指定する

テンプレートのパラメーターの値を入力または変更します。 各パラメーターの隣のアイコンをクリックするとヘルプ情報が表示されます。 また、[利用可能な VM のサイズ](virtual-machines-windows-sizes.md)のガイダンスもご覧ください。

「前提条件」で記録した**コンテナーの名前**、**コンテナーのリソース グループ**、**証明書の URL**、**証明書の拇印**などのパラメーターに値を指定します。

### <a name="step-3-review-legal-terms-and-create"></a>手順 3. 法律条項を確認して作成する
条項を確認するには、**[法律条項を確認してください]** をクリックします。 同意する場合は **[購入]**をクリックし、**[作成]** をクリックしてデプロイを開始します。

## <a name="connect-to-the-cluster"></a>クラスターへの接続
1. HPC Pack クラスターがデプロイされたら、[Azure Portal](https://portal.azure.com) に移動します。 **[リソース グループ]** をクリックし、クラスターがデプロイされたリソース グループを探します。 ヘッド ノードの仮想マシンが見つかります。

    ![ポータルでのクラスターのヘッド ノード](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterhns.png)

2. 1 つのヘッド ノードをクリックします (高可用性クラスターの場合はいずれかヘッド ノードをクリック)。 **Essentials** で、パブリック IP アドレスまたはクラスターの完全な DNS 名を検索できます。

    ![クラスターの接続設定](./media/virtual-machines-windows-hpcpack-2016-cluster/clusterconnect.png)

3. **[接続]** をクリックし、リモート デスクトップを使用して指定の管理者のユーザー名で任意のヘッド ノードにログインします。 デプロイしたクラスターが Active Directory ドメインにある場合、ユーザー名は <privateDomainName>\<adminUsername> の形式 (hpc.local\hpcadmin など) になります。

## <a name="next-steps"></a>次のステップ
* クラスターにジョブを送信します。 [Azure の HPC Pack クラスターに HPC のジョブを送信する方法](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)および[Azure Active Directory を使用した Azure の HPC Pack 2016 クラスターの管理](virtual-machines-windows-hpcpack-cluster-active-directory.md)に関する記事をご覧ください。




<!--HONumber=Feb17_HO2-->


