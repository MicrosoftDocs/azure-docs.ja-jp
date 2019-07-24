---
title: Azure Active Directory Domain Services:はじめに | Microsoft Docs
description: Azure Portal を使用して Azure Active Directory Domain Services を有効にする
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 3020d7b29f19ec2ab578acbebac8db8ea320a844
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098027"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を有効にする


## <a name="before-you-begin"></a>開始する前に
[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)のページを参照してください。


## <a name="task-2-configure-network-settings"></a>タスク 2: ネットワーク設定を構成する
次の構成タスクでは、Azure 仮想ネットワークを作成し、その中に専用サブネットを作成します。 仮想ネットワーク内のこのサブネットで、Azure Active Directory Domain Services を有効にします。 既存の仮想ネットワークを選択して、その中に専用サブネットを作成することもできます。

1. **[仮想ネットワーク]** をクリックし、仮想ネットワークを選択します。
    > [!NOTE]
    > **新しいデプロイでは、クラシック仮想ネットワークはサポートされません。** 新しいデプロイでは、クラシック仮想ネットワークはサポートされません。 クラシック仮想ネットワークにデプロイされている既存のマネージド ドメインは、引き続きサポートされます。 Microsoft は、近いうちに、既存の管理対象ドメインを、クラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行できる機能を提供します。
    >

2. **[仮想ネットワークの選択]** ページに、既存の仮想ネットワークがすべて表示されます。 **[基本]** ウィザード ページで選択したリソース グループと Azure の場所に属している仮想ネットワークのみが表示されます。
3. Azure AD Domain Services を有効にする仮想ネットワークを選択します。 既存の仮想ネットワークを選択することも、新しい仮想ネットワークを作成することもできます。

   > [!TIP]
   > **マネージド ドメインは、Azure AD Domain Services を有効にした後、別の仮想ネットワークに移動できません。** マネージド ドメインを有効にするには、適切な仮想ネットワークを選択します。 マネージド ドメインを作成したら、そのマネージド ドメインは、一度削除しないと別の仮想ネットワークには移動できません。 詳細については、「[Azure AD Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)」を参照してください。  
   >

4. **仮想ネットワークを作成する:** **[新規作成]** をクリックして新しい仮想ネットワークを作成します。 Azure AD Domain Services の専用サブネットをご使用ください。 たとえば、"DomainServices" という名前のサブネットを作成すると、サブネット内にデプロイされている内容を他の管理者が理解しやすくなります。 完了したら **[OK]** をクリックします。

    ![仮想ネットワークを選択する](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > 必ずプライベート IP アドレス空間の範囲内にあるアドレス空間を選んでください。 ご自身が所有していないパブリック アドレス空間内の IP アドレスを指定すると、Azure AD Domain Services 内でエラーが発生します。

5. **既存の仮想ネットワーク:** 既存の仮想ネットワークを選択する予定の場合、[仮想ネットワークの拡張機能を使用して専用サブネットを作成し](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)、そのサブネットを選択します。 **[仮想ネットワーク]** をクリックして、既存の仮想ネットワークを選択します。 **[サブネット]** をクリックして、新しいマネージド ドメインを有効にする、既存の仮想ネットワーク内の専用サブネットを選択します。 完了したら **[OK]** をクリックします。

    ![仮想ネットワーク内のサブネットを選択する](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **サブネットを選択するためのガイドライン**
   > 1. Azure AD Domain Services の専用サブネットをご使用ください。 他の仮想マシンをこのサブネットにデプロイしないでください。 この構成を使用すると、マネージド ドメインに支障をきたすことなく、ワークロード/仮想マシンのネットワーク セキュリティ グループ (NSG) を構成できます。 詳細については、[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)を参照してください。
   > 2. ゲートウェイ サブネットは、サポートされている構成ではないため、Azure AD Domain Services のデプロイでは選択しないでください。
   > 3. 選択したサブネットは、アドレススペースの中で使用できるIPアドレスを、少なくとも 3 個から 5 個持っていなければなりません。

6. 完了したら、**[OK]** をクリックして、ウィザードの **[管理者グループ]** ページに進みます。


## <a name="next-step"></a>次のステップ
[タスク 3: 管理グループを構成して Azure AD Azure AD Domain Services を有効にする](active-directory-ds-getting-started-admingroup.md)
