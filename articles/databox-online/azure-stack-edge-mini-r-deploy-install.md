---
title: 'チュートリアル: Azure Stack Edge Mini R 物理デバイスを設置する | Microsoft Docs'
description: Azure Stack Edge Mini R の設置に関する 2 番目のチュートリアルでは、物理デバイスの電源とネットワークをケーブル接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464976"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>チュートリアル:Azure Stack Edge Mini R を設置する

このチュートリアルでは、Azure Stack Edge Mini R 物理デバイスを設置する方法を説明します。 設置手順には、デバイスのケーブル接続が含まれます。

設置を完了するまでに 30 分程度かかる場合があります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイスを点検する
> * デバイスをケーブル接続する

## <a name="prerequisites"></a>前提条件

物理デバイスをインストールするための前提条件は以下のとおりです。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

開始する前に次の点を確認します。

* [Azure Stack Edge Mini R の配置の準備](azure-stack-edge-mini-r-deploy-prep.md)に関するページの手順をすべて完了していること。
    * デバイスを配置する Azure Stack Edge リソースを作成済みであること。
    * Azure Stack Edge リソースを使用して、デバイスをアクティブ化するためのアクティブ化キーを生成済みであること。

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Azure Stack Edge Mini R 物理デバイスの前提条件

デバイスを配置する前に次の点を確認します。

- デバイスが平らで安定した水平の安全な作業台に置かれていることを確認します。
- セットアップを行う予定の現場に以下のものがあることを確認します。
    - 独立した電源からの標準 AC 電源、または
    - ラック電力分配装置 (PDU)。 
    

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合

作業を開始する前に、次のことを行います。

- Azure Stack Edge Mini R を配置するためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成します。 詳細については、[Azure Stack Edge のネットワーク要件](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements)に関する記事を参照してください。

- デバイスが最適に動作するように、インターネットの最小帯域幅が 20 Mbps であることを確認します。 <!-- engg TBC -->


## <a name="inspect-the-device"></a>デバイスを点検する

このデバイスは、単一ユニットとして出荷されます。 以下のステップを完了して、デバイスを開梱します。

1. 平坦で水平な場所に箱を置きます。
2. デバイスのケースに損傷がないかどうかを点検します。 ケースを開けて、デバイスを点検します。 ケースまたはデバイスが破損していると思われる場合は、Microsoft サポートに問い合わせて、デバイスが正常に使用できる状態にあるかどうかを評価してください。
3. ケースを開けたら、次のことを確認してください。
    - サイド バンパーが取り付けられたポータブル Azure Stack Edge Mini R デバイス 1 台
    - バッテリ 1 個、デバイスに取り付けられた背面カバー。 
    - バッテリを電源に接続するための電源コード 1 本 

上記の項目の中で足りないものがある場合は、Azure Stack Edge サポートに連絡してください。 次の手順では、ご利用のデバイスをケーブル接続します。


## <a name="cable-the-device"></a>デバイスをケーブル接続する

以降の手順では、Azure Stack Edge デバイスの電源およびネットワーク用ケーブルを接続する方法を説明します。

デバイスのケーブル接続を開始する前に、次の前提条件を満たす必要があります。

- 設置場所の Azure Stack Edge Mini R 物理デバイス。
- 電源ケーブル 1 本。
- 管理インターフェイスに接続するための 1-GbE RJ-45 ネットワーク ケーブルを 1 本以上。 デバイスには、管理用とデータ用に 1 つずつ、2 つの 1-GbE ネットワーク インターフェイスがあります。
- 構成するデータ ネットワーク インターフェイスのそれぞれに対して、10-GbE SFP+ 銅線ケーブル 1 本。 少なくとも 1 つのデータ ネットワーク インターフェイス (ポート 3 からポート 4) がインターネットに接続されている必要があります (Azure への接続が可能なもの)。  
- 1 台の電力分配装置へのアクセス (推奨)。

