---
title: Azure Data Box Heavy のセットアップ方法のチュートリアル | Microsoft Docs
description: ケーブルを配線して Azure Data Box Heavy を接続する方法を学習します
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 3e6bfe4a93ab8c97bcffb84bda08977f8d811fa8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592367"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>チュートリアル:Azure Data Box Heavy に配線して接続する


このチュートリアルでは、Azure Data Box Heavy のケーブル配線、接続、起動の方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box Heavy にケーブルを配線する
> * Data Box Heavy に接続する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関する](data-box-heavy-deploy-ordered.md)を完了していること。
2. Data Box Heavy の受け取りが済んでいて、ポータル内での注文の状態が **[配送済み]** であること。
3. [Data Box Heavy の安全性に関するガイドライン](data-box-safety.md)を確認していること。
4. データセンター内に、これだけの設置面積を占めるデバイスを収容可能な平らな場所があり、その近くに使用可能なネットワーク接続用設備があること。 このデバイスはラックにマウントできません。
5. ストレージ デバイスで使用するためのアース端子付き電源ケーブルを 4 本受け取っていること。
6. データセンター ネットワークに接続されているホスト コンピューターがあること。 Data Box Heavy はこのコンピューターからデータをコピーします。 お使いのホスト コンピューターで、[サポート対象のオペレーティング システム](data-box-heavy-system-requirements.md)が実行されていること。
7. お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 
8. RJ-45 ケーブルを使用してローカル UI に接続し、デバイスを構成するためのノート PC があること。 このノート PC を使用して、デバイスの各ノードで 1 度構成を行います。
9. デバイス ノードごとに 40 Gbps ケーブルまたは 10 Gbps ケーブルが 1 本あること。
    - [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) ネットワーク インターフェイスと互換性のあるケーブルを選択します。
    - 40 Gbps ケーブルの場合、ケーブルのデバイス側の端は QSFP+ である必要があります。
    - 10 Gbps ケーブルの場合、SFP+ ケーブルが必要です。ケーブルの一端を 10 Gbps スイッチに差し込み、QSFP+ から SFP+ へのアダプター (QSA アダプター) を使用して他端をデバイスに差し込みます。

## <a name="cable-your-device-for-power"></a>デバイスの電源ケーブルを接続する

次の手順に従って、デバイスにケーブルを配線します。

