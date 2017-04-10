---
title: "Azure AD ドメイン サービス: 仮想ネットワークの作成または選択 | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9e933774e3b618b1584b4f24a0491eda49e42077
ms.lasthandoff: 12/08/2016


---
# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Azure AD Domain Services の仮想ネットワークの作成または選択
## <a name="guidelines-to-select-an-azure-virtual-network"></a>Azure Virtual Network を選択するためのガイドライン
> [!NOTE]
> **開始する前に**: 「 [Azure AD Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)」を参照してください。
>
>

## <a name="task-2-create-an-azure-virtual-network"></a>タスク 2: Azure 仮想ネットワークを作成する
次の構成タスクでは、Azure 仮想ネットワークを作成し、その中にサブネットを作成します。 仮想ネットワーク内のこのサブネットで、Azure AD Domain Services を有効にします。 希望する仮想ネットワークが既にある場合は、この手順を省略してかまいません。

> [!NOTE]
> Azure AD ドメイン サービス用に作成 (または選択) する Azure Virtual Network が、Azure AD ドメイン サービスによってサポートされている Azure リージョンに属していることを確認してください。 Azure AD Domain Services を使用できる Azure リージョンを確認するには、 [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを参照してください。
>
>

仮想ネットワークの名前は書き留めておいてください。この後の構成手順で Azure AD Domain Services を有効にする際、適切な仮想ネットワークを選択する必要があります。

Azure AD Domain Services が利用できる Azure 仮想ネットワークを作成するには、次の構成手順を実行します。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。
2. 左側のウィンドウで、 **[ネットワーク]** ノードを選択します。

    ![Networks node](./media/active-directory-domain-services-getting-started/networks-node.png)
3. ページ下部の作業ウィンドウの **[新規]** をクリックします。

    ![[Virtual Networks] ノード](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. **[Network Services]** ノードの **[Virtual Network]** を選択します。
5. 仮想ネットワークを作成するには、 **[簡易作成]** をクリックします。

    ![Virtual network - quick create](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. 仮想ネットワークの **名前** を指定します。 このネットワークの **[アドレス空間]** や **[最大 VM 数]** を構成することもできます。 現時点では、**[DNS サーバー]**設定は [なし] のままでかまいません。 DNS サーバー設定は、Azure AD ドメイン サービスを有効にした後で更新できます。
7. **[場所]** ボックスの一覧で、サポート対象の Azure リージョンを確実に選択します。 Azure AD Domain Services を使用できる Azure リージョンを確認するには、 [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを参照してください。
8. 仮想ネットワークを作成するには、 **[仮想ネットワークの作成]** ボタンをクリックします。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. 仮想ネットワークが作成されたら、仮想ネットワークを選択し、**[構成]** タブをクリックします。

    ![サブネットの作成](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. **[仮想ネットワーク アドレス空間]** セクションに移動します。 **[サブネットの追加]** をクリックし、サブネット名として「**AaddsSubnet**」と指定します。 **[保存]** をクリックすると、サブネットが作成されます。

    ![Create a subnet for Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>タスク 3: Azure AD ドメイン サービスを有効にする
次に、 [Azure AD Domain Services を有効](active-directory-ds-getting-started-enableaadds.md)にします。

