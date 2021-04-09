---
title: Microsoft Azure Stack Edge Pro GPU デバイスで GPU モジュールを実行する | Microsoft Docs
description: Azure portal を使用して、Azure Stack Edge Pro デバイス上の GPU でモジュールを構成して実行する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 348ddff56ed61cd608d6b9f28417e7cd4c4e6b13
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563965"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイス上の GPU でモジュールを構成して実行する

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Azure Stack Edge Pro デバイスには、1 つ以上のグラフィックス処理装置 (GPU) が含まれています。 GPU は、並列処理機能を提供し、中央処理装置 (CPU) より画像のレンダリングを速く処理できるため、AI 計算によく使用されます。 Azure Stack Edge Pro デバイスに含まれる GPU の詳細については、[Azure Stack Edge Pro デバイスの技術仕様](azure-stack-edge-gpu-technical-specifications-compliance.md)をご覧ください。

この記事では、Azure Stack Edge Pro デバイス上の GPU でモジュールを構成して実行する方法について説明します。 この記事では、Nvidia T4 GPU 用に記述された、一般公開されているコンテナー モジュールの **Digits** を使用します。 この手順は、Nvidia がこの GPU 用に公開している他のすべてのモジュールの構成にも使用できます。


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. GPU 対応の 1 ノード Azure Stack Edge Pro デバイスにアクセスできること。 このデバイスが Azure のリソースでアクティブになっていること。  

## <a name="configure-module-to-use-gpu"></a>GPU を使用するようにモジュールを構成する

Azure Stack Edge Pro デバイス上の GPU を使用するようにモジュールを構成して、モジュールを実行するには、<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> 次の手順に従います。

1. Azure portal で、デバイスに関連付けられているリソースに移動します。

2. **[概要]** で、 **[IoT Edge]** を選択します。

    ![GPU を使用するようにモジュールを構成する 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. **[IoT Edge サービスを有効にする]** の **[追加]** を選択します。

   ![GPU を使用するようにモジュールを構成する 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. **[IoT Edge サービスの作成]** に、自分の IoT Hub リソースの設定を入力します。

   |フィールド   |値    |
   |--------|---------|
   |サブスクリプション      | Azure Stack Edge リソースによって使用されるサブスクリプション。 |
   |Resource group    | Azure Stack Edge リソースによって使用されるリソース グループ。 |
   |IoT Hub           | **[新規作成]** または **[既存のデータを使用する]** を選択します。 <br> 既定では、IoT リソースの作成には Standard レベル (S1) が使用されます。 Free レベルの IoT リソースを使用するには、それを作成してから既存のリソースを選択します。 <br> いずれの場合も、IoT Hub リソースでは、Azure Stack Edge リソースによって使用されるのと同じサブスクリプションとリソース グループが使用されます。     |
   |名前              | 新しい IoT Hub リソースに既定の名前を使用したくない場合は、別の名前を入力します。 |

   設定が完了したら、 **[確認および作成]** を選択します。 IoT Hub リソースの設定を確認し、 **[作成]** を選択します。

   ![コンピューティングの開始 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   IoT Hub リソースの作成には数分かかります。 リソースの作成後、IoT Edge サービスが実行中であることが **[概要]** に示されます。

   ![コンピューティングの開始 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. Edge コンピューティング ロールが構成されたことを確認するには、 **[プロパティ]** を選択します。

   ![コンピューティングの開始 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. **[プロパティ]** で、 **[IoT Edge デバイス]** のリンクを選択します。

   ![GPU を使用するようにモジュールを構成する 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   右側のウィンドウに、Azure Stack Edge Pro デバイスに関連付けられている IoT Edge デバイスが表示されます。 このデバイスは、IoT Hub リソースを作成するときに作成した IoT Edge デバイスに対応しています。
 
7. この IoT Edge デバイスを選択します。

   ![GPU を使用するようにモジュールを構成する 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. **[Set modules]\(モジュールの設定\)** を選びます。

   ![GPU を使用するようにモジュールを構成する 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. **[+ 追加]** を選択し、 **[+ IoT Edge モジュール]** を選択します。 

    ![GPU を使用するようにモジュールを構成する 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. **[IoT Edge モジュールを追加する]** タブで以下を行います。

    1. **[イメージの URI]** を指定します。 ここでは、一般公開されている Nvidia モジュールの **Digits** を使用します。 
    
    2. **[再起動ポリシー]** を **[常時]** に設定します。
    
    3. **[必要な状態]** を **[実行中]** に設定します。
    
    ![GPU を使用するようにモジュールを構成する 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. **[環境変数]** タブで、変数の名前と対応する値を指定します。 

    1. 現在のモジュールがこのデバイス上の GPU の 1 つを使用するようにするため、NVIDIA_VISIBLE_DEVICES を使用します。 

    2. 値を 0 または 1 に設定します。 0 または 1 の値により、少なくとも 1 つの GPU がこのモジュールのためにデバイスによって使用されます。 値を「0, 1」に設定すると、デバイス上の両方の GPU がこのモジュールによって使用されていることを意味します。

       ![GPU を使用するようにモジュールを構成する 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Nvidia GPU で使用できる環境変数の詳細については、[nVidia コンテナー ランタイム](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)に関するセクションを参照してください。

    > [!NOTE]
    > モジュールでは、一方または両方の GPU を使用するか、まったく使用しないことができます。

12. モジュールの名前を入力します。 ここで、コンテナーの作成オプションを指定したり、モジュール ツインの設定を変更したりするように選択できます。完了したら、 **[追加]** を選択します。 

    ![GPU を使用するようにモジュールを構成する 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. モジュールが実行されていることを確認し、 **[確認および作成]** を選択します。

    ![GPU を使用するようにモジュールを構成する 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. **[確認および作成]** タブに、選択したデプロイ オプションが表示されます。 オプションを確認し、 **[作成]** を選択します。
    
    ![GPU を使用するようにモジュールを構成する 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. モジュールの **[ランタイムの状態]** をメモします。
    
    ![GPU を使用するようにモジュールを構成する 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    モジュールのデプロイには数分かかります。 **[更新]** を選択すると、 **[ランタイムの状態]** が **[実行中]** に更新されていることが確認できます。

    ![GPU を使用するようにモジュールを構成する 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>次のステップ

- [Nvidia GPU で使用できる環境変数](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)の詳細を確認する。
