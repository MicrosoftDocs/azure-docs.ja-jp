---
title: "テンプレートからの Azure Service Fabric クラスターの作成 | Microsoft Docs"
description: "この記事では、Azure Resource Manager、Azure Key Vault、クライアント認証用の Azure Active Directory (Azure AD) を使用して、セキュリティで保護された Service Fabric クラスターを Azure で設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/22/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: b80ee30379e9aac207cfe420cae17da57ea123a5
ms.openlocfilehash: 9159f40fed17e52e6576efa1ea7e8a2dee98728e
ms.lasthandoff: 03/02/2017


---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Azure Resource Manager を使用して Service Fabric クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure リソース マネージャー](service-fabric-cluster-creation-via-arm.md)
> * [Azure ポータル](service-fabric-cluster-creation-via-portal.md)
>
>

このガイドでは、Azure Resource Manager を使用して、セキュリティで保護された Azure Service Fabric クラスターを Azure でセットアップする手順を説明します。 長い記事ですが、 この内容について精通していない場合は、必ず以下の各手順に従って進めてください。

このガイドでは次の手順について説明します。

* Azure Key Vault をセットアップして証明書をアップロードし、クラスターとアプリケーションのセキュリティを確保する
* Azure Resource Manager を使用してセキュリティで保護されたクラスターを Azure で作成する
* クラスター管理のため、Azure Active Directory (Azure AD) を使用してユーザーを認証する

セキュリティで保護されたクラスターは、管理操作に対して未承認のアクセスを防止するクラスターです。 これには、アプリケーション、サービス、およびそれらに格納されているデータのデプロイ、アップグレード、削除が含まれます。 セキュリティで保護されていないクラスターとは、だれでも管理操作にいつでも接続し、実行できるクラスターを指します。 セキュリティで保護されていないクラスターを作成することもできますが、最初からセキュリティで保護されたクラスターを作成することを強くお勧めします。 セキュリティで保護されていないクラスターを後でセキュリティで保護することはできないためです。

