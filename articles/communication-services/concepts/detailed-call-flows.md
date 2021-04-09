---
title: Azure Communication Services における通話フローのトポロジ
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services における通話フローのトポロジについて説明します。
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490638"
---
# <a name="call-flow-topologies"></a>通話フローのトポロジ
この記事では、Azure Communication Services における通話フローのトポロジについて説明します。 この記事は、管理下のネットワーク内に Communication Services を統合することを検討しているエンタープライズのお客様にぜひお勧めします。 Communication Services における通話フローの概要については、[通話フローの概念に関するドキュメント](./call-flows.md)を参照してください。

## <a name="background"></a>背景

### <a name="network-concepts"></a>ネットワークの概念

通話フローのトポロジを確認する前に、このドキュメントで言及されているいくつかの用語を定義しておきましょう。

**カスタマー ネットワーク** には、貴社が管理するネットワーク セグメントが存在します。 これには、オフィス内のワイヤード (有線) ネットワークとワイヤレス ネットワークのほか、オフィスとデータ センターとインターネット サービス プロバイダーを結ぶワイヤード ネットワークおよびワイヤレス ネットワークが含まれる場合もあります。

通常、カスタマー ネットワークにはいくつかのネットワーク境界が存在し、そこに組織のセキュリティ ポリシーを適用するプロキシ サーバーやファイアウォールが配置されます。 ご利用の通信ソリューションに最適なパフォーマンスと品質を確保するために、[包括的なネットワーク評価](/microsoftteams/3-envision-evaluate-my-environment)を実施することをお勧めします。

**Communication Services ネットワーク** は、Azure Communication Services をサポートするネットワーク セグメントです。 このネットワークは、Microsoft によって管理されます。世界全域に分散され、ほとんどのカスタマー ネットワークに近い場所にそのエッジが配置されています。 トランスポートのリレー、グループ通話のメディア処理など、多彩なリアルタイム メディア通信をサポートするコンポーネントをこのネットワークが担っています。

### <a name="types-of-traffic"></a>トラフィックの種類

Communication Services の基礎となるのは、主として **リアルタイム メディア** と **シグナリング** という 2 種類のトラフィックです。

**リアルタイム メディア** は、リアルタイム転送プロトコル (RTP) を使用して転送されます。 このプロトコルは、音声、動画、画面共有のデータ伝送をサポートします。 このデータは、ネットワーク待ち時間が大きな問題となります。 リアルタイム メディアの転送に TCP や HTTP を使用することもできますが、ハイパフォーマンスのエンドユーザー エクスペリエンスをサポートするために、トランスポートレイヤー プロトコルとしては UDP の使用をお勧めします。 RTP で転送されるメディア ペイロードは、SRTP を使用してセキュリティが確保されます。

Communication Services ソリューションのユーザーは、各自のクライアント デバイスからサービスに接続することになります。 これらのデバイスとサーバーの間の通信処理には、**シグナリング** が使用されます。 たとえば通話の開始やリアルタイム チャットを支えているのは、デバイスとサービスの間のシグナリングです。 ほとんどのシグナリング トラフィックには HTTPS REST が使用されますが、一部のシナリオでは、シグナリング トラフィック プロトコルとして SIP が使用されることもあります。 このタイプのトラフィックは待ち時間がさほど問題になりませんが、低遅延のシグナリングは、ソリューションのユーザーに快適なエンドユーザー エクスペリエンスをもたらします。

### <a name="interoperability-restrictions"></a>相互運用性の制限

Communication Services を流れるメディアは、次のように制限されています。

**サードパーティのメディア リレー。** サードパーティのメディア リレーとの相互運用性はサポートされません。 ご使用のメディア エンドポイントのいずれかが Communication Services である場合、メディアが通過できるのは、Microsoft ネイティブのメディア リレーだけです (Microsoft Teams や Skype for Business をサポートするものなど)。

SRTP を使用してセキュリティが確保された RTP/RTCP ストリームは、PSTN との境界にあるサードパーティのセッション ボーダー コントローラー (SBC) によって終了され、ネクスト ホップにリレーされません。 そのフローをネクスト ホップにリレーしたとしても、正しく解釈されないでしょう。

**サードパーティの SIP プロキシ サーバー。** サードパーティの SBC やゲートウェイに対する Communication Services のシグナリング SIP ダイアログは、Microsoft ネイティブの SIP プロキシを通過できます (Teams と同様)。 サードパーティの SIP プロキシとの相互運用性はサポートされません。

