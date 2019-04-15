---
title: PIM で管理する Azure リソースを検出する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で管理する Azure リソースを検出する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/23/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 717cec41f152f0ebe3120b25db76cd684f39cbe3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575013"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>PIM で管理する Azure リソースを検出する

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、Azure リソースの保護を向上させることができます。 これは、既に PIM を使用して Azure AD ロールを保護している組織と、運用リソースをセキュリティで保護しようとしている管理グループとサブスクリプションの所有者に役立ちます。

PIM を Azure リソース用に初めて設定するときは、PIM を使って保護するリソースを検出して選択する必要があります。 PIM を使って管理できるリソースの数に制限はありません。 ただし、最も重要な (運用) リソースから始めることをお勧めします。

## <a name="discover-resources"></a>リソースを探索する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

    Azure リソースに対して PIM を初めて使用する場合は、リソース検出ウィンドウが表示されます。

    ![リソース検出 - 初回](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    組織内の別のリソースまたはディレクトリ管理者が、既に PIM で Azure リソースを管理している場合は、現在管理されているリソースの一覧が表示されます。

    ![リソース検出ウィンドウ](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. **[リソースの検出]** をクリックして、検出エクスペリエンスを起動します。

    ![[検出] ウィンドウ](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. [検出] ウィンドウで、**[リソース状態のフィルター]** と **[リソースの種類の選択]** を使用して、書き込みアクセス許可がある管理グループまたはサブスクリプションをフィルター処理します。 おそらく、最も簡単なのは、**[すべて]** から始めることです。

    検索して選択できるのは、PIM を使用して管理する管理グループまたはサブスクリプション リソースのみです。 PIM で管理グループまたはサブスクリプションを管理するときは、子リソースも管理できます。

1. 管理するすべてのアンマネージド リソースの横にチェック マークを追加します。

1. **[リソースの管理]** をクリックして、選択したリソースの管理を開始します。

    > [!NOTE]
    > 管理グループまたはサブスクリプションは、いったんマネージドに設定されたら、アンマネージにすることはできません。 これにより、別のリソース管理者が PIM 設定を削除することを防ぎます。

    ![検出 - リソースの管理](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 選択した管理用リソースのオンボードを確認するメッセージが表示されたら、**[はい]** をクリックします。

    ![検出 - リソース メッセージの管理](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
