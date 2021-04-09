---
title: 'チュートリアル: Azure portal で Azure Stack Edge Mini R デバイス用の証明書を構成する | Microsoft Docs'
description: Azure Stack Edge Mini R を配置するチュートリアルでは、ご利用の物理デバイスに証明書を構成する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: c3a09242b895234c96c64d9e23449d980e47e387
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546739"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>チュートリアル:Azure Stack Edge Mini R 用の証明書、VPN、暗号化を構成する

このチュートリアルでは、ローカル Web UI を使用して、ご利用の Azure Stack Edge Mini R デバイス用の証明書、VPN、および暗号化を構成する方法について説明します。

この手順の所要時間は、選択した特定のオプションと、お使いの環境で証明書フローがどのように確立されるかによって変わります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する
> * VPN の構成
> * 保存時の暗号化の構成

## <a name="prerequisites"></a>前提条件

ご利用の Azure Stack Edge Mini R デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge Mini R の設置](azure-stack-edge-mini-r-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。

* 独自の証明書を持ち込む予定の場合:
    - 署名チェーン証明書を含む適切な形式で証明書を準備しておく必要があります。 証明書の詳細については、「[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)」を参照してください。

    - ご利用のデバイスが Azure Government、Azure Government Secret、または Azure Government のトップ シークレット クラウドに配置されていて、Azure パブリック クラウドに配置されていない場合は、デバイスをアクティブにする前に署名チェーン証明書が必要になります。 
    証明書の詳細については、[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)に関するページを参照してください。


## <a name="configure-certificates-for-device"></a>デバイスの証明書を構成する

1. 証明書の構成は、 **[証明書]** ページで行います。 **[デバイス]** ページでデバイス名または DNS ドメインを変更したかどうかに応じて、証明書に対して次のオプションのいずれかを選択できます。

    - 前の手順で既定のデバイス名または既定の DNS ドメインを変更しておらず、独自の証明書を持ち込まない場合は、この手順をスキップして次の手順に進むことができます。 デバイスによって、最初に自己署名証明書が自動的に生成されます。 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - デバイス名または DNS ドメインを変更した場合は、証明書の状態として **[無効]** が表示されます。 

        ![ローカル Web UI の [証明書] ページ 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        証明書を選択して、状態の詳細を表示します。

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        証明書には更新されたデバイス名と DNS ドメイン (これらはサブジェクト名とサブジェクトの代替で使用されます) が反映されていないため、証明書の状態は **[無効]** です。 ご利用のデバイスを正常にアクティブにするために、独自の署名済みエンドポイント証明書と対応する署名チェーンを持ち込むことができます。 まず署名チェーンを追加してから、エンドポイント証明書をアップロードします。 詳細については、[Azure Stack Edge Mini R デバイスへの独自の証明書の持ち込み](#bring-your-own-certificates)に関するセクションを参照してください。


    - デバイス名または DNS ドメインを変更したうえで、独自の証明書の持ち込みを行わない場合、**アクティブ化はブロックされます**。


#### <a name="bring-your-own-certificates"></a>独自の証明書を持ち込む

前の手順では、このデバイスで署名チェーンを追加しました。 これで、エンドポイント証明書、ノード証明書、ローカル UI 証明書、および VPN 証明書をアップロードできます。 次の手順に従って、独自の証明書を追加します。

1. 証明書をアップロードするには、 **[証明書]** ページで **[+ 証明書の追加]** を選択します。

    [![ローカル Web UI の [証明書] ページ 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. 他の証明書をアップロードすることもできます。 たとえば、Azure Resource Manager と BLOB ストレージ エンドポイントの証明書をアップロードできます。

    ![ローカル Web UI の [証明書] ページ 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. ローカル Web UI 証明書をアップロードすることもできます。 この証明書をアップロードした後、ブラウザーを起動してキャッシュをクリアする必要があります。 次に、デバイスのローカル Web UI に接続する必要があります。  

    ![ローカル Web UI の [証明書] ページ 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. ノード証明書をアップロードすることもできます。


    ![ローカル Web UI の [証明書] ページ 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. 最後に、VPN 証明書をアップロードできます。
        
    ![ローカル Web UI の [証明書] ページ](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. いつでも、証明書を選択して詳細を表示し、アップロードした証明書と一致していることを確認できます。

    [![ローカル Web UI の [証明書] ページ 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    証明書ページが更新され、新しく追加された証明書が反映されます。

    [![ローカル Web UI の [証明書] ページ 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Azure パブリック クラウドを除き、すべてのクラウド構成 (Azure Government または Azure Stack Hub) をアクティブにする前に、署名チェーン証明書を組み込む必要があります。


## <a name="configure-vpn"></a>VPN の構成

1. **[セキュリティ]** タイルで、[VPN] の **[構成]** を選択します。 

    VPN を構成するには、まず、必要なすべての構成が Azure で行われていることを確認する必要があります。 詳細については、[PowerShell を使用した Azure Stack Edge Mini R デバイスの VPN の構成](azure-stack-edge-placeholder.md)に関するページを参照してください。 これを完了すると、ローカル UI で構成を行うことができます。
    
    1. [VPN] ページで **[構成]** を選択します。
        ![VPN の構成ローカル UI 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. **[VPN の構成]** ブレードで、次の操作を行います。

        1. 電話帳を入力として指定します。
        2. Azure データ センターの IP 範囲の JSON ファイルを入力として指定します。 このファイルを [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) からダウンロードします。
        3. リージョンとして **[eastus]** を選択します。
        4. **[適用]** を選択します。

        ![VPN の構成ローカル UI 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. VPN のみを使用してアクセスする IP アドレス範囲を構成します。 
    
        - **[IP address ranges to be accessed using VPN only]\(VPN のみを使用してアクセスする IP アドレス範囲\)** で、 **[構成]** を選択します。
        - ご利用の Azure Virtual Network 用に選択した VNET IPv4 範囲を入力します。
        - **[適用]** を選択します。
    
        ![VPN の構成ローカル UI 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

これで、ご利用のデバイスを暗号化する準備ができました。 保存時の暗号化を構成します。


## <a name="enable-encryption"></a>暗号化を有効にする

1. **[セキュリティ]** タイルで、[encryption-at-rest]\(保存時の暗号化\) の **[構成]** を選択します。 これは必須の設定であり、これが正常に構成されないとデバイスをアクティブにすることができません。 

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ページ 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    工場でデバイスがイメージ化されると、ボリューム レベルの BitLocker 暗号化が有効になります。 デバイスを受け取った後、保存時の暗号化を構成する必要があります。 ストレージ プールとボリュームが再作成されます。保存時の暗号化を有効にする BitLocker キーを指定することにより、保存データに対する 2 番目の暗号化レイヤーを作成できます。

1. **[Encryption-at-rest]\(保存時の暗号化\)** ペインで、32 文字 (AES-256 ビット) の Base-64 でエンコードされたキーを指定します。 これは 1 回限りの構成です。このキーは、実際の暗号化キーを保護するために使用されます。 

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ページ 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    このキーを自動的に生成するように選択することもできます。

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ページ 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. **[適用]** を選択します。 この操作には数分かかり、操作の状態が **[セキュリティ]** タイルに表示されます。

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ページ 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. 状態が **[Completed]\(完了済み\)** と表示されたら、 **[[開始] に戻る]** に戻ります。

これでデバイスをアクティブにする準備ができました。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する
> * VPN の構成
> * 保存時の暗号化の構成

ご利用の Azure Stack Edge Mini R デバイスをアクティブにする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Mini R デバイスをアクティブにする](./azure-stack-edge-mini-r-deploy-activate.md)
