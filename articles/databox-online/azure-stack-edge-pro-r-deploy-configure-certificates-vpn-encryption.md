---
title: 'チュートリアル: Azure portal で Azure Stack Edge Pro R デバイス用の証明書を構成する | Microsoft Docs'
description: Azure Stack Edge Pro R を配置するチュートリアルでは、ご利用の物理デバイスに証明書を構成する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: abea192f209b968c6842e985620c067c93990687
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548150"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>チュートリアル:Azure Stack Edge Pro R 用の証明書を構成する

このチュートリアルでは、ローカル Web UI を使用して、ご利用の Azure Stack Edge Pro R デバイス用の証明書を構成する方法について説明します。

この手順の所要時間は、選択した特定のオプションと、お使いの環境で証明書フローがどのように確立されるかによって変わります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する
> * VPN の構成
> * 保存時の暗号化の構成

## <a name="prerequisites"></a>前提条件

ご利用の Azure Stack Edge Pro R デバイスを構成および設定する前に、次のことを確認してください。

* [Azure Stack Edge Pro R の設置](azure-stack-edge-pro-r-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。
* 独自の証明書を持ち込む予定の場合:
    - 署名チェーン証明書を含む適切な形式で証明書を準備しておく必要があります。 証明書の詳細については、「[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)」を参照してください。



## <a name="configure-certificates-for-device"></a>デバイスの証明書を構成する

1. 証明書の構成は、 **[証明書]** ページで行います。 **[デバイス]** ページでデバイス名または DNS ドメインを変更したかどうかに応じて、証明書に対して次のオプションのいずれかを選択できます。

    - 前の手順でデバイス名または DNS ドメインを変更していない場合は、この手順をスキップして次の手順に進むことができます。 デバイスによって、最初に自己署名証明書が自動的に生成されます。 

    - デバイス名または DNS ドメインを変更した場合は、証明書の状態として **[無効]** が表示されます。 

        ![ローカル Web UI の [証明書] ページ 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        証明書を選択して、状態の詳細を表示します。

        ![ローカル Web UI の [証明書] ページ 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        更新されたデバイス名と DNS ドメインが証明書に反映されないのはこのためです (それらはサブジェクト名とサブジェクトの別名に使用されます)。 ご利用のデバイスを正常にアクティブにするために、独自の署名済みエンドポイント証明書と対応する署名チェーンを持ち込むことができます。 まず署名チェーンを追加してから、エンドポイント証明書をアップロードします。 詳細については、[Azure Stack Edge Pro R デバイスへの独自の証明書の持ち込み](#bring-your-own-certificates)に関するセクションを参照してください。

    - デバイス名または DNS ドメインを変更したうえで、独自の証明書の持ち込みを行わない場合、**アクティブ化はブロックされます**。

    
#### <a name="bring-your-own-certificates"></a>独自の証明書を持ち込む

署名チェーンを含む独自の証明書を追加するには、次の手順を実行します。

1. 証明書をアップロードするには、 **[証明書]** ページで **[+ 証明書の追加]** を選択します。

    ![ローカル Web UI の [証明書] ページ 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. まず署名チェーンをアップロードし、 **[Validate & add]\(検証して追加\)** を選択します。

    ![ローカル Web UI の [証明書] ページ 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. これで、他の証明書をアップロードできるようになります。 たとえば、Azure Resource Manager と BLOB ストレージ エンドポイントの証明書をアップロードできます。

    ![ローカル Web UI の [証明書] ページ 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    ローカル Web UI 証明書をアップロードすることもできます。 この証明書をアップロードした後、ブラウザーを起動してキャッシュをクリアする必要があります。 次に、デバイスのローカル Web UI に接続する必要があります。  

    ![ローカル Web UI の [証明書] ページ 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    ノード証明書をアップロードすることもできます。

    ![ローカル Web UI の [証明書] ページ 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    最後に、VPN 証明書をアップロードできます。

    ![ローカル Web UI の [証明書] ページ 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    いつでも、証明書を選択して詳細を表示し、アップロードした証明書と一致していることを確認できます。

    証明書ページが更新され、新しく追加された証明書が反映されます。

    ![ローカル Web UI の [証明書] ページ 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Azure パブリック クラウドを除き、すべてのクラウド構成 (Azure Government または Azure Stack) をアクティブにする前に、署名チェーン証明書を組み込む必要があります。

1. **[< [開始] に戻る]** を選択します。

## <a name="configure-vpn"></a>VPN の構成

1. **[セキュリティ]** タイルで、[VPN] の **[構成]** を選択します。

    ![ローカル Web UI の [VPN] ページ](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    VPN を構成するには、まず、必要なすべての構成が Azure で行われていることを確認する必要があります。 詳細については、[前提条件の構成](azure-stack-edge-placeholder.md)および [VPN 用の Azure リソースの構成](azure-stack-edge-placeholder.md)に関するページを参照してください。 これを完了すると、ローカル UI で構成を行うことができます。
    
    1. [VPN] ページで **[構成]** を選択します。
    2. **[VPN の構成]** ブレードで、次の操作を行います。

    - **[VPN の設定]** を有効にします。
    - **VPN 共有シークレット** を指定します。 これは、Azure VPN 接続オブジェクトの作成時に指定した共有キーです。
    - **VPN ゲートウェイ IP** アドレスを指定します。 これは、Azure ローカル ネットワーク ゲートウェイの IP アドレスです。
    - **PFS グループ** の場合は、 **[なし]** を選択します。 
    - **DH グループ** の場合は、 **[Group2]** を選択します。
    - **[IPsec integrity method]\(IPsec 整合性方式\)** で、 **[SHA256]** を選択します。
    - **[IPsec cipher transform constants]\(IPsec 暗号化変換定数\)** で、 **[GCMAES256]** を選択します。
    - **[IPsec authentication transform constants]\(IPsec 認証変換定数\)** で、 **[GCMAES256]** を選択します。
    - **IKE 暗号化の方法** の場合は、 **[AES256]** を選択します。
    - **[適用]** を選択します。

        ![構成用ローカル UI 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. VPN ルート構成ファイルをアップロードするには、 **[アップロード]** を選択します。 
    
        ![構成用ローカル UI 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - 前の手順でご利用のローカル システムにダウンロードした VPN 構成 *json* ファイルを参照します。
        - [リージョン] で、デバイス、仮想ネットワーク、ゲートウェイに関連付けられている Azure リージョンを選択します。
        - **[適用]** を選択します。
    
            ![構成用ローカル UI 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. クライアント固有のルートを追加するには、VPN のみを使用してアクセスする IP アドレス範囲を構成します。 
    
        - **[IP address ranges to be accessed using VPN only]\(VPN のみを使用してアクセスする IP アドレス範囲\)** で、 **[構成]** を選択します。
        - IPv4 の有効な範囲を指定して、 **[追加]** を選択します。 他の範囲を追加するには、この手順を繰り返します。
        - **[適用]** を選択します。
    
            ![構成用ローカル UI 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. **[< [開始] に戻る]** を選択します。

## <a name="configure-encryption-at-rest"></a>保存時の暗号化の構成

1. **[セキュリティ]** タイルで、[encryption-at-rest]\(保存時の暗号化\) の **[構成]** を選択します。 これは必須の設定であり、これが正常に構成されないとデバイスをアクティブにすることができません。 

    工場でデバイスがイメージ化されると、ボリューム レベルの BitLocker 暗号化が有効になります。 デバイスを受け取った後、保存時の暗号化を構成する必要があります。 ストレージ プールとボリュームが再作成されます。保存時の暗号化を有効にする BitLocker キーを指定することにより、保存データに対する 2 番目の暗号化レイヤーを作成できます。

1. **[Encryption-at-rest]\(保存時の暗号化\)** ペインで、32 文字の Base-64 でエンコードされたキーを指定します。 これは 1 回限りの構成です。このキーは、実際の暗号化キーを保護するために使用されます。 このキーを自動的に生成するか、キーを入力するかを選択できます。

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ペイン](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    キーは、デバイスがアクティブになった後、 **[Cloud details]\(クラウドの詳細\)** ページのキー ファイルに保存されます。

1. **[適用]** を選択します。 この操作には数分かかり、操作の状態が **[セキュリティ]** タイルに表示されます。

    ![ローカル Web UI の [Encryption at rest]\(保存時の暗号化\) ページ](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. 状態が **[Completed]\(完了済み\)** と表示されたら、 **[< [開始] に戻る]** を選択します。

これでデバイスをアクティブにする準備ができました。 


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する
> * VPN の構成
> * 保存時の暗号化の構成

ご利用の Azure Stack Edge Pro R デバイスをアクティブにする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R デバイスをアクティブにする](./azure-stack-edge-pro-r-deploy-activate.md)
