---
title: Windows Virtual Desktop のネットワーク接続について
titleSuffix: Azure
description: Windows Virtual Desktop のネットワーク接続について説明します。
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221212"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Windows Virtual Desktop のネットワーク接続について

Windows Virtual Desktop は、Azure で実行されているセッション ホスト上でクライアント セッションをホストする機能を提供します。 Microsoft は、お客様に代わってサービスの一部を管理し、クライアントおよびセッション ホストに接続するためのセキュリティで保護されたエンドポイントを提供します。 次の図は、Windows Virtual Desktop で使用されるネットワーク接続の概要を示しています。

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Windows Virtual Desktop のネットワーク接続の図" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>セッション接続

Windows Virtual Desktop では、リモート デスクトップ プロトコル (RDP) を使用して、ネットワーク接続を介したリモートでの表示と入力の機能を提供します。 RDP は、Windows NT 4.0 ターミナル サーバー エディションで最初にリリースされ、Microsoft Windows と Windows Server のすべてのリリースで継続的に進化していました。 RDP は最初から基盤となるトランスポート スタックから独立するように開発され、現在は、複数の種類のトランスポートをサポートしています。

## <a name="reverse-connect-transport"></a>リバース接続トランスポート

Windows Virtual Desktop では、リモート セッションを確立し、RDP トラフィックを伝送するために、リバース接続トランスポートを使用しています。 オンプレミスのリモート デスクトップ サービス デプロイとは異なり、リバース接続トランスポートでは、受信 RDP 接続の受信に TCP リスナーを使用しません。 代わりに、HTTPS 接続を介して Windows Virtual Desktop インフラストラクチャへの送信接続を使用しています。

## <a name="session-host-communication-channel"></a>セッション ホストの通信チャネル

Windows Virtual Desktop セッション ホストのスタートアップ時に、リモート デスクトップ エージェント ローダー サービスによって、Windows Virtual Desktop ブローカーの永続的な通信チャネルが確立されます。 この通信チャネルは、セキュリティで保護されたトランスポート層セキュリティ (TLS) 接続上に階層化され、セッション ホストと Windows Virtual Desktop インフラストラクチャの間でサービス メッセージ交換を行うためのバスとして機能します。

## <a name="client-connection-sequence"></a>クライアント接続シーケンス

次に、クライアント接続シーケンスについて説明します。

1. サポートされている Windows Virtual Desktop クライアント ユーザーを使用して Windows Virtual Desktop ワークスペースをサブスクライブする
2. Azure Active Directory は、ユーザーを認証し、ユーザーが使用できるリソースを列挙するために使用されるトークンを返す
3. クライアントが Windows Virtual Desktop フィード サブスクリプション サービスにトークンを渡す
4. Windows Virtual Desktop フィード サブスクリプション サービスがトークンを検証する
5. Windows Virtual Desktop フィード サブスクリプション サービスにより、使用可能なデスクトップと RemoteApp の一覧が、デジタル署名された接続構成の形式でクライアントに返される
6. クライアントは、使用可能な各リソースの接続構成を .rdp ファイルのセットに格納する
7. ユーザーが接続先リソースを選択すると、クライアントは、関連付けられた .rdp ファイルを使用し、最も近い Windows Virtual Desktop ゲートウェイ インスタンスへのセキュリティで保護された TLS 1.2 接続を確立し、接続情報を渡す
8. Windows Virtual Desktop ゲートウェイは、要求を検証し、Windows Virtual Desktop ブローカーに接続を調整するように依頼する
9. Windows Virtual Desktop ブローカーは、セッション ホストを識別し、以前に確立された永続的な通信チャネルを使用して、接続を初期化する
10. リモート デスクトップ スタックは、クライアントによって使用されているのと同じ Windows Virtual Desktop ゲートウェイ インスタンスへの TLS 1.2 接続を開始する
11. クライアントとセッションのホストの両方がゲートウェイに接続されると、ゲートウェイは両方のエンドポイント間の生データのリレーを開始します。これにより、RDP の基本のリバース接続トランスポートが確立される
12. ベース トランスポートが設定されると、クライアントは RDP ハンドシェイクを開始する

## <a name="connection-security"></a>接続のセキュリティ

TLS 1.2 が、クライアントおよびセッション ホストから Windows Virtual Desktop インフラストラクチャ コンポーネントに開始されるすべての接続に使用されます。 Windows Virtual Desktop では、[Azure Front Door](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door) と同じ TLS 1.2 暗号化を使用します。 クライアント コンピューターとセッション ホストの両方でこれらの暗号を使用できるようにすることが重要です。
リバース接続トランスポートの場合、クライアントとセッションの両方のホストが、Windows Virtual Desktop ゲートウェイに接続します。 TCP 接続を確立すると、クライアントまたはセッション ホストは、Windows Virtual Desktop ゲートウェイの証明書を検証します。
ベース トランスポートを確立すると、RDP は、セッション ホストの証明書を使用して、クライアントとセッション ホストの間に入れ子になった TLS 接続を確立します。 既定では、RDP 暗号化に使用される証明書は、デプロイ中に OS によって自己生成されます。 必要に応じて、顧客は企業の証明機関によって発行され、一元管理された証明書を展開できます。 証明書の構成の詳細については、[Windows Server ドキュメント](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)を参照してください。

## <a name="next-steps"></a>次のステップ

* Windows Virtual Desktop の帯域幅の要件の詳細については、[Windows Virtual Desktop のリモート デスクトップ プロトコル (RDP) 帯域幅の要件の概要](rdp-bandwidth.md)に関するトピックを参照してください。
* Windows Virtual Desktop のサービス品質 (QoS) の使用を開始するには、「[Windows Virtual Desktop のサービス品質 (QoS) を実装する](rdp-quality-of-service-qos.md)」を参照してください。
