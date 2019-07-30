---
title: 複数の IP 構成での負荷分散 - Azure portal
titlesuffix: Azure Load Balancer
description: プライマリ IP 構成とセカンダリ IP 構成の間の負荷分散。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: dbf8cdd326d3e1c8f32f6dc2bd3486146993e06b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274730"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Azure Portal を使用した複数の IP 構成での負荷分散

> [!div class="op_single_selector"]
> * [ポータル](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


この記事では、1 つのセカンダリ ネットワーク インターフェイス コントローラー (NIC) に複数の IP アドレスがある Azure Load Balancer の使用方法について説明します。 次の図にこのシナリオを示します。

![ロード バランサーのシナリオ](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

このシナリオでは、次の構成を使用しています。

- Windows を実行している 2 つの仮想マシン (VM)。
- 各 VM には、プライマリ NIC とセカンダリ NIC が 1 つずつ存在します。
- セカンダリ NIC にはそれぞれ 2 つの IP 構成が割り当てられています。
- それぞれの VM は、contoso.com と fabrikam.com の 2 つの Web サイトをホストします。
- それぞれの Web サイトは、セカンダリ NIC の IP 構成の 1 つにバインドされています。
- Azure Load Balancer を使用して、それぞれの Web サイト用に 2 つのフロントエンド IP アドレスを公開します。 フロントエンド アドレスは、トラフィックを Web サイトのそれぞれの IP 構成に分散するのに使用します。
- フロントエンド IP アドレスとバックエンド プール IP アドレスの両方で同じポート番号が使用されます。

## <a name="prerequisites"></a>前提条件

このシナリオでは、次の構成の **contosofabrikam** という名前のリソース グループがあることを前提としています。

- リソース グループには **myVNet** という名前の仮想ネットワークが含まれています。
- **myVNet** ネットワークには、**VM1** と **VM2** という名前の 2 つの VM が含まれます。
- VM1 と VM2 は、**myAvailset** という名前の同じ可用性セット内に存在します。 
- VM1 と VM2 のプライマリ NIC の名前はそれぞれ **VM1NIC1** と **VM2NIC1** です。 
- セカンダリ NIC の名前はそれぞれ **VM1NIC2** と **VM2NIC2** です。

複数の NIC を持つ VM の作成の詳細については、「[PowerShell を使用して複数の NIC を持つ VM を作成する](../virtual-machines/windows/multiple-nics.md)」を参照してください。

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>複数の IP 構成で負荷分散を実行する

この記事で紹介するシナリオを実現するには、次の手順に従います。

### <a name="step-1-configure-the-secondary-nics"></a>手順 1: セカンダリ NIC を構成する

仮想ネットワーク内の各 VM に、次のようにセカンダリ NIC の IP 構成を追加します。  

1. Azure Portal にアクセスします: https://portal.azure.com Azure のアカウントを使用してサインインします。

2. 画面の左上にある **[リソース グループ]** を選択します。 次に、VM が配置されているリソース グループ (たとえば、**contosofabrikam**) を選択します。 すべてのリソースと VM の NIC の一覧が表示される **[リソース グループ]** ウィンドウが表示されます。

3. 各 VM のセカンダリ NIC に次のように IP 構成を追加します。

    1. 構成するセカンダリ NIC を選択します。
    
    2. **[IP 構成]** を選択します。 表示されたウィンドウの上部近くにある **[追加]** を選択します。

    3. **[Add IP configurations (IP 構成の追加)]** で、次のように NIC に 2 つ目の IP 構成を追加します。 

        1. セカンダリ IP 構成の名前を入力します。 (たとえば、VM1 と VM2 の IP 構成にそれぞれ **VM1NIC2-ipconfig2** と **VM2NIC2-ipconfig2** という名前を付けます)。

        2. **[プライベート IP アドレス]** の **[割り当て]** 設定で **[静的]** を選択します。

        3. **[OK]** を選択します。

セカンダリ NIC の 2 つ目の IP 構成が完了すると、該当する NIC の **[IP 構成]** 設定に表示されます。

### <a name="step-2-create-the-load-balancer"></a>手順 2: ロード バランサーを作成する

次のように構成のロード バランサーを作成します。

1. Azure Portal にアクセスします: https://portal.azure.com Azure のアカウントを使用してサインインします。

2. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[Load Balancer]** の順に選択します。 次に、 **[作成]** を選択します。

3. **[ロード バランサーの作成]** で、ロード バランサーの名前を入力します。 このシナリオでは、**mylb** という名前を使用します。

4. **[パブリック IP アドレス]** で、**PublicIP1** という名前の新しいパブリック IP を作成します。

5. **[リソース グループ]** で、VM の既存のリソース グループ (たとえば、**contosofabrikam**) を選択します。 ロード バランサーを展開する場所を選択してから、 **[OK]** を選択します。

ロード バランサーのデプロイが開始されます。 デプロイが正常に完了するまでに数分かかる場合があります。 デプロイが完了すると、ロード バランサーがリソース グループ内のリソースとして表示されます。

### <a name="step-3-configure-the-front-end-ip-pool"></a>手順 3:フロントエンド IP プールを構成する

次のように各 Web サイト (contoso.com と fabrikam.com) で、ロード バランサーのフロントエンド IP プールを構成します。

1. ポータルで **[その他のサービス]** を選択します。 フィルター ボックスに「**パブリック IP アドレス**」と入力して、 **[パブリック IP アドレス]** をクリックします。 表示されたウィンドウの上部近くにある **[追加]** を選択します。

2. 次のように両方の Web サイト (contoso.com と fabrikam.com) の 2 つのパブリック IP アドレス (**PublicIP1** と **PublicIP2**) を構成します。

   1. フロントエンド IP アドレスの名前を入力します。

   2. **[リソース グループ]** で、VM の既存のリソース グループ (たとえば、**contosofabrikam**) を選択します。

   3. **[場所]** で VM と同じ場所を選択します。

   4. **[OK]** を選択します。

      パブリック IP アドレスが作成されると、 **[パブリック IP アドレス]** に表示されます。

3. <a name="step3-3"></a>ポータルで **[その他のサービス]** を選択します。 フィルター ボックスに「**ロード バランサー**」と入力して、 **[Load Balancer]** を選択します。 

4. フロントエンド IP プールを追加するロード バランサー (**mylb**) を選択します。

5. **[設定]** で、 **[フロントエンド IP の構成]** を選択します。 表示されたウィンドウの上部近くにある **[追加]** を選択します。

6. フロントエンド IP アドレスの名前 (たとえば、**contosofe** または **fabrikamfe**) を入力します。

7. <a name="step3-7"></a> **[IP アドレス]** を選択します。 **[パブリック IP アドレスの選択]** で、フロントエンドの IP アドレス (**PublicIP1** または **PublicIP2**) を選択します。

8. 2 つ目のフロントエンド IP アドレスを作成するには、このセクションの<a href="#step3-3">手順 3</a> ～ <a href="#step3-7">7</a> を繰り返します。

フロントエンド プールの構成が完了すると、IP アドレスがロード バランサーの **[フロントエンド IP の構成]** 設定に表示されます。 
    
### <a name="step-4-configure-the-back-end-pool"></a>手順 4:バックエンド プールを構成する

次のように各 Web サイト (contoso.com と fabrikam.com) で、ロード バランサーのバックエンド アドレス プールを構成します。
        
1. ポータルで **[その他のサービス]** を選択します。 フィルター ボックスに「**ロード バランサー**」と入力して、 **[Load Balancer]** を選択します。

2. バックエンド プールを追加するロード バランサー (**mylb**) を選択します。

3. **[設定]** で、 **[バックエンド プール]** を選択します。 バックエンド プールの名前 (たとえば、**contosopool** や **fabrikampool**) を入力します。 表示されたウィンドウの上部近くにある **[追加]** を選択します。 

4. **[関連付け先]** で **[可用性セット]** を選択します。

5. **[可用性セット]** で **[myAvailset]** を選択します。

6. 次のように両方の VM のターゲット ネットワーク IP 構成を追加します。 

    ![ロード バランサー用にバックエンド プールを構成する](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. **[ターゲット仮想マシン]** で、バックエンド プールに追加する VM (たとえば、**VM1** や **VM2**) を選択します。

    2. **[Network IP configuration (ネットワーク IP 構成)]** で、前の手順で選択した VM のセカンダリ NIC の IP 構成 (たとえば、**VM1NIC2-ipconfig2** や **VM2NIC2-ipconfig2**) を選択します。

7. **[OK]** を選択します。

バックエンド プールの構成が完了すると、アドレスがロード バランサーの **[バックエンド プール]** 設定に表示されます。

### <a name="step-5-configure-the-health-probe"></a>手順 5:正常性プローブを構成する

次のようにロード バランサーの正常性プローブを構成します。

1. ポータルで **[その他のサービス]** を選択します。 フィルター ボックスに「**ロード バランサー**」と入力して、 **[Load Balancer]** を選択します。

2. 正常性プローブを追加するロード バランサー (**mylb**) を選択します。

3. **[設定]** で、 **[正常性プローブ]** を選択します。 表示されたウィンドウの上部近くにある **[追加]** を選択します。 

4. 正常性プローブの名前 (たとえば、**HTTP**) を入力します。 **[OK]** を選択します。

### <a name="step-6-configure-load-balancing-rules"></a>手順 6:負荷分散規則の構成

次のように各 Web サイト (contoso.com と fabrikam.com) で、負荷分散規則を構成します。
    
1. <a name="step6-1"></a> **[設定]** で、 **[負荷分散規則]** を選択します。 表示されたウィンドウの上部近くにある **[追加]** を選択します。 

2. **[名前]** に、負荷分散規則の名前 (たとえば、contoso.com には **HTTPc**、fabrikam.com には **HTTPf**) を入力します。

3. **[フロントエンド IP アドレス]** で、以前に作成したフロントエンド IP アドレス (たとえば、**contosofe** や **fabrikamfe**) を選択します。

4. **[ポート]** と **[バックエンド ポート]** については、既定値の **80** のままにします。

5. **[フローティング IP (ダイレクト サーバー リターン)]** で、 **[無効]** を選択します。

6. <a name="step6-6"></a> **[OK]** を選択します。

7. 2 つ目の負荷分散規則を作成するには、このセクションの<a href="#step6-1">手順 1</a> ～ <a href="#step6-6">6</a> を繰り返します。

規則の構成が完了すると、規則がロード バランサーの **[負荷分散規則]** 設定に表示されます。

### <a name="step-7-configure-dns-records"></a>手順 7:DNS レコードを構成する

最後に、Load Balancer の各フロントエンド IP アドレスを指すように DNS リソース レコードを構成します。 ドメインを Azure DNS でホストできます。 Azure DNS を Load Balancer で使用する方法の詳細については、「[Azure DNS を他の Azure サービスで使用する](../dns/dns-for-azure-services.md)」を参照してください。

## <a name="next-steps"></a>次の手順
- Azure で負荷分散サービスを組み合わせて使う方法について詳しくは、「[Azure で負荷分散サービスを使用する](../traffic-manager/traffic-manager-load-balancing-azure.md)」をご覧ください。
- 各種ログを使って、ロード バランサーの管理やトラブルシューティングを行う方法については、[Azure Load Balancer の Azure Monitor ログ](../load-balancer/load-balancer-monitor-log.md)に関するページを参照してください。