**サードパーティの B2BUA (または SBC)。** PSTN との間でやり取りされる Communication Services のメディア フローは、サードパーティの SBC によって終了されます。 Communication Services ネットワーク内では、サードパーティの SBC との相互運用性 (2 つの Communication Services エンドポイントをサードパーティの SBC が仲介すること) はサポートされません。

### <a name="unsupported-technologies"></a>サポートされないテクノロジ

**VPN ネットワーク。** Communication Services では、VPN でのメディア伝送がサポートされません。 「[Lync メディアを有効にして VPN トンネルをバイパスする](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)」に記載されているように、ユーザーが VPN クライアントを使用している場合、そのクライアントはメディア トラフィックを分割し、VPN 以外の接続でルーティングする必要があります。

"*注: タイトルには Lync とありますが、Azure Communication Services や Teams にも当てはまります。* "

**パケット シェーパー。** パケット スニッピング、パケット インスペクション、パケット シェーピングの各デバイスはサポートされておらず、品質が著しく低下する可能性があります。

### <a name="call-flow-principles"></a>通話フローの原則

Communication Services の通話フローは、次の 4 つの一般原則によって支えられています。

* **通話がホストされるリージョンは、Communication Services のグループ通話の最初の参加者によって決まる**。 後ほど説明するとおり、一部のトポロジでは、このルールに例外があります。
* **Communication Services の通話をサポートするために使用されるメディア エンドポイントは、メディア処理のニーズに基づいて選択** され、通話への参加者の数には左右されない。 たとえば、ポイントツーポイントの通話で、文字起こしやレコーディング用のメディアを処理するためにクラウド内のメディア エンドポイントが使用されることがあります。一方、参加者 2 名の通話でも、メディア エンドポイントは使用されないことがあります。 グループ通話では、ミキシングやルーティングを目的としてメディア エンドポイントが使用されます。 このエンドポイントは、通話がホストされているリージョンに基づいて選択されます。 クライアントからメディア エンドポイントに送信されたメディア トラフィックは直接ルーティングされるほか、(カスタマー ネットワークのファイアウォール制限によって要求された場合) Azure のトランスポート リレーがトラフィックに使用されることもあります。
* **ピアツーピア通話のメディア トラフィックには、利用可能な最も直接的なルートが使用される** (クラウド内のメディア エンドポイントを必要としない通話の場合)。 優先されるルートは、リモート ピア (クライアント) との直接ルートです。 直接ルートが利用できない場合は、1 つまたは複数のトランスポート リレーによってトラフィックがリレーされます。 メディア トラフィックは、パケット シェーパー、VPN サーバーのように、処理の遅延につながったり、エンドユーザー エクスペリエンスを低下させたりする働きをするサーバーを通過すべきではありません。
* **シグナリング トラフィックは必ず、ユーザーに最も近いサーバーに向かう。**

選択されるメディア パスについて詳しくは、[通話フローの概念に関するドキュメント](./call-flows.md)を参照してください。


## <a name="call-flows-in-various-topologies"></a>各種トポロジにおける通話フロー

### <a name="communication-services-internet"></a>Communication Services (インターネット)

このトポロジは、SIP Interface などのオンプレミス デプロイを使わず、クラウドから Communication Services を利用するお客様向けです。 このトポロジでは、Communication Services との間でやり取りされるトラフィックがインターネットを経由します。

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Communication Services のトポロジ。":::

*図 1 - Communication Services のトポロジ*

上の図における矢印の方向は、エンタープライズの境界領域での接続に関係する通信の開始方向を表しています。 メディアの UDP の場合、最初のパケットが逆方向に流れることもありますが、もう一方の方向のパケットが流れるようになるまで、それらのパケットはブロックされます。

フローの説明:
* フロー 2* - カスタマー ネットワーク上のユーザーが Communication Services を利用する過程でインターネットに向けて開始したフローを表します。 こうしたフローの例としては、DNS やピアツーピア メディア伝送があります。
* フロー 2 - リモート モバイル Communication Services ユーザーが、VPN を使用し、カスタマー ネットワークに向けて開始するフローを表します。
* フロー 3 - リモート モバイル Communication Services ユーザーが Communication Services エンドポイントに向けて開始するフローを表します。
* フロー 4 - カスタマー ネットワーク上のユーザーが Communication Services に向けて開始するフローを表します。
* フロー 5 - カスタマー ネットワーク内の Communication Services ユーザーどうしでやり取りされるピアツーピア メディア フローを表します。
* フロー 6 - リモート モバイル Communication Services ユーザーどうしがインターネット上でやり取りするピアツーピア メディア フローを表します。

### <a name="use-case-one-to-one"></a>ユース ケース:一対一

