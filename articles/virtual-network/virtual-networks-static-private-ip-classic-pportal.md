---
title: VM (クラシック) のプライベート IP アドレスの構成 - Azure Portal | Microsoft Docs
description: Azure Portal を使用して仮想マシン (クラシック) のプライベート IP アドレスを構成する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222906"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Azure Portal を使用して仮想マシン (クラシック) のプライベート IP アドレスを構成する

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイ モデルについて説明します。 [Resource Manager デプロイ モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-arm-pportal.md)こともできます。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

後述するサンプルの手順では、単純な環境が既に作成されていると想定しています。 このドキュメントに表示されている手順を実行する場合は、まず、 [vnet の作成](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)に関する記事に示されているテスト環境を構築します。

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM 作成時に静的プライベート IP アドレスを指定する方法
静的プライベート IP *192.168.1.101* を使用して、*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、次の手順を実行します。

1. ブラウザーで https://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[新規]**  >  **[Compute]**  >  **[Windows Server 2012 R2 Datacenter]** の順に選択し、 **[デプロイ モデルの選択]** の一覧に既に **[クラシック]** と表示されているのを確認してから **[作成]** を選択します。
   
    ![Azure portal を示すスクリーンショット。[新規] > [計算する] > [Windows Server 2012 R2 Datacenter] タイルの順で強調表示されています。](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. **[VM の作成]** で、作成する VM の名前 (ここでは *DNS01* )、ローカル管理者のアカウント、およびパスワードを入力します。
   
    ![VM、ローカル管理者のユーザー名、パスワードを入力して VM を作成する方法を示すスクリーンショット。](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. **[オプションの構成]**  >  **[ネットワーク]**  >  **[Virtual Network]** の順に選択し、 **[TestVNet]** を選択します。 **[TestVNet]** を使用できない場合は、場所として *[米国中部]* を使用しており、この記事の冒頭で説明されているテスト環境が作成済みであることを確認してください。
   
    ![[オプションの構成] > [ネットワーク] > [仮想ネットワーク] > [TestVNet] オプションの順に強調表示されているスクリーンショット。](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. **[ネットワーク]** で、以下のように、現在選択されているサブネットが *FrontEnd* であることを確認し、 **[IP アドレス]** を選択してから、 **[IP アドレスの割り当て]** の下にある **[静的]** を選択して、 **[IP アドレス]** に 「*192.168.1.101*」と入力します。
   
    ![[IP アドレス] フィールドが強調表示されているスクリーンショット。ここでは、静的 IP アドレスを入力できます。](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. **[IP アドレス]** で **[OK]** を選択し、 **[ネットワーク]** で **[OK]** を選択したら、 **[オプションの構成]** で **[OK]** を選択します。
7. **[VM の作成]** で **[作成]** を選択します。 ダッシュ ボードに以下のタイルが表示されることを確認します。
   
    ![[Creating Windows Server 2012 R2 Datacenter]\(Windows Server 2012 R2 Datacenter の作成\) タイルを示すスクリーンショット。](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM 用の静的プライベート IP アドレス情報を取得する方法
上記の手順で作成された VM の静的プライベート IP アドレス情報を表示するには、次の手順を実行します。

1. Azure Portal で、 **[すべて参照]**  >  **[仮想マシン (クラシック)]**  >  **[DNS01]**  >  **[すべての設定]**  >  **[IP アドレス]** の順に選択し、以下の IP アドレスの割り当てと IP アドレスを確認します。
   
    ![Azure ポータルでの VM の作成](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する方法

次の図に示すように、 **[IP アドレス]** で、 **[IP アドレスの割り当て]** の右側にある **[動的]** を選択し、 **[保存]** を選択したら、 **[はい]** を選択します。
   
![IP アドレスの割り当てラベルの右側にある [動的] を選択して VM から静的プライベート IP アドレスを削除する方法を示すスクリーンショット。](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>既存の VM に静的プライベート IP アドレスを追加する方法

1. 前に示した **[IP アドレス]** で、 **[IP アドレスの割り当て]** の右にある **[静的]** をクリックします。
2. **[IP アドレス]** に「*192.168.1.101*」と入力し、 **[保存]** を選択してから **[はい]** を選択します。

## <a name="set-ip-addresses-within-the-operating-system"></a>オペレーティング システム内で IP アドレスを設定する

VM のオペレーティング システム内で Azure 仮想マシンに割り当てられるプライベート IP は、必要でない限り静的に割り当てないことをお勧めします。 実際にオペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、それが Azure VM に割り当てられているプライベート IP アドレスと同じアドレスであるようにしてください。そうしないと、仮想マシンへの接続が失われる可能性があります。 仮想マシンのオペレーティング システム内で Azure の仮想マシンに割り当てられているパブリック IP アドレスを手動で割り当てないでください。

## <a name="next-steps"></a>次のステップ
* [予約済みパブリック IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) アドレスについて理解する。
* [インスタンスレベル パブリック IP (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) アドレスについて理解する。
* [予約済み IP REST API](/previous-versions/azure/reference/dn722420(v=azure.100))を確認する。