---
title: Cloud パートナー ポータルでのユーザーの管理 - Azure Marketplace | Microsoft Docs
description: クラウド パートナー ポータルでのユーザーの管理
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: pbutlerm
ms.openlocfilehash: 6ab010a5fb8399fb14d3f45c8bb3b4571657d883
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784421"
---
# <a name="managing-users-on-the-cloud-partner-portal"></a>Cloud パートナー ポータルでのユーザーの管理

[Cloud パートナー ポータル](https://cloudpartner.azure.com)では、仮想マシン オファーに対するロールベースのアクセス権を持つユーザーを追加することができます。 これにより、複数のユーザーがプランの作業中に、アクセス権とアクセス許可を管理するのに役立ちます。

## <a name="add-users-and-assign-roles"></a>ユーザーの追加とロールの割り当てを行う 

オファーを発行するために、ユーザーを追加し、ロールを割り当てるには、次の手順に従います。

1. 左側にあるナビゲーション ウィンドウで、**[ユーザー]** タブをクリックします。

    ![[ユーザー] タブ](./media/userstab.png)


2. **[ユーザーの追加]** をクリックします。

    ![[ユーザーの追加] を選択する](./media/adduser.png)


3. ユーザーのメール アドレスを入力し、ロールの割り当てを選択します。  新規ユーザーを "所有者" または "共同作成者" として追加することができます。

    **電子メール**: プランの発行に使用する他のチーム メンバーの電子メール アドレスを追加します。 Microsoft アカウント (Outlook、Hotmail、および Live) と組織 ID がサポートされています。

    - メンバーが退職する場合に備えて、"チーム/グループのメール エイリアス/セキュリティ グループ" を追加します。
    - Cloud パートナー ポータルで提供されるメール ID が、Microsoft からのすべての通信について監視されることを確認します。
    
    ![役割を割り当てる](./media/assignrole.png)

    **ロール**: 次の表を参照して、適切なユーザー ロール タイプを特定してください。

    ![ロール レベル](./media/roleaccesslevel.png)

    所有者のみが [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) の [Azure Payouts]\(Azure の支払い\) タブと [Azure Customer]\(Azure カスタマー\) タブにアクセスできます。


4. 新しいユーザーに追加する発行者名を選択し、[追加] をクリックしてユーザーの追加を完了します。

    > [!NOTE]
    > 所有者または共同作成者として追加する、電子メールの対象となる "発行者名" が選択の候補として **[発行元]** ドロップダウン リストに表示されます。


    ![ロールの割り当て - 発行元を選択する](./media/assignselectpublisher.png)


## <a name="delete-an-existing-user"></a>既存のユーザーを削除する

既存のユーザーを削除するには、**[ユーザー]** タブで目的のユーザーのメールを検索し、右側の **[削除]** ボタンを押します。


## <a name="next-steps"></a>次の手順

ポータルを使用して[発行元プロファイルを管理](./cpp-manage-publisher-profile.md)することもできます。
