---
title: Azure で Windows を実行する Service Fabric クラスターを作成する
description: このチュートリアルでは、PowerShell を使用して Windows Service Fabric クラスターを Azure 仮想ネットワークやネットワーク セキュリティ グループにデプロイする方法を学習します。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245043"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>チュートリアル:Azure 仮想ネットワークに Windows を実行する Service Fabric クラスターをデプロイする

このチュートリアルは、シリーズの第 1 部です。 PowerShell とテンプレートを使用して、Windows を実行している Azure Service Fabric クラスターを [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)および[ネットワーク セキュリティ グループ](../virtual-network/virtual-network-vnet-plan-design-arm.md)にデプロイする方法を説明します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。 Azure CLI を使用する Linux クラスターを作成する場合は、[セキュリティで保護された Linux クラスターを Azure で作成する](service-fabric-tutorial-create-vnet-and-linux-cluster.md)方法に関するページを参照してください。

このチュートリアルでは、本番環境シナリオについて説明します。 テスト目的で小規模のクラスターを作成する場合は、[テスト クラスターの作成](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * Azure Active Directory 認証を設定する
> * 診断の収集を構成する
> * EventStore サービスを設定する
> * Azure Monitor ログを設定する
> * セキュリティで保護された Service Fabric クラスターを Azure PowerShell に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure にセキュリティで保護されたクラスターを作成する
> * [クラスターを監視する](service-fabric-tutorial-monitor-cluster.md)
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [クラスターの削除](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールします。
* [Azure PowerShell](/powershell/azure/install-az-ps)をインストールします。
* [Azure クラスター](service-fabric-azure-clusters-overview.md)の主要な概念を確認します。
* 運用環境用のクラスターのデプロイを[計画して準備します](service-fabric-cluster-azure-deployment-preparation.md)。

次の手順で、7 ノードの Service Fabric クラスターを作成します。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して、Azure で Service Fabric クラスターを実行することによって発生するコストを計算します。

## <a name="download-and-explore-the-template"></a>テンプレートのダウンロードと詳細の確認

次の Azure Resource Manager テンプレート ファイルをダウンロードします。

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

このテンプレートでは、7 つの仮想マシンと 3 つのノード タイプから成るセキュリティ保護されたクラスターが、仮想ネットワークとネットワーク セキュリティ グループにデプロイされます。  [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) には他のサンプル テンプレートがあります。 [azuredeploy.json][template] では、次のような多数のリソースがデプロイされます。

### <a name="service-fabric-cluster"></a>Service Fabric クラスター

**Microsoft.ServiceFabric/clusters** リソースでは、以下の特性によって Windows クラスターが構成されます。

* 3 つのノード タイプ。
* プライマリ ノード タイプに 5 つのノード (テンプレート パラメーターで構成可能)、他の 2 つのノード タイプにそれぞれ 1 つのノード。
* OS: コンテナー搭載 Windows Server 2016 Datacenter (テンプレート パラメーターで構成可能)。
* 証明書のセキュリティ保護 (テンプレート パラメーターで構成可能)。
* [リバース プロキシ](service-fabric-reverseproxy.md)が有効。
* [DNS サービス](service-fabric-dnsservice.md)が有効。
* ブロンズ[持続性レベル](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)。
* シルバー[信頼性レベル](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (テンプレート パラメーターで構成可能)。
* クライアント接続エンドポイント: 19000 (テンプレート パラメーターで構成可能)。
* HTTP ゲートウェイ エンドポイント: 19080 (テンプレート パラメーターで構成可能)。

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft.Network/loadBalancers** リソースでは、ロード バランサーが構成されます。 プローブと規則は、以下のポートに対して設定されます。

* クライアント接続エンドポイント: 19000
* HTTP ゲートウェイ エンドポイント: 19080
* アプリケーション ポート: 80
* アプリケーション ポート: 443
* Service Fabric リバース プロキシ: 19081

その他のアプリケーション ポートが必要な場合は、トラフィックの受信を許可するように **Microsoft.Network/loadBalancers** リソースと **Microsoft.Network/networkSecurityGroups** リソースを調整する必要があります。

### <a name="virtual-network-subnet-and-network-security-group"></a>仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループ

仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループの名前は、テンプレート パラメーターで宣言されます。 仮想ネットワークとサブネットのアドレス空間も、テンプレート パラメーターで宣言され、**Microsoft.Network/virtualNetworks** リソース内に構成されます。

* 仮想ネットワークのアドレス空間:172.16.0.0/20
* Service Fabric サブネットのアドレス空間: 172.16.2.0/23

**Microsoft.Network/networkSecurityGroups** リソースでは、以下の受信トラフィック ルールが有効になります。 テンプレートの変数を変更することで、ポートの値を変更できます。

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* ノード間通信: 1025、1026、1027
* 一時的なポート範囲: 49152 から 65534 (256 ポート以上が必要)。
* アプリケーション用のポート: 80 および 443
* アプリケーション ポート範囲: 49152 から 65534 (サービス間通信に使用されます。 他のポートは Load Balancer では開かれません)。
* 他のすべてのポートをブロック

その他のアプリケーション ポートが必要な場合は、トラフィックの受信を許可するように **Microsoft.Network/loadBalancers** リソースと **Microsoft.Network/networkSecurityGroups** リソースを調整する必要があります。

### <a name="windows-defender"></a>Windows Defender
既定では、Windows Server 2016 には [Windows Defender ウイルス対策プログラム](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)がインストールされ、機能します。 一部の SKU ではユーザー インターフェイスが既定でインストールされますが、必須ではありません。 テンプレートで宣言されている各ノード タイプ/VM スケール セットに対して、Service Fabric ディレクトリとプロセスを除外するために、[Azure VM マルウェア対策拡張機能](../virtual-machines/extensions/iaas-antimalware-windows.md)が使用されます。

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>テンプレート パラメーターの設定

[azuredeploy.parameters.json][parameters] パラメーター ファイルでは、クラスターおよび関連リソースをデプロイするための多くの値が宣言されています。 実際のデプロイに合わせて変更するパラメーターは、次のとおりです。

**パラメーター** | **値の例** | **メモ** 
|---|---|---|
|adminUserName|vmadmin| クラスター VM の管理者ユーザー名。 [VM のユーザー名要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)。 |
|adminPassword|Password#1234| クラスター VM の管理者パスワード。 [VM のパスワード要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
|clusterName|mysfcluster123| クラスターの名前。 使用できる文字はアルファベットと数字のみです。 長さは 3 から 23 文字で指定できます。|
|location|southcentralus| クラスターの場所。 |
|certificateThumbprint|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の SHA1 サムプリントの値を入力します。 例: "6190390162C988701DB5676EB81083EA608DCCF3"。</p> |
|certificateUrlValue|| <p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。 </p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、証明書の URL を入力します。 例: "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346"。</p>|
|sourceVaultValue||<p>自己署名証明書を作成する場合または証明書ファイルを提供する場合は、値を空にする必要があります。</p><p>以前にキー コンテナーにアップロードされた既存の証明書を使用するには、ソース コンテナー値を入力します。 たとえば、"/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT" と入力します。</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory クライアント認証の設定
Azure でホストされているパブリック ネットワークにデプロイされた Service Fabric クラスターの場合、クライアントとノードの間の相互認証は次のようにすることを推奨します。
* クライアント ID には Azure Active Directory を使用する。
* サーバー ID および HTTP 通信の TLS 暗号化には証明書を使用する。

Service Fabric クラスターのクライアントを認証するための Azure Active Directory (Azure AD) の設定は、[クラスターを作成する](#createvaultandcert)前に行う必要があります。 Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) や [Visual Studio](service-fabric-manage-application-in-visual-studio.md) など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。  アプリケーションを作成した後は、ユーザーを読み取り専用ロールおよび管理者ロールに割り当てます。

> [!NOTE]
> クラスターを作成する前に、次の手順を完了する必要があります。 スクリプトはクラスター名とエンドポイントを想定しているため、値は作成済みのものではなく、計画する必要があります。

この記事では、既にテナントが作成されていることを前提としています。 まだ作成していない場合は、まず、[Azure Active Directory テナントを取得する方法](../active-directory/develop/quickstart-create-new-tenant.md)に関するページをお読みください。

Azure AD での Service Fabric クラスターに関する構成の手順を簡略化するために、一連の Windows PowerShell スクリプトが用意されています。 コンピューターに[スクリプトをダウンロード](https://github.com/Azure-Samples/service-fabric-aad-helpers)します。

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD アプリケーションを作成し、ユーザーをロールに割り当てる
クラスターへのアクセスを制御するために、Web アプリケーションとネイティブ アプリケーションの 2 つの Azure AD アプリケーションを作成します。 クラスターを表すアプリケーションを作成したら、[Service Fabric によってサポートされるロール](service-fabric-cluster-security-roles.md) (読み取り専用と管理者) にユーザーを割り当てます。

`SetupApplications.ps1` を実行します。パラメーターとして、テナント ID、クラスター名、および Web アプリケーション応答 URL を指定します。 ユーザーのユーザー名とパスワードを指定します。 次に例を示します。

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> ナショナル クラウド (たとえば Azure Government、Azure China、Azure Germany) については、`-Location` パラメーターを指定します。

*TenantId* (ディレクトリ ID) は、[Azure portal](https://portal.azure.com) で確認できます。 **[Azure Active Directory]**  >  **[プロパティ]** の順に選択し、 **[ディレクトリ ID]** の値をコピーします。

*ClusterName* は、スクリプトによって作成される Azure AD アプリケーションのプレフィックスとして使用されます。 実際のクラスター名と完全に一致している必要はありません。 これは、使用されている Service Fabric クラスターへの Azure AD アーティファクトのマッピングを容易にすることだけが目的です。

*WebApplicationReplyUrl* は、サインインの完了後に Azure AD によってユーザーに返される既定のエンドポイントです。 このエンドポイントをクラスターの Service Fabric Explorer エンドポイントして設定します。既定値は次のとおりです。

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD テナント用の管理特権を持っているアカウントにサインインすることを求められます。 サインインすると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションを作成します。 [Azure portal](https://portal.azure.com) で、テナントのアプリケーションに次の 2 つの新しいエントリが表示されるはずです。

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

スクリプトによって、クラスターを作成するときに Resource Manager テンプレートが必要とする JSON が出力されます。このため、PowerShell ウィンドウは開いたままにしておきます。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>クライアント アクセスに Azure AD を使用する Azure AD の構成を追加する
[azuredeploy.json][template] の **Microsoft.ServiceFabric/clusters** セクションで、Azure AD を構成します。 テナント ID、クラスター アプリケーション ID、およびクライアント アプリケーション ID のためのパラメーターを追加します。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
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

[azuredeploy.parameters.json][parameters] パラメーター ファイルで、パラメーター値を追加します。 次に例を示します。

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>クラスターにおける診断の収集の構成
Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログをアップロードして収集する 1 つの方法として、Azure Diagnostics (WAD) 拡張機能を使用します。この機能を使用すると、ログが Azure Storage にアップロードされますが、Azure Application Insights や Event Hubs にログを送信することもできます。 また、外部プロセスを使用してストレージからイベントを読み取り、Azure Monitor ログなどの分析プラットフォーム製品や別のログ解析ソリューションに配置することもできます。

このチュートリアルに従って操作している場合、診断の収集は[テンプレート][template]で既に構成されています。

まだ診断がデプロイされていない既存のクラスターがある場合は、クラスター テンプレートを使用して追加または更新を実行できます。 既存クラスターの作成に使用した Resource Manager テンプレートを変更するか、ポータルからテンプレートをダウンロードします。 次のタスクを実行して、template.json ファイルを変更します。

テンプレートの resources セクションに新しいストレージ リソースを追加します。
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

次に、ストレージ アカウント名と種類のパラメーターをテンプレートの parameters セクションに追加します。 プレースホルダー テキストの storage account name goes here をストレージ アカウントの名前に置き換えます。

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

次に、クラスター内の各 **Microsoft.Compute/virtualMachineScaleSets** リソースの **VirtualMachineProfile** プロパティの extensions 配列に **IaaSDiagnostics** 拡張機能を追加します。  [サンプル テンプレート][template]を使用している場合は、3 つの仮想マシン スケール セットがあります (クラスター内の各ノード タイプに 1 つずつ)。

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>EventStore サービスの構成
EventStore サービスは、Service Fabric の監視オプションです。 EventStore では、任意の時点でのクラスターまたはワークロードの状態を理解する手段が提供されます。 EventStore は、クラスターからのイベントを保持するステートフルな Service Fabric サービスです。 イベントは、Service Fabric Explorer や REST API を介して公開されます。 EventStore では、クラスターに対してクエリが直接行われて、クラスター内の任意のエンティティについて診断データが取得されます。EventStore は、以下に役立てるために使用します。

* 開発またはテストにおける問題の診断や、監視パイプラインを使用できる場所の診断
* クラスターに対して行っている管理アクションが、正しく処理されていることの確認
* Service Fabric が特定のエンティティと対話している方法の "スナップショット" の取得



クラスターで EventStore サービスを有効にするには、**Microsoft.ServiceFabric/clusters** リソースの **fabricSettings** プロパティに以下を追加します。

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>クラスターの Azure Monitor ログの設定

クラスター レベルのイベントを監視する手段として、Microsoft は Azure Monitor ログを推奨しています。 クラスターを監視するように Azure Monitor ログを設定するには、[クラスターレベルのイベントを確認できるように診断を有効にする](#configure-diagnostics-collection-on-the-cluster)必要があります。  

ワークスペースは、クラスターから取得する診断データとの接続を維持しておく必要があります。  このログ データは、*applicationDiagnosticsStorageAccountName* ストレージ アカウントの WADServiceFabric*EventTable、WADWindowsEventLogsTable、および WADETWEventTable テーブルに格納されます。

Azure Log Analytics ワークスペースを追加し、ワークスペースにソリューションを追加します。

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

次に、パラメーターを追加します。
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

次に、変数を追加します。
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Log Analytics エージェント拡張機能をクラスター内の各仮想マシン スケール セットに追加し、エージェントを Log Analytics ワークスペースに接続します。 これにより、コンテナー、アプリケーション、およびパフォーマンスの監視に関する診断データを収集できます。 これを、仮想マシン スケール セット リソースの拡張機能として追加することで、クラスターをスケーリングしても Azure Resource Manager によってすべてのノードにインストールされるようになります。

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>仮想ネットワークとクラスターのデプロイ

次に、ネットワーク トポロジを設定し、Service Fabric クラスターをデプロイします。 [azuredeploy.json][template] Resource Manager テンプレートによって、Service Fabric の仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループが作成されます。 このテンプレートを使用すると、証明書セキュリティが有効なクラスターもデプロイできます。 運用環境クラスターの場合は、証明機関から取得した証明書をクラスター証明書として使用します。 自己署名証明書を使用して、テスト クラスターを保護することができます。

この記事のテンプレートでは、クラスター証明書を識別するために証明書の拇印を使用するクラスターがデプロイされます。 2 つの証明書が同じ拇印を持つことはできず、そのことが証明書の管理をより困難にしています。 デプロイされたクラスターを証明書の拇印から証明書共通名に切り替えることで、証明書の管理がシンプルになります。 証明書の管理に証明書共通名を使用するようにクラスターを更新する方法については、[証明書共通名管理へのクラスターの変更](service-fabric-cluster-change-cert-thumbprint-to-cn.md)に関するページを参照してください。

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>既存の証明書を使用したクラスターの作成

次のスクリプトでは、[New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) コマンドレットとテンプレートを使用して、Azure に新しいクラスターをデプロイします。 このコマンドレットでは、Azure に新しいキー コンテナーが作成され、証明書がアップロードされます。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>新しい自己署名証明書を使用したクラスターの作成

次のスクリプトでは、[New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) コマンドレットとテンプレートを使用して、Azure に新しいクラスターをデプロイします。 このコマンドレットでは、Azure に新しいキー コンテナーが作成され、そのキー コンテナーに新しい自己署名証明書が追加されます。さらに、証明書ファイルがローカルにダウンロードされます。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する

Service Fabric SDK と共にインストールされた Service Fabric PowerShell モジュールを使用して、クラスターに接続します。  まず、この証明書を現在のユーザーのコンピューターの個人用 (マイ) ストアにインストールする必要があります。 次の PowerShell コマンドを実行します。

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。 セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の SHA1 サムプリントと接続エンドポイントの詳細は、前の手順の出力で確認できます。

以前に Azure AD クライアント認証を設定した場合は、次のコマンドを実行します。 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Azure AD クライアント認証を設定しなかった場合は、次のコマンドを実行します。
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) コマンドレットを使用して、接続とクラスターの正常性を確認します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアル シリーズの他の記事では、ここで作成したクラスターを使用します。 次の記事にすぐに進まない場合は、料金の発生を避けるため、[クラスターを削除](./service-fabric-tutorial-delete-cluster.md)することができます。

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進んで、お使いのクラスターをスケーリングする方法について学習します。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * Azure Active Directory 認証を設定する
> * 診断の収集を構成する
> * EventStore サービスを設定する
> * Azure Monitor ログを設定する
> * セキュリティで保護された Service Fabric クラスターを Azure PowerShell に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

次に、以下のチュートリアルに進んで、お使いのクラスターを監視する方法について学習します。
> [!div class="nextstepaction"]
> [クラスターを監視する](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
