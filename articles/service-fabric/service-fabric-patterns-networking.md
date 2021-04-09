---
title: Azure Service Fabric のネットワーク パターン
description: Service Fabric の一般的なネットワーク パターンと、Azure のネットワーク機能を使用してクラスターを作成する方法について説明します。
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421469"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric のネットワーク パターン
Azure Service Fabric クラスターを Azure の他のネットワーク機能と統合できます。 この記事では、次の機能を使用するクラスターを作成する方法について説明します。

- [既存の仮想ネットワークまたはサブネット](#existingvnet)
- [静的パブリック IP アドレス](#staticpublicip)
- [内部専用ロード バランサー](#internallb)
- [内部ロード バランサーと外部ロード バランサー](#internalexternallb)

Service Fabric は標準の仮想マシン スケール セットで実行されます。 仮想マシン スケール セットで使用できる機能は、Service Fabric クラスターでも使用できます。 仮想マシン スケール セットと Service Fabric のAzure Resource Manager テンプレートのネットワーク セクションは同じです。 既存の仮想ネットワークにデプロイしたら、Azure ExpressRoute、Azure VPN Gateway、ネットワーク セキュリティ グループ、仮想ネットワーク ピアリングなどの他のネットワーク機能を簡単に組み込むことができます。

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Service Fabric リソース プロバイダーによるクラスターに対するクエリの許可

Service Fabric には、他のネットワーク機能とは異なる点が 1 つあります。 [Azure Portal](https://portal.azure.com) がService Fabric リソース プロバイダーを内部で使用してクラスターを呼び出し、ノードとアプリケーションに関する情報を取得します。 Service Fabric リソース プロバイダーは、管理エンドポイントの受信 HTTP ゲートウェイ ポート (既定では 19080) にパブリックにアクセスできる必要があります。 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) では、管理エンドポイントを使用してクラスターを管理します。 また、Service Fabric リソース プロバイダーは、このポートを使用してクラスターに関する情報を照会し、Azure Portal に表示します。 

Service Fabric リソース プロバイダーからポート 19080 にアクセスできない場合、ポータルに "*ノードが見つかりません*" などのメッセージが表示され、ノードとアプリケーションの一覧が空になります。 Azure Portal でクラスターを確認する場合は、ロード バランサーでパブリック IP アドレスを公開し、ネットワーク セキュリティ グループでポート 19080 の受信トラフィックを許可する必要があります。 セットアップがこれらの要件を満たしていない場合、Azure Portal にクラスターの状態は表示されません。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>テンプレート

すべての Service Fabric テンプレートが [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking) に存在します。 次の PowerShell コマンドを使用して、テンプレートをそのままデプロイできます。 既存の Azure Virtual Network テンプレートまたは静的パブリック IP テンプレートをデプロイする場合は、まず、この記事の「[初期セットアップ](#initialsetup)」をお読みください。

<a id="initialsetup"></a>
## <a name="initial-setup"></a>初期セットアップ

### <a name="existing-virtual-network"></a>既存の仮想ネットワーク

次の例では、**ExistingRG** リソース グループにある "ExistingRG-vnet" という名前の既存の仮想ネットワークを使用します。 サブネットの名前は "default" です。 Azure Portal で標準の仮想マシン (VM) を作成すると、これらの既定のリソースが作成されます。 VM を作成せずに仮想ネットワークとサブネットを作成することもできますが、クラスターを既存の仮想ネットワークに追加する主な目的は、他の VM へのネットワーク接続を提供することです。 VM を作成することで、既存の仮想ネットワークの通常の使用方法の好例が示されます。 Service Fabric クラスターでパブリック IP アドレスを指定せずに内部ロード バランサーのみを使用している場合は、VM とそのパブリック IP をセキュリティで保護された "*ジャンプ ボックス*" として使用できます。

### <a name="static-public-ip-address"></a>静的パブリック IP アドレス

一般に、静的パブリック IP アドレスは、割り当て先の VM とは別に管理されている専用のリソースです。 静的パブリック IP アドレスは (Service Fabric クラスター リソース グループ内ではなく) 専用のネットワーク リソース グループにプロビジョニングされます。 Azure Portal または PowerShell を使用して、同じExistingRG リソース グループに "staticIP1" という名前の静的パブリック IP アドレスを作成します。

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric テンプレート

この記事の例では、Service Fabric template.json を使用します。 クラスターを作成する前に、標準のポータル ウィザードを使用してポータルからテンプレートをダウンロードできます。 また、[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)のいずれか ([セキュリティ保護された 5 ノード Service Fabric クラスター](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)など) を使用こともできます。

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>既存の仮想ネットワークまたはサブネット

1. サブネットのパラメーターを既存のサブネットの名前に変更し、既存の仮想ネットワークを参照する 2 つの新しいパラメーターを追加します。

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

   Azure portal のクラスター デプロイ ブレードで仮想ネットワーク名の入力を再び求めないよう、"virtualNetworkName" という名前でパラメーターをコメント アウトできます。

2. 既存のサブネットを使用し、手順 1 でこの変数を無効にしているため、`Microsoft.Compute/virtualMachineScaleSets` の `nicPrefixOverride` 属性をコメント アウトします。

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. 既存の仮想ネットワークを指すように `vnetID` 変数を変更します。

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Azure で新しい仮想ネットワークが作成されないように、リソースから `Microsoft.Network/virtualNetworks` を削除します。

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. 新しい仮想ネットワークの作成に依存しないように、`Microsoft.Compute/virtualMachineScaleSets` の `dependsOn` 属性から仮想ネットワークをコメント アウトします。

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. テンプレートをデプロイします。

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    デプロイが完了すると、仮想ネットワークに新しいスケール セット VM が追加されます。 仮想マシン スケール セットのノード タイプに、既存の仮想ネットワークとサブネットが示されます。 また、リモート デスクトップ プロトコル (RDP) を使用して、仮想ネットワークに既に存在する VM にアクセスしたり、新しいスケール セット VM への ping を実行したりできます。

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

別の例については、[Service Fabric に固有でない例](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet)をご覧ください。


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>静的パブリック IP アドレス

1. 既存の静的 IP のリソース グループ名、名前、完全修飾ドメイン名 (FQDN) のパラメーターを追加します。

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. `dnsName` パラメーターを削除します (静的 IP アドレスには既にパラメーターがあります)。

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. 既存の静的 IP アドレスを参照する変数を追加します。

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Azure で新しい IP アドレスが作成されないように、リソースから `Microsoft.Network/publicIPAddresses` を削除します。

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 新しい IP アドレスの作成に依存しないように、`Microsoft.Network/loadBalancers` の `dependsOn` 属性から IP アドレスをコメント アウトします。

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. `Microsoft.Network/loadBalancers` リソースで、`frontendIPConfigurations` の `publicIPAddress` 要素を変更して、新しく作成された IP アドレスではなく、既存の静的 IP アドレスを参照します。

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. `Microsoft.ServiceFabric/clusters` リソースで、`managementEndpoint` を静的 IP アドレスの DNS FQDN に変更します。 セキュリティで保護されたクラスターを使用している場合は、*http://* を *https://* に必ず変更してください (この手順は Service Fabric クラスターにのみ適用されます。 仮想マシン スケール セットを使用している場合は、この手順をスキップしてください)。

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. テンプレートをデプロイします。

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

デプロイが完了すると、ロード バランサーが他のリソース グループからパブリック静的 IP アドレスにバインドされていることを確認できます。 Service Fabric クライアント接続エンドポイントと [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) エンドポイントは、静的 IP アドレスの DNS FQDN を参照します。

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>内部ロード バランサー

このシナリオでは、既定の Service Fabric テンプレートの外部ロード バランサーを内部専用ロード バランサーに置き換えます。 Azure portal と Service Fabric リソース プロバイダーへの影響については、[この記事の前述の説明](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)をご覧ください。

1. `dnsName` パラメーターを削除します (不要です)。

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. 静的な割り当て方法を使用している場合は、必要に応じて静的 IP アドレス パラメーターを追加できます。 動的な割り当て方法を使用している場合は、この手順を実行する必要はありません。

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Azure で新しい IP アドレスが作成されないように、リソースから `Microsoft.Network/publicIPAddresses` を削除します。

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 新しい IP アドレスの作成に依存しないように、`Microsoft.Network/loadBalancers` の、IP アドレスの `dependsOn` 属性を削除します。 現在、ロード バランサーは仮想ネットワークのサブネットに依存しているため、仮想ネットワークの `dependsOn` 属性を追加します。

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. `publicIPAddress` を使用するのではなく、サブネットと `privateIPAddress` を使用するように、ロード バランサーの `frontendIPConfigurations` 設定を変更します。 `privateIPAddress` では、定義済みの静的内部 IP アドレスを使用します。 動的 IP アドレスを使用するには、`privateIPAddress` 要素を削除し、`privateIPAllocationMethod` を **Dynamic** に変更します。

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. `Microsoft.ServiceFabric/clusters` リソースで、内部ロード バランサーのアドレスを指すように `managementEndpoint` を変更します。 セキュリティで保護されたクラスターを使用している場合は、*http://* を *https://* に必ず変更してください (この手順は Service Fabric クラスターにのみ適用されます。 仮想マシン スケール セットを使用している場合は、この手順をスキップしてください)。

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. テンプレートをデプロイします。

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

デプロイが完了すると、ロード バランサーでプライベート静的 IP アドレス (10.0.0.250) が使用されます。 同じ仮想ネットワーク内に別のマシンがある場合は、内部の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) エンドポイントに移動できます。 このエンドポイントは、ロード バランサーの背後にあるノードの 1 つに接続しています。

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>内部ロード バランサーと外部ロード バランサー

このシナリオでは、既存の単一ノード タイプの外部ロード バランサーを使用し、同じノード タイプの内部ロード バランサーを追加します。 バックエンド アドレス プールに接続されたバックエンド ポートは、1 つのロード バランサーにのみ割り当てることができます。 アプリケーション ポートを配置するロード バランサーと、管理エンドポイント (ポート 19000 と 19080) を配置するロード バランサーを選択します。 管理エンドポイントを内部ロード バランサーに配置する場合は、[この記事で前述した](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) Service Fabric リソース プロバイダーの制限に注意してください。 ここで使用する例では、管理エンドポイントは引き続き外部ロード バランサーに配置しています。 また、アプリケーション ポート 80 を追加して内部ロード バランサーに配置します。

2 ノード タイプのクラスターでは、ノード タイプの 1 つを外部ロード バランサーに配置し、 もう 1 つのノード タイプを内部ロード バランサーに配置します。 2 ノード タイプのクラスターを使用するには、ポータルで作成された (2 つのロード バランサーを含む) 2 ノード タイプ テンプレートで、2 つ目のロード バランサーを内部ロード バランサーに切り替えます。 詳細については、「[内部ロード バランサー](#internallb)」をご覧ください。

1. 内部ロード バランサーの静的 IP アドレス パラメーターを追加します (動的 IP アドレスの使用に関する注意事項については、この記事の前のセクションをご覧ください)。

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. アプリケーション ポート 80 パラメーターを追加します。

3. 既存のネットワーク変数の内部バージョンを追加するには、それらの変数をコピーして貼り付け、名前に "-Int" を追加します。

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. アプリケーション ポート 80 を使用する、ポータルで生成されたテンプレートを使用すると、ポータルの既定のテンプレートによって外部ロード バランサーに AppPort1 (ポート 80) が追加されます。 この場合、外部ロード バランサーの `loadBalancingRules` とプローブから AppPort1 を削除して、これを内部ロード バランサーに追加できるようにします。

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. 2 つ目の `Microsoft.Network/loadBalancers` リソースを追加します。 これは、「[内部ロード バランサー](#internallb)」で作成した内部ロード バランサーに似ていますが、"-Int" ロード バランサー変数を使用し、アプリケーション ポート 80 のみを実装します。 これにより、パブリック ロード バランサーの RDP エンドポイントを保持するために `inboundNatPools` も削除されます。 内部ロード バランサーで RDP が必要な場合は、`inboundNatPools` を外部ロード バランサーからこの内部ロード バランサーに移動します。

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. `Microsoft.Compute/virtualMachineScaleSets` リソースの `networkProfile` で、内部バックエンド アドレス プールを追加します。

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. テンプレートをデプロイします。

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

デプロイが完了すると、リソース グループに 2 つのロード バランサーが表示されます。 ロード バランサーを参照すると、パブリック IP アドレスと、パブリック IP アドレスに割り当てられている管理エンドポイント (ポート 19000 と 19080) を確認できます。 また、静的内部 IP アドレスと、内部ロード バランサーに割り当てられているアプリケーション エンドポイント (ポート 80) も確認できます。 ロード バランサーはどちらも同じ仮想マシン スケール セットのバックエンド プールを使用します。

## <a name="notes-for-production-workloads"></a>運用環境のワークロードに関する注意事項

上記の GitHub テンプレートは、Azure Standard Load Balancer (SLB) の既定の SKU である Basic SKU で動作するように設計されています。 この SLB には SLA がないため、運用環境のワークロードには Standard SKU を使用する必要があります。 詳しくは、「[Azure Standard Load Balancer の概要](../load-balancer/load-balancer-overview.md)」をご覧ください。 SLB の Standard SKU を使用しているすべての Service Fabric クラスターで、ポート 443 での送信トラフィックを許可するルールがノード タイプごとに設定されていることを確認する必要があります。 これはクラスターの設定を完了するために必要であり、このルールのないデプロイは失敗します。 上記の "内部専用" ロード バランサーの例では、追加の外部ロード バランサーを、ポート 443 での送信トラフィックを許可するルールと共にテンプレートに追加する必要があります。

## <a name="next-steps"></a>次のステップ
[クラスターの作成](service-fabric-cluster-creation-via-arm.md)

デプロイが完了すると、リソース グループに 2 つのロード バランサーが表示されます。 ロード バランサーを参照すると、パブリック IP アドレスと、パブリック IP アドレスに割り当てられている管理エンドポイント (ポート 19000 と 19080) を確認できます。 また、静的内部 IP アドレスと、内部ロード バランサーに割り当てられているアプリケーション エンドポイント (ポート 80) も確認できます。 ロード バランサーはどちらも同じ仮想マシン スケール セットのバックエンド プールを使用します。
