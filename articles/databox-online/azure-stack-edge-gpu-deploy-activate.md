---
title: 'チュートリアル: Azure portal で GPU 搭載の Azure Stack Edge デバイスをアクティブにする | Microsoft Docs'
description: Azure Stack Edge GPU を配置するチュートリアルでは、お使いの物理デバイスをアクティブにする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267940"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>チュートリアル:GPU 搭載の Azure Stack Edge をアクティブにする

このチュートリアルでは、ローカル Web UI を使用して、オンボード GPU 搭載の Azure Stack Edge デバイスをアクティブにする方法について説明します。

このアクティブ化プロセスの所要時間は約 5 分です。

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスをアクティブにする

## <a name="prerequisites"></a>前提条件

GPU 搭載の Azure Stack Edge デバイスを構成および設定する前に、次のことを確認してください。

* 物理デバイスについて: 
    
    - [Azure Stack Edge の設置](azure-stack-edge-gpu-deploy-install.md)に関するページで詳細に説明されているように、物理デバイスが設置されていること。
    - [ネットワーク、コンピューティング ネットワーク、Web プロキシの構成](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)に関するページの説明に従って、ネットワークとコンピューティング ネットワークの設定を構成していること。
    - **[デバイス]** ページでデバイス名または DNS ドメインを変更した場合は、独自のデバイス証明書をデバイスにアップロードしたか、生成したこと。 この手順を実行していない場合、デバイスのアクティブ化中にエラーが表示され、アクティブ化はブロックされます。 詳細については、[証明書の構成](azure-stack-edge-gpu-deploy-configure-certificates.md)に関するページを参照してください。
    
* Azure Stack Edge デバイスを管理するために作成した Azure Stack Edge サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Stack Edge の配置の準備](azure-stack-edge-gpu-deploy-prep.md)に関するページをご覧ください。


## <a name="activate-the-device"></a>デバイスをアクティブにする

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。
2. **[アクティブ化]** タイルで、 **[アクティブにする]** を選択します。 

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. **[アクティブにする]** ペインに、「[アクティブ化キーの取得](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)」で Azure Stack Edge 用に取得した**アクティブ化キー**を入力します。

4. **[適用]** を選択します。

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. まず、デバイスがアクティブ化されます。 キー ファイルをダウンロードするように求められます。
    
    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    **[Download key file]\(ダウンロード キー ファイル\)** を選択し、*keys.json* ファイルをデバイスの外部の安全な場所に保存します。 **このキー ファイルには、デバイス上の OS ディスクとデータ ディスクの回復キーが含まれています**。 *keys.json* ファイルの内容を次に示します。

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    次の表では、さまざまなキーについて説明します。
    
    |フィールド  |説明  |
    |---------|---------|
    |`Id`    | これはデバイスの ID です。        |
    |`DataVolumeBitLockerExternalKeys`|これらはデータ ディスクの BitLockers キーであり、デバイス上のローカル データを復旧するために使用されます。|
    |`SystemVolumeBitLockerRecoveryKey`| これはシステム ボリュームの BitLocker キーです。 このキーは、デバイスのシステム構成とシステム データの回復に役立ちます。 |
    |`ServiceEncryptionKey`| このキーによって、Azure サービスを経由するデータが保護されます。 このキーがあることで、Azure サービスが侵害されても、格納されている情報は侵害されません。 |

6. **[概要]** ページに移動します。 デバイスの状態は **[アクティブ化済み]** と表示されます。

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
デバイスのアクティブ化は完了しました。 これで、デバイスで共有を追加できます。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスをアクティブにする

Azure Stack Edge デバイスでデータを転送する方法を学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge を使用してデータを転送する](./azure-stack-edge-j-series-deploy-add-shares.md)