1. デバイスを調べて、開梱された形跡や、その他明らかな損傷がないか確認します。 開梱されている場合またはひどい損傷がある場合には、続行しないでください。 デバイスが正常に使用できる状態にあるかどうか、また交換してもらう必要があるかどうかを評価するために、すぐに [Microsoft サポートにお問い合わせください](data-box-disk-contact-microsoft-support.md)。
2. デバイスを設置場所に移動します。

    ![Data Box Heavy デバイスの設置場所](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. 以下に示すように、デイバスの後部のキャスターをロックします。

    ![Data Box Heavy デバイスのキャスターのロック](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. デバイスの前面と背面のドアのロックを解除するノブを見つけます。 前面のドアをロック解除し、デバイスの側面にぴったり重なるまで移動します。 背面のドアにも同じ動作を繰り返します。
    デバイスを稼働するときには両方のドアを開けたままにし、デバイスの前後に最適な空気の流れを確保する必要があります。

    ![Data Box Heavy のドアが開いた状態](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. デバイスの背面のトレイに 4 本の電源ケーブルがあります。 トレイからすべてのケーブルを取り出し、横に置きます。

    ![Data Box Heavy のトレー内の電源コード](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. 次の手順は、デバイスの背面にあるさまざまなポートの確認です。 **NODE1** と **NODE2** の 2 つのデバイス ノードがあります。 各ノードには、**MGMT**、**DATA1**、**DATA2**、**DATA3** の 4 つのネットワーク インターフェイスがあります。 **MGMT** は、デバイスの初期構成時に、管理を構成するために使用されます。 **DATA1**- から **DATA3** はデータ ポートです。 **MGMT** ポートと **DATA3** ポートは 1 Gbps、**DATA1** と **DATA2** は 40 Gbps ポートまたは 10 Gbps ポートとして機能させることができます。 2 つのデバイス ノードの底部は、2 つのデバイス ノード間で共有される 4 つの電源装置ユニット (PSU) です。 これらの **PSU** は、デバイスに向かって左から右に、**PSU1**、**PSU2**、**PSU3**、**PSU4** です。

    ![Data Box Heavy のポート](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. 4 つの電源ケーブルをすべてデバイスの電源装置に接続します。 緑色の LED が点灯し、点滅します。
8. 前面の電源ボタンを使用して、デバイス ノードの電源を入れます。 青色のライトが点灯するまで、電源ボタンを数秒間押したままにします。 これで、デバイスの背面にある電源装置の緑色の LED は、すべて途切れずに点灯します。 デバイスの前面の操作パネルにも障害 LED があります。 障害 LED が点灯している場合、PSU やファンの障害またはディスク ドライブの問題を示しています。  

    ![Data Box Heavy 前面の操作パネル](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>最初のノードをネットワークにケーブル接続する

デバイスのいずれかのノードで、次の手順に従ってネットワークにケーブル接続します。

1. CAT 6 RJ-45 ネットワーク ケーブル (図の青いケーブル) を使用して、ホスト コンピューターを 1 Gbps の管理ポートに接続します。
2. QSFP+ ケーブル (ファイバーまたは銅線) を使用して、40 Gbps 以上 (1 Gbps よりも望ましい) のデータ用ネットワーク インターフェイスを少なくとも 1 つ接続します。 10 Gbps スイッチを使用する場合は、SFP+ ケーブルと、QSFP+ から SFP+ へのアダプター (QSA アダプター) を使用して、データ用の 40 Gbps ネットワーク インターフェイスに接続します。

    ![ケーブル接続した Data Box Heavy のポート](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 と DATA2 は交換され、ローカル Web UI での表示とは一致しません。
    > 40 Gbps ケーブル アダプターは、以下に示すように挿入した場合に接続されます。

    ![Data Box Heavy の 40 Gbps ケーブル アダプター](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>最初のノードを構成する

次の手順に従って、ローカル構成と Azure portal を使用してデバイスを設定します。

1. ポータルからデバイスの資格情報をダウンロードします。 **[全般] > [デバイスの詳細]** に移動します。 **[デバイスのパスワード]** をコピーします。 これらのパスワードは、ポータルで特定の注文に関連付けられています。 Data Box Heavy の 2 つのノードに対応する 2 つのデバイス シリアル番号が表示されます。 両方のノードのデバイス管理者パスワードは同じです。

    ![Data Box Heavy デバイスの資格情報](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. CAT6 RJ-45 ネットワーク ケーブルを使用して、クライアント ワークステーションをデバイスに接続します。
3. 静的 IP アドレス `192.168.100.5` とサブネット `255.255.255.0` を使用して、デバイスに接続するために使用するコンピューターのイーサネット アダプターを構成します。

    ![Data Box Heavy をローカル Web UI に接続する](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. `http://192.168.100.10` という URL で、デバイスのローカル Web UI に接続します。 **[詳細設定]** をクリックし、 **[Proceed to 192.168.100.10 (unsafe)]** .(192.168.100.10 へ進む (安全でない)) をクリックします。
5. ローカル Web UI の **[サインイン]** ページが表示されます。
    
    - このページのいずれかのノード シリアル番号が、ポータル UI とローカル Web UI の両方で一致します。 そのノード番号とシリアル番号のマッピングを書き留めておきます。 ポータルには、2 つのノードと 2 つのデバイスのシリアル番号が表示されます。 このマッピングにより、シリアル番号に対応するノードがわかります。
    - デバイスはこの時点でロックされます。
    - 前の手順で取得したデバイス管理者パスワードを入力して、デバイスにサインインします。 **[サインイン]** をクリックします。

    ![Data Box Heavy ローカル Web UI にサインインする](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. ダッシュボードで、ネットワーク インターフェイスが構成されていることを確認します。 デバイス ノードには、4 つのネットワーク インターフェイス (1 Gbps が 2 つと 40 Gbps が 2 つ) があります。 1 Gbps インターフェイスの 1 つは管理インターフェイスであるため、ユーザーが構成することはできません。 残りの 3 つのネットワーク インターフェイスはデータ専用であり、ユーザーが構成できます。

- ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。
- DHCP が有効になっていない場合は、必要に応じて [ネットワーク インターフェイスの設定] に移動し、静的 IP を割り当てます。

    ![Data Box Heavy ダッシュボードのノード 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>2 つ目のノードを構成する

デバイスの 2 つ目のノードに対して、「[最初のノードを構成する](#configure-first-node)」で説明されている手順を実行します。

![Data Box Heavy ダッシュボードのノード 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

デバイスの設定が完了したら、デバイス共有に接続し、コンピューターからデバイスにデータをコピーすることができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Heavy に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Heavy にケーブルを配線する
> * Data Box Heavy に接続する

次のチュートリアルに進み、Data Box Heavy にデータをコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [データを Data Box にコピーする](./data-box-heavy-deploy-copy-data.md)
