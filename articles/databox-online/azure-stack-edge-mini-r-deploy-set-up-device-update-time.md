---
title: 'チュートリアル: Azure portal で Azure Stack Edge Mini R デバイスのデバイス、更新、時間を設定する'
description: Azure Stack Edge Mini R を配置するチュートリアルでは、ご利用の物理デバイスのデバイス、更新、および時間設定を設定する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: e9f4d4f4ad27081bb105cdb1698438837fc2fe02
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546671"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>チュートリアル:Azure Stack Edge Mini R のデバイス設定を構成する

このチュートリアルでは、ご利用のオンボード GPU 搭載の Azure Stack Edge Mini R デバイス用のデバイス関連の設定を構成する方法について説明します。 ローカル Web UI を使用して、デバイス名、更新サーバー、タイム サーバーを設定できます。

デバイスの設定に要する時間は約 5 から 7 分です。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * デバイス設定を構成する
> * 更新プログラムを構成する 
> * 時刻の構成

## <a name="prerequisites"></a>前提条件

ご利用の GPU 搭載の Azure Stack Edge Mini R デバイスでデバイス関連の設定を構成する前に、次のことを確認してください。

* 物理デバイスについて:

    - [Azure Stack Edge Mini R の設置](azure-stack-edge-mini-r-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。
    - デバイスでネットワークを構成し、コンピューティング ネットワークを有効にして構成してある (詳細については、「[チュートリアル: GPU 搭載の Azure Stack Edge Mini R のネットワークを構成する](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)方法のチュートリアルを参照)。


## <a name="configure-device-settings"></a>デバイス設定を構成する

次の手順に従って、デバイス関連の設定を構成します。

1. **[デバイス]** ページで、次の手順のようにします。

    1. デバイスのフレンドリ名を入力します。 フレンドリ名は 1 から 13 文字にする必要があり、文字、数字、ハイフンを使用できます。

    2. デバイスの **[DNS ドメイン]** を指定します。 このドメインは、デバイスをファイル サーバーとして設定するために使用されます。

    3. 構成したデバイス設定を検証および適用するには、 **[適用]** を選択します。

        ![ローカル Web UI の [デバイス] ページ 1](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        デバイス名と DNS ドメインを変更した場合、デバイスに存在していた自己署名証明書は機能しなくなります。 

        ![ローカル Web UI の [デバイス] ページ 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        証明書を構成するときは、次のいずれかのオプションを選択する必要があります。 
        
        - デバイス証明書を生成してダウンロードします。 
        - 署名チェーンを含め、デバイスの独自の証明書を取り込みます。
    

    4. デバイス名と DNS ドメインを変更すると、SMB エンドポイントが作成されます。  

        ![ローカル Web UI の [デバイス] ページ 3](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. 設定が適用されたら、 **[Next: Update server]\(次へ: 更新サーバー\)** を選択します。


## <a name="configure-update"></a>更新プログラムを構成する

1. **[Update]\(更新\)** ページで、デバイスの更新プログラムをダウンロードする場所を構成できます。  

    - 更新プログラムは、**Microsoft Update サーバー** から直接取得できます。

        ![ローカル Web UI の [Update Server]\(更新サーバー\) ページ](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        **Windows Server Update Services** (WSUS) から更新プログラムをデプロイすることもできます。 WSUS サーバーのパスを指定します。
        
        ![ローカル Web UI の [Update Server]\(更新サーバー\) ページ 2](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > 別の Windows Update サーバーが構成されていて、*http* ではなく *https* 経由で接続することを選択した場合は、更新サーバーに接続するための署名チェーン証明書が必要になります。 証明書を作成してアップロードする方法については、[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)に関する記事を参照してください。 Modular Data Center に階層化している Azure Stack Edge デバイスなど、切断モードで作業するには、WSUS オプションを有効にします。 アクティブ化中、デバイスは更新プログラムをスキャンします。サーバーが設定されていない場合、アクティブ化は失敗します。 

2. **[適用]** を選択します。
3. 更新サーバーを構成した後は、 **[Next: Time]\(次へ: 時刻\)** を選択します。
    

## <a name="configure-time"></a>時刻の構成

次の手順に従って、デバイスでの時刻の設定を構成します。 

> [!IMPORTANT]
> 時刻の設定は省略可能ですが、デバイス用にローカル ネットワークでプライマリ NTP サーバーとセカンダリ NTP サーバーを構成することを強くお勧めします。 ローカル サーバーを使用できない場合は、パブリック NTP サーバーを構成できます。

デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。

1. **[時刻]** ページで、タイム ゾーンを選択し、デバイスのプライマリ NTP サーバーとセカンダリ NTP サーバーを選択できます。  
    
    1. **[タイム ゾーン]** ドロップダウン リストで、デバイスがデプロイされている地理的な場所に対応するタイム ゾーンを選択します。
        デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。

    2. **[プライマリ NTP サーバー]** ボックスで、デバイスのプライマリ サーバーを入力するか、または time.windows.com の既定値をそのまま使用します。  
        ネットワークでデータセンターからインターネットへの NTP トラフィックの流れが許可されていることを確認してください。

    3. オプションで、 **[セカンダリ NTP サーバー]** ボックスにデバイスのセカンダリ サーバーを入力します。

    4. 構成された時刻の設定を検証および適用するには、 **[適用]** を選択します。

        ![ローカル Web UI の [時刻] ページ](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. 設定が適用されたら、 **[Next: Certificate]\(次へ: 証明書\)** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * デバイス設定を構成する
> * 更新プログラムを構成する 
> * 時刻の構成

ご利用の Azure Stack Edge Mini R デバイスの証明書を構成する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [証明書の構成](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
