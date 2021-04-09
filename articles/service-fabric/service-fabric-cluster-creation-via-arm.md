---
title: Azure Service Fabric クラスターを作成する
description: Azure で Azure Resource Manager を使用してセキュリティで保護された Service Fabric クラスターを設定する方法について説明します。  既定のテンプレートを使用して、または独自のクラスター テンプレートを使用して、クラスターを作成することができます。
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791813"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Azure Resource Manager を使用して Service Fabric クラスターを作成する 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Azure Service Fabric クラスター](service-fabric-deploy-anywhere.md)はネットワークで接続された一連の仮想マシンです。マイクロサービスは Service Fabric クラスターにデプロイされ、そこで管理されます。  Azure で動作する Service Fabric クラスターは Azure リソースであり、Azure Resource Manager を使用して展開されます。 この記事では、Resource Manager を使用して Azure にセキュリティで保護された Service Fabric クラスターを展開する方法について説明します。 既定のクラスター テンプレートまたはカスタム テンプレートを使用できます。  カスタム テンプレートがまだない場合は、[作成方法の説明](service-fabric-cluster-creation-create-template.md)をご覧ください。

クラスターをセキュリティで保護するために選択したセキュリティの種類 (つまり、Windows ID、X509 など) は、クラスターの初期作成時に指定する必要があり、その後は変更できません。 クラスターをセットアップする前に、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」をお読みください。 Azure の Service Fabric では、x509 証明書を使用してクラスターとそのエンドポイントをセキュリティで保護し、クライアントを認証し、データを暗号化します。 管理エンドポイントへのアクセスをセキュリティで保護するために、Azure Active Directory も推奨されます。 詳しくは、「[Set up Azure AD to authenticate clients](service-fabric-cluster-creation-setup-aad.md)」(クライアントを認証するための Azure AD のセットアップ) をご覧ください。

運用ワークロードを実行するための運用クラスターを展開する場合は、最初に「[運用環境の準備状況チェックリスト](service-fabric-production-readiness-checklist.md)」を読むことをお勧めします。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件 
この記事では、Service Fabric RM PowerShell または Azure CLI モジュールを使用してクラスターを展開します。

* [Azure PowerShell 4.1 以降][azure-powershell]
* [Azure CLI バージョン 2.0 以降][azure-CLI]

Service Fabric のモジュールのリファレンス ドキュメントは以下をご覧ください。
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [az SF CLI モジュール](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Azure へのサインイン

この記事のコマンドを実行する前に、Azure にサインインします。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>システムで生成された自己署名証明書を使用して新しいクラスターを作成する

次のコマンドを使用して、システムによって生成された自己署名証明書でセキュリティ保護されたクラスターを作成します。 このコマンドで、クラスターのセキュリティに使用されるプライマリ クラスターの証明書が設定されます。また、その証明書を使用して管理操作を実行する管理者アクセス権が設定されます。  自己署名証明書は、テスト クラスターをセキュリティ保護する場合に役立ちます。  運用クラスターの場合は、証明機関 (CA) の証明書でセキュリティ保護する必要があります。

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>モジュールに付属する既定のクラスター テンプレートを使用する

次のコマンドを使用し、最小限のパラメーターと既定のテンプレートを指定して、簡単にクラスターを作成します。

ここで使用されているテンプレートは、[Azure Service Fabric テンプレート サンプル: Windows テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)と [Ubuntu テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)のページで入手できます。

次のコマンドでは、Windows または Linux のクラスターを作成できます。どちらかの OS を指定する必要があります。 次の PowerShell/CLI コマンドでは、指定した *CertificateOutputFolder* に証明書も出力されます。ただし、証明書フォルダーはあらかじめ作成しておく必要があります。 このコマンドは、VM SKU など他のパラメーターも受け取ります。

> [!NOTE]
> 次の PowerShell コマンドを Azure PowerShell `Az` モジュールで使用できます。 Azure Resource Manager PowerShell の現在のバージョンを確認するには、PowerShell コマンド "Get-Module Az" を実行します。 Azure Resource Manager PowerShell のバージョンをアップグレードするには、[こちらのリンク](/powershell/azure/install-Az-ps)に従ってください。 
>
>

PowerShell を使用してクラスターを展開します。

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Azure CLI を使用してクラスターを展開します。

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>独自のカスタム テンプレートを使用する

ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 [クラスター テンプレートをカスタマイズする][customize-your-cluster-template]方法をご覧ください。

カスタム テンプレートが既にある場合は、テンプレートとパラメーター ファイルで、3 つの証明書関連パラメーターの名前が次のように指定されていて、値が null であることを確認します。

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell を使用してクラスターを展開します。

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLI を使用してクラスターを展開します。

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>独自の X.509 証明書を使用して新しいクラスターを作成する

クラスターのセキュリティ保護に使用する証明書を持っている場合は、次のコマンドを使用してクラスターを作成します。

CA で署名された証明書で、他の用途にも使用する予定がある場合は、Key Vault 専用のリソース グループを用意することをお勧めします。 Key Vault は専用のリソース グループに配置することをお勧めします。 そうすることで、必要なキーとシークレットを失うことなく、コンピューティング リソース グループやストレージ リソース グループを削除することができます (Service Fabric クラスターのあるリソース グループを含む)。 **Key Vault を持つリソース グループは、それを使用するクラスターと *同じリージョンにある必要があります*。**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>モジュールに付属する既定の 5 ノードと 1 ノード タイプのテンプレートを使用する
ここで使用されているテンプレートは、[Azure サンプル: Windows テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)と [Ubuntu テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)のページで入手できます。

PowerShell を使用してクラスターを展開します。

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Azure CLI を使用してクラスターを展開します。

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>独自のカスタム クラスター テンプレートを使用する
ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 [クラスター テンプレートをカスタマイズする][customize-your-cluster-template]方法をご覧ください。

カスタム テンプレートを既に持っている場合は、3 つの証明書すべてがテンプレートのパラメーターに関連付けられ、パラメーターのファイル名がテンプレートで指定され、値がテンプレートで null であることを慎重に確認してください。

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell を使用してクラスターを展開します。

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Azure CLI を使用してクラスターを展開します。

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>キー コンテナーにアップロードされたシークレットへのポインターを使用する

既存のキー コンテナーを使用するには、コンピューティング リソース プロバイダーがキー コンテナーから証明書を取得し、クラスター ノードにインストールできるように、キー コンテナーで[展開を有効にする](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI)必要があります。

PowerShell を使用してクラスターを展開します。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLI を使用してクラスターを展開します。

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>次のステップ
この時点では、セキュリティで保護されたクラスターが Azure で実行されています。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。

テンプレートを使用するための JSON の構文とプロパティについては、[Microsoft.ServiceFabric/clusters のテンプレート リファレンス](/azure/templates/microsoft.servicefabric/clusters)に関するページを参照してください。

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
