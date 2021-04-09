---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524925"
---
Azure Stack Edge Pro にコンピューティングを構成するには、Azure portal を使用して IoT Hub リソースを作成します。

1. Azure portal で、Azure Stack Edge リソースの **[概要]** に移動し、 **[IoT Edge]** を選択します。

   ![コンピューティングの開始](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. **[IoT Edge サービスを有効にする]** の **[追加]** を選択します。

   ![コンピューティングを構成する](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. **[Edge コンピューティングの構成]** ブレードで、次の情報を入力します。
   
    |フィールド  |値  |
    |---------|---------|
    |サブスクリプション     |IoT Hub リソースのサブスクリプションを選択します。 Azure Stack Edge リソースで使用されているものと同じサブスクリプションを使用できます。         |
    |Resource group     |IoT Hub リソースのリソース グループを選択します。 Azure Stack Edge リソースで使用されているものと同じリソース グループを使用できます。         |
    |IoT Hub     | **[新規]** または **[既存]** を選択します。 <br> 既定では、IoT リソースの作成には Standard レベル (S1) が使用されます。 Free レベルの IoT リソースを使用するには、それを作成してから既存のリソースを選択します。 <br> いずれの場合も、IoT Hub リソースでは、Azure Stack Edge リソースによって使用されるのと同じサブスクリプションとリソース グループが使用されます。     |
    |名前     |既定の名前をそのまま使用するか、IoT ハブ リソースの名前を入力します。         |

   ![コンピューティングの開始 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. 設定が完了したら、 **[確認および作成]** を選択します。 IoT Hub リソースの設定を確認し、 **[作成]** を選択します。

   IoT Hub リソースの作成には数分かかります。 リソースの作成後、IoT Edge サービスが実行中であることが **[概要]** に示されます。

   ![コンピューティングの開始 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Edge コンピューティング ロールが構成されたことを確認するには、 **[IoT Edge] > [プロパティ]** の順に移動します。

   ![コンピューティングの開始 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。 IoT Edge ランタイムは、この IoT Edge デバイス上でも動作しています。 現時点では、お客様の IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。

背後で仮想マシンと Kubernetes クラスターが作成されているため、コンピューティングの構成には 20 分から 30 分かかることがあります。

Azure portal でコンピューティングを正常に構成すると、Kubernetes クラスターと、IoT 名前空間 (Azure Stack Edge Pro によって制御されるシステム名前空間) に関連付けられた既定のユーザーが作成されます。
