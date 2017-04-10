---
title: "Azure Load Balancer の複数 VIP | Microsoft Docs"
description: "Azure Load Balancer の複数 VIP の概要"
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: 
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2016
ms.author: chkuhtz
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: d9e88b859020be2a96a57a01e5624052ed134b64
ms.lasthandoff: 03/21/2017

---

# <a name="multiple-vips-for-azure-load-balancer"></a>Azure Load Balancer の複数 VIP

Azure Load Balancer は、複数のポート、複数の IP アドレス、またはその両方のサービスの負荷を分散できます。 パブリックおよび内部ロード バランサーの定義を使用して、一連の VM のフローの負荷を分散できます。

この記事では、この機能の基礎と重要な概念、制約について説明します。 1 つの IP アドレスにのみサービスを公開する場合は、[パブリック](load-balancer-get-started-internet-portal.md)または[内部](load-balancer-get-started-ilb-arm-portal.md)ロード バランサーの構成に関する簡略化された手順が用意されています。 複数 VIP の追加は、1 つの VIP 構成に対する増分です。 この記事の概念を使用することで、簡略化された構成をいつでも展開できます。

Azure Load Balancer を定義するとき、フロントエンドおよびバックエンドの構成は規則に接続されています。 規則によって参照される正常性プローブは、新しいフローをバックエンド プールのノードに送信する方法を決定します。 フロントエンドは仮想 IP (VIP) によって定義されます。VIP は、IP アドレス (パブリックまたは内部)、トランスポート プロトコル (UDP または TCP)、およびポート番号の 3 組で構成されます。 DIP は、バックエンド プール内の VM に接続されている Azure の仮想 NIC の IP アドレスです。

次の表に、フロントエンド構成の例をいくつか示します。

| VIP | IP アドレス | protocol | ポート |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

この表は、4 つの異なるフロントエンドを示します。 フロントエンド #1、#2 および #3 は、複数の規則を持つ 1 つの VIP です。 同じ IP アドレスが使用されますが、各フロントエンドでポートやプロトコルは異なります。 フロントエンド #1 および #4 は、複数の VIP で同じフロントエンド プロトコルとポートが再利用されている複数 VIP の例です。

Azure Load Balancer は、負荷分散規則を定義する際に柔軟性を提供します。 規則は、フロントエンドのアドレスとポートをバックエンドの送信先アドレスとポートにマッピングする方法を宣言します。 バックエンドのポートが規則間で再利用されるかどうかは、規則のタイプによって変わります。 各規則のタイプには、ホストの構成とプローブの設計に影響を与える可能性がある特定の要件があります。 次の 2 つのタイプの規則があります。

1. バックエンドのポートを再利用しない既定の規則
2. バックエンドのポートが再利用される Floating IP 規則

Azure Load Balancer を使用すると、同じロード バランサーの構成で両方のタイプの規則を混在させることができます。 規則の制約に従っている限り、ロード バランサーは指定の VM に対してそれらを同時または任意の組み合わせで使用できます。 選択する規則タイプは、アプリケーションの要件やその構成をサポートするための複雑さによって変わります。 該当するシナリオに対してどのタイプの規則が最適であるか評価してください。

これらのシナリオについてさらに詳しく見ていきましょう。まずは既定の動作から始めます。

## <a name="rule-type-1-no-backend-port-reuse"></a>規則タイプ #1: バックエンドのポートを再利用しない

