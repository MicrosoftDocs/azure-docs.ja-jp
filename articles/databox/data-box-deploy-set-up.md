---
title: Azure Data Box のセットアップ方法のチュートリアル | Microsoft Docs
description: ケーブルを配線して Azure Data Box を接続する方法を学習する
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 528be96f95523089c78562814c292fb0f40849e1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70240238"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>チュートリアル:ケーブルを配線して Azure Data Box に接続する

::: zone-end

::: zone target="chromeless"

# <a name="cable-and-connect-to-your-device"></a>ケーブルを配線してデバイスに接続する

::: zone-end

::: zone target="docs"

このチュートリアルでは、Azure Data Box のケーブル配線、接続、起動の方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box にケーブルを配線する
> * Data Box に接続する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [チュートリアル:Azure Data Box を注文する](data-box-deploy-ordered.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータルで注文の状態が **[配信済み]** に更新されていること。 
    - 現在のラベルの下にデバイスに貼付されているクリア ポーチに、配送先住所ラベルがあります。 このラベルは返品用に使用するため、安全に保管してください。
    - ヨーロッパの一部のリージョンについては、デバイスが箱に梱包されて発送されます。 デバイスを開梱し、箱は返送用に必ず保管しておいてください。
3. [Data Box の安全性に関するガイドライン](data-box-safety.md)を確認していること。
4. 100 TB ストレージ デバイスで使用するためのアース端子付き電源ケーブルを 1 本受け取っていること。
5. Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 
6. Data Box を設置できる平らな場所があること。 デバイスを標準的なラック棚に設置しようとする場合は、データ センター ラックに 7U のスロットが必要です。 デバイスは平面に置くことも、ラックに立てて置くこともできます。
7. Data Box をホスト コンピューターに接続するために以下のケーブルを用意していること。
    - 1 本以上の 10 GbE SFP+ Twinax 銅線ケーブルまたは SFP+ 光ファイバー ケーブル (DATA 1、DATA 2 ネットワーク インターフェイスで使用)。 Data Box には、PCI Express 3.0 ネットワーク インターフェイスを備えた Mellanox ConnectX®-3 Pro EN Dual-Port 10GBASE-T アダプターが搭載されています。そのため、このインターフェイスと互換性のあるケーブルであれば正常に機能します。 たとえば社内テストでは、CISCO SFP-H10GB-CU3M 10GBASE-CU TWINMAX SFP +3M ケーブルが使用されています。 詳しくは、[Mellanox 社提供のサポートされているケーブルおよびスイッチの一覧](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf)をご覧ください。
    - RJ-45 CAT 6 ネットワーク ケーブル 1 本 (MGMT ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

## <a name="cable-your-device"></a>デバイスにケーブルを配線する

デバイスにケーブルを配線するには、次のステップを実行します。

1. デバイスを調べて、開梱された形跡や、その他明らかな損傷がないか確認します。 開梱されている場合またはひどい損傷がある場合には、続行しないでください。 すぐに Microsoft サポートに問い合わせて、デバイスが正常に使用できる状態にあるかどうか、また交換ディスクを発送してもらう必要があるかどうかを評価してください。
2. 電源と接続する場所にデバイスを運びます。 平らな場所にデバイスを置きます。 デバイスは標準のラック棚に設置することもできます。
3. 電源ケーブルとネットワーク ケーブルを接続します。 一般的な構成用に接続したデバイスのバックプレーンを以下に示します。 環境に応じて、他の[オプション](data-box-cable-options.md)から選択できます。
    
    ![ケーブル配線した Data Box デバイスのバックプレーン](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. ラベルで表示された電源入力端子に電源ケーブルを接続します。 電源ケーブルのもう一方の端は、電力配分装置に接続してください。
    2. RJ-45 CAT 6 ケーブルを使用して、一方の端を MGMT ポート、もう一方の端をノート PC に接続します。            
    3. RJ-45 CAT 6A ケーブルを使用し、一方の端を DATA 3 ポートに接続します。 DATA 3 は、RJ-45 CAT 6A ケーブルで接続すると 10 GbE として、RJ-45 CAT 6 ケーブルで接続すると 1 GbE として構成されます。
    4. データ転送用に接続するネットワーク インターフェイスに応じて、最大 2 本の 10 GbE SFP+ Twinax 銅線ケーブルまたは SFP+ 光ファイバー ケーブルを使用して、それぞれ DATA 1 ポートまたは DATA 2 ポートを接続します。 
    5. データ ポートからのケーブルのもう一方の端を、10 GbE スイッチ経由でホスト コンピューターに接続します。

4. デバイス前面の操作パネルの電源ボタンを確認します。 デバイスの電源を入れます。

    ![Data Box の電源ボタン](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

デバイスを受け取ったら、デバイスにケーブルを配線してデバイスに接続する必要があります。 

## <a name="cable-your-device"></a>デバイスにケーブルを配線する

1. デバイスが改ざんされたり破損していたりする形跡がある場合、作業を続行しないでください。 交換用のデバイスを発送するよう Microsoft サポートに問い合わせてください。
2. デバイスのケーブルを接続する前に、次のケーブルがあることを確認します。
    
    - (付属品) デバイス接続用の IEC60320 C-13 コネクタが付いた定格 10A 以上のアース付き電源コード。
    - RJ-45 CAT 6 ネットワーク ケーブル 1 本 (MGMT ネットワーク インターフェイスで使用)
    - 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (10 Gbps DATA 1、DATA 2 ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

3. デバイスを取り外して平らな場所に置きます。 
    
4. 次に示すようにしてデバイスのケーブルを接続します。  

    ![ケーブル配線した Data Box デバイスのバックプレーン](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 電源ケーブルをデバイスに接続します。
    2. RJ-45 CAT 6 ネットワーク ケーブルを使用して、ホスト コンピューターをデバイス上の管理ポート (MGMT) に接続します。 
    3. SFP+ Twinax 銅線ケーブルを使用して、10 Gbps 以上 (1 Gbps よりも望ましい) のデータ用 DATA 1 または DATA 2 ネットワーク インターフェイスを少なくとも 1 つ接続します。 
    4. デバイスの電源を入れます。 電源ボタンはデバイスのフロント パネルにあります。

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>デバイスに接続する

次のステップを実行して、ローカル Web UI とポータル UI を使用してデバイスを設定します。

1. デバイスに接続するために使用するノート PC のイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。 
2. デバイスの MGMT ポートに接続し、https\://192.168.100.10 のローカル Web UI にアクセスします。 デバイスを起動してから最大 5 分かかることがあります。
3. **[詳細]** をクリックしてから **[Web ページへ移動]** をクリックします。

   ![ローカル Web UI への接続](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. ローカル Web UI の **[サインイン]** ページが表示されます。 デバイスのシリアル番号が、ポータル UI とローカル Web UI で一致することを確認します。 デバイスはこの時点でロックされます。
5. [Azure Portal](https://portal.azure.com) にサインインします。
6. ポータルからデバイスの資格情報をダウンロードします。 **[全般] > [デバイスの詳細]** に移動します。 **[デバイスのパスワード]** をコピーします。 デバイスのパスワードは、ポータルで特定の注文に関連付けられています。 

    ![デバイスの資格情報の取得](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 前の手順で Azure portal から取得したデバイスのパスワードを指定して、デバイスのローカル Web UI にサインインします。 **[サインイン]** をクリックします。
8. **[ダッシュボード]** で、ネットワーク インターフェイスが構成されていることを確認します。 
   - 環境で DHCP が有効になっている場合、ネットワーク インターフェイスは自動的に構成されます。 
   - DHCP が有効ではない場合、必要であれば **[ネットワーク インターフェイスの設定]** に移動し静的な IP を割り当てます。

     ![デバイス ダッシュボード](media/data-box-deploy-set-up/data-box-dashboard-1.png)

データ ネットワーク インターフェイスが構成されると、DATA 1 から DATA 3 までのインターフェイスのいずれかの IP アドレスを使用して、ローカルの Web UI (`https://<IP address of a data network interface>`) にアクセスすることもできます。 

デバイスの設定が完了したら、デバイス共有に接続し、コンピューターからデバイスにデータをコピーすることができます。 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>ご利用のデバイスを接続する

1. デバイスのパスワードを取得するには、[Azure Portal](https://portal.azure.com) で **[全般] > [デバイスの詳細]** に移動します。
2. Data Box に接続するために使用するコンピューターのイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。 `https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。 デバイスを起動してから接続するまで最大 5 分かかることがあります。 
3. Azure portal からパスワードを使用してサインインします。 Web サイトのセキュリティ証明書に問題があることを示すエラーが表示されます。 ブラウザー固有の手順に従い Web ページに進みます。
4. 既定では、10 Gbps のデータ インターフェイス(または 1 Gbps) のネットワーク設定は、DHCP として構成されます。 必要に応じて、このインターフェイスを静的インターフェイスとして構成し、IP アドレスを入力することができます。 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box にケーブルを配線する
> * Data Box に接続する

次のチュートリアルに進み、Data Box にデータをコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [データを Data Box にコピーする](./data-box-deploy-copy-data.md)

::: zone-end

