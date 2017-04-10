---
title: "Azure Service Fabric のリバース プロキシ | Microsoft Docs"
description: "Service Fabric のリバース プロキシを使用して、クラスターの内外からマイクロサービスとの通信を行います。"
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 02/23/2017
ms.author: bharatn
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a68855e0b6ba436849c4de13f1439f0e70009b6a
ms.lasthandoff: 03/14/2017


---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric のリバース プロキシ
Azure Service Fabric に組み込まれたリバース プロキシは、Service Fabric クラスター内の HTTP エンドポイントを公開するマイクロサービスをアドレス指定します。

## <a name="microservices-communication-model"></a>マイクロサービス通信モデル
通常、Service Fabric のマイクロサービスは、クラスター内の仮想マシンのサブセットで実行され、さまざまな理由から仮想マシン間で移動できます。 そのため、マイクロサービスのエンドポイントが動的に変更される可能性があります。 マイクロサービスと通信するための標準的なパターンは、次の解決ループです。

1. ネーム サービスによって最初にサービスの場所を解決します。
2. サービスに接続します。
3. 接続エラーの原因を特定し、必要に応じてサービスの場所を再度解決します。

一般に、このプロセスでは、サービス解決および再試行ポリシーを実装する再試行ループにクライアント側の通信ライブラリをラップする必要があります。
詳細については、[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事をご覧ください。

### <a name="communicating-by-using-the-reverse-proxy"></a>リバース プロキシを使用した通信
Service Fabric のリバース プロキシは、クラスターのすべてのノードで実行されます。 リバース プロキシはクライアントに代わってサービス解決プロセス全体を実行し、クライアント要求を転送します。 そのため、クラスターで実行されているクライアントは、クライアント側の HTTP 通信ライブラリを使用して、同じノードでローカルで実行されているリバース プロキシ経由でターゲット サービスと通信できます。

![内部通信][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>クラスターの外部からのマイクロサービスへの到達
マイクロサービスの既定の外部通信モデルは、外部クライアントから各サービスに直接アクセスできないオプトイン モデルです。 [Azure Load Balancer](../load-balancer/load-balancer-overview.md) は、マイクロサービスと外部クライアントの間のネットワーク境界であり、ネットワーク アドレス変換を実行して、外部要求を内部の IP:port エンドポイントに転送します。 マイクロサービスのエンドポイントが外部クライアントに直接アクセスできるようにするには、クラスター内のサービスで使用される各ポートにトラフィックを転送するように Load Balancer を構成しておく必要があります。 さらに、ほとんどのマイクロサービス (特にステートフル マイクロサービス) は、クラスターのすべてのノードに存在するわけではありません。 マイクロサービスは、フェールオーバー時にノード間を移動する可能性があります。 このような場合、Load Balancer はトラフィックの転送先となるレプリカのターゲット ノードの場所を実際には特定できません。

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>クラスターの外部からのリバース プロキシ経由でのマイクロサービスへの到達
Load Balancer で個々のサービスのポートを構成するのではなく、リバース プロキシのポートだけを構成できます。 この構成では、クラスターの外部のクライアントは、追加構成なしにリバース プロキシを使用してクラスター内のサービスに到達できます。

![外部通信][0]

> [!WARNING]
> Load Balancer でリバース プロキシのポートを構成すると、クラスター内の HTTP エンドポイントを公開するすべてのマイクロサービスをクラスターの外部からアドレス指定できるようになります。
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>リバース プロキシを使用してサービスのアドレス指定を行うための URI 形式
リバース プロキシでは、次の URI (Uniform Resource Identifier) 形式を使用して、受信要求の転送先となるサービス パーティションを特定します。

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** HTTP または HTTPS トラフィックを受け入れるようにリバース プロキシを構成できます。 HTTPS トラフィックの場合、リバース プロキシで SSL (Secure Sockets Layer) 終了が発生します。 リバース プロキシでは、HTTP を使用してクラスター内のサービスに要求を転送します。

    HTTPS サービスは現在サポートされていません。
* **Cluster FQDN (完全修飾ドメイン名) | internal IP:** 外部クライアントの場合、クラスターのドメイン (例: mycluster.eastus.cloudapp.azure.com) を介して到達できるようにリバース プロキシを構成できます。 既定では、リバース プロキシはすべてのノードで実行されます。 内部トラフィックの場合、リバース プロキシには localhost または任意の内部ノード IP (例: 10.0.0.1) で到達できます。
* **Port:** リバース プロキシに指定されているポートです (例: 19008)。
* **ServiceInstanceName:** "fabric:/" スキームなしで到達しようとしているデプロイ済みのサービス インスタンスの完全修飾名です。 たとえば、*fabric:/myapp/myservice/* サービスに到達するには、*myapp/myservice* を使用します。
* **Suffix path:** 接続先となるサービスの実際の URL パスです (例: *myapi/values/add/3*)。
* **PartitionKey:** パーティション分割されたサービスの場合、到達するパーティションの計算済みのパーティション キーです。 これはパーティション ID の GUID ではありません ** 。 シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
* **PartitionKind:** サービス パーティション構成です。 これには、"Int64Range" または "Named" を指定できます。 シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
* **ListenerName**: サービスのエンドポイント。{"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}} の形式で指定します。 サービスが複数のエンドポイントを公開している場合、このパラメーターによって、クライアント要求の転送先となるエンドポイントを特定します。 サービスにリスナーが&1; つしかない場合、このパラメーターは省略できます。
* **TargetReplicaSelector**: ターゲット レプリカまたはインスタンスの選択方法を指定します。
  * ターゲット サービスがステートフルの場合、TargetReplicaSelector には "PrimaryReplica"、"RandomSecondaryReplica"、"RandomReplica" のいずれかを指定できます。 このパラメーターが指定されていない場合、既定値は "PrimaryReplica" です。
  * ターゲット サービスがステートレスの場合、リバース プロキシは要求の転送先となるサービス パーティションのインスタンスをランダムに選択します。
* **Timeout:** クライアント要求の代わりに、リバース プロキシによって作成される、サービスに対する HTTP 要求のタイムアウトを指定します。 既定値は 60 秒です。 これは省略可能なパラメーターです。

### <a name="example-usage"></a>使用例
例として、次の URL で HTTP リスナーを開く *fabric:/MyApp/MyService* サービスを取得します。

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

サービスのリソースは次のとおりです。

* `/index.html`
* `/api/users/<userId>`

サービスがシングルトン パーティション構成を使用している場合、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターは不要であり、次のようにゲートウェイを使用してサービスに到達できます。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
* 内部: `http://localhost:19008/MyApp/MyService`

サービスが Uniform Int64 パーティション構成を使用している場合、サービスのパーティションにアクセスするには、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターを使用する必要があります。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* 内部: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

サービスが公開するリソースに到達するには、URL のサービス名の後にリソース パスを配置します。

* 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* 内部: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

ゲートウェイは、サービスの URL にこれらの要求を転送します。

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>ポートを共有するサービスの特別な処理
サービスに到達できない場合、Application Gateway はサービス アドレスの解決を再度試み、要求を再試行します。 クライアント コードで独自のサービス解決と解決ループを実装する必要がないため、これは Application Gateway の主な利点の&1; つです。

一般に、サービスに到達できないときは、サービス インスタンスまたはレプリカが通常のライフサイクルの一環として別のノードに移動しています。 この場合、Application Gateway は、エンドポイントが解決された元のアドレスではもう開いていないことを示すネットワーク接続エラーを受信する可能性があります。

ただし、レプリカまたはサービス インスタンスはホスト プロセスを共有できます。また、次のような http.sys ベースの Web サーバーによってホストされているときには、ポートを共有している場合もあります。

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

この状況では、Web サーバーはホスト プロセスで要求に応答することができますが、解決されたサービス インスタンスまたはレプリカはホストで使用できなくなっています。 この場合、ゲートウェイは Web サーバーから HTTP 404 応答を受信します。 結果的に、HTTP 404 は次の 2 つの異なる意味を持つことになります。

- ケース 1: サービス アドレスは正しいが、ユーザーから要求されたリソースが存在しない。
- ケース 2: サービス アドレスが間違っており、ユーザーから要求されたリソースが、実際には別のノードに存在する可能性がある。

最初のケースは通常の HTTP 404 であり、ユーザー エラーと見なされます。 ただし、2 番目のケースでは、ユーザーは存在するリソースを要求しています。 サービス自体が移動しているため、Application Gateway がリソースを見つけることができませんでした。 この場合、Application Gateway はアドレスを再度解決し、要求を再試行する必要があります。

そのため、Application Gateway にはこの&2; つのケースを区別する手段が必要です。 この区別を行うには、サーバーからのヒントが必要になります。

* 既定では、Application Gateway はケース 2 を想定し、アドレスを再度解決して要求を再発行することを試みます。
* Application Gateway にケース 1 であることを示すには、サービスが次の HTTP 応答ヘッダーを返す必要があります。

  `X-ServiceFabric : ResourceNotFound`

この HTTP 応答ヘッダーは、要求されたリソースが存在しないという通常の HTTP 404 の状況を示すので、Application Gateway はサービス アドレスの解決を再度試みることはなくなります。

## <a name="setup-and-configuration"></a>セットアップと構成
[Azure Resource Manager テンプレート](service-fabric-cluster-creation-via-arm.md)を使用して、クラスターで Service Fabric のリバース プロキシを有効にすることができます。

証明書を使用してセキュリティで保護されたリバース プロキシを構成する Azure Resource Manager テンプレートのサンプルと、証明書のロールオーバーの処理については、「[Configure HTTPS Reverse Proxy in a secure cluster (セキュリティで保護されたクラスターで HTTPS リバース プロキシを構成する)](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster)」をご覧ください。

まず、デプロイするクラスター用テンプレートを用意します。 サンプル テンプレートを使用することも、カスタムの Resource Manager テンプレートを作成することもできます。 テンプレートを用意したら、次の手順に従ってリバース プロキシを有効にすることができます。

1. テンプレートの [Parameters セクション](../azure-resource-manager/resource-group-authoring-templates.md) で、リバース プロキシのポートを定義します。

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. **クラスター**の [resources の type](../azure-resource-manager/resource-group-authoring-templates.md) セクションで、nodeType オブジェクトごとにポートを指定します。

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
3. Azure クラスターの外部からリバース プロキシのアドレス指定を行うには、手順 1. で指定したポートの Azure Load Balancer 規則を設定します。

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
4. リバース プロキシのポートで SSL 証明書を構成するには、**クラスター**の [resources の type セクション](../resource-group-authoring-templates.md)で ***reverseProxyCertificate*** プロパティに証明書を追加します。

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
 リバース プロキシ証明書が、クラスターをセキュリティで保護する証明書とは異なる場合、前に指定した証明書を仮想マシンにインストールし、Service Fabric がアクセスできるようにアクセス制御リスト (ACL) に追加する必要があります。 これは、[resources の type セクション](../resource-group-authoring-templates.md)の **virtualMachineScaleSets** で実行できます。 インストールで、その証明書を osProfile に追加します。 テンプレートの extensions セクションで、ACL 内の証明書を更新できます。

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
> 既存のクラスターで、クラスター証明書とは異なる証明書を使用してリバース プロキシを有効にするときは、リバース プロキシを有効にする前に、クラスターにリバース プロキシ証明書をインストールし、ACL を更新する必要があります。 手順 1. ～ 4. に従ってリバース プロキシを有効にするデプロイを開始する前に、前述の設定を使用して [Azure Resource Manager テンプレート](service-fabric-cluster-creation-via-arm.md)のデプロイを完了しておきます。

## <a name="next-steps"></a>次のステップ
* [GitHub のサンプル プロジェクト](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount)で、サービス間の HTTP 通信の例を確認します。
* [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services の OWIN を使用する Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services を使用した WCF 通信](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

