---
title: Azure Network Watcher のデータ所在地 | Microsoft Docs
description: Network Watcher サービスのデータ所在地について理解する
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117837"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher のデータ所在地
Azure Network Watcher では、接続モニター (プレビュー) サービスを除いて、顧客データは格納されません。


## <a name="connection-monitor-preview-data-residency"></a>接続モニター (プレビュー) のデータ所在地
*接続モニター (プレビュー)* サービスを使用すると、顧客データが格納されます。 このデータは、Network Watcher によって 1 つのリージョンに自動的に格納されます。 そのため、*接続モニター (プレビュー)* は、[Trust Center](https://azuredatacentermap.azurewebsites.net/)で指定されているように、リージョン内のデータ所在地の要件を自動的に満たします。

## <a name="singapore-data-residency"></a>シンガポールのデータ所在地

Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋地域の東南アジア リージョン (シンガポール) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[セキュリティ センター](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

> [!Note]
> **以前のレプリケーション** エンド ユーザーの IP アドレスに関連する到達の可能性、待機時間、ネットワーク トポロジの変更を Network Watcher によって監視きるように、顧客には、エンド ユーザーの IP アドレスを Network Watcher インスタンスに格納するオプションがあります。 これらのエンド ユーザーの IP アドレスは、顧客データとして分類される場合があります。 2020 年 7 月 15 日の時点で、Network Watcher では、このデータは 1 つのリージョンに格納されます。 (顧客データは HK にレプリケートされなくなりました)。このデータは HK にレプリケートされなくなりました。 この顧客データは、保存時に暗号化されています。
> 
> この顧客データがサード パーティによってアクセスできる場合、サード パーティは IP アドレスを知ることができますが、その IP アドレスに関連付けられているマシンまたはデバイスへのサード パーティのアクセス権は付与されません。 Network Watcher では、サード パーティは HK にあるこの顧客データにはアクセスできないと考えます。

## <a name="next-steps"></a>次の手順

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) の概要をお読みください。