> [!NOTE]
> - データ ネットワーク インターフェイスを 1 つだけ接続する場合は、Azure へのデータ送信には、ポート 3、ポート 4 などの 10 GbE ネットワーク インターフェイスを使用することをお勧めします。 
> - 最善のパフォーマンスを実現し、大量のデータを処理するには、すべてのデータ ポートを接続することを検討してください。
> - Azure Stack Edge デバイスは、データ ソース サーバーからデータを取り込むことができるように、データセンター ネットワークに接続する必要があります。 <!-- engg TBC -->

Azure Stack Edge デバイスの仕様は以下のとおりです。

- フロント パネルに SSD キャリアが装備されています。 

    - デバイスのスロットに SSD ディスクが 1 台搭載されています。 
    - デバイスには、オペレーティング システム ディスクのストレージとして機能する CFx カードも装備されています。
    
- フロント パネルに、ネットワーク インターフェイスと Wi-Fi へのアクセスが装備されています。

    - 1 GbE RJ 45 ネットワーク インターフェイス x 2。 これらは、デバイスのローカル UI のポート 1 とポート 2 です。
    - 10 GbE SFP+ ネットワーク インターフェイス x 2。 これらは、デバイスのローカル UI のポート 3 とポート 4 です。 
    - Wi-Fi トランシーバーが接続された Wi-Fi ポート 1 基。

- フロント パネルには電源ボタンもあります。 

- 背面パネルには、デバイスに取り付けられるバッテリとカバーが含まれています。 


以下の手順に従って、デバイスの電源とネットワークのケーブルを接続します。

1. ご利用のデバイスのフロント プレーンにある各種のネットワークおよびストレージ コンポーネントを確認します。

    ![デバイス上のネットワーク インターフェイスとストレージ インターフェイス](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. デバイスの前面の左下隅にある電源ボタンを確認します。 

    ![デバイスの電源ボタンがある、デバイスのフロント プレーン](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. バッテリはデバイスのバック プレーンに接続されています。 バッテリ上の 2 番目の電源ボタンを確認します。 

    ![バッテリの電源ボタンがある、デバイスのフロント プレーン](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    電源コードの一方の端をバッテリに接続し、もう一方の端を電源コンセントに接続します。 

    ![電源コードの接続](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    バッテリのみで運用する場合 (バッテリが電源に接続されていない場合)、前面の電源スイッチとバッテリのスイッチの両方をオンの位置に切り替える必要があります。 バッテリが電源に接続されている場合は、デバイスの前面にある電源ボタンのみをオンの位置に切り替える必要があります。 

4. フロント プレーンの電源ボタンを押して、デバイスの電源を入れます。 
    
    > [!NOTE]
    > デバイスの電源をオンまたはオフにするには、電源ボタンの上にある黒いボタンを押してから、電源ボタンをオンまたはオフの位置に切り替える必要があります。 

5. このデバイスで Wi-Fi を構成する場合は、次のケーブル接続図を使用してください。

    ![Wi-Fi 用のケーブル接続](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - 1 GbE ネットワーク インターフェイスのポート 1 を、物理デバイスの構成に使用するコンピューターに接続します。 ポート 1 は管理専用のインターフェイスです。


    このデバイスにワイヤード (有線) 構成を使用する場合は、次の図を使用してください。
     
    ![ワイヤード (有線) のケーブル配線](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - 1 GbE ネットワーク インターフェイスのポート 1 を、物理デバイスの構成に使用するコンピューターに接続します。 ポート 1 は管理専用のインターフェイスです。
    - ポート 2、ポート 3、またはポート 4 のうちの 1 つ以上をデータセンター ネットワークまたはインターネットに接続します。
    
        - ポート 2 を接続する場合は、RJ-45 ネットワーク ケーブルを使用します。
        - 10-GbE ネットワーク インターフェイスの場合は、SFP+ 銅線ケーブルを使用します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge で次のことを行う方法に関するトピックについて説明しました。

> [!div class="checklist"]
> * デバイスを開梱する
> * デバイスをケーブル接続する

次のチュートリアルに進み、デバイスの接続、設定、およびアクティブ化を行う方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge を接続して設定する](./azure-stack-edge-mini-r-deploy-connect.md)
