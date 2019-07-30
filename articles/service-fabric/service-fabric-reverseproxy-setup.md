---
title: Azure Service Fabric でのリバース プロキシの設定 | Microsoft Docs
description: Service Fabric のリバース プロキシを設定して構成する方法について説明します。
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/13/2018
ms.author: chackdan
ms.openlocfilehash: df25c52e7a3f35355eb52bd95a39f55852adfcae
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876590"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric でリバース プロキシを設定して構成する
オプションの Azure Service Fabric サービスであるリバース プロキシは、Service Fabric クラスターで実行されているマイクロサービスが HTTP エンドポイントのある他のサービスを検出してそのサービスと通信するのに役立ちます。 詳しくは、「[Azure Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)」をご覧ください。 この記事では、クラスターでリバース プロキシを設定して構成する方法を示します。 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Azure portal を使用してリバース プロキシを有効にする

Azure portal には、新しい Service Fabric クラスターを作成するときに、リバース プロキシを有効にするオプションがあります。 ポータルで既存のクラスターをアップグレードしてリバース プロキシを使用することはできません。 

[Azure portal を使用してクラスターを作成する](./service-fabric-cluster-creation-via-portal.md)ときにリバース プロキシを構成するには、以下のことを行ってください。

1. **[ステップ 2:クラスター構成]** で、 **[ノード タイプの構成]** の **[リバース プロキシを有効にする]** をオンにします。

   ![ポータルでリバース プロキシを有効にする](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (省略可能) セキュリティ保護されたリバース プロキシを構成するには、SSL 証明書を構成する必要があります。 **[ステップ 3:セキュリティ]** で、 **[クラスターのセキュリティ設定の構成]** の **[構成の種類]** で **[カスタム]** を選択します。 その後、 **[リバース プロキシの SSL 証明書]** で **[リバース プロキシの SSL 証明書を含める]** を選択し、証明書の詳細を入力します。

   ![ポータルでセキュリティ保護されたリバース プロキシを構成する](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   クラスターを作成するときに証明書を使ってリバース プロキシを構成しなかった場合は、後でクラスターのリソース グループの Resource Manager テンプレートを使って構成できます。 詳しくは、「[Azure Resource Manager テンプレートでリバース プロキシを有効にする](#enable-reverse-proxy-via-azure-resource-manager-templates)」をご覧ください。

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでリバース プロキシを有効にする

Azure のクラスターでは、Azure Resource Manager テンプレートを使って、Service Fabric でのリバース プロキシを有効にすることができます。 クラスターを作成するときに、または後でクラスターを更新することにより、リバース プロキシを有効にできます。 

新しいクラスターでは、[カスタム Resource Manager テンプレートを作成する](service-fabric-cluster-creation-via-arm.md)こと、またはサンプル テンプレートを使用することができます。 

Azure クラスター用のセキュリティ保護されたリバース プロキシの構成に役立つ Resource Manager テンプレートのサンプルは、GitHub の[セキュリティ保護されたリバース プロキシのサンプル テンプレート](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample)のページにあります。 証明書によるセキュリティ保護されたリバース プロキシの構成と証明書のロールオーバーの処理の手順とそれに使用するテンプレートについては、README の「[Configure HTTPS Reverse Proxy in a secure cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster)」(セキュリティ保護されたクラスターで HTTPS リバース プロキシを構成する) をご覧ください。

既存のクラスターの場合、[Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)、または [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) を使用して、クラスターのリソース グループの Resource Manager テンプレートをエクスポートすることができます。

Resource Manager テンプレートを用意した後、次の手順でリバース プロキシを有効にできます。

1. テンプレートの [Parameters セクション](../azure-resource-manager/resource-group-authoring-templates.md) で、リバース プロキシのポートを定義します。

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) の[リソースの種類セクション](../azure-resource-manager/resource-group-authoring-templates.md)で、nodeType オブジェクトごとにポートを指定します。

    ポートは、reverseProxyEndpointPort というパラメーター名で識別されます。

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. リバース プロキシのポートで SSL 証明書を構成するには、**Microsoft.ServiceFabric/clusters** の[リソースの種類セクション](../resource-group-authoring-templates.md)で ***reverseProxyCertificate*** プロパティに証明書を追加します。

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>クラスター証明書とは異なるリバース プロキシ証明書のサポート
 リバース プロキシ証明書が、クラスターをセキュリティで保護する証明書とは異なる場合、前に指定した証明書を仮想マシンにインストールし、Service Fabric がアクセスできるようにアクセス制御リスト (ACL) に追加する必要があります。 これは、[**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) の[リソースの種類セクション](../resource-group-authoring-templates.md)で実行できます。 インストールで、証明書を osProfile に追加します。 テンプレートの extensions セクションで、ACL 内の証明書を更新できます。

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> 既存のクラスターで、クラスター証明書とは異なる証明書を使用してリバース プロキシを有効にするときは、リバース プロキシを有効にする前に、クラスターにリバース プロキシ証明書をインストールし、ACL を更新する必要があります。 デプロイを開始してステップ 1 から 3 でリバース プロキシを有効にする前に、前述の設定を使用して [Azure Resource Manager テンプレート](service-fabric-cluster-creation-via-arm.md)のデプロイを完了しておきます。

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>スタンドアロン クラスターでリバース プロキシを有効にする

スタンドアロン クラスターの場合は、ClusterConfig.json ファイルでリバース プロキシを有効にします。 クラスターの作成時または既存クラスターの構成のアップグレード時に、リバース プロキシを有効にすることができます。 ClusterConfig.json ファイルで使用可能な設定の詳細については、[スタンドアロン クラスターの設定](./service-fabric-cluster-manifest.md)に関するページをご覧ください。

以下の手順では、リバース プロキシを有効にし、必要に応じて X.509 証明書でリバース プロキシをセキュリティ保護するために使用する設定を示します。 

1. リバース プロキシを有効にするには、クラスター構成の **properties** でノードの種類に対して **reverseProxyEndpointPort** の値を設定します。次の JSON では、種類が "NodeType0" のノードに対してリバース プロキシ エンドポイント ポートを 19081 に設定しています。

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (省略可能) セキュリティ保護されたリバース プロキシの場合は、**properties** の **security** セクションで証明書を構成します。 
   - 開発環境またはテスト環境では、**ReverseProxyCertificate** の設定を使用できます。

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - 運用環境では、**ReverseProxyCertificateCommonNames** の設定をお勧めします。

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   スタンドアロン クラスターの証明書の構成と管理、およびリバース プロキシのセキュリティ保護に使用する証明書の構成の詳細については、[X509 証明書ベースのセキュリティ](./service-fabric-windows-cluster-x509-security.md)に関するページをご覧ください。

リバース プロキシを有効にするように ClusterConfig.json ファイルを変更した後は、「[クラスター構成のアップグレード](service-fabric-cluster-config-upgrade-windows-server.md)」の説明に従って、クラスターに変更をプッシュします。


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Azure Load Balancer を使用してパブリック ポートでリバース プロキシを公開する

Azure クラスターの外部からリバース プロキシのアドレス指定を行うには、リバース プロキシ ポートに対して Azure Load Balancer 規則と Azure 正常性プローブを設定します。 これらの手順は、クラスターを作成した後であればいつでも、Azure portal または Resource Manager テンプレートを使って実行できます。 

> [!WARNING]
> Load Balancer でリバース プロキシのポートを構成すると、クラスター内の HTTP エンドポイントを公開するすべてのマイクロサービスをクラスターの外部からアドレス指定できるようになります。 つまり、内部的なものであるマイクロサービスを、悪意のあるユーザーが検出できる可能性があります。 これにより、次のような、悪用される可能性のある重大な脆弱性が潜在的に存在することになります。
>
> * 悪意のあるユーザーは、セキュリティで十分に保護されていない攻撃対象が存在する内部サービスを繰り返し呼び出すことによって、サービス拒否攻撃を加える場合があります。
> * 悪意のあるユーザーは無効なパケットを内部サービスに送信し、予期しない動作を発生させる場合があります。
> * 内部的なものであるサービスは公開することを意図していない個人情報または機密情報をクラスターの外部のサービスに返す場合があり、それによって機密情報が悪意のあるユーザーに晒される可能性があります。 
>
> リバース プロキシのポートをパブリックにする前に、クラスターおよびクラスター上で実行されているアプリに対する潜在的なセキュリティ上の影響を完全に理解し、それを軽減するようにしてください。 
>

スタンドアロン クラスターのリバース プロキシをパブリックに公開する場合の方法は、クラスターをホストしているシステムに依存し、この記事の範囲外です。 ただし、リバース プロキシのパブリックな公開に関する上記の警告は、引き続き適用されます。

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Azure portal を使用してリバース プロキシを公開する 

1. Azure portal で、クラスターのリソース グループをクリックし、クラスターのロード バランサーをクリックします。
2. リバース プロキシ ポートの正常性プローブを追加するには、ロード バランサー ウィンドウの左側のウィンドウで、 **[設定]** の **[正常性プローブ]** をクリックします。 正常性プローブ ウィンドウの上部にある **[追加]** をクリックし、リバース プロキシ ポートの詳細を入力して、 **[OK]** をクリックします。 既定のリバース プロキシ ポートは 19081 ですが、クラスター作成時に変更した場合を除きます。

   ![リバース プロキシの正常性プローブを構成する](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. リバース プロキシ ポートを公開するための Load Balancer 規則を追加するには、ロード バランサー ウィンドウの左側のウィンドウで、 **[設定]** の **[負荷分散規則]** をクリックします。 次に、負荷分散規則ウィンドウの上部にある **[追加]** をクリックして、リバース プロキシ ポートの詳細を入力します。 **[ポート]** の値はリバース プロキシを公開するポートに設定し、 **[バックエンド ポート]** の値はリバース プロキシを有効にしたときに設定したポートに設定し、 **[正常性プローブ]** の値は前の手順で構成した正常性プローブに設定します。 他のフィールドは適切に設定して、 **[OK]** をクリックします。

   ![リバース プロキシのロード バランサー規則を構成する](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Resource Manager テンプレートを使用してリバース プロキシを公開する

次の JSON では、「[Azure Resource Manager テンプレートでリバース プロキシを有効にする](#enable-reverse-proxy-via-azure-resource-manager-templates)」で使われているものと同じテンプレートを参照します。 Resource Manager テンプレートを作成する方法または既存のクラスターのテンプレートをエクスポートする方法については、そちらのセクションをご覧ください。  [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) の[リソースの種類セクション](../resource-group-authoring-templates.md)を変更します。

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>ファブリックの設定を使用してリバース プロキシの動作をカスタマイズする

Azure でホストされているクラスターの Resource Manager テンプレートまたはスタンドアロン クラスターの ClusterConfig.json ファイルに含まれるファブリックの設定を使用して、リバース プロキシの動作をカスタマイズすることができます。 リバース プロキシの動作を制御する設定は、クラスターの **properties** セクションの **fabricSettings** セクションの [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) セクションにあります。 

たとえば、次の JSON では、リバース プロキシに対する要求のタイムアウトを設定する **DefaultHttpRequestTimeout** の値を 180 秒間に設定しています。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Azure クラスターのファブリックの設定の更新について詳しくは、「[Resource Manager テンプレートを使用してクラスター設定をカスタマイズする](service-fabric-cluster-config-upgrade-azure.md)」をご覧ください。 スタンドアロン クラスターについては、「[スタンドアロン クラスターのクラスター設定をカスタマイズする](service-fabric-cluster-config-upgrade-windows-server.md)」をご覧ください。 

リバース プロキシとサービスの間にセキュリティ保護された通信を確立するには、いくつかのファブリック設定を使います。 これらの設定について詳しくは、「[リバース プロキシを使用したセキュリティで保護されたサービスへの接続](service-fabric-reverseproxy-configure-secure-communication.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
* [リバース プロキシを使用したセキュリティで保護された HTTP サービスへの転送の設定](service-fabric-reverseproxy-configure-secure-communication.md)
* リバース プロキシの構成オプションについては、Service Fabric クラスターの設定のカスタマイズに関するページの「[ApplicationGateway/Http](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)」セクションをご覧ください。
