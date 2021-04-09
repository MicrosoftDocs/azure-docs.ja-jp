---
title: 接続の問題のトラブルシューティング - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs での接続の問題のトラブルシューティングについて説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012682"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>接続に関する問題のトラブルシューティング - Azure Event Hubs
クライアント アプリケーションがイベント ハブに接続できない理由はさまざまです。 発生した接続の問題は、永続的である場合もあれば、一時的なものである場合もあります。 問題が常に (永続的に) 発生する場合は、接続文字列、組織のファイアウォール設定、IP ファイアウォール設定、ネットワーク セキュリティ設定 (サービス エンドポイント、プライベート エンドポイントなど) などを確認することをお勧めします。 一時的な問題の場合は、SDK の最新バージョンへのアップグレード、ドロップされたパケットを確認するコマンドの実行、ネットワーク トレースの取得によって問題のトラブルシューティングに役立つ場合があります。 

この記事では、Azure Event Hubs の接続に関する問題のトラブルシューティングに関するヒントを提供します。 

## <a name="troubleshoot-permanent-connectivity-issues"></a>永続的な接続の問題のトラブルシューティング
アプリケーションがイベント ハブにまったく接続できない場合は、このセクションの手順に従って問題のトラブルシューティングを行います。 

### <a name="check-if-there-is-a-service-outage"></a>サービス停止があるかどうかを確認する
[Azure サービスの状態サイト](https://azure.microsoft.com/status/)で Azure Event Hubs のサービス停止を確認します。

### <a name="verify-the-connection-string"></a>接続文字列を確認する 
使用している接続文字列が正しいことを確認します。 Azure portal、CLI、または PowerShell を使用して接続文字列を取得するには、[接続文字列の取得](event-hubs-get-connection-string.md)に関するページを参照してください。 

Kafka クライアントの場合、producer.config または consumer.config の各ファイルが正しく構成されていることを確認します。 詳細については、「[Event Hubs で Kafka を使用してメッセージを送受信する](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)」を参照してください。

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>ネットワーク セキュリティ グループで AzureEventGrid サービス タグが許可されていることを確認する
アプリケーションがサブネット内で実行されていて、関連付けられているネットワーク セキュリティ グループがある場合は、インターネット アウトバウンドが許可されているか、または AzureEventGrid サービス タグが許可されているかを確認します。 「[仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)」を参照し、`EventHub` を検索してください。

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>アプリケーションを vnet の特定のサブネットで実行する必要があるかどうかを確認する
名前空間にアクセスできる仮想ネットワーク サブネットでアプリケーションが実行されていることを確認します。 そうでない場合は、名前空間にアクセスできるサブネットでアプリケーションを実行するか、アプリケーションが実行されているマシンの IP アドレスを [IP ファイアウォール](event-hubs-ip-filtering.md)に追加します。 

イベントハブ名前空間の仮想ネットワーク サービス エンドポイントを作成すると、名前空間では、サービス エンドポイントにバインドされているサブネットからのトラフィックのみを受け入れます。 この動作には例外があります。 イベント ハブ パブリック エンドポイントへのアクセスを有効にするために、IP ファイアウォールで特定の IP アドレスを追加できます。 詳細については、[ネットワーク サービス エンドポイント](event-hubs-service-endpoints.md)に関するページを参照してください。

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>名前空間の IP ファイアウォール設定を確認する
アプリケーションが実行されているマシンのパブリック IP アドレスが IP ファイアウォールによってブロックされていないことを確認します。  

既定では、要求に有効な認証と承認がある限り、Event Hubs 名前空間にはインターネットからアクセスできます。 これは IP ファイアウォールを使用して、さらに [CIDR (クラスレス ドメイン間ルーティング)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記の一連の IPv4 アドレスまたは IPv4 アドレス範囲のみに制限できます。

IP ファイアウォール規則は、Event Hubs 名前空間レベルで適用されます。 したがって、規則は、サポートされているプロトコルを使用するクライアントからのすべての接続に適用されます。 Event Hubs 名前空間上の許可 IP 規則に一致しない IP アドレスからの接続試行は、未承認として拒否されます。 IP 規則に関する記述は応答に含まれません。 IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

詳細については、「[Azure Event Hubs 名前空間に対する IP ファイアウォール規則を構成する](event-hubs-ip-filtering.md)」を参照してください。 IP フィルター処理、仮想ネットワーク、または証明書チェーンの問題があるかどうかを確認するには、「[ネットワーク関連の問題をトラブルシューティングする](#troubleshoot-network-related-issues)」を参照してください。

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>プライベート エンドポイントのみを使用して名前空間にアクセスできるかどうかを確認する
Event Hubs がプライベート エンドポイント経由でのみアクセスできるように構成されている場合は、クライアント アプリケーションがプライベート エンドポイント経由で名前空間にアクセスしていることを確認します。 

[Azure Private Link サービス](../private-link/private-link-overview.md)を使用すると、仮想ネットワーク内の **プライベート エンドポイント** 経由で Azure Event Hubs にアクセスできます。 プライベート エンドポイントとは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントは、ご自分の仮想ネットワークからのプライベート IP アドレスを使用して、サービスを実質的に仮想ネットワークに取り込みます。 サービスへのすべてのトラフィックをプライベート エンドポイント経由でルーティングできるため、ゲートウェイ、NAT デバイス、ExpressRoute または VPN 接続、パブリック IP アドレスは必要ありません。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 最高レベルの細分性でアクセスを制御しながら Azure リソースのインスタンスに接続できます。

詳細については、[プライベート エンドポイントの構成](private-link-service.md)に関するページを参照してください。 プライベート エンドポイントが使用されていることを確認するには、**プライベート エンドポイント接続の動作検証** に関するセクションを参照してください。 

### <a name="troubleshoot-network-related-issues"></a>ネットワーク関連の問題をトラブルシューティングする
Event Hubs のネットワーク関連の問題をトラブルシューティングするには、次の手順を実行します。 

`https://<yournamespacename>.servicebus.windows.net/` の参照または [wget](https://www.gnu.org/software/wget/) を行います。 これは、IP フィルタリング、仮想ネットワーク、または証明書チェーンの問題 (Java SDK の使用時に最も一般的) があるかどうかを確認するのに役立ちます。

**成功したメッセージ** の例を次に示します。

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

**失敗したエラー メッセージ** の例を次に示します。

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>一時的な接続の問題をトラブルシューティングする
断続的な接続の問題が発生している場合は、次のセクションでトラブルシューティングのヒントを参照してください。 

### <a name="use-the-latest-version-of-the-client-sdk"></a>最新バージョンのクライアント SDK を使用する
一時的な接続の問題の一部は、使用しているバージョンよりも新しいバージョンの SDK で修正されている可能性があります。 アプリケーションでクライアント SDK の最新バージョンを使用していることを確認します。 SDK は、新機能、更新された機能、バグ修正によって継続的に改善されているため、常に最新のパッケージを使用してテストしてください。 修正された問題と追加または更新された機能については、リリース ノートを確認してください。 

クライアント SDK の詳細については、「[Azure Event Hubs - クライアント SDK](sdks.md)」を参照してください。 

### <a name="run-the-command-to-check-dropped-packets"></a>コマンドを実行して、破棄されたパケットを確認する
接続の問題が断続的に発生する場合は、次のコマンドを実行して、破棄されたパケットがあるかどうかを確認します。 このコマンドは、1 秒ごとに 25 件の異なる TCP 接続をサービスと確立しようとします。 次に、成功または失敗した数を確認し、TCP 接続の待機時間も確認します。 `psping` ツールは、[こちら](/sysinternals/downloads/psping)からダウンロードできます。

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
`tnc` や`ping` などの他のツールを使用している場合は、同等のコマンドを使用できます。 

前の手順で解決できない場合は、ネットワーク トレースを取得して [Wireshark](https://www.wireshark.org/) などのツールを使用して分析します。 必要に応じて [Microsoft サポート](https://support.microsoft.com/) にお問い合わせください。 

### <a name="service-upgradesrestarts"></a>サービスのアップグレードまたは再起動
一時的な接続の問題は、バックエンド サービスのアップグレードと再起動によって発生する可能性があります。 発生すると、次のような現象が発生する可能性があります。 

- 受信メッセージや要求がドロップされる可能性があります。
- ログ ファイルにエラー メッセージが含まれる可能性があります。
- アプリケーションが数秒間サービスから切断される可能性があります。
- 要求が一時的に調整される場合があります。

アプリケーションのコードで SDK を利用している場合、再試行ポリシーは既に組み込まれており、アクティブになっています。 アプリケーションやワークフローに大きな影響を与えずに、アプリケーションは再接続されます。 これらの一時的なエラーをキャッチし、バックオフしてから、呼び出しを再試行すると、コードがこれらの一時的な問題に対する回復性を持つようになります。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

* [認証と承認の問題のトラブルシューティング](troubleshoot-authentication-authorization.md)
