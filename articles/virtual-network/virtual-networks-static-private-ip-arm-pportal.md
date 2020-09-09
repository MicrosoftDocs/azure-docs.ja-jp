---
title: VM のプライベート IP アドレスの構成 - Azure portal
description: Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707856"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Azure portal を使用して VM のプライベート IP アドレスを構成する

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下のサンプルの手順では、単純な環境が既に作成されていると想定しています。 このドキュメントに表示されているとおりに手順を実行する場合は、最初に[仮想ネットワークを作成します](quick-create-portal.md#create-a-virtual-network)。 ただし手順 3 では、代わりに以下の値を使用します。

| 設定 | 値 |
| ------- | ----- |
| 名前 | *TestVNet* |
| アドレス空間 | *192.168.0.0/16* |
| Resource group | **TestRG** (必要な場合は **[新規作成]** を選択して作成します) |
| サブネット - 名前 | *FrontEnd* |
| サブネット アドレス範囲 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>静的プライベート IP アドレスをテストするための VM を作成する
Resource Manager デプロイ モードで VM を作成するときには、Azure portal を使用して静的プライベート IP アドレスを設定できません。 その代わりに、まず VM を作成します。 その後で、そのプライベート IP を静的に設定できます。

*TestVNet* という名前の仮想ネットワークの *FrontEnd* サブネット内に、*DNS01* という名前の VM を作成するには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) メニューから **[リソースの作成]** を選択します。

    ![リソースの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. **[Compute]**  >  **[仮想マシン]** の順に選択します。

    ![VM の作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. **[基本]** で、次の表で説明するように項目の値を指定します。 次に、 **[次へ&nbsp;:&nbsp;ディスク]** を選択し、 **[次へ&nbsp;:&nbsp;ネットワーク]** を選択します。

    | Item | 値 |
    | --- | --- |
    | **サブスクリプション** | お使いの現在のサブスクリプション |
    | **リソース グループ** | **TestRG** (ドロップダウン リストから選択) |
    | **仮想マシン名** | *DNS01* |
    | **リージョン** | **(米国) 米国東部** |
    | **Image** | **Windows Server 2019 Datacenter** |
    | **[サイズ]** | **[VM サイズ]** は **[B1ls]** 、 **[オファー]** は **[Standard]** |
    | **ユーザー名** | 管理者アカウントのユーザー名 |
    | **パスワード** | 管理者アカウントのユーザー名のパスワード |
    | **[パスワードの確認入力]** | パスワードの再入力 |

    ![[基本] タブ、仮想マシンの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. **[ネットワーク]** で、次の表で説明するように項目の値を指定し、 **[次へ]** を選択します。

    | Item | 値 |
    | --- | --- |
    | **Virtual Network** | **TestVNet** |
    | **サブネット** | **FrontEnd** |

    ![[ネットワーク] タブ、仮想マシンの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. **[管理]** の **[診断ストレージアカウント]** で、 **[vnetstorage]** を選択します。 そのストレージ アカウントが一覧に表示されない場合は、 **[新規作成]** を選択し、*vnetstorage* の **[名前]** を指定して **[OK]** を選択します。 最後に、 **[確認と作成]** を選択します。

    ![[管理] タブ、仮想マシンの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. **[確認と作成]** を選択し、概要情報を確認したら、 **[作成]** を選択します。

    ![[確認と作成] タブ、仮想マシンの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

VM が作成されると次のメッセージが表示されます。

![デプロイ完了メッセージ、仮想マシンの作成、Azure portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>VM のプライベート IP アドレス情報を取得する
新しい VM のプライベート IP アドレス情報を表示するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にアクセスして目的の VM を見つけます。 **[仮想マシン]** を検索して選択します。

    ![仮想マシン、検索ボックス、Azure portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. 新しい VM の名前 (**DNS01**) を選択します。

    ![仮想マシンの一覧、Azure portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. **[ネットワーク]** を選択し、表示された唯一のネットワーク インターフェイスを選択します。

    ![ネットワーク インターフェイス、ネットワーク、仮想マシン、Azure portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. **[IP 構成]** を選択し、表の一覧に示された IP 構成を選択します。

    ![IP 構成、ネットワーク インターフェイス、ネットワーク、仮想マシン、Azure portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. **[プライベート IP アドレスの設定]** で、**TestVNet/FrontEnd** 仮想ネットワーク/サブネットの下にある、 **[割り当て]** の値 ( **[動的]** または **[静的]** ) と **IP アドレス**を書き留めます。

    ![動的または静的割り当て、古いプライベート IP アドレス設定、IP 構成、ネットワーク インターフェイス、ネットワーク、仮想マシン、Azure portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>既存の VM に静的プライベート IP アドレスを追加する
新しい VM に静的プライベート IP アドレスを追加するには、次のようにします。

1. [IP 構成] ページで、プライベート IP アドレスの割り当てを **[静的]** に設定します。
2. プライベート **IP アドレス**を *192.168.1.101* に変更し、 **[保存]** を選択します。
   
    ![動的または静的割り当て、新しいプライベート IP アドレスの設定、IP 構成、ネットワーク インターフェイス、ネットワーク、仮想マシン、Azure portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> **[保存]** の選択後、割り当てがまだ **[動的]** に設定されていることに気付いた場合、入力した IP アドレスは既に使用中です。 別の IP アドレスを試してください。

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する
VM から静的プライベート IP アドレスを削除するには、次のようにします。

[IP 構成] ページで、プライベート IP アドレスの割り当てを **[動的]** に設定してから、 **[保存]** を選択します。

## <a name="set-ip-addresses-within-the-operating-system"></a>オペレーティング システム内で IP アドレスを設定する

VM のオペレーティング システム内から、Azure VM に割り当てられている*プライベート* IP を静的に割り当てないでください。 [VM に多数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-portal.md)ときなど、必要な場合にのみ、プライベート IP の静的割り当てを行います。 オペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、そのアドレスが、Azure の[ネットワーク インターフェイス](virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと一致していることを確認します。 そのようにしないと、VM への接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。

また、仮想マシンのオペレーティング システム内で、Azure の仮想マシンに割り当てられている*パブリック* IP アドレスを手動で割り当てないでください。

## <a name="next-steps"></a>次のステップ

[IP アドレス設定](virtual-network-network-interface-addresses.md)の管理について学習します。
