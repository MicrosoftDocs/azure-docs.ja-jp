---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544636"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>クラウド アプライアンス上にパブリック エンドポイントを作成するには

1. Azure portal にサインインします。
2. **[Virtual Machines]** に移動し、クラウド アプライアンスとして使用している仮想マシンを選択してクリックします。
    
3. 仮想マシンとの間のトラフィック フローを制御するネットワーク セキュリティ グループ (NSG) 規則を作成する必要があります。 NSG 規則を作成するには、次の手順を実行します。
    1. **[ネットワーク セキュリティ グループ]** を選択します。
        ![仮想マシン ページのスクリーンショット。 [設定] セクションで、[ネットワーク セキュリティ グループ] が強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 表示された既定のネットワーク セキュリティ グループをクリックします。
        ![[ネットワーク セキュリティ グループ] ページのスクリーンショット。 既定のネットワーク セキュリティ グループが強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. **[受信セキュリティ規則]** を選択します。
        ![既定のネットワーク セキュリティ グループのプロパティを表示しているページのスクリーンショット。 ナビゲーション ペインで、受信セキュリティ規則が強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. **[+ 追加]** をクリックすると、受信セキュリティ規則が作成されます。
        ![[受信セキュリティ規則] ページのスクリーンショット。 プラス記号と [追加] という単語が隣り合って強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        [受信セキュリティ規則の追加] ブレードで、次の操作を行います。

        1. **[名前]** に、エンドポイントの名前として「WinRMHttps」と入力します。
        
        2. **[優先度]** で、1000 (既定の規則の優先度) よりも小さい数字を選択します。 数字が大きいほど、優先度は低くなります。

        3. **[ソース]** を **[任意]** に設定します。

        4. **[サービス]** で **[WinRM]** を選択します。 **[プロトコル]** は自動的に **[TCP]** に設定され、 **[ポート範囲]** は "**5986**" に設定されます。

        5. **[OK]** をクリックして規則を作成します。

            ![[受信セキュリティ規則の追加] ブレードのスクリーンショット。 手順での説明のように値が入力され、[OK] ボタンが強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。 ネットワーク セキュリティ グループをサブネットに関連付ける手順は次のとおりです。
    1. **[サブネット]** に移動します。
    2. **[+ 関連付け]** をクリックします。
        ![[サブネット] ページのスクリーンショット。 プラス記号と [関連付け] という単語が隣り合って強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 仮想ネットワークを選択し、適切なサブネットを選択します。
    4. **[OK]** をクリックして規則を作成します。

        ![[サブネットの関連付け] ページのスクリーンショット。 仮想ネットワークが選択され、[OK] ボタンが強調表示されています。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

規則を作成した後、詳細を表示して、パブリック仮想 IP (VIP) アドレスを決定できます。 このアドレスを記録します。


