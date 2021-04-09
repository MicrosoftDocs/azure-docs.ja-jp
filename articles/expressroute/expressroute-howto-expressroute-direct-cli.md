---
title: 'Azure ExpressRoute: ExpressRoute Direct を構成する: CLI'
description: Microsoft のグローバル ネットワークに直接接続するために、Azure CLI を使用して Azure ExpressRoute Direct を構成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: d68011afe044535783dd8a8c56ed5d950c6d06b1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099881"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Azure CLI の使用による ExpressRoute Direct の構成

ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所を通じて Microsoft のグローバル ネットワークに直接接続できます。 詳しくは、[ExpressRoute Direct の接続](expressroute-erdirect-about.md)に関する記事をご覧ください。

## <a name="before-you-begin"></a>開始する前に

ExpressRoute Direct を利用する前に、まず、サブスクリプションを登録する必要があります。 ExpressRoute Direct を利用する前に、まず、サブスクリプションを登録する必要があります。 登録するには、Azure PowerShell から次の操作を行ってください。
1.  Azure にサインインして、登録するサブスクリプションを選択します。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 次のコマンドを使用して、サブスクリプションをパブリック プレビューに登録します。
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

登録したら、**Microsoft.Network** リソース プロバイダーがサブスクリプションに登録されていることを確認します。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。

## <a name="create-the-resource"></a><a name="resources"></a>リソースを作成する

1. Azure にサインインして、ExpressRoute を含むサブスクリプションを選択します。 ExpressRoute Direct リソースおよび ExpressRoute 回線は、同じサブスクリプション内にある必要があります。 Azure CLI で、次のコマンドを実行します。

   ```azurecli
   az login
   ```

   アカウントのサブスクリプションを確認します。 

   ```azurecli
   az account list 
   ```

   ExpressRoute 回線を作成するサブスクリプションを選択します。

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. expressrouteportslocation および expressrouteport API にアクセスするために、Microsoft.Network へのサブスクリプションを再登録します。

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. ExpressRoute Direct がサポートされるすべての場所を一覧表示します。
    
   ```azurecli
   az network express-route port location list
   ```

   **出力例**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. 上記の手順で示されている場所のいずれかに使用可能な帯域幅があるかどうかを確認します。

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **出力例**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. 上記の手順で選択した場所に基づく ExpressRoute Direct リソースを作成します。

   ExpressRoute Direct では、QinQ と Dot1Q 両方のカプセル化がサポートされます。 QinQ を選択した場合、各 ExpressRoute 回線に S-Tag が動的に割り当てられ、ExpressRoute Direct リソース全体で一意になります。 回線上の各 C-Tag は、その回線で一意である必要がありますが、ExpressRoute Direct リソース全体では、その必要はありません。  

   Dot1Q カプセル化を選択した場合、ExpressRoute Direct リソース全体で C-Tag (VLAN) が一意になるように管理する必要があります。  

   > [!IMPORTANT]
   > ExpressRoute Direct ではカプセル化の種類を 1 つしか選択できません。 ExpressRoute Direct リソースを作成した後は、カプセル化の種類を変更することはできません。
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > **Encapsulation** 属性を **Dot1Q** に設定することもできます。 
   >

   **出力例**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="change-adminstate-for-links"></a><a name="state"></a>リンクの AdminState を変更する

このプロセスは、レイヤー 1 のテストを実行するために使用します。 各交差接続が適切にパッチされ、各ルーター内のプライマリおよびセカンダリのポートに入力されていることを確認してください。

1. リンクを **[Enabled]\(有効\)** に設定します。 この手順を繰り返して、各リンクを **[有効]** に設定します。

   Links[0] はプライマリ ポート、Links[1] はセカンダリ ポートです。

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **出力例**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   `AdminState = "Disabled"` を使用して、同じ手順でポートを停止させます。

## <a name="create-a-circuit"></a><a name="circuit"></a>回線を作成する

既定では、ExpressRoute Direct リソースを含むサブスクリプション内に 10 個の回線を作成できます。 この既定の制限は、Microsoft サポートが増やすことができます。 プロビジョニングされる帯域幅と使用される帯域幅を追跡してください。 プロビジョニングされる帯域幅は、ExpressRoute Direct リソース上のすべての回線の帯域幅の合計です。 使用される帯域幅は、基礎となる物理インターフェイスの実際の使用量です。

ExpressRoute Direct では、ここで説明するシナリオをサポートするためだけの回線帯域幅を追加で使用できます。 帯域幅は、40 Gbps および 100 Gbps です。

**SkuTier** は Local、Standard、または Premium にできます。

**SkuFamily** は MeteredData のみにすることができます。 ExpressRoute Direct では、Unlimited はサポートされていません。

ExpressRoute Direct リソース上に回線を作成します。

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  その他の帯域幅には、5 Gbps、10 Gbps、40 Gbps があります。

  **出力例**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>次のステップ

ExpressRoute Direct について詳しくは、「[概要](expressroute-erdirect-about.md)」に関する記事をご覧ください。
