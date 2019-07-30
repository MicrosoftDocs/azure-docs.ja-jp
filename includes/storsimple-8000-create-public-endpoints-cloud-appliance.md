---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181237"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>クラウド アプライアンス上にパブリック エンドポイントを作成するには

1. Azure ポータルにサインインします。
2. **[Virtual Machines]** に移動し、クラウド アプライアンスとして使用している仮想マシンを選択してクリックします。
    
3. 仮想マシンとの間のトラフィック フローを制御するネットワーク セキュリティ グループ (NSG) 規則を作成する必要があります。 NSG 規則を作成するには、次の手順を実行します。
    1. **[ネットワーク セキュリティ グループ]** を選択します。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 表示された既定のネットワーク セキュリティ グループをクリックします。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. **[受信セキュリティ規則]** を選択します。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. **[+ 追加]** をクリックすると、受信セキュリティ規則が作成されます。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        [受信セキュリティ規則の追加] ブレードで、次の操作を行います。

        1. **[名前]** に、エンドポイントの名前として「WinRMHttps」と入力します。
        
        2. **[優先度]** で、1000 (既定の規則の優先度) よりも小さい数字を選択します。 数字が大きいほど、優先度は低くなります。

        3. **[ソース]** を **[任意]** に設定します。

        4. **[サービス]** で **[WinRM]** を選択します。 **[プロトコル]** は自動的に **[TCP]** に設定され、 **[ポート範囲]** は "**5986**" に設定されます。

        5. **[OK]** をクリックして規則を作成します。

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。 ネットワーク セキュリティ グループをサブネットに関連付ける手順は次のとおりです。
    1. **[サブネット]** に移動します。
    2. **[+ 関連付け]** をクリックします。
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 仮想ネットワークを選択し、適切なサブネットを選択します。
    4. **[OK]** をクリックして規則を作成します。

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

規則を作成した後、詳細を表示して、パブリック仮想 IP (VIP) アドレスを決定できます。 このアドレスを記録します。


