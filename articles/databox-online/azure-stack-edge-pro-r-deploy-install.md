---
title: 'チュートリアル: Azure Stack Edge Pro R 物理デバイスを設置する | Microsoft Docs'
description: Azure Stack Edge Pro R の設置に関する 2 番目のチュートリアルでは、物理デバイスの電源とネットワークをケーブル接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464596"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>チュートリアル:Azure Stack Edge Pro R を設置する

このチュートリアルでは、Azure Stack Edge Pro R 物理デバイスを設置する方法を説明します。 設置手順には、デバイスのケーブル接続が含まれます。

設置を完了するまでに 30 分程度かかる場合があります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイスを点検する
> * デバイスをケーブル接続する

## <a name="prerequisites"></a>前提条件

物理デバイスをインストールするための前提条件は以下のとおりです。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

開始する前に次の点を確認します。

* [Azure Stack Edge Pro R の配置の準備](azure-stack-edge-pro-r-deploy-prep.md)に関するページの手順をすべて完了していること。
    * デバイスを配置する Azure Stack Edge リソースを作成済みであること。
    * Azure Stack Edge リソースを使用して、デバイスをアクティブ化するためのアクティブ化キーを生成済みであること。

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Azure Stack Edge Pro R 物理デバイスの前提条件

デバイスを配置する前に次の点を確認します。

- デバイスが平らで安定した水平の安全な作業台に置かれていることを確認します。
- セットアップを行う予定の現場に以下のものがあることを確認します。
    - 独立した電源からの標準 AC 電源

        \- または -
    - ラック電力分配装置 (PDU)。 デバイスには無停電電源装置 (UPS) が付属しています。
    

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合

作業を開始する前に、次のことを行います。

- Azure Stack Edge Pro R を配置するためのネットワーク要件を確認し、その要件に従ってデータセンター ネットワークを構成します。 詳細については、[Azure Stack Edge Pro R のネットワーク要件](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements)に関するセクションを参照してください。

- デバイスが最適に動作するように、インターネットの最小帯域幅が 20 Mbps であることを確認します。


## <a name="inspect-the-device"></a>デバイスを点検する

このデバイスは、単一ユニットとして出荷されます。 以下のステップを完了して、デバイスを開梱します。

1. 平坦で水平な場所に箱を置きます。
2. デバイスのケースに損傷がないかどうかを点検します。 ケースを開けて、デバイスを点検します。 ケースまたはデバイスが破損していると思われる場合は、Microsoft サポートに問い合わせて、デバイスが正常に使用できる状態にあるかどうかを評価してください。
3. ケースを開けたら、次のことを確認してください。
    - Azure Stack Edge Pro R デバイス筐体 1 台
    - 無停電電源装置 (UPS) 1 台
    - デバイスを UPS に接続するための短い電源ケーブル 2 本
    - UPS を電源に接続するための電源ケーブル 1 本

上記の項目の中で足りないものがある場合は、Azure Stack Edge Pro R サポートに連絡してください。 次の手順では、ご利用のデバイスをケーブル接続します。


## <a name="cable-the-device"></a>デバイスをケーブル接続する

以降の手順では、ご利用の Azure Stack Edge Pro R デバイスの電源およびネットワーク用ケーブルを接続する方法を説明します。

デバイスのケーブル接続を開始する前に、次の前提条件を満たす必要があります。

- 設置場所の Azure Stack Edge Pro R 物理デバイス。
- 電源ケーブル 1 本。
- 管理インターフェイスに接続するための 1-GbE RJ-45 ネットワーク ケーブルを 1 本以上。 デバイスには、管理用とデータ用に 1 つずつ、2 つの 1-GbE ネットワーク インターフェイスがあります。
- 構成するデータ ネットワーク インターフェイスのそれぞれに対して、10 または 25 GbE SFP+ 銅線ケーブル 1 本。 少なくとも 1 つのデータ ネットワーク インターフェイス (ポート 3 からポート 4) がインターネットに接続されている必要があります (Azure への接続が可能なもの)。  
- 1 台の電力分配装置へのアクセス (推奨)。

> [!NOTE]
> - データ ネットワーク インターフェイスを 1 つだけ接続する場合は、Azure へのデータ送信には、ポート 3、ポート 4 などの 25 または 10 GbE ネットワーク インターフェイスを使用することをお勧めします。 
> - 最善のパフォーマンスを実現し、大量のデータを処理するには、すべてのデータ ポートを接続することを検討してください。
> - データ ソース サーバーからデータを取り込むことができるように、Azure Stack Edge Pro R デバイスをデータセンター ネットワークに接続する必要があります。

Azure Stack Edge Pro R デバイスについて:

- フロント パネルには、ディスク ドライブと電源ボタンがあります。

    - デバイスの前面には 8 個のディスク スロットがあります。
    - また、デバイスには、オペレーティング システム ディスクとして機能する 2 台の M.2 SATA ディスクが搭載されています。 

- バック プレーンには、冗長電源ユニット (PSU) が搭載されています。
- バック プレーンには、次の 4 つのネットワーク インターフェイスがあります。

    - 1 Gbps インターフェイス (2 つ)。
    - 10 Gbps インターフェイスとしても使える 25 Gbps インターフェイス (2 つ)。
    - ベースボード管理コントローラー (BMC)

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
以下の手順に従って、デバイスの電源とネットワークのケーブルを接続します。

1. お使いのデバイスのバックプレーンにあるさまざまなポートを確認してください。

    ![ケーブル接続されたデバイスのバックプレーン](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. デバイス前面にあるディスク スロットと電源ボタンを確認します。

    ![デバイスのフロント プレーン](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. 電源コードの一方の端を UPS に接続します。 電源コードのもう一方の端をラック電力分配装置 (PDU) に接続します。 
5. 電源ボタンを押してデバイスを有効にします。
6. 1 GbE ネットワーク インターフェイスのポート 1 を、物理デバイスの構成に使用するコンピューターに接続します。 ポート 1 は管理専用のインターフェイスです。
7. ポート 2、ポート 3、またはポート 4 のうちの 1 つ以上をデータセンター ネットワークまたはインターネットに接続します。

    - ポート 2 を接続する場合は、RJ-45 ネットワーク ケーブルを使用します。
    - 10/25 GbE ネットワーク インターフェイスの場合は、SFP+ 銅線ケーブルを使用します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge Pro R で次のことを行う方法に関するトピックについて説明しました。

> [!div class="checklist"]
> * デバイスを開梱する
> * デバイスをケーブル接続する

次のチュートリアルに進み、デバイスに接続する方法について学習してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R に接続する](./azure-stack-edge-pro-r-deploy-connect.md)
