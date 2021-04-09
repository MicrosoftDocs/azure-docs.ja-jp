---
title: Azure Marketplace から Microsoft Azure Stack Edge Pro デバイスに GPU モジュールをデプロイする | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイスに GPU 対応 IoT モジュールをデプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e6a58a1f404228564e4e1ac6d77051a747debaa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438160"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>GPU 対応 IoT モジュールを Azure Marketplace から Microsoft Azure Stack Edge Pro GPU デバイスにデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、グラフィックス処理装置 (GPU) 対応の IoT Edge モジュールを Azure Marketplace から Microsoft Azure Stack Edge Pro デバイスにデプロイする方法について説明します。 

この記事では、次のことについて説明します。
  - GPU モジュールを実行するために Azure Stack Edge Pro を準備します。
  - GPU 対応 IoT モジュールを Azure Marketplace からダウンロードしてデプロイします。
  - モジュールの出力を監視します。

## <a name="about-sample-module"></a>サンプル モジュールについて

この記事の GPU サンプル モジュールには、CPU と GPU を比較する PyTorch および TensorFlow のベンチマーク サンプル コードが含まれています。

## <a name="prerequisites"></a>前提条件

開始する前に、次のものがあることを確認します。

- GPU 対応の 1 ノード Azure Stack Edge デバイスにアクセスできること。 このデバイスと Azure のリソースがアクティブになっていること。 
- このデバイスでコンピューティングを構成済みであること。 「[チュートリアル: Azure Stack Edge デバイスでコンピューティングを構成する](azure-stack-edge-gpu-deploy-configure-compute.md)」の手順に従います。
- Windows クライアント上の次の開発リソース:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)   


## <a name="get-module-from-azure-marketplace"></a>Azure Marketplace からモジュールを入手する

1. [Azure Marketplace のアプリ](https://azuremarketplace.microsoft.com/marketplace/apps)をすべて表示します。

    ![Azure Marketplace のアプリの表示](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. 「**Getting started with GPUs**」を検索します。

    ![GPU サンプル モジュールの検索](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. **[今すぐ入手する]** を選択します。

    ![サンプル モジュールの入手](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. **[続行]** を選択して、プロバイダーの使用条件とプライバシー ポリシーを確認します。 

    ![サンプル モジュールの入手 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Microsoft Azure Stack Edge Pro デバイスのデプロイに使用したサブスクリプションを選択します。

    ![サブスクリプションの選択](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Microsoft Azure Stack Edge Pro デバイスを構成するときに作成した IoT Hub サービスの名前を入力します。 この IoT Hub サービス名を確認するには、Azure portal で、デバイスに関連付けられている Azure Stack Edge リソースにアクセスします。 

    1. 左側のペインのメニュー オプションで、 **[Edge サービス]、[IoT Edge]** の順にアクセスします。 

        ![コンピューティング構成の表示](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. **[プロパティ]** に移動します。 

        1. Microsoft Azure Stack Edge Pro デバイスでコンピューティングを構成するときに作成された IoT Hub サービスをメモします。
        2. コンピューティングを構成するときに作成された IoT Edge デバイスの名前をメモします。 この名前は後の手順で使用します。

        ![Edge コンピューティングの構成](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. **[デバイスへのデプロイ]** を選択します。

11. IoT Edge デバイスの名前を入力するか、または **[デバイスの検索]** を選択してハブに登録されているデバイスを参照します。

    ![デバイスの検索](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. **[作成]** を選択して、必要な場合の他のモジュールの追加など、デプロイ マニフェストの構成の標準的な処理を続行します。 イメージの URI、作成オプション、必要なプロパティなどの新しいモジュールの詳細はあらかじめ定義されていますが、変更できます。

    ![作成の選択](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. モジュールが Azure portal の IoT Hub にデプロイされていることを確認します。 デバイスを選択し、 **[モジュールの設定]** を選択します。モジュールは **IoT Edge モジュール** セクションに表示されます。

    ![作成の選択 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>モジュールの監視  

1. VS Code コマンド パレットで、**[Azure IoT Hub: Select IoT Hub]\(Azure IoT Hub: IoT ハブの選択\)** を実行します。

2. 構成する IoT Edge デバイスが含まれているサブスクリプションと IoT ハブを選択します。 この場合、Azure Stack Edge Pro デバイスのデプロイに使用するサブスクリプションを選択し、Azure Stack Edge Pro デバイス用に作成された IoT Edge デバイスを選択します。 これは、前の手順で Azure portal からコンピューティングを構成した場合に発生します。

3. VS Code エクスプローラーで、[Azure IoT Hub] セクションを展開します。 **[デバイス]** に、Azure Stack Edge Pro デバイスに対応する IoT Edge デバイスが表示されます。 

    1. そのデバイスを選択して右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択します。
  
        ![監視の開始](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **[デバイス]、[モジュール]** の順にアクセスして、**GPU モジュール** が実行中であることを確認します。

    3. VS Code ターミナルでも Azure Stack Edge Pro デバイスの監視出力に IoT Hub イベントが含まれていることを確認します。

        ![監視出力](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        CPU よりも GPU の方が、同じ操作セット (5000 回の図形変換) の実行にかかった時間がずっと短いことがわかります。

## <a name="next-steps"></a>次の手順

- [モジュールを使用するために GPU を構成する](azure-stack-edge-j-series-configure-gpu-modules.md)方法を確認します。