セキュリティで保護されたクラスターを作成する概念は、Linux のクラスターでも Windows のクラスターでも同じです。 セキュリティで保護された Linux クラスターの作成に関する情報とヘルパー スクリプトについて詳しくは、「[セキュリティで保護されたクラスターを Linux 上に作成する](#secure-linux-clusters)」をご覧ください。

## <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン
このガイドでは [Azure PowerShell][azure-powershell] を使用します。 新しい PowerShell セッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

Azure アカウントにサインインします。

```powershell
Login-AzureRmAccount
```

サブスクリプションを選択します。

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Key Vault の設定
このセクションでは、Azure Service Fabric クラスターと Service Fabric アプリケーション用の Key Vault を作成する手順を説明します。 Azure Key Vault の完全なガイドについては、[Key Vault のファースト ステップ ガイド][key-vault-get-started]に関するページをご覧ください。

Service Fabric では、X.509 証明書を使用してクラスターをセキュリティで保護し、アプリケーションのセキュリティ機能を提供します。 Key Vault を使用して Azure の Service Fabric クラスターの証明書を管理します。 Azure にクラスターがデプロイされると、Service Fabric クラスターの作成を担当する Azure リソース プロバイダーが Key Vault から証明書を取得し、クラスター VM にインストールします。

次の図は、Azure Key Vault、Service Fabric クラスター、およびクラスターの作成時に Key Vault に格納された証明書を使用する Azure リソース プロバイダー間の関係を示しています。

![証明書のインストールの図][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>リソース グループの作成
まず、Key Vault 専用のリソース グループを作成します。 Key Vault は専用のリソース グループに配置することをお勧めします。 そうすることで、必要なキーとシークレットを失うことなく、コンピューティング リソース グループやストレージ リソース グループを削除することができます (Service Fabric クラスターのあるリソース グループを含む)。 Key Vault を持つリソース グループは、それを使用するクラスターと同じリージョンにある必要があります__。

複数のリージョンでクラスターをデプロイする場合は、そのリソース グループと Key Vault が属しているリージョンがわかるような名前を付けることをお勧めします。  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
出力は次のようになります。

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>新しいリソース グループに Key Vault を作成する
コンピューティング リソース プロバイダーが Key Vault から証明書を取得し、仮想マシンのインスタンスにインストールするには、その Key Vault をデプロイ用に有効にする必要があります。__

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

出力は次のようになります。

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>既存の Key Vault を使用する

既存の Key Vault を使用するには、コンピューティング リソース プロバイダーがそこから証明書を取得し、クラスター ノードにインストールできるように、その Key Vault をデプロイ用に有効にする必要があります。__

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Key Vault に証明書を追加する

Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」をご覧ください。

### <a name="cluster-and-server-certificate-required"></a>クラスターとサーバーの証明書 (必須)
この証明書はクラスターをセキュリティで保護し、クラスターに対する未承認のアクセスを防ぐために必要です。 クラスターのセキュリティは次の&2; つの方法で確保されます。

* クラスター認証: クラスター フェデレーション用のノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。
* サーバー認証: 管理クライアントに対するクラスター管理エンドポイントを認証します。これで、管理クライアントにより、実際のクラスターと通信していることが認識されるようになります。 この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対して SSL も提供します。

この目的のため、証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成される必要があり、Personal Information Exchange (.pfx) ファイルにエクスポートできます。
* 証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から .cloudapp.azure.com ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

### <a name="application-certificates-optional"></a>アプリケーション証明書 (省略可能)
アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

* アプリケーション構成値の暗号化と復号化。
* レプリケーション中のノード間のデータの暗号化。

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Azure リソース プロバイダー用の証明書の書式設定
秘密キー ファイル (.pfx) は Key Vault を通じて直接追加および使用できます。 ただし、Azure コンピューティング リソース プロバイダーには、特別な JavaScript Object Notation (JSON) 形式で格納されるキーが必要です。 形式には、base 64 でエンコードされた文字列と秘密キーのパスワードとして .pfx ファイルが含まれています。 これらの要件に対応するには、キーを JSON 文字列に配置して、"シークレット" として Key Vault に格納する必要があります。

このプロセスを簡単に進めるために、[PowerShell モジュールを GitHub から入手][service-fabric-rp-helpers]できます。 モジュールを使用するには、次の操作を行います。

1. リポジトリの内容全体をローカル ディレクトリにダウンロードします。
2. ローカル ディレクトリに移動します。
2. PowerShell ウィンドウで ServiceFabricRPHelpers モジュールをインポートします。

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

この PowerShell モジュールの `Invoke-AddCertToKeyVault` コマンドは、証明書の秘密キーを JSON 文字列に自動的にフォーマットし、Key Vault にアップロードします。 このコマンドを使用してクラスター証明書と追加のアプリケーション証明書 (ある場合) を Key Vault に追加します。 必要に応じて、クラスターにインストールする追加の証明書についてこの手順を繰り返します。

#### <a name="uploading-an-existing-certificate"></a>既存の証明書をアップロードする

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

ここに示されているようなエラーが発生した場合は、通常、リソース URL の競合があることを意味します。 競合を解決するには、Key Vault 名を変更します。

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

競合が解決されると、出力は次のようになります。

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>セキュリティで保護された Service Fabric クラスターを設定し、アプリケーションのセキュリティ確保のために使用しているアプリケーション証明書を取得するには、先頭に CertificateThumbprint、SourceVault、および CertificateURL の&3; つの文字列が必要です。 文字列を保存しない場合、後で Key Vault を照会してそれらを取得するのが困難になることがあります。

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>自己署名証明書を作成して Key Vault にアップロードする

Key Vault に既に証明書をアップロードしている場合は、この手順をスキップします。 この手順では、自己署名証明書を新しく生成して Key Vault にアップロードします。 次のスクリプトのパラメーターを変更して実行すると、証明書のパスワードを求められます。  

```powershell

$ResouceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

ここに示されているようなエラーが発生した場合は、通常、リソース URL の競合があることを意味します。 競合を解決するには、Key Vault 名や RG 名などを変更します。

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

競合が解決されると、出力は次のようになります。

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>セキュリティで保護された Service Fabric クラスターを設定し、アプリケーションのセキュリティ確保のために使用しているアプリケーション証明書を取得するには、先頭に CertificateThumbprint、SourceVault、および CertificateURL の&3; つの文字列が必要です。 文字列を保存しない場合、後で Key Vault を照会してそれらを取得するのが困難になることがあります。

 この時点で、次の要素が配置されます。

* Key Vault リソース グループ。
* Key Vault とその URL (上記の PowerShell の出力で呼び出された SourceVault)。
* Key Vault 内のクラスター サーバー認証証明書とその URL。
* Key Vault 内のアプリケーション証明書とその URL。


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>クライアント認証用に Azure Active Directory をセットアップする

Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 アプリケーションは、Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションに分けられます。 この記事では、既にテナントを作成していることを前提としています。 まだ作成していない場合は、まず「[Azure Active Directory テナントを取得する方法][active-directory-howto-tenant]」をお読みください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer][service-fabric-visualizing-your-cluster] や [Visual Studio][service-fabric-manage-application-in-visual-studio] など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

Azure AD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

1. コンピューターに[スクリプトをダウンロード][sf-aad-ps-script-download]します。
2. zip ファイルを右クリックし、**[プロパティ]** を選択して **[ブロック解除]** チェックボックスをオンにし、**[適用]** をクリックします。
3. zip ファイルを解凍します。
4. `SetupApplications.ps1` を実行します。パラメーターとして、TenantId、ClusterName、WebApplicationReplyUrl を指定します。 For example:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    TenantId は、PowerShell コマンド `Get-AzureSubscription` を実行することで検索できます。 このコマンドを実行すると、すべてのサブスクリプションの TenantId が表示されます。

    ClusterName は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、一緒に使用される Service Fabric クラスターに対して、Azure AD のアーティファクトのマッピングを容易にするためだけに使用します。

    WebApplicationReplyUrl は、サインインの完了後に Azure AD がユーザーに返す既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

    https://&lt;cluster_domain&gt;:19080/Explorer

    Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure クラシック ポータル][azure-classic-portal]でテナントのアプリケーションを調べると、次の&2; つの新しいエントリがあることがわかります。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

   次のセクションでクラスターを作成するとき、スクリプトによって、Azure Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Service Fabric クラスターの Resource Manager テンプレートを作成する
このセクションでは、前述の PowerShell コマンドの出力が、Service Fabric クラスターの Resource Manager テンプレートで使用されます。

サンプルの Resource Manager テンプレートは、[GitHub の Azure クイック スタート テンプレート ギャラリー][azure-quickstart-templates]で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。

### <a name="create-the-resource-manager-template"></a>Resource Manager テンプレートの作成
このガイドでは、[セキュリティで保護された&5; ノード クラスター][service-fabric-secure-cluster-5-node-1-nodetype]の例にあるテンプレートとテンプレート パラメーターを使用します。 `azuredeploy.json` と `azuredeploy.parameters.json` をコンピューターにダウンロードして、任意のテキスト エディターで両方のファイルを開きます。

### <a name="add-certificates"></a>証明書の追加
証明書は、証明書キーを含む Key Vault を参照することによって、Cluster Resource Manager テンプレートに追加されます。 Key Vault の値は Resource Manager テンプレート パラメーターのファイルに配置することをお勧めします。 これにより Resource Manager テンプレートのファイルを再利用可能にし、デプロイに合わせて値を変更できます。

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>仮想マシン スケール セットの osProfile にすべての証明書を追加する
クラスターにインストールするすべての証明書は、スケール セット リソース (Microsoft.Compute/virtualMachineScaleSets) の [osProfile] セクションで構成する必要があります。 このアクションにより、リソース プロバイダーに対して VM に証明書をインストールするよう指示されます。 このインストールには、クラスター証明書とアプリケーションで使用する予定のあらゆるアプリケーションのセキュリティ証明書が含まれます。

```json
{
  "apiVersion": "2016-03-30",
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

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書を構成する
クラスターの認証証明書は、Service Fabric クラスター リソース (Microsoft.ServiceFabric/clusters) と、仮想マシン スケール セット リソースの仮想マシン スケール セット用 Service Fabric 拡張機能で構成する必要があります。 これにより、Service Fabric のリソース プロバイダーを使用してクラスターの認証および管理エンドポイントのサーバー認証用に構成できます。

##### <a name="virtual-machine-scale-set-resource"></a>仮想マシン スケール セット リソース:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
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

##### <a name="service-fabric-resource"></a>Service Fabric リソース:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Azure AD 構成の挿入
先ほど作成した Azure AD 構成は、Resource Manager テンプレートに直接挿入することができます。 ただし、Resource Manager テンプレートを再利用可能にし、デプロイに合わせて値を変更できるように、まず値をパラメーター ファイルに抽出することをお勧めします。

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
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

### <a name="a-configure-arm-aconfigure-resource-manager-template-parameters"></a><a "configure-arm" ></a>Resource Manager テンプレート パラメーターの構成
最後に、Key Vault と Azure AD PowerShell コマンドからの出力値を使用してパラメーター ファイルを作成します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
この時点で、次の要素が配置されます。

* Key Vault リソース グループ
  * Key Vault
  * クラスター サーバー認証証明書
  * データ暗号化証明書
* Azure Active Directory テナント
  * Web ベースの管理用の Azure AD アプリケーションと Service Fabric Explorer
  * ネイティブ クライアント管理用の Azure AD アプリケーション
  * ユーザーとユーザーに割り当てられたロール
* Service Fabric クラスター Resource Manager テンプレート
  * Key Vault で構成された証明書
  * 構成済みの Azure Active Directory

次の図は、Key Vault と Azure AD の構成を Resource Manager テンプレートに適合させる場所を示しています。

![Resource Manager の依存関係マップ][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>クラスターを作成する
これで、[Azure リソース テンプレート デプロイ][resource-group-template-deploy]を使用してクラスターを作成する準備ができました。

#### <a name="test-it"></a>テストする
次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをテストします。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>デプロイする
Resource Manager テンプレートのテストに合格した場合は、次の PowerShell コマンドを使用して、パラメーター ファイルで Resource Manager テンプレートをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>ユーザーをロールに割り当てる
クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てます。 [Azure クラシック ポータル][azure-classic-portal]を使用してロールを割り当てることができます。

1. Azure Portal で、テナントに移動し、**[アプリケーション]** を選択します。
2. `myTestCluster_Cluster`のような名前を持つ Web アプリケーションを選択します。
3. **[ユーザー]** タブをクリックします。
4. 割り当てるユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

    ![ユーザーをロールに割り当てるためのボタン][assign-users-to-roles-button]
5. ユーザーに割り当てるロールを選択します。

    ![[ユーザーの割り当て] ダイアログ ボックス][assign-users-to-roles-dialog]

> [!NOTE]
> Service Fabric でのロールの詳細については、「 [ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。
>
>

 <a name="secure-linux-cluster"></a>

## <a name="create-secure-clusters-on-linux"></a>セキュリティで保護されたクラスターを Linux 上に作成する
プロセス簡略化のために、[ヘルパー スクリプト](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)を用意しました。 このヘルパー スクリプトを使用する前に、Azure コマンド ライン インターフェイス (CLI) が既にインストールされており、パス内にあることを確認します。 ダウンロード後、必ず `chmod +x cert_helper.py` を実行して、スクリプトに実行権限を割り当ててください。 まず、CLI から `azure login` コマンドを実行して Azure アカウントにサインインします。 Azure アカウントへのサインイン後、次のコマンドに示すように、証明機関の署名入りの証明書と一緒にヘルパー スクリプトを指定します。

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

-ifile パラメーターには、証明書の種類として .pfx ファイルまたは .pem ファイル (pfx または pem、自己署名証明書の場合は ss) を指定できます。
パラメーター -h はヘルプ テキストを出力します。


コマンドの出力として次の&3; つの文字列が返されます。

* SourceVaultID: 新しく作成された KeyVault ResourceGroup の ID
* CertificateUrl: 証明書にアクセスするために使用
* CertificateThumbprint: 認証に使用

次の例は、コマンドの使用方法を示しています。

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
先頭のコマンドを実行すると、次の&3; つの文字列が返されます。

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 CA から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

これらのサブジェクト名は、「[Resource Manager テンプレート パラメーターの構成](#configure-arm)」で取り上げた、セキュリティで保護された Service Fabric クラスターを (Azure AD なしで) 作成するために必要なエントリです。 セキュリティで保護されたクラスターには、[クラスターに対するクライアント アクセスの認証](service-fabric-connect-to-secure-cluster.md)に関するページの手順に従って接続できます。 Linux プレビュー クラスターでは、Azure AD 認証がサポートされません。 「[ユーザーをロールに割り当てる](#assign-roles)」セクションの説明に従って管理者ロールとクライアント ロールを割り当ててください。 Linux プレビュー クラスターに管理者とクライアントのロールを指定する際には、認証に対して証明書の拇印を提供する必要があります  (このプレビュー リリースではチェーン検証や失効が行われないため、サブジェクト名は指定しません)。

テストに自己署名証明書を使用する場合は、同じスクリプトを使用して生成できます。 その後、証明書のパスと名前を指定する代わりに、フラグ `ss` を指定して Key Vault に証明書をアップロードできます。 たとえば自己署名証明書を作成してアップロードするには、次のコマンドを使用します。

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
このコマンドにより、3 つの同じ文字列 (SourceVault、CertificateUrl、および CertificateThumbprint) が返されます。 その後、これらの文字列を使用して、セキュリティで保護された Linux クラスターと自己署名証明書が配置される場所を作成できます。 クラスターに接続するには、自己署名証明書が必要です。 セキュリティで保護されたクラスターには、[クラスターに対するクライアント アクセスの認証](service-fabric-connect-to-secure-cluster.md)に関するページの手順に従って接続できます。

証明書のサブジェクト名は Service Fabric クラスターへのアクセスに使用されるドメインと一致している必要があります。 この整合性は、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 CA から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書のサブジェクト名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

ヘルパー スクリプトから得られるパラメーターは、「[Azure Portal でのクラスターの作成](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)」セクションの説明に従って Azure Portal に入力できます。

## <a name="next-steps"></a>次のステップ
この時点で、管理認証を提供する Azure Active Directory で、セキュリティで保護されたクラスターの準備ができています。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>クライアント認証用の Azure Active Directory のセットアップのトラブルシューティングを行う
クライアント認証のために Azure AD をセットアップしているときに問題が発生した場合は、このセクションの考えられる解決策を確認してください。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer に、証明書の選択を求めるメッセージが表示される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD に正常にサインインすると、ブラウザーはホームページに戻りますが、証明書の選択を求めるメッセージが表示されます。

![SFX の証明書を選択するためのダイアログ][sfx-select-certificate-dialog]

#### <a name="reason"></a>理由
ユーザーに Azure AD クラスター アプリケーション内でロールが割り当てられていません。 このため、Service Fabric クラスターで Azure AD の認証が失敗します。 Service Fabric エクスプローラーは、証明書認証にフォールバックします。

#### <a name="solution"></a>解決策
次の説明に従って Azure AD をセットアップしてユーザー ロールを割り当てます。 また、`SetupApplications.ps1` のように [アプリにアクセスするにはユーザー割り当てが必要] をオンにすることをお勧めします。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>PowerShell を使用した接続が失敗し、"指定した資格情報が無効です" というエラーが返される
#### <a name="problem"></a>問題点
"AzureActiveDirectory" セキュリティ モードで PowerShell を使用してクラスターに接続すると、Azure AD に正常にサインインした後で接続に失敗し、"指定した資格情報が無効です" というエラーが返されます。

#### <a name="solution"></a>解決策
前の問題の解決策と同じです。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>サインインするときに Service Fabric Explorer で "AADSTS50011" エラーが返される
#### <a name="problem"></a>問題点
Service Fabric Explorer で Azure AD へのサインインを試行すると、"AADSTS50011: 応答アドレス &lt;url&gt; が、アプリケーションに対して構成された応答アドレス &lt;guid&gt; と一致していません" というエラーが返されます。

![SFX の応答アドレスが一致しない][sfx-reply-address-not-match]

#### <a name="reason"></a>理由
Service Fabric Explorer に相当するクラスター (Web) アプリケーションは Azure AD に対する認証を試み、要求の一部として、戻り先のリダイレクト URL を指定しています。 しかし、その URL が Azure AD アプリケーションの **[応答 URL]** のリストに表示されません。

#### <a name="solution"></a>解決策
Service Fabric Explorer の URL をクラスター (Web) アプリケーションの **[構成]** タブの **[応答 URL]** のリストに追加するか、リスト内の項目のいずれかを置換します。 完了したら、変更を保存します。

![Web アプリケーションの応答 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell で Azure AD 認証を使用してクラスターに接続する
Service Fabric クラスターに接続するには、次の PowerShell コマンド例を使用します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Connect-ServiceFabricCluster コマンドレットについて詳しくは、「[Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)」をご覧ください。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>複数のクラスターで同じ Azure AD テナントを再利用できますか?
はい。 ただし、Service Fabric Explorer の URL をクラスター (Web) アプリケーションに追加することを忘れないでください。 そうしないと、Service Fabric Explorer は動作しません。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD が有効になっているときもサーバーの証明書が必要なのはどうしてですか?
FabricClient と FabricGateway では、相互認証が実行されます。 Azure AD 認証中、Azure AD 統合がクライアント ID をサーバーに提供し、サーバー証明書を使用してサーバー ID の確認が行われます。 Service Fabric の証明書について詳しくは、「[X.509 証明書と Service Fabric][x509-certificates-and-service-fabric]」をご覧ください。

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

