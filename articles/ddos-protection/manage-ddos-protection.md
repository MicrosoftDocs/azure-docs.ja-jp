---
title: Azure Portal を使用した Azure DDoS Protection Standard の管理
description: Azure DDoS Protection Standard を使用して攻撃を軽減する方法について説明します。
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 336eeee7d522647d11f55a65dc9dc4e65e8b7d47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535240"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>クイックスタート: Azure DDoS Protection Standard の作成と構成

Azure portal を使用して Azure DDoS Protection Standard の使用を開始します。 

DDoS Protection プランでは、サブスクリプションの境界を越えて、DDoS Protection Standard が有効になった仮想ネットワークのセットを定義します。 組織で 1 つの DDoS Protection プランを構成し、複数のサブスクリプションから同じプランに仮想ネットワークをリンクできます。 

このクイックスタートでは、DDoS 保護プランを作成し、それを仮想ネットワークにリンクします。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- Azure Portal ( https://portal.azure.com ) にサインインします。 ご自分のアカウントに、[ネットワーク共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](manage-permissions.md)」の攻略ガイドに記載されている適切なアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) アカウントが割り当てらていることを確認します。

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection プランを作成する

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
2. *DDoS* という用語を検索します。 **[DDoS protection plan]\(DDoS Protection プラン\)** が検索結果に表示されたら、それを選択します。
3. **［作成］** を選択します
4. 次の値を入力または選択し、 **[作成]** を選択します。

    |設定        |値                                              |
    |---------      |---------                                          |
    |名前           | 「_MyDdosProtectionPlan_」と入力します。                     |
    |サブスクリプション   | サブスクリプションを選択します。                         |
    |Resource group | **[新規作成]** を選択し、「_MyResourceGroup_」と入力します。|
    |場所       | 「_米国東部_」と入力します。                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>仮想ネットワークの DDoS 保護を有効にする

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>新しい仮想ネットワークの DDoS 保護を有効にする

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。
3. 次の値を入力するか選択し、それ以外は既定値をそのまま使用して、 **[作成]** を選択します。

    | 設定         | 値                                           |
    | ---------       | ---------                                       |
    | 名前            | 「_MyVnet_」と入力します。                                 |
    | サブスクリプション    | サブスクリプションを選択します。                                    |
    | Resource group  | **[既存のものを使用]** 、 **[MyResourceGroup]** の順に選択します。 |
    | 場所        | 「_米国東部_」と入力します。                                                    |
    | DDoS Protection Standard | **[有効化]** を選択します。 選択したプランは仮想ネットワークと同じサブスクリプションまたは異なるサブスクリプションのどちらにあっても構いませんが、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。|

仮想ネットワークに対して DDoS Standard が有効になっている場合、仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。 DDoS Standard が有効になっている仮想ネットワークを移動する必要がある場合は、まず DDoS Standard を無効にし、仮想ネットワークを移動してから、DDoS Standard を有効にします。 移動後に、仮想ネットワーク内のすべての保護されたパブリック IP アドレスの自動調整されたポリシーしきい値がリセットされます。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>既存の仮想ネットワークの DDoS 保護を有効にする

1. 既存の DDoS Protection プランがない場合は、「[DDoS Protection プランを作成する](#create-a-ddos-protection-plan)」の手順を実行して DDoS Protection プランを作成します。
2. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
3. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\) ボックス** に、DDoS Protection Standard を有効にする仮想ネットワークの名前を入力します。 仮想ネットワークの名前が検索結果に表示されたら、それを選びます。
4. **[設定]** で、 **[DDoS Protection]** を選択します。
5. **[Standard]** を選択します。 **[DDoS protection plan]\(DDoS Protection プラン\)** で、既存の DDoS Protection プランまたは手順 1. で作成したプランを選び、 **[保存]** を選択します。 選択したプランは仮想ネットワークと同じサブスクリプションまたは異なるサブスクリプションのどちらにあっても構いませんが、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>すべての仮想ネットワークの DDoS 保護を有効にする

この[ポリシー](https://aka.ms/ddosvnetpolicy)により、定義されているスコープ内で、DDoS Protection Standard が有効にされていない仮想ネットワークが検出された後、必要に応じて、VNet を保護するための関連付けを作成する修復タスクが作成されます。 このポリシーをデプロイする方法の詳細な手順については、 https://aka.ms/ddosvnetpolicy-techcommunity を参照してください。

## <a name="validate-and-test"></a>検証とテスト

まず、DDoS 保護プランの詳細を確認します。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*DDoS*」と入力します。 結果に **[DDoS protection plans]\(DDoS Protection プラン\)** が表示されたら、それを選択します。
3. 一覧から DDoS 保護プランを選択します。

_MyVnet_ 仮想ネットワークが一覧表示されます。 

### <a name="view-protected-resources"></a>保護されたリソースを表示する
**[保護されたリソース]** で、保護された仮想ネットワークとパブリック IP アドレスを表示したり、DDoS 保護プランに仮想ネットワークをさらに追加したりすることができます。

![保護されたリソースを表示する](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルのためにリソースを保持しておくことができます。 不要になったら、_MyResourceGroup_ リソース グループを削除します。 リソース グループを削除する際に、DDoS 保護プランとその関連リソースもすべて削除します。 この DDoS 保護プランを使用する予定がない場合は、不要な料金が発生しないようリソースを削除してください。

   >[!WARNING]
   >このアクションは元に戻すことができません。

1. Azure portal で **[リソース グループ]** を探して選択するか、Azure portal のメニューから **[リソース グループ]** を選択します。

2. フィルター処理するか下へスクロールして、_MyResourceGroup_ リソース グループを見つけます。

3. リソース グループを選択し、 **[リソース グループの削除]** を選択します。

4. リソース グループの名前を入力して確認し、 **[削除]** を選択します。

仮想ネットワークに対して DDoS 保護を無効にするには: 

1. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\) ボックス** に、DDoS Protection Standard を無効にする仮想ネットワークの名前を入力します。 仮想ネットワークの名前が検索結果に表示されたら、それを選びます。
2. **[Under DDoS Protection Standard]\(DDoS Protection Standard の下\)** で **[無効にする]** を選択します。

DDoS 保護プランを削除する場合は、最初にそのプランからすべての仮想ネットワークの関連付けを解除する必要があります。 

## <a name="next-steps"></a>次のステップ

DDoS 保護プラン用にテレメトリを表示および構成する方法を学習するには、チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護テレメトリの表示と構成](telemetry.md)
