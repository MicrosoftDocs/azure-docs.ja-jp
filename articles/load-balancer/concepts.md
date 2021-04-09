---
title: Azure Load Balancer の概念
description: Azure Load Balancer の概念の概要
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 3f8c288f950f34e1764c50e8eb74a8a73b39b3d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698531"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer のアルゴリズム

ロード バランサーは、UDP と TCP の両方のアプリケーションに対していくつかの機能を提供します。

## <a name="load-balancing-algorithm"></a>負荷分散アルゴリズム

負荷分散規則を作成して、フロントエンドのトラフィックをバックエンド プールに分散させることができます。 Azure Load Balancer は、ハッシュ アルゴリズムを使用してインバウンド フロー (バイトではなくフロー) の分散を行い、 バックエンド プール インスタンスへのフローのヘッダーを書き換えます。 バックエンド エンドポイントが正常であることを正常性プローブが示している場合、サーバーは新しいフローを受信できます。

既定では、Load Balancer は 5 タプル ハッシュを使用します。

ハッシュには次のものが含まれます。

- **ソース IP アドレス**
- **ソース ポート**
- **宛先 IP アドレス**
- **宛先ポート**
- **使用可能なサーバーにフローをマップするための IP プロトコル番号**

送信元 IP アドレスへのアフィニティは、2 タプル ハッシュまたは 3 タプル ハッシュを使用して作成されます。 同じフローのパケットは、負荷分散されたフロントエンドの背後にある同じインスタンスに到着します。

クライアントが同じ送信元 IP から新しいフローを開始するとき、送信元ポートが変化します。 その結果、5 タプルのハッシュによって、トラフィックは別のバックエンド エンドポイントに向かうことになります。
詳細については、「[Azure Load Balancer の分散モードを構成する](./load-balancer-distribution-mode.md)」を参照してください。

次の図は、ハッシュベースの分散を示しています。

![ハッシュベースの分散](./media/load-balancer-overview/load-balancer-distribution.png)

*図:ハッシュベースの分散*

## <a name="application-independence-and-transparency"></a>アプリケーションの独立と透過性

Load Balancer は、TCP、UDP、またはアプリケーション レイヤーと直接やり取りしません。 TCP または UDP アプリケーションのシナリオがサポートされています。 Load Balancer がフローを終了または開始したり、フローのペイロードと対話したりすることはなく、 アプリケーション レイヤーのゲートウェイ機能を提供することもありません。 プロトコル ハンドシェイクは、常にクライアントとバックエンド プール インスタンスの間で直接発生します。 受信フローへの応答は常に、仮想マシンからの応答です。 仮想マシンにフローが到着するときは、元のソース IP アドレスも保持されます。

- すべてのエンドポイントは、VM によって応答されます。 たとえば、TCP ハンドシェイクは、クライアントと選択されたバックエンド VM との間で行われます。 フロントエンドへの要求に対する応答は、バックエンドの VM によって生成される応答です。 フロントエンドへの接続を正常に検証するときは、少なくとも 1 つのバックエンド仮想マシンへの接続を全体にわたって検証していることになります。
- アプリケーション ペイロードはロード バランサーに対して透過的です。 任意の UDP または TCP アプリケーションをサポートできます。
- ロード バランサーは TCP ペイロードとやり取りせず、TLS オフロードを提供しているため、包括的な暗号化シナリオを構築できます。 ロード バランサーを使用すると、VM 自体の TLS 接続が終了されることで、TLS アプリケーションの大規模なスケールアウトを実現できます。 たとえば、TLS セッションのキー容量は、バックエンド プールに追加する VM の数と種類によってのみ制限されます。

## <a name="next-steps"></a>次のステップ

- Azure Load Balancer を構成する[コンポーネント](components.md)について確認します。
- Load Balancer の使用を始めるには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参考に Load Balancer を 1 つ作成し、カスタム IIS 拡張機能がインストールされている VM を作成して、Web アプリを VM 間で負荷分散します。
- [Azure Load Balancer のアウトバウンド接続](load-balancer-outbound-connections.md)について学習する。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)の詳細を確認する。