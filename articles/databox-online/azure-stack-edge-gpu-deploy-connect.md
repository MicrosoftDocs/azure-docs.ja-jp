---
title: 'チュートリアル: Azure portal で GPU 搭載 Azure Stack Edge デバイスに接続し、それを構成およびアクティブ化する | Microsoft Docs'
description: Azure Stack Edge GPU を配置するチュートリアルでは、お使いの物理デバイスを接続し、設定し、アクティブにする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 713f9eeef87cbfe3d8cb3d9717ad703328b54fe3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262721"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>チュートリアル:GPU 搭載の Azure Stack Edge に接続する

このチュートリアルでは、ローカル Web UI を使用して、オンボード GPU 搭載の Azure Stack Edge デバイスに接続する方法について説明します。

この接続プロセスの所要時間は約 5 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスに接続する


## <a name="prerequisites"></a>前提条件

GPU 搭載の Azure Stack Edge デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge の設置](azure-stack-edge-gpu-deploy-install.md)に関するページで詳細に説明されているように、物理デバイスが設置されていること。


## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する

1. Azure Stack Edge デバイスに接続するために、お使いのコンピューター上のイーサネット アダプターで静的 IP アドレス 192.168.100.5 とサブネット 255.255.255.0 を構成します。

2. デバイスでコンピューターをポート 1 に接続します。 次の図を使用して、デバイス上のポート 1 を識別してください。

    ![ケーブル接続されたデバイスのバックプレーン](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    デバイスのバックプレーンは、どのモデルを受け取ったかによって若干異なる場合があります。


3. ブラウザー ウィンドウを開き、`https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。  
    このアクションは、そのデバイスに電源を入れてから数分かかることがあります。

    Web サイトのセキュリティ証明書に問題があることを示すエラーまたは警告が表示されます。 
   
    ![Web サイトのセキュリティ証明書のエラー メッセージ](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. **[この Web ページの閲覧を続ける]** を選択します。  
    これらの手順は、使用しているブラウザーによって異なることがあります。

5. ご利用のデバイスの Web UI にサインインします。 既定のパスワードは *Password1* です。 
   
    ![Azure Stack Edge デバイスのサインイン ページ](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. プロンプトで、デバイス管理者のパスワードを変更します。  
    新しいパスワードは 8 から 16 文字にする必要があります。 さらに、大文字、小文字、数字、および特殊文字のうちの 3 種類の文字を含める必要があります。

これでデバイスの **[概要]** ページが表示されます。 次の手順は、デバイスのネットワーク設定を構成することです。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスに接続する


Azure Stack Edge デバイスでネットワーク設定を構成する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [ネットワークを構成する](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
