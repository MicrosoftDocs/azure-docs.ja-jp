---
title: "Azure Relay のよく寄せられる質問 (FAQ) | Microsoft Docs"
description: "Azure Relay についてよく寄せられる質問 (FAQ) とその回答を紹介します。"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 4b54ad027a7de02cba7821f2a9b7fd06ef3a825b
ms.openlocfilehash: ca2767340cb232722def8f06277cc84d5c76c1bf


---
# <a name="relay-faq"></a>Relay に関する FAQ

この記事では、[Microsoft Azure Relay](https://azure.microsoft.com/services/service-bus/) についてよく寄せられる質問 (FAQ) とその回答を紹介します。 Azure の価格およびサポートに関する一般的な情報については、「 [Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 」も参照してください。

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-relay"></a>Azure Relay とは
ハイブリッド アプリケーションに [Azure Relay サービス](relay-what-is-it.md)を使用すると、ファイアウォール接続を開放せず、または企業ネットワークのインフラストラクチャ内部を変更せずに、企業のエンタープライズ ネットワーク内部にあるサービスを安全にパブリック クラウドに公開することができます。

### <a name="what-is-a-relay-namespace"></a>Relay 名前空間とは何ですか?
[名前空間](relay-create-namespace-portal.md)は、アプリケーション内で Relay リソースをアドレス指定するためのスコープ コンテナーを提供します。 Relay を使用するには、名前空間を作成する必要があります。これは、最初に実行する手順の&1; つです。

### <a name="what-happened-to-the-previously-named-relay-service"></a>以前の名前付き Relay サービスはどうなったのですか?
以前の名前付き Service Bus **Relay** サービスは、"*WCF リレー*" と呼ばれるようになっています。 このサービスは、これまでと同じように引き続き使用できます。 ハイブリッド接続は、BizTalk から移植されたサービスの更新バージョンです。 WCF リレーとハイブリッド接続はどちらも、今後もサポートされます。

## <a name="pricing"></a>価格
このセクションでは、Relay の価格体系についてよく寄せられる質問 (FAQ) とその回答を紹介します。 Microsoft Azure の全般的な価格情報については、 [Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) も参照してください。 Relay の価格の詳細については、 [Service Bus の価格の詳細](https://azure.microsoft.com/pricing/details/service-bus/)に関するページを参照してください。

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>ハイブリッド接続と WCF リレーの課金方法を教えてください。
Relay の価格の詳細については、[Service Bus の価格の詳細][Pricing overview]に関するページをご覧ください。 既に説明した価格に加え、ご利用のアプリケーションがプロビジョニングされているデータ センターから外部に送信される関連データ転送に料金が発生します。

### <a name="how-am-i-billed-for-hybrid-connections"></a>ハイブリッド接続はどのように課金されますか?
次の&3; つのシナリオの例を示します。

1. インストールされた Hybrid Connections Manager のインスタンスなどのリスナー 1 つを 1 か月間継続的に稼働させて、その接続で 1 か月で 3 GB のデータを送信した場合、合計料金は 5 ドルになります。
2. インストールされた Hybrid Connections Manager のインスタンスなどのリスナー 1 つを 1 か月間継続的に稼働させて、その接続で 1 か月で 10 GB のデータを送信した場合、合計料金は 7.50 ドルで、その内訳は 5 ドル (接続および最初の 5 GB) + 2.50 ドル (追加のデータ 5 GB 分) になります。
3. インストールされた Hybrid Connections Manager のインスタンスが 2 つ (A および B) があり、1 か月間継続的に稼働させて、接続 A で 3 GB のデータ、接続 B で 6 GB のデータを送信した場合、合計料金は 10.50 ドルで、その内訳は 5 ドル (接続 A) + 5 ドル (接続 B) + 0.50 ドル (接続 B の超過分 1 GB) になります。

サンプルで使用された料金はプレビュー期間にのみ適用されます。ハイブリッド接続の一般利用可能時には変更されることがありますのでご注意ください。

### <a name="how-are-wcf-relay-hours-calculated"></a>WCF リレー時間はどのように計算されますか?
リレー時間は、それぞれの Service Bus Relay が "開いている" 状態の累積時間に対して課金されます。 リレー対応の WCF サービス (リレー リスナー) が特定の Service Bus アドレス (サービスの名前空間の URL) に初めて接続すると、そのアドレスでリレーが暗黙的にインスタンス化されて開かれます。 リレーは、最後のリスナーがそのアドレスから切断したときにのみ閉じられます。 したがって、課金の目的上、最初のリレー リスナーが接続した時刻から最後のリレー リスナーがそのリレーの Service Bus アドレスから切断された時刻まで、リレーは "開いている" と見なされます。

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>特定の Relay に複数のリスナーを接続した場合はどうなりますか?
場合によっては、1 つの Relay に複数のリスナーが接続されていることも考えられます。 Relay は、接続されている Relay リスナーが&1; つでもあれば、"開いている" と見なされます。 開いている Relay にリスナーを追加すると、Relay 時間が追加で発生します。 また、Relay に接続された Relay センダー (Relay に対するメッセージの呼び出しまたは送信を実行するクライアント) の数が Relay 時間の計算に影響することもありません。

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>WCF Relay の場合、メッセージ数のメーターはどのようにして計算されますか?
**これは WCF Relay にのみ適用されます。ハイブリッド接続のコストではありません。**

一般に、Relay の場合も、前述した仲介型エンティティ (キュー、トピック、サブスクリプション) と同じ方法で、課金対象メッセージが計算されます。 ただし、注目すべき違いがいくつかあります。

Service Bus Relay へのメッセージ送信は、Service Bus Relay への送信とその後の Relay リスナーへの配信としてではなく、そのメッセージを受信する Relay リスナーに対する "直接の" 送信として扱われます。 したがって、Relay リスナーに対する (最大 64 KB の) 要求/応答形式のサービス呼び出しの場合、課金対象のメッセージは、要求のメッセージと応答のメッセージ (応答も \<64 KB 以下を想定) の 2 つになります。 クライアントとサービス間の仲介にキューを使用した場合は、この点が異なります。 後者の場合、同じ要求/応答パターンでも、キューに対する要求の送信、そのキューからサービスへのデキュー/配信、別のキューへの応答の送信、そのキューからクライアントへのデキュー/配信の順番で実行することが必要になります。 メッセージのサイズが最初から最後まで同じ (\<64 KB 以下) であると仮定した場合、仲介型キュー パターンでは課金対象のメッセージが 4 つとなり、Relay を使って同じパターンを実装した場合の 2 倍の料金が課金されることになります。 もちろん、キューを使用してこのパターンを実現すれば、持続性や負荷平準化などの利点が生まれます。 これらは追加費用に見合った利点です。

netTCPRelay WCF バインドを使って開いたリレーは、メッセージを個別ではなく、システムを流れるデータのストリームとして扱います。 つまり、このバインドを使って送受信された個々のメッセージを&1; つのまとまりとして認識できるのは、センダーとリスナーだけです。 したがって、netTCPRelay バインドを使った Relay の場合、課金対象のメッセージ数を計算するために、すべてのデータがストリームとして扱われます。 この場合、Service Bus は、個々の Relay を介して 5 分間に送受信されたデータの総量を計算し、その総量を 64 KB で割ることで、その期間における当該 Relay の課金対象のメッセージ数を特定します。

## <a name="quotas"></a>Quotas (クォータ)
| クォータ名 | スコープ | 型 | 超過したときの動作 | 値 |
| --- | --- | --- | --- | --- |
| リレーの同時リスナー |エンティティ |静的 |追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。 |25 |
| 同時リレー リスナー |システム全体 |静的 |追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。 |2,000 |
| あるサービス名前空間に含まれるリレー エンドポイント全部の同時リレー接続 |システム全体 |静的 |- |5,000 |
| サービス名前空間ごとのリレー エンドポイント |システム全体 |静的 |- |10,000 |
| [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) と [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) リレーのメッセージ サイズ |システム全体 |静的 |これらのサイズ クォータを超えるメッセージは受信が拒否され、呼び出し元のコードが例外を受け取ります。 |64 KB |
| [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) と [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) リレーのメッセージ サイズ |システム全体 |静的 |- |無制限 |

### <a name="does-relay-have-any-usage-quotas"></a>Relay に使用量クォータはありますか?
マイクロソフトは、既定で、任意のクラウド サービスに関して、お客様の全サブスクリプション全体で算出される月単位の総使用量クォータを設定しています。 実際のニーズがこれらの制限を上回る可能性があることは認識しておりますので、お客様のニーズを把握し、適宜制限を調整できるようにするためにも、お気軽にカスタマー サービスまでお問い合わせください。 Service Bus の総使用量クォータは次のとおりです。

* 50 億メッセージ
* 200 万 Relay 時間

マイクロソフトは、ある月に使用量クォータを超えた場合に、お客様のアカウントを無効にする権利を保有しています。ただし、その場合は電子メールでお客様にその旨をお知らせし、何度かお客様に連絡を試みたうえで、措置を講じることといたします。 このようなクォータを超えたお客様についても、その超過分が課金の対象となります。

### <a name="naming-restrictions"></a>名前に関する制限
Relay 名前空間の名前は 6 ～ 50 文字にする必要があります。

## <a name="subscription-and-namespace-management"></a>サブスクリプションと名前空間の管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションに名前空間を移行する方法を教えてください
PowerShell コマンドを使用して、Azure サブスクリプションを別の名前空間に移行できます ([このページ](../service-bus-messaging/service-bus-powershell-how-to-provision.md)を参照してください)。 操作を実行するには、名前空間が既にアクティブになっている必要があります。 また、コマンドを実行するユーザーは、ソースとターゲットの両方のサブスクリプションの管理者である必要があります。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Azure Relay API によって生成される例外とその推奨されるアクションをいくつか教えてください。
「[Relay の例外][Relay exceptions]」では、例外と推奨されるアクションがいくつか説明されています。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Shared Access Signature とは何ですか。どの言語で署名の生成がサポートされますか。
Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Node、PHP、Java、および C\# で独自の署名を生成する方法については、「[Shared Access Signature][Shared Access Signatures]」を参照してください。

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Relay エンドポイントをホワイトリストに登録することはできますか。
はい。 Relay クライアントは、完全修飾ドメイン名を使用して Relay サービスへの接続を確立します。 これにより、DNS ホワイトリスト登録をサポートするファイアウォールで、お客様が `*.servicebus.windows.net` のエントリを追加できます。

## <a name="next-steps"></a>次のステップ
* [名前空間を作成する](relay-create-namespace-portal.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Feb17_HO1-->


