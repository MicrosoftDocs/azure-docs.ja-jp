---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507450"
---
| リソース | 制限 |
| --- | --- |
| データ スループット |30 Gbps<sup>1</sup> |
|ルール|10,000。 すべての規則の種類が結合されます。|
|最大 DNAT ルール|1 つのパブリック IP アドレスに対して 298。<br>追加のパブリック IP アドレスは、使用可能な SNAT ポートに寄与しますが、使用可能な DNAT 規則の数は少なくなります。 たとえば、2 つのパブリック IP アドレスの場合、297 個の DNAT 規則を使用できます。 規則のプロトコルが TCP と UDP の両方に対して構成されている場合は、2 つの規則としてカウントされます。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|1 - 65535|
|パブリック IP アドレス|最大 250 個。 すべてのパブリック IP アドレスは DNAT ルールで使用できます。これらはすべて、利用可能な SNAT ポートに影響します。|
|IP グループの IP アドレス|ファイアウォールあたり最大 100 の IP グループ。<br>各 IP グループあたりの最大 5000 の個々の IP アドレスまたは IP プレフィックス。
|ルート テーブル|AzureFirewallSubnet には既定で、NextHopType の値が **Internet** に設定された 0.0.0.0/0 ルートがあります。<br><br>Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でそれをオーバーライドする必要があります。 既定では、Azure Firewall はオンプレミス ネットワークへの強制トンネリングをサポートしません。<br><br>ただし、オンプレミス ネットワークへの強制トンネリングが必要な構成の場合、Microsoft は状況に応じてサポートします。 サポートにお問い合わせいただければ、お客様の状況を確認させていただきます。 受け付けると、Microsoft ではサブスクリプションを許可し、必要なファイアウォールのインターネット接続が確保されていることを確認します。|

<sup>1</sup>これらの制限値を引き上げる必要がある場合は、Azure サポートにお問い合せください。