![複数 VIP の図](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

このシナリオでは、フロントエンドの VIP が次のように構成されています。

| VIP | IP アドレス | protocol | ポート |
| --- | --- | --- | --- |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP は受信フローの宛先です。 バックエンド プールで、各 VM は DIP の一意のポートで目的のサービスを公開します。 このサービスは、規則の定義を通じてフロントエンドに関連付けられます。

次の 2 つのルールを定義します。

| ルール | フロントエンドのマッピング | バックエンド プールへ |
| --- | --- | --- |
| 1 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 |![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80、 ![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 |![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81、 ![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Azure Load Balancer の完全なマッピングは次のようになりました。

| ルール | VIP の IP アドレス | protocol | ポート | 変換先 | ポート |
| --- | --- | --- | --- | --- | --- |
| ![ルール](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP の IP アドレス |80 |
| ![ルール](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP の IP アドレス |81 |

各規則は、宛先 IP アドレスと宛先ポートの一意の組み合わせでフローを生成する必要があります。 フローの宛先ポートを変えることで、複数の規則が同じ DIP の異なるポートに対してフローを配信できます。

正常性プローブは、常に VM の DIP に送られます。 プローブが VM の正常性を反映していることを確認する必要があります。

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>規則タイプ #2: Floating IP を使用してバックエンド ポートを再利用する

Azure Load Balancer は、使用する規則タイプに関係なく、フロントエンドのポートを複数の VIP で再利用する柔軟性を提供します。 さらに、一部のアプリケーション シナリオでは、バックエンド プール内の 1 つの VM で複数のアプリケーション インスタンスによって同じポートが使用されることを選択するまたは使用されることが求められる場合があります。 ポートを再利用する一般的な例としては、高可用性のためにクラスタリングする、ネットワークの仮想アプライアンス、再暗号化なしに複数の TLS エンドポイントを公開する場合などが挙げられます。

複数の規則でバックエンド ポートを再利用するには、規則の定義で Floating IP を有効にする必要があります。

Floating IP は、Direct Server Return (DSR) と呼ばれるものの一部です。 DSR は、フロー トポロジーと IP アドレスのマッピング スキームの 2 つの部分で構成されます。 プラットフォーム レベルでは、Azure Load Balancer は Floating IP が有効かどうかに関係なく、常に DSR フロー トポロジで動作します。 これは、フローの送信部分は常に起点に直接フローするように正しく書き換えられることを意味します。

既定の規則タイプを使用することで、Azure は従来の負荷分散 IP アドレスのマッピング スキームを、簡単に使用できるように公開します。 Floating IP を有効にすると、以下に示すように、IP アドレスのマッピング スキームを追加の柔軟性をもたらすように変更します。

次の図はこの構成を示します。

![複数 VIP の図](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

このシナリオでは、バックエンド プール内のすべての VM に次の 3 つのネットワーク インターフェイスがあります。

* DIP: VM に関連付けられている仮想 NIC (Azure の NIC リソースの IP 構成)
* VIP1: VIP1 の IP アドレスで構成されているゲスト OS 内のループバック インターフェイス
* VIP2: VIP2 の IP アドレスで構成されているゲスト OS 内のループバック インターフェイス

> [!IMPORTANT]
> 論理インターフェイスの構成については、ゲスト OS 内で実行されます。 この構成は Azure で実行および管理されていません。 この構成なしでは、規則は機能しません。 正常性プローブの定義は、論理 VIP ではなく VM の DIP を使用します。 したがって、お使いのサービスでは、論理 VIP で提供されているサービスの状態を反映する、DIP ポートのプローブ応答を提供する必要があります。

前のシナリオと同じフロントエンド構成であると仮定します。

| VIP | IP アドレス | protocol | ポート |
| --- | --- | --- | --- |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

次の 2 つのルールを定義します。

| ルール | フロントエンドのマッピング | バックエンド プールへ |
| --- | --- | --- |
| 1 |![ルール](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 |![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (VM1 と VM2 で) |
| 2 |![ルール](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 |![バックエンド](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (VM1 と VM2 で) |

次の表は、ロード バランサーの完全なマッピングを示します。

| ルール | VIP の IP アドレス | protocol | ポート | 変換先 | ポート |
| --- | --- | --- | --- | --- | --- |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |VIP と同じ (65.52.0.1) |VIP と同じ (80) |
| ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |VIP と同じ (65.52.0.2) |VIP と同じ (80) |

受信フローの宛先は、VM のループバック インターフェイスの VIP アドレスです。 各規則は、宛先 IP アドレスと宛先ポートの一意の組み合わせでフローを生成する必要があります。 フローの宛先 IP アドレスを変えることで、同じ VM でポートを再利用できます。 お使いのサービスは、ロード バランサーを VIP の IP アドレスと対応するループバック インターフェイスのポートにバインドすることによって、ロード バランサーに公開されます。

この例では、宛先ポートが変わらないことに注意してください。 これは Floating IP のシナリオですが、Azure Load Balancer はバックエンドの宛先ポートを書き換える規則の定義もサポートし、フロントエンドの宛先ポートとは異なる規則にします。

Floating IP 規則タイプは、いくつかのロード バランサーの構成パターンの基盤になります。 現在利用できる例の 1 つは、 [複数リスナーによる SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) の構成です。 他のシナリオについては、徐々に文書化します。

## <a name="limitations"></a>制限事項

* 複数 VIP の構成は、IaaS VM でのみサポートされます。
* Floating IP 規則では、お使いアプリケーションは送信フローに DIP を使用する必要があります。 お使いのアプリケーションがゲスト OS のループバック インターフェイスに構成されている VIP アドレスにバインドされると、SNAT が送信フローを書き換えることができないため、フローが失敗します。
* パブリック IP アドレスは課金に影響します。 詳細については、「 [IP アドレスの価格](https://azure.microsoft.com/pricing/details/ip-addresses/)
* サブスクリプションの制限が適用されます。 詳細については、「 [サービスの制限](../azure-subscription-service-limits.md#networking-limits) 」を参照してください。

