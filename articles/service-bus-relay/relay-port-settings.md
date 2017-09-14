---
title: "Azure リレー ポート設定 | Microsoft Docs"
description: "Azure リレー ポートの値について説明します。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5906495c565dad583e74a43b2e5eed57e0c68df1
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---

# <a name="azure-relay-port-settings"></a>Azure リレー ポート設定

Azure リレーのポート値に必要な構成を次の表に示します。

## <a name="hybrid-connections"></a>Hybrid Connections
ハイブリッド接続は、基盤となるトランスポート構造に WebSocket を採用し、**HTTPS** のみを使用します。 

## <a name="wcf-relays"></a>WCF リレー
  
|バインド|トランスポート セキュリティ|ポート|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (クライアント)|はい|HTTPS| 
| |" |なし|HTTP|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (クライアント)|はい|9351/HTTPS|  
||" |なし|9350/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[NetTcpRelayBinding クラス](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (クライアント/サービス)|使用できるのは|5671/9352/HTTP (ハイブリッドを使用している場合は 9352/9353)|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (クライアント)|はい|9351/HTTPS|  
||" |なし|9350/HTTP|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (クライアント)|はい|HTTPS|  
||" |なし|HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (サービス)|使用できるのは|9351/HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (クライアント)|はい|HTTPS|  
||" |なし|HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (サービス)|使用できるのは|9351/HTTP|

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)
