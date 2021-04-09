---
title: Azure API Management サービスの IP アドレス | Microsoft Docs
description: Azure API Management サービスの IP アドレスを取得する方法と、それらが変更されるタイミングについて学習します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80047741"
---
# <a name="ip-addresses-of-azure-api-management"></a>Azure API Management の IP アドレス

この記事では、Azure API Management サービスの IP アドレスを取得する方法について説明します。 サービスが仮想ネットワーク内にある場合、IP アドレスはパブリックまたはプライベートにすることができます。

IP アドレスを使用して、ファイアウォール規則の作成、バックエンド サービスへの受信トラフィックのフィルター処理、または送信トラフィックの制限を行うことができます。

## <a name="ip-addresses-of-api-management-service"></a>API Management サービスの IP アドレス

Developer、Basic、Standard、または Premium レベルのすべての API Management サービス インスタンスには、そのサービス インスタンス専用のパブリック IP アドレスがあります (他のリソースとは共有されません)。 

IP アドレスは、Azure portal のリソースの概要ダッシュボードから取得できます。

![API Management の IP アドレス](media/api-management-howto-ip-addresses/public-ip.png)

次の API 呼び出しを使用してプログラムでフェッチすることもできます。

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

パブリック IP アドレスは、応答の一部になります。

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

[複数リージョンのデプロイ](api-management-howto-deploy-multi-region.md)では、各地域のデプロイにパブリック IP アドレスが 1 つあります。

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>VNet 内の API Management サービスの IP アドレス

API Management サービスが仮想ネットワーク内にある場合、パブリックとプライベートの 2 種類の IP アドレスがあります。

パブリック IP アドレスは、ポート `3443` での内部通信に使用されます。これは、構成の管理用です (たとえば、Azure Resource Manager 経由)。 外部 VNet 構成では、ランタイム API トラフィックにも使用されます。 要求が API Management から公開されている (インターネットに接続された) バックエンドに送信されると、パブリック IP アドレスが要求の送信元として表示されます。

[内部 VNet モード](api-management-using-with-internal-vnet.md)で **のみ** 使用可能なプライベート仮想 IP (VIP) アドレス は、ネットワーク内から API Management エンドポイント (ゲートウェイ、開発者ポータル、および直接 API アクセス用の管理プレーン) に接続するために使用されます。 ネットワーク内の DNS レコードを設定するために、これらを使用できます。

Azure portal と API 呼び出しの応答に両方の型のアドレスが表示されます。

![VNet での API Management の IP アドレス](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management では、VNet 外部の接続にパブリック IP アドレスが使用され、VNet 内の接続にプライベート IP アドレスが使用されます。

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>従量課金レベルの API Management サービスの IP アドレス

API Management サービスが従量課金レベルのサービスの場合、専用 IP アドレスはありません。 従量課金レベルのサービスは、決定論的 IP アドレスを使用せずに、共有インフラストラクチャ上で実行されます。 

トラフィック制限のために、Azure データ センターの IP アドレスの範囲を使用できます。 詳細な手順については、[Azure Functions のドキュメントの記事](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses)を参照してください。

## <a name="changes-to-the-ip-addresses"></a>IP アドレスの変更

API Management の Developer、Basic、Standard、Premium の各レベルで、パブリック IP アドレス (VIP) は、次の例外を除いて、サービスの有効期間にわたって静的です。

* サービスが削除された後、再作成された。
* サービスのサブスクリプションが (未払いなどの理由により) [中断](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)または[警告あり](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states)となり、その後復元された。
* Azure Virtual Network がサービスに追加されるか、サービスから削除された。
* API Management サービスで、外部と内部の Vnet デプロイ モードが切り替えられた。

[複数リージョンのデプロイ](api-management-howto-deploy-multi-region.md)では、リージョンが空いていて再び復元されると、そのリージョンの IP アドレスが変更されます。
