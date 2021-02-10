---
title: 'チュートリアル: Azure Stack Edge Pro GPU でコンピューティングを使用してデータのフィルター処理や分析を行う | Microsoft Docs'
description: Azure Stack Edge Pro GPU にコンピューティング ロールを構成し、それを使用して、Azure に送信する前にデータを変換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/07/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: eb71db05a61a0e32f3f092f37a4da72bc04e581d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525757"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>チュートリアル:Azure Stack Edge Pro GPU デバイスにコンピューティングを構成する

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

このチュートリアルでは、Azure Stack Edge Pro デバイスにコンピューティング ロールを構成し、Kubernetes クラスターを作成する方法について説明します。 

この手順の所要時間は約 20 分から 30 分です。


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コンピューティングを構成する
> * Kubernetes エンドポイントを取得する

 
## <a name="prerequisites"></a>前提条件

Azure Stack Edge Pro デバイスにコンピューティング ロールを設定する前に、次のことを確認してください。

- [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事の説明に従って、Azure Stack Edge Pro デバイスをアクティブ化していること。
- [コンピューティング ネットワークの有効化](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)に関するページの手順に従っていること。
    - コンピューティング用のネットワーク インターフェイスを有効にしていること。
    - Kubernetes ノード IP と Kubernetes 外部サービス IP を割り当て済みであること。

## <a name="configure-compute"></a>コンピューティングを構成する

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Kubernetes エンドポイントを取得する

Kubernetes クラスターにアクセスするようにクライアントを構成するには、Kubernetes エンドポイントが必要です。 次の手順に従って、Azure Stack Edge Pro デバイスのローカル UI から Kubernetes API エンドポイントを取得します。

1. デバイスのローカル Web UI で **[デバイス]** ページに移動します。
2. **[Device endpoints]\(デバイスのエンドポイント\)** で、 **[Kubernetes API service]\(Kubernetes API サービス\)** エンドポイントをコピーします。 このエンドポイントは `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` という形式の文字列です。 

    ![ローカル UI の [デバイス] ページ](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. エンドポイント文字列を保存します。 後でクライアントを構成し、kubectl 経由で Kubernetes クラスターにアクセスするときにこのエンドポイント文字列を使用します。

4. ローカル Web UI では、次のことができます。

    - Kubernetes API に移動し、 **[詳細設定]** を選択し、Kubernetes の詳細な構成ファイルをダウンロードします。 

        ![ローカル UI の [デバイス] ページ 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Microsoft からキーが提供されている場合 (一部のユーザーはキーを持っている場合があります)、この構成ファイルを使用できます。

        ![ローカル UI の [デバイス] ページ 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - **[Kubernetes ダッシュボード]** エンドポイントに移動して `aseuser` 構成ファイルをダウンロードすることもできます。 
    
        ![ローカル UI の [デバイス] ページ 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        この構成ファイルを使用して、Kubernetes ダッシュボードにサインインすることや、Kubernetes クラスターの問題をデバッグすることができます。 詳細については、[Kubernetes ダッシュボードへのアクセス](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)に関するページを参照してください。 


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * コンピューティングを構成する
> * Kubernetes エンドポイントを取得する


お使いの Azure Stack Edge Pro デバイスを管理する方法を確認するには、次を参照してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Stack Edge Pro を管理する](azure-stack-edge-manage-access-power-connectivity-mode.md)
