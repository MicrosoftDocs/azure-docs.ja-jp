---
title: 'チュートリアル: Azure portal で GPU 搭載の Azure Stack Edge Pro デバイス用の証明書を構成する | Microsoft Docs'
description: GPU 搭載の Azure Stack Edge Pro を配置するチュートリアルでは、物理デバイスに証明書を構成する手順を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: d8d21f9eea0258175195529c4d8b72ee9085dc77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546977"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>チュートリアル:GPU 搭載の Azure Stack Edge Pro 用の証明書を構成する

このチュートリアルでは、ローカル Web UI を使用して、オンボード GPU 搭載の Azure Stack Edge Pro デバイス用の証明書を構成する方法について説明します。

この手順の所要時間は、選択した特定のオプションと、お使いの環境で証明書フローがどのように確立されるかによって変わります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する

## <a name="prerequisites"></a>前提条件

GPU 搭載の Azure Stack Edge Pro デバイスの構成と設定を行う前に、次のことを確認してください。

* [Azure Stack Edge Pro の設置](azure-stack-edge-gpu-deploy-install.md)に関する記事の詳細に従って、物理デバイスを設置していること。
* 独自の証明書を持ち込む予定の場合:
    - 署名チェーン証明書を含む適切な形式で証明書を準備しておく必要があります。 証明書の詳細については、「[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)」を参照してください。

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>デバイスの証明書を構成する

