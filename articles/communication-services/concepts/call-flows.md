---
title: Azure Communication Services での通話フロー
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services での通話フローについて説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cc8e0edd1109162f0b426be31eb875ba8465d091
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490774"
---
# <a name="call-flow-basics"></a>通話フローの基礎

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

次のセクションでは、Azure Communication Services での通話フローの概要を示します。 信号とメディアのフローは、ユーザーが行う通話の種類によって異なります。 通話の種類の例としては、一対一の VoIP、一対一の PSTN、および VoIP と PSTN で接続された参加者の組み合わせを含むグループ通話などがあります。 [通話の種類](./voice-video-calling/about-call-types.md)を確認してください。

## <a name="about-signaling-and-media-protocols"></a>信号とメディアのプロトコルについて

ピアツーピアまたはグループ通話を確立する際には、2 つのプロトコル (信号用の HTTP (REST) とメディア用の SRTP) がバックグラウンドで使用されます。

クライアント ライブラリ間、またはクライアント ライブラリと Communication Services Signaling Controllers 間の信号は、HTTP REST (TLS) を使用して処理されます。 リアルタイム メディア トラフィック (RTP) の場合、ユーザー データグラム プロトコル (UDP) が優先されます。 ファイアウォールによって UDP の使用が許可されない場合、クライアント ライブラリは、メディア用に伝送制御プロトコル (TCP) を使用します。

さまざまなシナリオでの信号とメディアのプロトコルを確認します。

## <a name="call-flow-cases"></a>通話フローのケース

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>ケース 1: 2 つのデバイス間の直接接続が可能な VoIP

一対一の VoIP またはビデオ通話では、トラフィックは最も直接的なパスを優先します。 "直接パス" とは、2 つのクライアント ライブラリがお互いを直接参照できる場合に、直接接続を確立することを意味します。 これは、通常、2 つのクライアント ライブラリが同じサブネット (サブネット 192.168.1.0/24 など) に存在する場合、または 2 つのデバイスがお互いを参照できるサブネットにそれぞれ存在する (サブネット 10.10.0.0/16 および 192.168.1.0/24 のクライアント ライブラリが相互に到達できる) 場合に可能です。

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="ユーザーと Communication Services 間の直接 VoIP 通話を示す図。":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>ケース 2: デバイス間の直接接続はできないが、NAT デバイス間の接続は可能な VoIP

サブネットに配置された 2 つのデバイスが相互に到達できない (たとえば、Alice がコーヒー ショップで仕事をしていて、Bob がホーム オフィスで仕事をしている) が、NAT デバイス間の接続は可能な場合、クライアント側のクライアント ライブラリは NAT デバイス経由で接続を確立します。

Alice の場合、コーヒー ショップの NAT が使用され、Bob の場合、ホーム オフィスの NAT が使用されます。 Alice のデバイスは彼女の NAT の外部アドレスを送信し、Bob のデバイスも同様に送信します。 クライアント ライブラリは、Azure Communication Services が無料で提供している STUN (Session Traversal Utilities for NAT) サービスから外部アドレスを学習します。 Alice と Bob の間のハンドシェイクを処理するロジックは、Azure Communication Services 提供のクライアント ライブラリ内に組み込まれています。 (追加の構成は不要です)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="STUN 接続を利用する VoIP 通話を示す図。":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>ケース 3: 直接接続も NAT 接続もできない VoIP

一方または両方のクライアント デバイスが対称 NAT の内側にある場合は、2 つのクライアント ライブラリ間でメディアを中継するための別のクラウド サービスが必要です。 このサービスは、TURN (Traversal Using Relays around NAT) と呼ばれ、Communication Services でも提供されています。 Communication Services の通話クライアント ライブラリは、検出されたネットワーク状態に基づいて自動的に TURN サービスを使用します。 Microsoft の TURN サービスのご利用には別途料金がかかります。

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="TURN 接続を利用する VoIP 通話を示す図。":::

### <a name="case-4-group-calls-with-pstn"></a>ケース 4:PSTN での通話のグループ化

PSTN 通話の信号とメディアの両方で、Azure Communication Services のテレフォニー リソースが使用されます。 このリソースは、他の通信事業者と相互接続されています。

PSTN メディア トラフィックは、メディア プロセッサと呼ばれるコンポーネントを介して流れます。

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Communication Services を使用した PSTN グループ通話を示す図。":::

> [!NOTE]
> メディア処理に詳しいユーザー向けの情報ですが、Microsoft のメディア プロセッサも [RFC 3261 SIP:セッション開始プロトコル](https://tools.ietf.org/html/rfc3261)で定義されている Back to Back User Agent です。したがって、Microsoft と通信事業者ネットワーク間の通話を処理する際にコーデックを変換できます。 Azure Communication Services Signaling Controller は、同じ RFC に準拠した SIP プロキシの Microsoft の実装です。

グループ通話の場合、メディアと信号は常に Azure Communication Services バックエンドを介して流れます。 すべての参加者のオーディオまたはビデオ、あるいはその両方は、メディア プロセッサ コンポーネントでミックスされます。 グループ通話のすべてのメンバーは、オーディオまたはビデオ ストリーム、あるいはその両方をメディア プロセッサに送信し、そこからミックスされたメディア ストリームが返されます。

グループ通話の既定のリアルタイム プロトコル (RTP) は、ユーザー データグラム プロトコル (UDP) です。

> [!NOTE]
> メディア プロセッサは、Multipoint Control Unit (MCU) または Selective Forwarding Unit (SFU) として機能できます。

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Communication Services 内の UDP メディア処理フローを示す図。":::

ファイアウォールの制限が原因で、クライアント ライブラリがメディアに対して UDP を使用できない場合は、伝送制御プロトコル (TCP) の使用が試行されます。 メディア プロセッサ コンポーネントには UDP が必要であるため、このような場合、TCP を UDP に変換するために Communication Services TURN サービスがグループ通話に追加されることに注意してください。 この場合、TURN 機能を手動で無効にしない限り、TURN の料金が発生します。

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Communication Services 内の TCP メディア処理フローを示す図。":::

### <a name="case-5-communication-services-client-library-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>ケース 5: スケジュールされた Teams の会議に Communication Services クライアント ライブラリと Microsoft Teams が参加

シグナリングのフローは、シグナリング コントローラーを経由します。 メディアのフローは、メディア プロセッサを経由します。 シグナリング コントローラーとメディア プロセッサは、Communication Services と Microsoft Teams との間で共有されます。

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="スケジュールされた Teams の会議に参加する Communication Services クライアント ライブラリと Teams クライアントを示す図。":::



## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [通話の概要](../quickstarts/voice-video-calling/getting-started-with-calling.md)

次のドキュメントもご覧ください。

- [通話の種類](../concepts/voice-video-calling/about-call-types.md)についてさらに学習する
- [クライアント - サーバー アーキテクチャ](./client-and-server-architecture.md)について学習する
- [通話フローのトポロジ](./detailed-call-flows.md)について学習する
