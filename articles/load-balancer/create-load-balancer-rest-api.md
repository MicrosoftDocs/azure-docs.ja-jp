---
title: REST API を使用して Azure Load Balancer を作成する
titlesuffix: Azure Load Balancer
description: REST API を使用して Azure Load Balancer を作成する方法を説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: ae8fb4494d27d0c145963c9b32757bdb802e0cc7
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275555"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>REST API を使用して基本の Azure Load Balancer を作成する

Azure Load Balancer は、ロード バランサ―のフロントエンドに到着した新しい受信フローを、ルールと正常性プローブに従って、バックエンド プールのインスタンスに分配します。 Load Balancer で使用できる SKU は 2 種類です。Basic と Standard です。 2 つの SKU バージョンの違いを理解するために、[Load Balancer のSKU を比較](load-balancer-overview.md#skus)します。
 
この手引きでは、複数の VM 間での受信要求の負荷分散に役立てるための[Azure REST API](/rest/api/azure/)を使用したAzure Basic Load Balancer の作成方法を示しています。 完全なリファレンス ドキュメントと追加サンプルは[Azure Load Balancer REST reference](/rest/api/load-balancer/)で確認できます。
 
## <a name="build-the-request"></a>要求を作成する
次の HTTP PUT 要求を使用して、新しい Azure Basic Load Balancer を作成します。
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI パラメーター

|EnableAdfsAuthentication  |イン  |必須 |Type |説明 |
|---------|---------|---------|---------|--------|
|subscriptionId   |  path       |  True       |   string      |  Microsoft Azure サブスクリプションを一意に識別するサブスクリプションの資格情報。 サブスクリプション ID は、全ての修理依頼についてURI の一部を生じさせます。      |
|resourceGroupName     |     path    | True        |  string       |   リソース グループの名前。     |
|loadBalancerName     |  path       |      True   |    string     |    ロード バランサ―の名前。    |
|api-version    |   query     |  True       |     string    |  クライアント API バージョン。      |



### <a name="request-body"></a>要求本文

要求される唯一のパラメーターは`location`です。 *SKU*のバージョンを定義しなければ、Basic Load Balancer が規定で作成されます。  [省略可能なパラメーター](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body)を使用してロード バランサ―をカスタマイズします。

| EnableAdfsAuthentication | Type | 説明 |
| :--- | :--- | :---------- |
| location | string | リソースの場所。 [List Locations](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations)操作を使用して、最新の場所一覧を取得します。 |


## <a name="example-create-and-update-a-basic-load-balancer"></a>例:Basic Load Balancer の作成と更新

この例では、最初にリソースと一緒に Basic Load Balancer を作成します。 次に、フロント エンド IP 構成、バックエンド アドレス プール、負荷分散規則、正常性プローブ、そして受信の NAT 規則を含むロード バランサ―のリソースを構成します。

次の例を使用してロード バランサーを作成する前に、*subnetlb*という名前のサブネットを含む*vnetlb*という名前の仮想ネットワークを場所**米国東部**の*rg1*という言う名前のリソース グループ内に作成します。

### <a name="step-1-create-a-basic-load-balancer"></a>手順1。 Basic Load Balancer を作成する
この手順で*lb*という名前の Basic Load Balancer を*rg1*リソース グループ内の場所**米国東部**に作成します。
#### <a name="sample-request"></a>要求のサンプル

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>要求本文

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>手順 2. ロード バランサーのリソースを構成する
この手順では、フロント エンド IP 構成(*fe-lb*)、バックエンド アドレス プール(*be-lb*)、負荷分散規則(*rulelb*)、正常性プローブ(*probe-lb*)、そして受信の NAT 規則(*in-nat-rule*)を含むロード バランサ―の*lb*リソースを構成します。
#### <a name="sample-request"></a>要求のサンプル

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>要求本文

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