1. 証明書の構成は、 **[証明書]** ページで行います。 **[デバイス]** ページでデバイス名または DNS ドメインを変更したかどうかに応じて、証明書に対して次のオプションのいずれかを選択できます。

    - 前の手順でデバイス名または DNS ドメインを変更しておらず、独自の証明書を持ち込まない場合は、この手順をスキップして次の手順に進むことができます。 デバイスによって、最初に自己署名証明書が自動的に生成されます。 

        ![ローカル Web UI の [証明書] ページ](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - デバイス名または DNS ドメインを変更した場合は、証明書の状態として **[無効]** が表示されます。 

        ![ローカル Web UI の [証明書] ページ 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        証明書を選択して、状態の詳細を表示します。

        ![ローカル Web UI の [証明書] ページ 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        更新されたデバイス名と DNS ドメインが証明書に反映されないのはこのためです (それらはサブジェクト名とサブジェクトの別名に使用されます)。 デバイスを正常にアクティブ化するには、次のいずれかのオプションを選択します。 
    
        - **[Bring your own certificates]\(すべてのデバイス証明書を生成する\)** 。 これらのデバイス証明書はテストにのみ使用し、運用環境のワークロードでは使用しないでください。 詳細については、[Azure Stack Edge Pro のデバイス証明書を生成する](#generate-device-certificates)方法に関するセクションを参照してください。

        - **[Bring your own certificates]\(独自の証明書を持ち込む\)** 。 独自の署名済みエンドポイント証明書と対応する署名チェーンを持ち込むことができます。 まず署名チェーンを追加してから、エンドポイント証明書をアップロードします。 **運用環境のワークロードには、常に独自の証明書を持ち込むことをお勧めします。** 詳細については、[Azure Stack Edge Pro デバイスに独自の証明書を持ち込む](#bring-your-own-certificates)方法に関するセクションを参照してください。
    
        - 独自の証明書を複数持ち込み、複数のデバイス証明書を生成することができます。 **[Generate certificates]\(証明書を生成する\)** オプションを選択すると、デバイス証明書の再生成のみが行われます。

    - デバイス名または DNS ドメインを変更したが、証明書の生成も独自の証明書の持ち込みも行わない場合、**アクティブ化はブロックされます**。


### <a name="generate-device-certificates"></a>デバイス証明書を生成する

デバイス証明書を生成するには、次の手順を実行します。

次の手順を使用して、Azure Stack Edge Pro デバイス証明書を再生成してダウンロードします。

1. デバイスのローカル UI で、 **[構成] > [証明書]** に移動します。 **[Generate certificates]\(証明書の生成\)** を選択します。

    ![証明書の生成とダウンロード 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. **[Generate device certificates]\(デバイス証明書の生成\)** で **[生成]** を選択します。 

    ![証明書の生成とダウンロード 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    これでデバイス証明書が生成され、適用されます。 証明書の生成と適用には数分かかります。
    
    > [!IMPORTANT]
    > 証明書の生成操作の実行中は、独自の証明書を持ち込まず、 **[+ 証明書の追加]** オプションを使用して追加してください。

    操作が正常に完了すると、通知が表示されます。 **キャッシュの問題の可能性を回避するために、ブラウザーを再起動します。**
    
    ![証明書の生成とダウンロード 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. 証明書が生成された後: 

    - すべての証明書の状態として **[有効]** が表示されます。 

        ![証明書の生成とダウンロード 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - 特定の証明書名を選択し、証明書の詳細を表示できます。 

        ![証明書の生成とダウンロード 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - **[ダウンロード]** 列に値が設定されています。 この列には、再生成された証明書をダウンロードするためのリンクが含まれます。 

        ![証明書の生成とダウンロード 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. 証明書のダウンロード リンクを選択し、メッセージが表示されたら、証明書を保存します。 

    ![証明書の生成とダウンロード 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. ダウンロードするすべての証明書に対してこのプロセスを繰り返します。 
    
    ![証明書の生成とダウンロード 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    デバイスで生成された証明書は、次の名前形式で DER 証明書として保存されます。 

    `<Device name>_<Endpoint name>.cer`. これらの証明書には、デバイスにインストールされている対応する証明書の公開キーが含まれています。 

ASE デバイス上のエンドポイントへのアクセスに使用しているクライアント システムにこれらの証明書をインストールする必要があります。 これらの証明書によって、クライアントとデバイス間に信頼が確立します。

デバイスへのアクセスに使用しているクライアントにこれらの証明書をインポートしてインストールするには、[Azure Stack Edge Pro デバイスにアクセスするクライアントに証明書をインポートする](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)方法の手順を実行します。 

Azure Storage Explorer を使用する場合、クライアントに PEM 形式で証明書をインストールする必要があります。また、デバイスで生成された証明書を PEM 形式に変換する必要があります。 

> [!IMPORTANT]
> - ダウンロード リンクは、デバイスで生成された証明書にのみ使用できます。独自の証明書を持ち込む場合は使用できません。
> - 他の証明書の要件が満たされている限り、デバイスで生成された証明書を組み合わせて独自の証明書を持ち込むことができます。 詳細については、「[証明書の要件](azure-stack-edge-gpu-certificate-requirements.md)」を参照してください。
    

### <a name="bring-your-own-certificates"></a>独自の証明書を持ち込む

署名チェーンを含む独自の証明書を追加するには、次の手順を実行します。

1. 証明書をアップロードするには、 **[証明書]** ページで **[+ 証明書の追加]** を選択します。

    ![ローカル Web UI の [証明書] ページ 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. まず署名チェーンをアップロードし、 **[Validate & add]\(検証して追加\)** を選択します。

    ![ローカル Web UI の [証明書] ページ 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. これで、他の証明書をアップロードできるようになります。 たとえば、Azure Resource Manager と BLOB ストレージ エンドポイントの証明書をアップロードできます。

    ![ローカル Web UI の [証明書] ページ 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    ローカル Web UI 証明書をアップロードすることもできます。 この証明書をアップロードした後、ブラウザーを起動してキャッシュをクリアする必要があります。 次に、デバイスのローカル Web UI に接続する必要があります。  

    ![ローカル Web UI の [証明書] ページ 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    ノード証明書をアップロードすることもできます。

    ![ローカル Web UI の [証明書] ページ 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    いつでも、証明書を選択して詳細を表示し、アップロードした証明書と一致していることを確認できます。

    ![ローカル Web UI の [証明書] ページ 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    証明書ページが更新され、新しく追加された証明書が反映されます。

    ![ローカル Web UI の [証明書] ページ 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Azure パブリック クラウドを除き、すべてのクラウド構成 (Azure Government または Azure Stack) をアクティブにする前に、署名チェーン証明書を組み込む必要があります。


これでデバイスをアクティブにする準備ができました。 **[< [開始] に戻る]** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 前提条件
> * 物理デバイスの証明書を構成する

Azure Stack Edge Pro デバイスをアクティブにする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro デバイスをアクティブにする](./azure-stack-edge-gpu-deploy-activate.md)
