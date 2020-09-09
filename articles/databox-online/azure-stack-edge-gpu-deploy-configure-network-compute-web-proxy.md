---
title: 'チュートリアル: Azure portal で GPU 搭載の Azure Stack Edge デバイスのネットワーク設定を構成する | Microsoft Docs'
description: Azure Stack Edge GPU を配置するチュートリアルでは、物理デバイスのネットワーク、コンピューティング ネットワーク、および Web プロキシ設定を構成する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 6e7dbc2b96a53d220554e07228a5e30857d12d9c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262976"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>チュートリアル:GPU 搭載の Azure Stack Edge 用にネットワークを構成する

このチュートリアルでは、ローカル Web UI を使用して、オンボード GPU 搭載の Azure Stack Edge デバイス用にネットワークを構成する方法について説明します。

この接続プロセスの所要時間は約 20 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * ネットワークを構成する
> * コンピューティング ネットワークを有効にする
> * Web プロキシの


## <a name="prerequisites"></a>前提条件

GPU 搭載の Azure Stack Edge デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge の設置](azure-stack-edge-gpu-deploy-install.md)に関するページで詳細に説明されているように、物理デバイスが設置されていること。
* [Azure Stack Edge への接続](azure-stack-edge-gpu-deploy-connect.md)に関するページの説明に従ってデバイスのローカル Web UI に接続していること。


## <a name="configure-network"></a>ネットワークを構成する

**[開始]** ページには、物理デバイスを構成して Azure Stack Edge サービスに登録するために必要なさまざまな設定が表示されます。 

デバイスのネットワークを構成するには、次の手順を実行します。

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。 