一対一の通話では、IP アドレスとポートから成る一連の候補を呼び出し元が取得する一般的なモデルが使用されます。候補には、ローカル、リレー、リフレクティブ (リレー側から見たクライアントのパブリック IP アドレス) があります。 呼び出し元がこれらの候補を呼び出し先に送信すると、呼び出し先も、同様の一連の候補を取得して呼び出し元に送信します。 正常に機能している呼び出し元と呼び出し先のメディア パスが、STUN 接続チェック メッセージを使用して特定され、最適なパスが選択されます。 その後、選択された候補のペアを使用して、メディア (SRTP を使用して保護された RTP または RTCP パケット) が送信されます。 トランスポート リレーは、Azure Communication Services の構成要素としてデプロイされます。

IP アドレスとポートから成るローカル候補またはリフレクティブ候補での通信が可能であれば、メディアには、クライアント間の直接パス (または NAT を使用したパス) が選択されます。 両方のクライアントがカスタマー ネットワークに存在する場合は、直接パスが選択されなければなりません。 そのためには、直接 UDP 接続がカスタマー ネットワーク内に存在している必要があります。 クライアントがどちらもノマド クラウド ユーザーである場合、NAT (またはファイアウォール) に応じて、メディアに直接接続を使用することができます。

一方のクライアントがカスタマー ネットワークの内部に、またもう一方のクライアントが外部にいる場合 (モバイル クラウド ユーザーなど)、ローカルまたはリフレクティブ候補間で直接接続が使用できる可能性は低くなります。 この場合は、どちらかのクライアントから、いずれかのトランスポート リレー候補を使用することになります (たとえば、仮に内部クライアントが Azure 内のトランスポート リレーからリレー候補を取得した場合、外部クライアントは、STUN、RTP、RTCP パケットをトランスポート リレーに送信できる必要があります)。 または、モバイル クラウド クライアントによって取得されたリレー候補に対して内部クライアントが送信するという選択肢もあります。 メディアには UDP 接続が強く推奨されますが、TCP もサポートされています。

**大まかな手順:**
1.  Communication Services ユーザー A がフロー 2 を使用して URL のドメイン名 (DNS) を解決します。
2.  ユーザー A がフロー 4 を使用して、Teams トランスポート リレーにメディア リレー ポートを割り当てます。
3.  Communication Services ユーザー A がフロー 4 を使用し、ICE 候補を含む "招待状" を Communication Services に送信します。
4.  Communication Services がフロー 4 を使用してユーザー B に通知します。
5.  ユーザー B がフロー 4 を使用して、Teams トランスポート リレーにメディア リレー ポートを割り当てます。
6.  ユーザー B がフロー 4 を使用して、ICE 候補を含む "応答" を送信します。この応答が、フロー 4 を使用してユーザー A に転送されます。
7.  ユーザー A とユーザー B が ICE 接続テストを呼び出し、利用できる最適なメディア パスが選択されます (各種のユース ケースについては下の各図を参照してください)。
8.  両方のユーザーがフロー 4 を使用して Communication Services にテレメトリを送信します。

### <a name="customer-network-intranet"></a>カスタマー ネットワーク (イントラネット)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="カスタマー ネットワーク内のトラフィック フロー。":::

*図 2 - カスタマー ネットワーク内*

手順 7. では、ピアツーピアのメディア フロー 5 が選択されています。

このメディア伝送は双方向です。 フロー 5 の方向は、接続の観点から一方の側が通信を開始することを示します。 このケースでは、両方のエンドポイントがカスタマー ネットワーク内に存在するため、どちらの方向が使用されるかは問題ではありません。

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>カスタマー ネットワークから外部ユーザー (メディアは Teams トランスポート リレーによって中継)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="リレーを介した一対一の通話フロー。":::

*図 3 - カスタマー ネットワークから外部ユーザー (メディアは Azure トランスポート リレーによって中継)*

手順 7. で、フロー 4 (カスタマー ネットワークから Communication Services) とフロー 3 (リモート モバイル Communication Services ユーザーから Azure Communication Services) が選択されます。

これらのフローは、Azure 内の Teams トランスポート リレーによって中継されます。

このメディア伝送は双方向です。 接続の観点から、どちら側から通信を開始するかが、方向によって示されます。 このケースでは、これらのフローはシグナリングとメディアに使用されます (それぞれ異なるトランスポート プロトコルとアドレスが使用されます)。

### <a name="customer-network-to-external-user-direct-media"></a>カスタマー ネットワークから外部ユーザー (ダイレクト メディア)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="外部ユーザーとの一対一の通話フロー。":::

