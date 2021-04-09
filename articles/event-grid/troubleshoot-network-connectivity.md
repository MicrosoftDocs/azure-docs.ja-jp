---
title: ネットワーク接続の問題のトラブルシューティング - Azure Event Grid | Microsoft Docs
description: この記事では、Azure Event Grid でのネットワーク接続の問題のトラブルシューティングについて説明します。
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96339747"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>接続に関する問題のトラブルシューティング - Azure Event Grid

クライアント アプリケーションが Event Grid トピックまたはドメインに接続できない理由はさまざまです。 発生した接続の問題は、永続的である場合もあれば、一時的なものである場合もあります。 問題が頻繁に (永続的に) 発生する場合は、組織のファイアウォール設定、IP ファイアウォール設定、サービス タグ、プライベート エンドポイントなどを確認することをお勧めします。 一時的な問題の場合、破棄されたパケットを確認するコマンドを実行し、ネットワーク トレースを取得すると、問題のトラブルシューティングに役立つ場合があります。

この記事では、Azure Event Grid の接続に関する問題のトラブルシューティングに関するヒントを提供します。

## <a name="troubleshoot-permanent-connectivity-issues"></a>永続的な接続の問題のトラブルシューティング

アプリケーションが Event Grid にまったく接続できない場合は、このセクションの手順に従って問題のトラブルシューティングを行います。

### <a name="check-if-theres-a-service-outage"></a>サービス停止があるかどうかを確認する

[Azure サービスの状態サイト](https://azure.microsoft.com/status/)で Azure Event Grid のサービス停止を確認します。

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Event Grid との通信に必要なポートが組織のファイアウォールによってブロックされていないかどうかを確認する

Azure Event Grid との通信に使用されるポートが組織のファイアウォールでブロックされていないことを確認します。 Azure Event Grid と通信するために開く必要がある送信ポートについては、次の表を参照してください。

| Protocol | Port |
| -------- | ----- |
| HTTPS    | 443   |

443 ポートがブロックされているかどうかを確認するサンプル コマンドを次に示します。

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Linux の場合:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>企業のファイアウォールで IP アドレスが許可されていることを確認する

Azure を使用している場合、使用している、または使用しようとしているすべての Azure サービスにアクセスするために、企業のファイアウォールまたはプロキシで特定の IP アドレス範囲または URL を許可することが必要になる場合があります。 Event Grid によって使用される IP アドレスでトラフィックが許可されていることを確認します。 Azure Event Grid によって使用される IP アドレスについては、「[Azure の IP 範囲とサービス タグ - パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」と[サービス タグ - AzureEventGrid](network-security.md#service-tags) を参照してください。

「[Azure の IP 範囲とサービス タグ - パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」ドキュメントには、**リージョン別** の IP アドレスも記載されています。 企業のファイアウォールまたはプロキシで、**トピックのリージョン** と **ペアになっているリージョン** に対してアドレス範囲を許可することができます。 リージョンのペアになっているリージョンについては、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)」をご覧ください。 

> [!NOTE]
> 新しい IP アドレスを AzureEventGrid サービス タグに追加することはできますが、通常ではありません。 サービス タグを週単位で確認することをお勧めします。

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>ネットワーク セキュリティ グループで AzureEventGrid サービス タグが許可されていることを確認する

アプリケーションがサブネット内で実行されていて、関連付けられているネットワーク セキュリティ グループがある場合は、インターネット送信が許可されているか、または AzureEventGrid サービス タグが許可されているかを確認します。 [サービス タグ](../virtual-network/service-tags-overview.md)に関するページを参照してください。

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>トピックまたはドメインの IP ファイアウォール設定を確認する

アプリケーションが実行されているマシンのパブリック IP アドレスが EventGrid のトピックまたはドメインの IP ファイアウォールによってブロックされていないことを確認します。

既定では、要求に有効な認証と承認がある限り、Event Grid のトピックまたはドメインにはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

IP ファイアウォール規則は Event Grid のトピックまたはドメイン レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Event Grid のトピックまたはドメインの許可された IP 規則と一致しない IP アドレスからの接続試行は、禁止されているため拒否されます。 その応答に、IP 規則に関する記述は含まれません。

詳細については、「[Azure Event Grid のトピックまたはドメインに対して IP ファイアウォールを構成する](configure-firewall.md)」を参照してください。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>IP ファイアウォールによってブロックされている IP アドレスを見つける

Event Grid のトピックまたはドメインの[診断ログを有効にします](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic)。 拒否された接続の IP アドレスが表示されます。

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Event Grid のトピックまたはドメインにプライベート エンドポイントのみを使用してアクセスできるかどうかを確認する

Event Grid のトピックまたはドメインがプライベート エンドポイント経由でのみアクセスできるように構成されている場合は、クライアント アプリケーションがプライベート エンドポイント経由でトピックまたはドメインにアクセスしていることを確認します。 これを確認するには、クライアント アプリケーションがサブネット内で実行されていて、そのサブネットに Event Grid のトピックまたはドメインのプライベート エンドポイントがあるかどうかを確認します。

[Azure Private Link サービス](../private-link/private-link-overview.md)を使用すると、仮想ネットワーク内の **プライベート エンドポイント** 経由で Azure Event Grid にアクセスできます。 プライベート エンドポイントとは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

詳細については、[プライベート エンドポイントの構成](configure-private-endpoints.md)に関するページを参照してください。

## <a name="troubleshoot-transient-connectivity-issues"></a>一時的な接続の問題のトラブルシューティング

断続的な接続の問題が発生している場合は、次のセクションでトラブルシューティングのヒントを参照してください。

### <a name="run-the-command-to-check-dropped-packets"></a>コマンドを実行して、破棄されたパケットを確認する

接続の問題が断続的に発生する場合は、次のコマンドを実行して、破棄されたパケットがあるかどうかを確認します。 このコマンドは、1 秒ごとに 25 件の異なる TCP 接続をサービスと確立しようとします。 次に、成功または失敗した数を確認し、TCP 接続の待機時間も確認します。 `psping` ツールは、[こちら](/sysinternals/downloads/psping)からダウンロードできます。

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

`tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php) などの他のツールを使用している場合は、同等のコマンドを使用できます。

前の手順で解決できない場合は、ネットワーク トレースを取得して [Wireshark](https://www.wireshark.org/) などのツールを使用して分析します。 必要に応じて [Microsoft サポート](https://support.microsoft.com/) にお問い合わせください。

### <a name="service-upgradesrestarts"></a>サービスのアップグレードまたは再起動

一時的な接続の問題は、バックエンド サービスのアップグレードと再起動によって発生する可能性があります。 発生すると、次のような現象が発生する可能性があります。

- 受信メッセージや要求がドロップされる可能性があります。
- ログ ファイルにエラー メッセージが含まれる可能性があります。
- アプリケーションが数秒間サービスから切断される可能性があります。
- 要求が一時的に調整される場合があります。

これらの一時的なエラーをキャッチし、バックオフしてから、呼び出しを再試行すると、コードがこれらの一時的な問題に対する回復性を持つようになります。

## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。