2. **[ネットワーク]** タイルで **[構成]** を選択します。  
    
    ![ローカル Web UI の [ネットワーク設定] タイル](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    物理デバイスには 6 つのネットワーク インターフェイスがあります。 ポート 1 とポート 2 は、1 Gbps ネットワーク インターフェイスです。 ポート 3、ポート 4、ポート 5、およびポート 6 はすべて、10 Gbps ネットワーク インターフェイスとしても機能できる 25 Gbps ネットワーク インターフェイスです。 ポート 1 は管理専用ポートとして自動的に構成され、ポート 2 からポート 6 はすべてデータ ポートです。 新しいデバイスの場合、 **[ネットワーク設定]** ページは次のようになります。
    
    ![ローカル Web UI の [ネットワーク設定] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. ネットワーク設定を変更するには、ポートを選択し、表示される右側のペインで IP アドレス、サブネット、ゲートウェイ、プライマリ DNS、およびセカンダリ DNS を変更します。 

    - ポート 1 を選択すると、静的として事前に構成されていることがわかります。 

        ![ローカル Web UI のポート 1 の [ネットワーク設定]](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - [ポート 2]、[ポート 3]、[ポート 4]、または [ポート 5] を選択した場合、これらのポートはすべて既定で DHCP と構成されます。

        ![ローカル Web UI のポート 3 の [ネットワーク設定]](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    ネットワーク設定を構成するときは、次のことに注意してください。

   * ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
   * DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
   * 使用するネットワーク インターフェイスは、IPv4 として構成できます。
   * 25 Gbps インターフェイスでは、RDMA (Remote Direct Access Memory) モードを iWarp または RoCE (RDMA over Converged Ethernet) に設定できます。 低待機時間が主な要件であり、スケーラビリティが問題にならない場合は、RoCE を使用します。 待機時間が重要な要件であり、使いやすさとスケーラビリティも優先度が高い場合は、iWARP が最適な候補です。
   * ポートのシリアル番号は、ノードのシリアル番号に対応しています。

    デバイス ネットワークが構成されると、ページは以下のように更新されます。

    ![ローカル Web UI の [ネットワーク設定] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * デバイスに接続する別の IP アドレスがない限り、ネットワーク インターフェイスのローカル IP アドレスを静的から DHCP に切り替えないことをお勧めします。 あるネットワーク インターフェイスを使用していて、DHCP に切り替えた場合、DHCP アドレスを判別する方法がありません。 DHCP アドレスに変更する場合は、デバイスがサービスでアクティブになるまで待機してから変更してください。 その後、サービスについて Azure portal の **[デバイスのプロパティ]** にすべてのアダプターの IP が表示されます。


    ネットワーク設定を構成して適用したら、 **[開始]** に戻ります。

## <a name="enable-compute-network"></a>コンピューティング ネットワークを有効にする

次の手順に従って、コンピューティングを有効にし、コンピューティング ネットワークを構成します。

1. デバイスのローカル Web UI で **[開始]** ページに移動します。 **[ネットワーク]** タイルで、 **[コンピューティング ネットワーク]** を選択します。  

    ![ローカル UI の [コンピューティング] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. **[コンピューティング]** ページで、コンピューティングを有効にするネットワーク インターフェイスを選択します。 

    ![ローカル UI の [コンピューティング] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. **[ネットワーク設定]** ダイアログで、 **[有効にする]** を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイス上のデバイスに仮想スイッチが作成されます。 仮想スイッチは、デバイスのコンピューティング インフラストラクチャに使用されます。 
    
3. **Kubernetes ノードの IP** を割り当てます。 これらの静的 IP アドレスは、コンピューティング VM 用です。 

    *n* ノード デバイスの場合、開始 IP アドレスと終了 IP アドレスを使用して、少なくとも *n+1* 個 (またはそれ以上) の IPv4 アドレスの連続した範囲がコンピューティング VM に提供されます。 Azure Stack Edge が 1 ノード デバイスである場合、少なくとも 2 つの連続する IPv4 アドレスが提供されます。 

4. **Kubernetes 外部サービス IP** を割り当てます。 これらは、負荷分散 IP アドレスでもあります。 これらの連続した IP アドレスは、Kubernetes クラスターの外部に公開するサービス用であり、公開するサービスの数に応じて静的 IP 範囲を指定します。 
    
    > [!IMPORTANT]
    > Azure Stack Edge Hub サービスからコンピューティング モジュールにアクセスするには、最低 1 つの IP アドレスを指定することを強くお勧めします。 必要に応じて、クラスターの外部からアクセスする必要がある他のサービスまたは IoT Edge モジュールに追加の IP アドレス (サービスやモジュールごとに 1 つ) を指定することもできます。 サービス IP アドレスは後で更新できます。 
    
5. **[適用]** を選択します。

    ![ローカル UI の [コンピューティング] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Web プロキシの

これはオプション構成です。

> [!IMPORTANT]
> * Azure Stack Edge デバイス上でコンピューティングを有効にして IoT Edge モジュールを使用する場合には、Web プロキシ認証を **[なし]** に設定しておくことをお勧めします。 NTLM はサポートされていません。
>* プロキシ自動構成 (PAC) ファイルはサポートされていません。 PAC ファイルは、Web ブラウザーやその他のユーザー エージェントが、特定の URL をフェッチするための適切なプロキシ サーバー (アクセス方法) を自動的に選択する方法を定義します。 プロキシの証明書は信頼されていないため、すべてのトラフィックをインターセプトして読み取る (その後、独自の証明書を使用してすべてに再署名する) プロキシは互換性がありません。 通常、透過プロキシは、Azure Stack Edge で適切に動作します。 非透過の Web プロキシはサポートされていません。

1. デバイスのローカル Web UI で **[開始]** ページに移動します。
2. **[ネットワーク]** タイルで、Web プロキシ サーバーの設定を構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、このページでのみ構成できます。

   ![ローカル Web UI の [Web proxy settings] (Web プロキシ設定) ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. **[Web プロキシの設定]** ページで、次の手順を実行します。

    1. **[Web プロキシ URL]**  ボックスに、`http://host-IP address or FQDN:Port number` という形式の URL を入力します。 HTTPS URL はサポートされていません。

    2. **[認証]** で、 **[なし]** または **[NTLM]** を選択します。 Azure Stack Edge デバイス上でコンピューティングを有効にして IoT Edge モジュールを使用する場合には、Web プロキシ認証を **[なし]** に設定しておくことをお勧めします。 **NTLM** はサポートされていません。

    3. 認証を使用している場合は、ユーザー名とパスワードを入力します。

    4. 構成された Web プロキシ設定を検証して適用するには、 **[適用]** を選択します。
    
   ![ローカル Web UI の [Web proxy settings] (Web プロキシ設定) ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. 設定を適用した後、 **[開始]** に戻ります。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * ネットワークを構成する
> * コンピューティング ネットワークを有効にする
> * Web プロキシの


Azure Stack Edge デバイスを設定する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [デバイス設定を構成する](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
