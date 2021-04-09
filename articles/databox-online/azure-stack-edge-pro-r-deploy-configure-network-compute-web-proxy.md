---
title: 'チュートリアル: Azure portal で Azure Stack Edge Pro R デバイスのネットワーク設定を構成する | Microsoft Docs'
description: Azure Stack Edge Pro R を配置するチュートリアルでは、ご利用の物理デバイスのネットワーク、コンピューティング ネットワーク、および Web プロキシ設定を構成する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 11c1d76b5784587f234455f81595778897569eb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594352"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>チュートリアル:Azure Stack Edge Pro R のネットワークを構成する

このチュートリアルでは、ローカル Web UI を使用してご利用の Azure Stack Edge Pro R デバイスのネットワークを構成する方法について説明します。

この接続プロセスの所要時間は約 20 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * ネットワークを構成する
> * コンピューティング ネットワークを有効にする
> * Web プロキシを構成する


## <a name="prerequisites"></a>前提条件

ご利用の Azure Stack Edge Pro R デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge Pro R の設置](azure-stack-edge-gpu-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。
* [Azure Stack Edge Pro R への接続](azure-stack-edge-gpu-deploy-connect.md)に関する記事の説明に従って、デバイスのローカル Web UI に接続していること


## <a name="configure-network"></a>ネットワークを構成する

**[開始]** ページには、物理デバイスを構成して Azure Stack Edge サービスに登録するために必要なさまざまな設定が表示されます。 

デバイスのネットワークを構成するには、次の手順を実行します。

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。 

2. **[ネットワーク]** タイルで、 **[構成]** を選択して **[ネットワーク]** ページに移動します。 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    ご利用の物理デバイスには 4 つのネットワーク インターフェイスがあります。 ポート 1 とポート 2 は、1 Gbps ネットワーク インターフェイスです。 ポート 3 とポート 4 は、どちらも 10 または 25 Gbps ネットワーク インターフェイスです。 ポート 1 は管理専用ポートとして自動的に構成され、ポート 2 からポート 4 はすべてデータ ポートです。 **[ネットワーク]** ページを次に示します。
    
    ![ローカル Web UI の [ネットワーク設定] ページ](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. ネットワーク設定を変更するには、ポートを選択し、表示される右側のペインで IP アドレス、サブネット、ゲートウェイ、プライマリ DNS、およびセカンダリ DNS を変更します。 

    - ポート 1 を選択すると、静的として事前に構成されていることがわかります。 

        ![ローカル Web UI のポート 1 の [ネットワーク設定]](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - [ポート 2]、[ポート 3]、または [ポート 4] を選択した場合、これらのポートはすべて既定で DHCP と構成されます。

        ![ローカル Web UI のポート 3 の [ネットワーク設定]](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    ネットワーク設定を構成するときは、次のことに注意してください。

   * ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
   * DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
   * 使用するネットワーク インターフェイスは、IPv4 として構成できます。
   * ネットワーク インターフェイス カード (NIC) のチーミングまたはリンク アグリゲーションは、Azure Stack Edge ではサポートされていません。
   * ポートのシリアル番号は、ノードのシリアル番号に対応しています。
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    デバイス ネットワークが構成されると、ページは以下のように更新されます。

    ![ローカル Web UI の [ネットワーク設定] ページ 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * デバイスに接続する別の IP アドレスがない限り、ネットワーク インターフェイスのローカル IP アドレスを静的から DHCP に切り替えないことをお勧めします。 あるネットワーク インターフェイスを使用していて、DHCP に切り替えた場合、DHCP アドレスを判別する方法がありません。 DHCP アドレスに変更する場合は、デバイスがサービスでアクティブになるまで待機してから変更してください。 その後、サービスについて Azure portal の **[デバイスのプロパティ]** にすべてのアダプターの IP が表示されます。


    ネットワーク設定を構成して適用したら、コンピューティング ネットワークを構成するために **[Next: Compute]\(次へ: コンピューティング\)** を選択します。

## <a name="enable-compute-network"></a>コンピューティング ネットワークを有効にする

次の手順に従って、コンピューティングを有効にし、コンピューティング ネットワークを構成します。 

1. **[コンピューティング]** ページで、コンピューティングを有効にするネットワーク インターフェイスを選択します。 

    ![ローカル UI の [コンピューティング] ページ 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. **[ネットワーク設定]** ダイアログで、 **[有効にする]** を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイス上のデバイスに仮想スイッチが作成されます。 仮想スイッチは、デバイスのコンピューティング インフラストラクチャに使用されます。 
    
1. **Kubernetes ノードの IP** を割り当てます。 これらの静的 IP アドレスは、コンピューティング VM 用です。  

    *n* ノード デバイスの場合、開始 IP アドレスと終了 IP アドレスを使用して、少なくとも *n+1* 個 (またはそれ以上) の IPv4 アドレスの連続した範囲がコンピューティング VM に提供されます。 Azure Stack Edge が 1 ノード デバイスである場合、少なくとも 2 つの連続する IPv4 アドレスが提供されます。 これらの IP アドレスは、コンピューティングが有効で仮想スイッチが作成されている同じネットワーク内にある必要があります。

    > [!IMPORTANT]
    > Azure Stack Edge の Kubernetes では、172.27.0.0/16 サブネットがポッドに使用され、172.28.0.0/16 サブネットがサービスに使用されています。 これらがご利用のネットワークで使用されていないことを確認してください。 これらのサブネットがご利用のネットワークで既に使用されている場合は、デバイスの PowerShell インターフェイスから `Set-HcsKubeClusterNetworkInfo` コマンドレットを実行することで、これらのサブネットを変更できます。 詳細については、「[Kubernetes ポッドとサービス サブネットの変更](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)」を参照してください。


1. **Kubernetes 外部サービス IP** を割り当てます。 これらは、負荷分散 IP アドレスでもあります。 これらの連続した IP アドレスは、Kubernetes クラスターの外部に公開するサービス用であり、公開するサービスの数に応じて静的 IP 範囲を指定します。 
    
    > [!IMPORTANT]
    > Azure Stack Edge Pro R Hub サービスからコンピューティング モジュールにアクセスするには、最低 1 つの IP アドレスを指定することを強くお勧めします。 必要に応じて、クラスターの外部からアクセスする必要がある他のサービスまたは IoT Edge モジュールに追加の IP アドレス (サービスやモジュールごとに 1 つ) を指定することもできます。 サービス IP アドレスは後で更新できます。 
    
1. **[適用]** を選択します。

    ![ローカル UI の [コンピューティング] ページ 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. 構成の適用には数分かかり、ブラウザーの更新が必要になる場合があります。 指定したポートでコンピューティングが有効になっていることを確認できます。 
 
    ![ローカル UI の [コンピューティング] ページ 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    **[次へ: Web プロキシ]** を選択して、Web プロキシを構成します。  

  
## <a name="configure-web-proxy"></a>Web プロキシを構成する

これはオプション構成です。

> [!IMPORTANT]
> * ご利用の Azure Stack Edge Pro R デバイスでコンピューティングを有効にして IoT Edge モジュールを使用する場合は、Web プロキシ認証を **[なし]** に設定しておくことをお勧めします。 NTLM はサポートされていません。
>* プロキシ自動構成 (PAC) ファイルはサポートされていません。 PAC ファイルは、Web ブラウザーやその他のユーザー エージェントが、特定の URL をフェッチするための適切なプロキシ サーバー (アクセス方法) を自動的に選択する方法を定義します。 プロキシの証明書は信頼されていないため、すべてのトラフィックをインターセプトして読み取る (その後、独自の証明書を使用してすべてに再署名する) プロキシは互換性がありません。 通常、透過プロキシは、Azure Stack Edge Pro R で適切に動作します。非透過の Web プロキシはサポートされていません。


1. **[Web プロキシの設定]** ページで、次の手順を実行します。

    1. **[Web プロキシ URL]**  ボックスに、`http://host-IP address or FQDN:Port number` という形式の URL を入力します。 HTTPS URL はサポートされていません。

    2. **[認証]** で、 **[なし]** または **[NTLM]** を選択します。 ご利用の Azure Stack Edge Pro R デバイスでコンピューティングを有効にして IoT Edge モジュールを使用する場合は、Web プロキシ認証を **[なし]** に設定しておくことをお勧めします。 **NTLM** はサポートされていません。

    3. 認証を使用している場合は、ユーザー名とパスワードを入力します。

    4. 構成された Web プロキシ設定を検証して適用するには、 **[適用]** を選択します。
    
   ![ローカル Web UI の [Web プロキシ設定] ページ 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. 設定が適用されたら、 **[次へ: デバイス]** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * ネットワークを構成する
> * コンピューティング ネットワークを有効にする
> * Web プロキシを構成する


ご利用の Azure Stack Edge Pro R デバイスを設定する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [デバイス設定を構成する](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
