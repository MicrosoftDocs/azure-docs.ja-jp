---
title: 'チュートリアル: Azure portal で Azure Stack Edge Pro R デバイスに接続し、それを構成してアクティブにする | Microsoft Docs'
description: ローカル Web UI を使用してご利用の Azure Stack Edge Pro R デバイスに接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464609"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>チュートリアル:Azure Stack Edge Pro R に接続する

このチュートリアルでは、ローカル Web UI を使用してご利用の Azure Stack Edge Pro R デバイスに接続する方法について説明します。

この接続プロセスの所要時間は約 5 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスに接続する


## <a name="prerequisites"></a>前提条件

ご利用の Azure Stack Edge Pro R デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge Pro R の設置](azure-stack-edge-pro-r-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。


## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する

1. Azure Stack Edge Pro R デバイスに接続するために、ご利用のコンピューター上のイーサネット アダプターで静的 IP アドレス 192.168.100.5 とサブネット 255.255.255.0 を構成します。

2. デバイスでコンピューターをポート 1 に接続します。 デバイスにコンピューターを (スイッチなしで) 直接接続する場合、クロスオーバー ケーブルか USB イーサネット アダプターを使用します。 次の図を使用して、デバイス上のポート 1 を識別してください。

    ![ケーブル接続されたデバイスのバックプレーン](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. ブラウザー ウィンドウを開き、`https://192.168.100.10` からデバイスのローカル Web UI にアクセスします。  
    このアクションは、そのデバイスに電源を入れてから数分かかることがあります。

    Web サイトのセキュリティ証明書に問題があることを示すエラーまたは警告が表示されます。 
   
    ![Web サイトのセキュリティ証明書のエラー メッセージ](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. **[この Web ページの閲覧を続ける]** を選択します。  
    これらの手順は、使用しているブラウザーによって異なることがあります。

5. ご利用のデバイスの Web UI にサインインします。 既定のパスワードは *Password1* です。 
   
    ![Azure Stack Edge デバイスのサインイン ページ](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. プロンプトで、デバイス管理者のパスワードを変更します。  
    新しいパスワードは 8 から 16 文字にする必要があります。 さらに、大文字、小文字、数字、および特殊文字のうちの 3 種類の文字を含める必要があります。

これでデバイスの **[概要]** ページが表示されます。 次の手順は、デバイスのネットワーク設定を構成することです。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスに接続する


ご利用の Azure Stack Edge Pro R デバイスでネットワーク設定を構成する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [ネットワークを構成する](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
