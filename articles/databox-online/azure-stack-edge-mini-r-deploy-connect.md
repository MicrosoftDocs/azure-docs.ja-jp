---
title: 'チュートリアル: Azure portal で Azure Stack Edge Mini R に接続する'
description: ローカル Web UI を使用してご利用の Azure Stack Edge Mini R デバイスに接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464996"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>チュートリアル:Azure Stack Edge Mini R に接続する

このチュートリアルでは、ローカル Web UI を使用してご利用の Azure Stack Edge Mini R デバイスに接続する方法について説明します。

この接続プロセスの所要時間は約 5 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスに接続する



## <a name="prerequisites"></a>前提条件

Azure Stack Edge デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge の設置](azure-stack-edge-mini-r-deploy-install.md)に関するページで詳細に説明されているように、物理デバイスが設置されていること。


## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する

1. Azure Stack Edge Pro デバイスに接続するために、お使いのコンピューター上のイーサネット アダプターで静的 IP アドレス 192.168.100.5 とサブネット 255.255.255.0 を構成します。

2. デバイスでコンピューターをポート 1 に接続します。 デバイスにコンピューターを (スイッチなしで) 直接接続する場合、クロスオーバー ケーブルか USB イーサネット アダプターを使用します。 次の図を使用して、デバイス上のポート 1 を識別してください。

    ![Wi-Fi 用のケーブル接続](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスに接続する


ご利用のデバイスでネットワーク設定を構成する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [ネットワークを構成する](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