*図 4 - カスタマー ネットワークから外部ユーザー (ダイレクト メディア)*

手順 7. で、フロー 2 (カスタマー ネットワークからインターネット経由でクライアントのピア) が選択されます。

リモート モバイル ユーザーとの (Azure によって中継されない) ダイレクト メディアは任意です。 つまり、Azure のトランスポート リレーを経由したメディア パスを強制的に適用するために、このパスはブロックすることができます。

このメディア伝送は双方向です。 リモート モバイル ユーザーに向かうフロー 2 の方向は、接続の観点から一方の側が通信を開始することを示します。

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN ユーザーから内部ユーザー (メディアは Teams トランスポート リレーによって中継)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="リレーを介した VPN ユーザーとの一対一の通話フロー。":::

*図 5 - VPN ユーザーから内部ユーザー (メディアは Azure Relay によって中継)*

VPN とカスタマー ネットワークとの間のシグナリングにはフロー 2* が使用されます。 カスタマー ネットワークと Azure との間のシグナリングにはフロー 4 が使用されます。 一方、メディアは VPN をバイパスし、フロー 3 とフロー 4 を使用して、Azure メディア リレー経由でルーティングされます。

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN ユーザーから内部ユーザー (ダイレクト メディア)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="ダイレクト メディアを使用した VPN との一対一の通話フロー (内部ユーザー)":::

*図 6 - VPN ユーザーから内部ユーザー (ダイレクト メディア)*

VPN とカスタマー ネットワークとの間のシグナリングにはフロー 2' が使用されます。 カスタマー ネットワークと Azure との間のシグナリングにはフロー 4 が使用されています。 一方、メディアは VPN をバイパスし、カスタマー ネットワークからフロー 2 を使用してインターネットへとルーティングされます。

このメディア伝送は双方向です。 リモート モバイル ユーザーに向かうフロー 2 の方向は、接続の観点から一方の側が通信を開始することを示します。

### <a name="vpn-user-to-external-user-direct-media"></a>VPN ユーザーから外部ユーザー (ダイレクト メディア)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="ダイレクト メディアを使用した VPN との一対一の通話フロー (外部ユーザー)":::

*図 7 - VPN ユーザーから外部ユーザー (ダイレクト メディア)*

VPN ユーザーとカスタマー ネットワークとの間のシグナリングにはフロー 2* が、さらに Azure との間のシグナリングにはフロー 4 が使用されます。 一方、メディアは VPN をバイパスし、フロー 6 を使用してルーティングされます。

このメディア伝送は双方向です。 リモート モバイル ユーザーに向かうフロー 6 の方向は、接続の観点から一方の側が通信を開始することを示します。

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>ユース ケース: Communication Services クライアントから Communication Services トランクを経由して PSTN へ

Communication Services では、公衆交換電話網 (PSTN) との間で電話をかけたり受けたりすることができます。 Communication Services から提供された電話番号を使用して PSTN トランクが接続されていれば、このユース ケースに関して特別な接続要件はありません。 独自のオンプレミス PSTN トランクを Azure Communication Services に接続したい場合は、SIP インターフェイス (2021 年に提供) を使用することができます。

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="PSTN 参加者との一対一の通話":::

*図 8 -Communication Services ユーザーが Azure トランク経由で PSTN に接続*

この場合、カスタマー ネットワークから Azure へのシグナリングとメディアにはフロー 4 が使用されます。

### <a name="use-case-communication-services-group-calls"></a>ユース ケース:Communication Services のグループ通話

VBSS (音声、動画、画面共有) サービスは、Azure Communication Services に含まれます。 このサービスには、カスタマー ネットワークおよびノマド クラウド クライアントから到達可能なパブリック IP アドレスが割り当てられます。 それぞれのクライアントとエンドポイントがサービスに接続できる必要があります。

内部クライアントは、前述した一対一の通話と同じ方法で、ローカル、リフレクティブ、リレーの各候補を取得します。 これらの候補が招待状に追加されて、クライアントからサービスに送信されます。 このサービスには、パブリックに到達可能な IP アドレスが割り当てられるのでリレーは使用されません。そのため応答には、対応するローカル IP アドレス候補が使用されます。 クライアントとサービスは、前述した一対一の通話と同じ方法で接続をチェックします。

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS グループ通話":::

*図 9 - Communication Services のグループ通話*

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話の概要](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次のドキュメントもご覧ください。

- [通話の種類](../concepts/voice-video-calling/about-call-types.md)についてさらに学習する
- [クライアント - サーバー アーキテクチャ](./client-and-server-architecture.md)について学習する