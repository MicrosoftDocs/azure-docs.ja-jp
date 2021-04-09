---
title: PIM で管理する Azure リソースを検出する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理する Azure リソースを検出する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539011"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Privileged Identity Management で管理する Azure リソースを検出する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、Azure リソースの保護を向上させることができます。 これは、次の場合に役立ちます。

- 既に Privileged Identity Management を使用して Azure AD ロールを保護している組織
- 運用リソースをセキュリティで保護しようとしている管理グループとサブスクリプションの所有者

Privileged Identity Management を Azure リソース用に初めて設定するときは、Privileged Identity Management を使用して保護するリソースを検出して選択する必要があります。 Privileged Identity Management を使用して管理できるリソースの数に制限はありません。 ただし、最も重要な運用リソースから始めることをお勧めします。

## <a name="discover-resources"></a>リソースを探索する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

    Azure リソースに対して Privileged Identity Management を初めて使用する場合は、**[リソースを検出する]** ページが表示されます。

    ![初めて使用する場合の、リソースが一覧表示されていない [リソースの検出] ウィンドウ](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    組織内の別の管理者が、既に Privileged Identity Management で Azure リソースを管理している場合は、現在管理されているリソースの一覧が表示されます。

    ![現在管理されているリソースが一覧表示されている [リソースの検出] ウィンドウ](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **[リソースを検出する]** を選択して、検出エクスペリエンスを起動します。

    ![[検出] ウィンドウに、サブスクリプション、管理グループなど、管理できるリソースが一覧表示されています](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. **[検出]** ページで、**[リソース状態のフィルター]** と **[リソースの種類の選択]** を使用して、書き込みアクセス許可がある管理グループまたはサブスクリプションをフィルター処理します。 おそらく、最も簡単なのは、**[すべて]** から始めることです。

   管理グループまたはサブスクリプション リソースを検索して選択し、Privileged Identity Management で管理できます。 Privileged Identity Management で管理グループまたはサブスクリプションを管理する場合は、その子リソースも管理できます。

   > [!Note]
   > PIM で管理されている管理グループに新しい子 Azure リソースを追加すると、PIM で子リソースを検索することにより、それを管理対象にすることができます。

1. 管理するアンマネージド リソースを選択します。

1. **[リソースの管理]** を選択して、選択したリソースの管理を開始します。

    > [!NOTE]
    > 管理グループまたはサブスクリプションは、いったん管理対象となったら、管理対象外にすることはできません。 これにより、別のリソース管理者が Privileged Identity Management 設定を削除できなくなります。

    ![リソースが選択され、[リソースの管理] オプションが強調表示されている [検出] ウィンドウ](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 管理対象として選択したリソースのオンボードを確認するメッセージが表示されたら、**[はい]** を選択します。 これで、リソースの下にあるすべての新規および既存の子オブジェクトを管理するように PIM が構成されます。

    ![管理対象として選択したリソースのオンボードを確認するメッセージ](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
