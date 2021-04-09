---
title: Azure リレー ポート設定 | Microsoft Docs
description: この記事には、Azure Relay のポート値に必要な構成を示す表が含まれています。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85314271"
---
# <a name="azure-relay-port-settings"></a>Azure リレー ポート設定

Azure リレーのポート値に必要な構成を次の表に示します。

## <a name="hybrid-connections"></a>ハイブリッド接続

ハイブリッド接続は、基盤となるトランスポート構造として TLS を使用するポート 443 の WebSocket を採用し、**HTTPS** のみを使用します。 

## <a name="wcf-relays"></a>WCF リレー
  
|バインド|トランスポート セキュリティ|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (クライアント)|はい|HTTPS| 
|" |いいえ|HTTP|  
|[BasicHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (クライアント)|はい|9351/HTTPS|  
|" |いいえ|9350/HTTP|  
|[NetEventRelayBinding クラス](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[NetTcpRelayBinding クラス](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (クライアント/サービス)|接続前/接続後|5671/9352/HTTP (ハイブリッドを使用している場合は 9352/9353)|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (クライアント)|はい|9351/HTTPS|  
|" |いいえ|9350/HTTP|  
|[NetOnewayRelayBinding クラス](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (クライアント)|はい|HTTPS|  
|" |いいえ|HTTP|  
|[WebHttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (サービス)|接続前/接続後|9351/HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (クライアント)|はい|HTTPS|  
|" |いいえ|HTTP|  
|[WS2007HttpRelayBinding クラス](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (サービス)|接続前/接続後|9351/HTTP|

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)