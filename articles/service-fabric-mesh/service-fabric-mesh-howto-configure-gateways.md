---
title: 要求をルーティングするようにゲートウェイを構成する
description: Service Fabric Mesh で実行されているアプリケーションに対する受信トラフィックを処理するゲートウェイを構成する方法について説明します。
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 40bba03c35568c013e383c08f0a13f765c1cc8bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625856"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>要求をルーティングするようにゲートウェイ リソースを構成する

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

ゲートウェイ リソースは、アプリケーションが収納されているネットワークに受信トラフィックをルーティングするために使用されます。 要求の構造に基づいて特定のサービスまたはエンドポイントに要求が送られる規則を指定するように構成します。 Mesh でのネットワークとゲートウェイについて詳しくは、「[Introduction to networking in Service Fabric Mesh (Service Fabric Mesh でのネットワークの概要)](service-fabric-mesh-networks-and-gateways.md)」をご覧ください。 

ゲートウェイ リソースは、デプロイ テンプレート (JSON または yaml) の一部として宣言する必要があり、ネットワーク リソースに依存します。 このドキュメントでは、ゲートウェイに対して設定できるさまざまなプロパティの概要を説明し、ゲートウェイ構成のサンプルを示します。

## <a name="options-for-configuring-your-gateway-resource"></a>ゲートウェイ リソースを構成するためのオプション

ゲートウェイ リソースは、アプリケーションのネットワークと、基になっているインフラストラクチャのネットワーク (`open` ネットワーク) の間のブリッジとして機能します。 1 つだけ構成する必要があります (Mesh プレビューでは、ゲートウェイはアプリごとに 1 つに制限されています)。 リソースの宣言は、2 つの主要な部分、つまりリソース メタデータとプロパティで構成されます。 

### <a name="gateway-resource-metadata"></a>ゲートウェイのリソース メタデータ

ゲートウェイは、次のメタデータで宣言されます。
* `apiVersion` - "2018-09-01-preview" (将来はそれ以降) に設定する必要があります
* `name` - このゲートウェイの名前の文字列
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - アプリ/ネットワークの場所に設定する必要があります。通常は、デプロイの場所パラメーターへの参照です
* `dependsOn` - このゲートウェイがイングレス ポイントとして機能するネットワーク

Azure Resource Manager (JSON) のデプロイ テンプレートでの表記を次に示します。 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>ゲートウェイのプロパティ

プロパティ セクションは、間にゲートウェイが存在するネットワークと、要求のルーティング規則を定義するために使用されます。 

#### <a name="source-and-destination-network"></a>送信元ネットワークと送信先ネットワーク 

各ゲートウェイには、`sourceNetwork` と `destinationNetwork` が必要です。 送信元ネットワークは、アプリが受け取る受信要求が送信されたネットワークと定義されます。 その name プロパティは、常に "Open" に設定する必要があります。 送信先ネットワークは、要求のターゲットであるネットワークです。 これの name の値は、アプリのローカル ネットワークのリソース名に設定する必要があります (リソースへの完全な参照が含まれる必要があります)。 後のサンプル構成では、"myNetwork" という名前のネットワークでのデプロイの場合にこれがどうなるかを示します。

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>ルール 

ゲートウェイでは、受信トラフィックの処理方法を指定する複数のルーティング規則を設定できます。 ルーティング規則では、リスニング ポートと、特定のアプリケーションに対する送信先エンドポイントの間の関係を定義します。 TCP のルーティング規則では、ポートとエンドポイントの間に 1 対 1 のマッピングがあります。 HTTP のルーティング規則では、要求のパスと、必要に応じてヘッダーを調べて、要求のルーティング方法を決定する、さらに複雑なルーティング規則を設定できます。 

ルーティング規則は、ポートごとに指定します。 各イングレス ポートには、ゲートウェイ構成のプロパティ セクション内に、独自の規則の配列があります。 

#### <a name="tcp-routing-rules"></a>TCP のルーティング規則 

TCP のルーティング規則は、次のプロパティで構成されます。 
* `name` - 規則への参照であり、任意の文字列を使用できます 
* `port` - 受信要求をリッスンするポート 
* `destination` - 要求をそこにルーティングする必要がある、`applicationName`、`serviceName`、`endpointName` を含むエンドポイントの指定

TCP のルーティング規則の例を次に示します。

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP のルーティング規則 

HTTP のルーティング規則は、次のプロパティで構成されます。 
* `name` - 規則への参照であり、任意の文字列を使用できます 
* `port` - 受信要求をリッスンするポート 
* `hosts` - 上で指定されているポートのさまざまな "ホスト" に対して受信した要求に適用されるポリシーの配列。 ホストは、ネットワークで実行されて受信要求に対応できる一連のアプリケーションとサービスです (つまり、Web アプリ)。 ホスト ポリシーは順番に解釈されるので、詳細さのレベルが高いものから順に、以下を作成する必要があります
    * `name` - 次のルーティング規則の指定対象であるホストの DNS 名。 ここで "*" を使用すると、すべてのホストに対するルーティング規則が作成されます。
    * `routes` - この特定のホストに対するポリシーの配列
        * `match` - `path` に基づく、この規則が適用される受信要求の構造の指定
            * `path` - `value` (受信 URI)、`rewrite` (要求の転送方法)、および `type` (現在使用できるのは "Prefix" だけ) が含まれます
            * `header` - 要求が path の指定 (上記) と一致する場合に、要求のヘッダーと一致するヘッダー値のオプションの配列。
              * 各エントリには、`name`(一致するヘッダーの文字列名)、`value` (要求内のヘッダーの文字列値)、`type` (現在使用できるのは "Exact" のみ) が含まれます
        * `destination` - 要求が一致する場合、`applicationName`、`serviceName`、`endpointName` を使用して指定されるこの送信先にルーティングされます

次に示す HTTP ルーティング規則の例は、ポート 80 で受信した、このネットワークのアプリによって提供されるすべてのホストへの要求に適用されます。 要求 URL の構造が、path の指定つまり `<IPAddress>:80/pickme/<requestContent>` と一致する場合は、`myListener` エンドポイントに送られます。  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>ゲートウェイ リソースの構成のサンプル 

ゲートウェイ リソースの完全な構成の例を次に示します (これは、[Mesh サンプル リポジトリ](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)にあるイングレス サンプルからの引用です)。

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

このゲートウェイは、ポート 80 でリッスンしており、少なくとも 2 つのサービス "helloWorldService" と "counterService" で構成される、Linux アプリケーション "meshAppLinux" 用に構成されています。 受信要求の URL の構造に応じて、要求はこれらのサービスのいずれかにルーティングされます。 
* "\<IPAddress>:80/helloWorld/\<request\>" の場合は、要求は helloWorldService の "helloWorldListener" に送られます。 
* "\<IPAddress>:80/counter/\<request\>" の場合は、要求は counterService の "counterListener" に送られます。 

## <a name="next-steps"></a>次のステップ
* [Ingress サンプル](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress)をデプロイして、ゲートウェイの動作を確認する
