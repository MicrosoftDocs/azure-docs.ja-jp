---
title: Azure での Load Balancer の TCP リセットおよびアイドルのタイムアウト
titleSuffix: Azure Load Balancer
description: この記事では、アイドル タイムアウト時に双方向 TCP RST パケットを使用する Azure Load Balancer について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696746"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer の TCP リセットおよびアイドルのタイムアウト

[Standard Load Balancer](./load-balancer-overview.md) を使用して、特定の規則のアイドル時の TCP リセットを有効にすることにより、シナリオのより予測可能なアプリケーション動作を作成します。 ロード バランサーの既定の動作では、フローのアイドル タイムアウトに達したときに、警告なしでフローを削除します。  この機能を有効にすると、ロード バランサーがアイドル タイムアウト時に双方向 TCP リセット (TCP RST パケット) を送信します。  これは、接続がタイムアウトしたため、使用できなくなったことをアプリケーション エンドポイントに通知します。  エンドポイントは、必要に応じて直ちに新しい接続を確立できます。

![ロード バランサー TCP リセット](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP リセット

この既定の動作は変更でき、受信 NAT 規則、負荷分散規則、[送信規則](./load-balancer-outbound-connections.md#outboundrules)に基づいて、アイドル タイムアウト時の TCP リセットの送信を有効にできます。  規則ごとに有効にすると、ロード バランサーは双方向 TCP リセット (TCP RST パケット) を、クライアントとサーバーの両方のエンドポイントに対して、一致するすべてのフローのアイドル タイムアウト時に送信します。

TCP RST パケットを受信するエンドポイントでは、対応するソケットをすぐに閉じます。 これは、接続のリリースが行われ、今後同じ TCP 接続での通信は失敗するという即時通知をエンドポイントに提供します。  必要に応じてソケットが閉じられ接続が再確立されるときに、最終的にTCP 接続のタイムアウトまで待つことなく、アプリケーションは接続を削除できます。

多くのシナリオでは、フローのアイドル タイムアウトを更新するために、TCP (またはアプリケーション層) キープアライブを送信する必要を減らすことができます。 

アイドル状態の期間が構成で許可されている期間を超えた場合、または TCP リセットが有効になっているアプリケーション望ましくない動作を示す場合、TCP 接続の存続性を監視するために、TCP キープアライブ (またはアプリケーション層キープアライブ) を引き続き使用することが必要になる場合があります。  さらに、キープアライブは、接続がパスのどこかでプロキシされている場合にも引き続き役立ちます (特にアプリケーション レイヤー キープアライブ)。  

エンド ツー エンド シナリオ全体を注意深く調べて、TCP リセットの有効化とアイドル タイムアウトの調整にメリットがあるかどうか、および目的のアプリケーション動作を確保するために追加の手順が必要かどうかを判断します。

## <a name="configurable-tcp-idle-timeout"></a>構成可能な TCP アイドル タイムアウト

Azure Load Balancer のアイドルには、次のタイムアウト範囲があります。
-  送信規則の場合は、4 分から 100 分
-  Load Balancer 規則と受信 NAT 規則の場合は、4 分から 30 分

既定では 4 分に設定されています。 アイドル時間がタイムアウト値よりも長い場合、クライアントとクラウド サービス間の TCP または HTTP セッションが維持されるという保証はありません。

接続が閉じられると、クライアント アプリケーションは、次のエラー メッセージを受信する場合があります。"基になる接続が閉じられました: 維持される必要があった接続が、サーバーによって切断されました。"

一般的な方法として、TCP keep-alive を使用します。 この方法を使用すると、接続が長時間アクティブ状態に維持されます。 詳細については、こちらの [.NET の例](/dotnet/api/system.net.servicepoint.settcpkeepalive)をご覧ください。 keep-alive を有効にすると、接続のアイドル時間にパケットが送信されます。 keep-alive パケットにより、アイドル タイムアウト値に達することがなくなり、接続が長時間維持されます。

設定は、着信接続に対してのみ有効です。 接続の切断を避けるためには、アイドル タイムアウト設定よりも小さい間隔で、TCP keep-alive を構成するか、アイドル タイムアウト値を大きくします。 これらのシナリオをサポートするために、構成可能なアイドル タイムアウトのサポートが追加されました。

TCP keep-alive は、バッテリーの寿命に制約がないシナリオに適しています。 モバイル アプリケーションでは推奨されません。 モバイル アプリケーションで TCP keep-alive を使用すると、デバイスのバッテリーの消耗を速める可能性があります。


## <a name="limitations"></a>制限事項

- TCP リセットが送信されるのは、ESTABLISHED 状態の TCP 接続時のみです。
- TCP リセットは、HA ポートが構成されている内部 Load Balancers には送信されません。
- TCP アイドル タイムアウトは、UDP プロトコルの負荷分散規則には影響しません。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](./load-balancer-overview.md) を参照します。
- [送信の規則](./load-balancer-outbound-connections.md#outboundrules)を参照します。
- [アイドル タイムアウトでの TCP RST の構成](load-balancer-tcp-idle-timeout.md)