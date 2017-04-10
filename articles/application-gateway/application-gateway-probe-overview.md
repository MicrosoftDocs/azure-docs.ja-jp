---
title: "Azure Application Gateway の正常性監視の概要 | Microsoft Docs"
description: "Azure Application Gateway の監視機能の概要"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7eeba328-bb2d-4d3e-bdac-7552e7900b7f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 899115d213e626f17e58c2e5f01313f760f9e7f4
ms.lasthandoff: 03/27/2017


---

# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway による正常性監視の概要

既定では、Azure Application Gateway はバック エンド プールにあるすべてのリソースの状態を監視して、異常とみなしたリソースをプールから自動的に削除します。 Application Gateway は異常なインスタンスを継続的に監視し、このインスタンスが利用可能になり正常性プローブに応答するようになると、正常バック エンド プールに戻します。 Application Gateway は、バックエンドの HTTP 設定で定義されているポートを使用して正常性プローブを送信します。 この構成により、顧客がバックエンドへの接続に使用しているポートをプローブで確実にテストできます。

![application gateway probe example][1]

既定の正常性プローブによる監視を行うだけでなく、アプリケーションの要件に合わせて正常性プローブをカスタマイズすることもできます。 この記事では、既定とカスタムの両方の正常性プローブについて説明します。

> [!NOTE]
> Application Gateway サブネット上に NSG がある場合、Application Gateway サブネットで 65503 ～ 65534 のポート範囲をインバウンド トラフィック用に開いておく必要があります。 これらのポートは、バックエンドの正常性 API が機能するために必要です。

## <a name="default-health-probe"></a>既定の正常性プローブ

カスタム プローブ構成を設定しない場合、アプリケーション ゲートウェイにより既定の正常性プローブが自動で構成されます。 監視は、バックエンド プールに構成済みの IP アドレスに対して HTTP 要求を行うことで実行されます。 既定のプローブでは、バックエンドの http 設定が HTTPS に対応するように構成されている場合、プローブはバックエンドの正常性をテストする際に HTTPS も使用します。

たとえば、バックエンド サーバー A、B、C を使用して ポート 80 で HTTP ネットワーク トラフィックを受信するように、アプリケーション ゲートウェイを構成したとします。 既定の正常性監視では、30 秒ごとにこれら 3 つのサーバーに対して HTTP 応答が正常であるかどうかがテストされます。 正常な HTTP 応答の [状態コード](https://msdn.microsoft.com/library/aa287675.aspx) は、200 から 399 の間です。

サーバー A に対する既定のプローブ チェックが失敗した場合、アプリケーション ゲートウェイはサーバー A をバックエンド プールから削除するため、ネットワーク トラフィックがこのサーバーに送られなくなります。 既定のプローブは、削除後もサーバー A を 30 秒ごとにチェックし続けます。 サーバー A は、既定の正常性プローブからの要求に正常に応答するようになるとバックエンド プールに「正常」として戻され、このサーバーへのトラフィックの送信が再開されます。

### <a name="default-health-probe-settings"></a>既定の正常性プローブの設定

| プローブのプロパティ | 値 | Description |
| --- | --- | --- |
| プローブの URL |http://127.0.0.1:\<port\>/ |URL パス |
| 間隔 |30 |プローブの間隔 (秒) |
| タイムアウト |30 |プローブのタイムアウト (秒) |
| 異常のしきい値 |3 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

> [!NOTE]
> ポートはバックエンドの HTTP 設定と同じポートです。

既定のプローブは、正常性状態を判断する際に http://127.0.0.1:\<port\> だけをチェックします。 カスタム URL をチェックするように正常性プローブを構成するか、その他の設定を変更する必要がある場合は、以下の手順に従ってカスタム プローブを使用する必要があります。

## <a name="custom-health-probe"></a>カスタムの正常性プローブ

カスタム プローブを使用すると、正常性監視をより細かく制御できます。 カスタム プローブを使用する場合、プローブの間隔、テスト対象の URL とパス、バックエンド プール インスタンスを「異常」とマークするまでの応答の失敗回数を構成することができます。

### <a name="custom-health-probe-settings"></a>カスタムの正常性プローブの設定

カスタム正常性プローブのプロパティの定義を次の表に示します。

| プローブのプロパティ | Description |
| --- | --- |
| Name |プローブの名前。 この名前は、バックエンドの HTTP 設定でプローブを参照するために使用されます。 |
| プロトコル |プローブを送信するために使用するプロトコル。 プローブでは、バックエンドの HTTP 設定で定義されているプロトコルを使用します |
| Host |プローブを送信するホスト名。 Application Gateway でマルチサイトが構成されている場合にのみ適用されます。それ以外の場合は、"127.0.0.1" を使用します。 この値は VM ホスト名とは異なります。 |
| パス |プローブの相対パス。 パスは、先頭が '/' である必要があります。 |
| 間隔 |プローブの間隔 (秒)。 この値は、2 つの連続するプローブの時間間隔です。 |
| タイムアウト |プローブのタイムアウト (秒)。 このタイムアウト期間内に正常な応答が受信されなかった場合は、プローブが「失敗」とマークされます。  |
| 異常のしきい値 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

> [!IMPORTANT]
> Application Gateway を単一のサイトで構成する場合、既定ではホスト名は "127.0.0.1" と指定する必要があります (カスタム プローブで構成する場合は除く)。
> プローブは、\<protocol\>://\<host\>:\<port\>\<path\> に送信されます。 使用されるポートは、バックエンドの HTTP 設定で定義されているものと同じポートになります。

## <a name="next-steps"></a>次のステップ
Application Gateway による正常性監視について学習した後は、Azure Portal で[カスタム正常性プローブ](application-gateway-create-probe-portal.md)を構成することも、PowerShell と Azure Resource Manager デプロイト モデルを使用して[カスタム正常性プローブ](application-gateway-create-probe-ps.md)を構成することもできます。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

