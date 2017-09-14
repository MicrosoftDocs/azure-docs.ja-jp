---
title: "Azure Service Fabric コンテナー サービスのネットワーク モードを構成する | Microsoft Docs"
description: "Azure Service Fabric がサポートするさまざまなネットワーク モードを設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 8ca04ac79665d5ecc639addd48939f71825f2024
ms.contentlocale: ja-jp
ms.lasthandoff: 09/02/2017

---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric コンテナー ネットワーク モード

コンテナー サービスの Service Fabric クラスターで提供されている既定のネットワーク モードは、`nat` ネットワーク モードです。 `nat` ネットワーク モードでは、同じポートをリッスンしているコンテナー サービスが複数あると、デプロイ エラーが発生します。 同じポートをリッスンしているサービスをいくつか実行している場合、Service Fabric は `open` ネットワーク モード (バージョン 5.7 以降) をサポートしています。 `open` ネットワーク モードでは、各コンテナー サービスに動的に IP アドレスが割り当てられるので、内部的には複数のサービスが同じポートをリッスンできます。   

したがって、サービス マニフェストに定義されている静的エンドポイントがある単一のサービスの種類の場合、デプロイ エラーが発生することなく、`open` ネットワーク モードを使用して新しいサービスを作成および削除することができます。 同様に、ポート マッピングが静的な同じ `docker-compose.yml` ファイルを使用して、複数のサービスを作成することができます。

サービスの再起動時や別のノードへの移行時に IP アドレスは変化するため、動的に割り当てられた IP を使用してサービスを検出することは推奨されません。 サービスの検出には、**Service Fabric ネーム サービス**または **DNS サービス**のみを使用してください。 


> [!WARNING]
> Azure では 1 つの vNET で使用できる IP の数は合計 4096 個までです。 したがって、ノード数とコンテナー サービス インスタンス数 (`open` ネットワークの場合) の合計は、vNET 内の 4096 個を超えることはできません。 このような高密度シナリオの場合は、`nat` ネットワーク モードが推奨されます。
>

## <a name="setting-up-open-networking-mode"></a>open ネットワーク モードの設定

1. `fabricSettings` で DNS サービスと IP プロバイダーを有効にして、Azure Resource Manager テンプレートを設定します。 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. 複数の IP アドレスをクラスターの各ノードに構成できるように、ネットワーク プロファイル セクションを設定します。 次の例では、Windows Service Fabric クラスターの 1 つのノードに 5 つの IP アドレスを設定します (そのため、各ノードのポートを 5 つのサービス インスタンスからリッスンできます)。

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
    ```

    Linux クラスターの場合、パブリック IP 構成を追加して、発信接続を可能にすることができます。 次のスニペットは、Linux クラスターで 1 つのノードに 5 つの IP アドレスを設定します。

    ```json
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "publicipaddressconfiguration": {
                              "name": "devpub",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "publicipaddressconfiguration": {
                              "name": "[concat('devpub', 1)]",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "publicipaddressconfiguration": {
                              "name": "[concat('devpub', 2)]",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "publicipaddressconfiguration": {
                              "name": "[concat('devpub', 3)]",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "publicipaddressconfiguration": {
                              "name": "[concat('devpub', 4)]",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "publicipaddressconfiguration": {
                              "name": "[concat('devpub', 5)]",
                              "properties": {
                                "idleTimeoutInMinutes": 15
                              }
                            },
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
    ```

3. Windows クラスターの場合にのみ、次の値を使用して、vNET 用にポート UDP/53 を開く NSG 規則を設定します。

   | 優先順位 |    名前    |    ソース      |  変換先   |   サービス    | [操作] |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | ALLOW  |


4. 各サービスの `<NetworkConfig NetworkType="open">` について、アプリ マニフェストでネットワーク モードを指定します。  モード `open` にすると、結果としてサービスに専用 IP アドレスが割り当てられます。 モードが指定されていない場合、既定で基本の `nat` モードです。 したがって、次のマニフェスト例では、`NodeContainerServicePackage1` と `NodeContainerServicePackage2` がそれぞれ同じポートをリッスンできます (いずれのサービスも `Endpoint1` をリッスンしています)。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Windows クラスターの場合、アプリケーション内のサービス全体で複数のネットワーク モードを混在させ、対応付けることができます。 そのため、一部のサービスは `open` モードに、一部のサービスは `nat` ネットワーク モードにすることができます。 `nat` を使用してサービスを構成する場合、リッスンするポートを一意にする必要があります。 Linux クラスターの場合、複数のサービスについてネットワーク モードを混在させることはサポートされていません。 


## <a name="next-steps"></a>次のステップ
この記事では、Service Fabric が提供しているネットワーク モードについて説明します。  

* [Service Fabric のアプリケーション モデル](service-fabric-application-model.md)
* [Service Fabric サービス マニフェスト リソース](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Windows Server 2016 上での Service Fabric への Windows コンテナーのデプロイ](service-fabric-get-started-containers.md)
* [Linux 上での Service Fabric への Docker コンテナーのデプロイ](service-fabric-get-started-containers-linux.md)

