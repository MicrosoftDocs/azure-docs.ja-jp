---
title: Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する
titleSuffix: Azure Load Balancer
description: Azure Instance Metadata Service を使用してロード バランサーのメタデータを取得する方法について説明します。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739782"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS) を使用してロード バランサーのメタデータを取得する

## <a name="prerequisites"></a>前提条件

* 要求の[最新の API バージョン](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions)を使用します。

## <a name="sample-request-and-response"></a>要求と応答の例
> [!IMPORTANT]
> この例では、プロキシをバイパスします。 IMDS に対してクエリを実行する場合は、プロキシをバイパスする **必要があります**。 詳細については、「[プロキシ](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies)」を参照してください。
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> -NoProxy パラメーターは PowerShell 6.0 で導入されました。 以前のバージョンの PowerShell を使用している場合は、要求本文から -NoProxy を削除して、IMDS 情報の取得中にプロキシを使用しないようにします。 [こちら](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies)をご覧ください。
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>応答のサンプル

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>次のステップ
[一般的なエラー コードとトラブルシューティングの手順](troubleshoot-load-balancer-imds.md)

[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) についての詳細情報

[インスタンスのすべてのメタデータを取得する](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Standard ロード バランサーをデプロイする](quickstart-load-balancer-standard-public-portal.md)

