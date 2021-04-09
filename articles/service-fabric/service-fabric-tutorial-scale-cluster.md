---
title: Azure で Service Fabric クラスターのスケーリングを行う
description: このチュートリアルでは、Azure の Service Fabric クラスターをスケールアウトおよびスケールインする方法と、残ったリソースをクリーンアップする方法について説明します。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc7ba8d6c83ae72ffb8f1afae3954b3a46dc6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788030"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>チュートリアル:Azure で Service Fabric クラスターのスケーリングを行う

このチュートリアルはシリーズ第 3 部で、既存のクラスターをスケールアウトおよびスケールインする方法を示します。 このチュートリアルを終了すると、クラスターをスケールする方法や残ったリソースをクリーンアップする方法について知ることができます。  Azure 内で実行されているクラスターのスケーリングの詳細については、[Service Fabric クラスターのスケーリング](service-fabric-cluster-scaling.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ノードを追加および削除する (スケールアウトおよびスケールイン)
> * ノード タイプを追加および削除する (スケールアウトおよびスケールイン)
> * ノード リソースを増加する (スケールアップ)

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して Azure にセキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成する
> * [クラスターを監視する](service-fabric-tutorial-monitor-cluster.md)
> * クラスターをスケールインまたはスケールアウトする
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * [クラスターの削除](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
* [Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を Azure に作成します

## <a name="important-considerations-and-guidelines"></a>重要な考慮事項とガイドライン

アプリケーションのワークロードが時間の経過と共に変化し、既存のサービスに対してリソースの増加 (または減少) が必要か。  クラスター リソースを増減させるには、1 つのノード タイプの[ノードを追加または削除](#add-nodes-to-or-remove-nodes-from-a-node-type)します。

100 を超えるノードをクラスターに追加する必要があるか。  Service Fabric の 1 つのノード タイプ/スケール セットには、VM あたり 100 を超えるノードを含めることはできません。  100 ノードを超えてクラスターをスケーリングするには、[別のノード タイプを追加](#add-nodes-to-or-remove-nodes-from-a-node-type)します。

アプリケーションに複数のサービスが存在するか、またその中に、外部に公開 (インターネットに接続) しなければならないサービスはあるか。  標準的なアプリケーションには、クライアントからの入力を受け取るフロントエンド ゲートウェイ サービスと、そのフロントエンド サービスと通信するバックエンド サービスが存在します。 この場合は、クラスターに[少なくとも 2 つのノード タイプを追加](#add-nodes-to-or-remove-nodes-from-a-node-type)することをお勧めします。  

インフラストラクチャ ニーズの異なる複数のサービスが存在するか (大容量の RAM が必要、高い CPU 処理能力が必要など)。 たとえば、ご自分のアプリケーションにフロントエンド サービスとバックエンド サービスが含まれているとします。 フロントエンド サービスはバックエンド サービスよりも小さい VM (D2 など) で実行できますが、インターネットに対してポートを開放する必要があります。 一方バックエンド サービスは計算負荷が高く、より大きな VM (D4、D6、D15 など) で実行する必要はありますが、インターネット接続は不要です。 この場合、クラスターに [2 つ以上のノード タイプを追加](#add-nodes-to-or-remove-nodes-from-a-node-type)することをお勧めします。 そうすることでノード タイプごとに、インターネットの接続性や VM サイズなど異なる特性を持たせることができます。 VM 数を個別に増減させることもできます。

Azure クラスターをスケーリングするときには、次のガイドラインに留意してください。

* Service Fabric の 1 つのノード タイプ/スケール セットには、VM あたり 100 を超えるノードを含めることはできません。  100 ノードを超えてクラスターをスケーリングするには、別のノード タイプを追加します。
* 実稼働ワークロードを実行するプライマリ ノード タイプの場合、[持続性レベル][durability]が Gold または Silver であり、必ず 5 つ以上のノードが必要です。
* ステートフルの実稼働ワークロードを実行する非プライマリ ノード タイプの場合、必ず 5 つ以上のノードが必要です。
* ステートレスの実稼働ワークロードを実行する非プライマリ ノード タイプの場合、必ず 2 つ以上のノードが必要です。
* [耐久性レベル][durability]が Gold または Silver であるノードの場合、どの種類であっても必ず 5 つ以上のノードが必要です。
* プライマリ ノード タイプをスケールインする (ノードを削除する) 場合、[信頼性レベル][reliability]で必要な数を下回るまでインスタンスの数を減らさないでください。

詳細については、[クラスター容量のガイダンス](service-fabric-cluster-capacity.md)を参照してください。

## <a name="export-the-template-for-the-resource-group"></a>リソース グループのテンプレートをエクスポートする

セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を作成し、リソース グループを正常に設定したら、リソース グループの Resource Manager テンプレートをエクスポートします。 テンプレートをエクスポートすると、クラスターとそのリソースの将来のデプロイを簡単に自動化できます。これは、テンプレートに完全なインフラストラクチャが含まれているためです。  テンプレートのエクスポートの詳細については、「[Manage Azure Resource Manager resource groups by using the Azure portal (Azure portal を使用して Azure Resource Manager リソース グループを管理する)](../azure-resource-manager/management/manage-resource-groups-portal.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) で、目的のクラスターを含むリソース グループ (このチュートリアルに従っている場合は **sfclustertutorialgroup**) に移動します。 

2. 左側のウィンドウで、 **[デプロイ]** を選択するか、 **[デプロイ]** の下にあるリンクを選択します。 

3. 最新の正常なデプロイを一覧から選択します。

4. 左側のウィンドウで、 **[テンプレート]** 、 **[ダウンロード]** の順に選択して、テンプレートを ZIP ファイルとしてエクスポートします。  テンプレートとパラメーターをご自分のローカル コンピューターに保存します。

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>ノード タイプのノードを追加または削除する

スケールインおよびスケールアウト (水平スケーリング) では、クラスター内のノードの数を変更します。 スケールインまたはスケールアウトする場合には、スケール セットに仮想マシン インスタンスを追加します。 これらのインスタンスは、Service Fabric で使用されるノードになります。 Service Fabric は、(スケールアウトによって) スケール セットにインスタンスがいつ追加されるかを認識し、自動的に反応します。 クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。

### <a name="update-the-template"></a>テンプレートを更新する

リソース グループから最新のデプロイの[テンプレートとパラメーター ファイルをエクスポート](#export-the-template-for-the-resource-group)します。  *parameters.json* ファイルを開きます。  このチュートリアルの [サンプル テンプレート][template]を使用してクラスターをデプロイした場合は、クラスター内に 3 つのノード タイプがあり、各ノード タイプのノード数を設定する 3 つのパラメーター (*nt0InstanceCount*、*nt1InstanceCount*、および *nt2InstanceCount*) があります。  たとえば、*nt1InstanceCount* パラメーターは 2 つ目のノード タイプのインスタンスの数を設定し、関連付けられている仮想マシン スケール セット内の VM の数を設定します。

そのため、*nt1InstanceCount* の値を更新すると、2 つ目のノード タイプのノードの数が変更されます。  100 ノードを超えてノード タイプをスケールアウトすることができない点に注意してください。  ステートフルの実稼働ワークロードを実行する非プライマリ ノード タイプの場合、必ず 5 つ以上のノードが必要です。 ステートレスの実稼働ワークロードを実行する非プライマリ ノード タイプの場合、必ず 2 つ以上のノードが必要です。

[持続性レベル][durability]が Bronze のノード タイプをスケールインする (ノードを削除する) 場合、[それらのノードの状態を手動で削除する](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)必要があります。  持続性レベルが Silver および Gold の場合、これらの手順はプラットフォームによって自動的に行われます。

### <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
*template.json* ファイルと *parameters.json* ファイルに対する変更を保存します。  アップロードしたテンプレートをデプロイするには、次のコマンドを実行します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
または、次の Azure CLI コマンドを使用します。
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>クラスターにノード タイプを追加する

Azure 内で実行されている Service Fabric クラスターで定義されているすべてのノード タイプは、[個別の仮想マシン スケール セット](service-fabric-cluster-nodetypes.md)として設定されます。 その後は、ノードの種類ごとに個別に管理できます。 各ノードの種類は、個別にスケールアップまたはスケールダウンしたり、異なるポートのセットを開いたり、別の容量メトリックを使用したりすることができます。 各クラスター ノード上で実行される OS SKU は個別に変更することもできますが、サンプル クラスター内で Windows と Linux を混在させて実行させることはできない点に注意してください。 1 つのノード タイプ/スケール セットには、100 を超えるノードを含めることはできません。  100 ノードを超えてクラスターを水平方向にスケーリングするには、別のノード タイプ/スケール セットを追加します。 クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。

### <a name="update-the-template"></a>テンプレートを更新する

リソース グループから最新のデプロイの[テンプレートとパラメーター ファイルをエクスポート](#export-the-template-for-the-resource-group)します。  *parameters.json* ファイルを開きます。  このチュートリアルの[サンプル テンプレート][template]を使用してクラスターをデプロイした場合は、クラスター内に 3 つのノード タイプがあります。  このセクションでは、Resource Manager テンプレートを更新し、デプロイして、4 つ目のノード タイプを追加します。 

新しいノード タイプだけでなく、関連付けられている仮想マシン スケール セット (仮想ネットワークの別のサブネット内で実行されます) とネットワーク セキュリティ グループも追加します。  新しいスケール セットのために、新規または既存のパブリック IP アドレスと Azure Load Balancer のリソースを追加できます。  新しいノード タイプは、[持続性レベル][durability]が Silver で、サイズが "Standard_D2_V2" です。

*template.json* ファイル内で、次の新しいパラメーターを追加します。
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

*template.json* ファイル内で、次の新しい変数を追加します。
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

*template.json* ファイル内で、仮想ネットワーク リソースに新しいサブネットを追加します。
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

*template.json* ファイル内で、新しいパブリック IP アドレスと Load Balancer のリソースを追加します。
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
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
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

*template.json* ファイル内で、新しいネットワーク セキュリティ グループと仮想マシン スケール セットのリソースを追加します。  仮想マシン スケール セットの Service Fabric 拡張機能プロパティ内にある NodeTypeRef プロパティにより、指定されたノード タイプがスケール セットにマップされます。

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
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
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
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
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
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
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
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
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

*template.json* ファイル内で、クラスター リソースを更新し、新しいノード タイプを追加します。
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

*parameters.json* ファイル内で、次の新しいパラメーターと値を追加します。
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
*template.json* ファイルと *parameters.json* ファイルに対する変更を保存します。  アップロードしたテンプレートをデプロイするには、次のコマンドを実行します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
または、次の Azure CLI コマンドを使用します。
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>ノード タイプをクラスターから削除する
Service Fabric クラスターを作成した後は、ノード タイプ (仮想マシン スケール セット) とそのノードすべてを削除することで、クラスターを水平方向にスケーリングできます。 クラスターは、クラスターでワークロードを実行中であっても、いつでもスケーリングできます。 クラスターをスケーリングすると、アプリケーションも自動的にスケーリングされます。

> [!WARNING]
> 運用環境のクラスターからノード タイプを削除するために Remove-AzServiceFabricNodeType を使用することは、頻繁に使用する方法としては推奨されません。 ノード タイプの背後にある仮想マシン スケール セット リソースが削除されるため、危険なコマンドです。 

ノード タイプを削除するには、[Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) コマンドレットを実行します。  ノード タイプの[持続性レベル][durability]は Silver または Gold でなければなりません。このコマンドレットは、ノード タイプに関連付けられているスケール セットを削除します。完了するまでに時間がかかります。  その後、削除する各ノードに対して [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) コマンドレットを実行します。これにより、ノードの状態が削除され、クラスターからノードが削除されます。 そのノード上にサービスが存在する場合、それらのサービスは最初に別のノードに移動されます。 クラスター マネージャーで、レプリカ/サービス用のノードを見つけられない場合、操作は遅延またはブロックされます。

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>ノード リソースを増加する 
Service Fabric クラスターを作成した後は、クラスターのノード タイプを垂直方向にスケーリング (ノードのリソースを変更) したり、元のノード タイプを (VM SKU または OS イメージを更新した) 新しいノード タイプに置き換えることによってそのノード タイプの VM のオペレーティング システムをアップグレードしたりすることができます。 詳細については、[Azure Service Fabric ノード タイプのスケールアップ](service-fabric-scale-up-primary-node-type.md)に関するページを参照してください。

> [!IMPORTANT]
> VM SKU や OS イメージをインプレースで変更することは避けてください。これは危険な操作でありサポートされません。

それが不可能な場合は、新しいクラスターを作成し、古いクラスターから[アプリケーション状態を復元する](service-fabric-reliable-services-backup-restore.md) ことができます (妥当な場合)。 システム サービスの状態を復元する必要はありません。それらは新しいクラスターにアプリケーションをデプロイしたときに再作成されます。 クラスターでステートレス アプリケーションだけを実行していた場合、実行するのはアプリケーションの新しいクラスターへのデプロイのみであり、復元するものはありません。

### <a name="update-the-template"></a>テンプレートを更新する

リソース グループから最新のデプロイの[テンプレートとパラメーター ファイルをエクスポート](#export-the-template-for-the-resource-group)します。  *parameters.json* ファイルを開きます。  このチュートリアルの[サンプル テンプレート][template]を使用してクラスターをデプロイした場合は、クラスター内に 3 つのノード タイプがあります。  

2 つ目のノード タイプの VM のサイズは、*vmNodeType1Size* パラメーターで設定されています。  *vmNodeType1Size* パラメーターの値を Standard_D2_V2 から [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md) に変更します。そうすると、各 VM インスタンスのリソースが 2 倍になります。

3 つすべてのノード タイプの VM SKU は、*vmImageSku* パラメーターで設定されています。  繰り返しになりますが、ノード タイプの VM SKU の変更は慎重に行う必要があり、プライマリ ノード タイプではお勧めしません。

### <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
*template.json* ファイルと *parameters.json* ファイルに対する変更を保存します。  アップロードしたテンプレートをデプロイするには、次のコマンドを実行します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
または、次の Azure CLI コマンドを使用します。
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ノードを追加および削除する (スケールアウトおよびスケールイン)
> * ノード タイプを追加および削除する (スケールアウトおよびスケールイン)
> * ノード リソースを増加する (スケールアップ)

次のチュートリアルでは、クラスターのランタイムをアップグレードする方法について説明します。
> [!div class="nextstepaction"]
> [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
