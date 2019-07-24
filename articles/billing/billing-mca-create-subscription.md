---
title: お使いの課金アカウントに Azure サブスクリプションを追加作成する | Microsoft Docs
description: Azure portal で新しい Azure サブスクリプションを作成する方法を説明します。
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897107"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Microsoft 顧客契約用に追加の Azure サブスクリプションを作成する

開発およびテスト、セキュリティ、またはコンプライアンス上の理由からのデータの分離のために、お使いの課金アカウントにサブスクリプションを追加作成して、別の環境を設定できます。

この記事では、Microsoft 顧客契約の課金アカウントについて説明します。 [Microsoft 顧客契約にアクセスできるかどうかを確認](#check-access-to-a-microsoft-customer-agreement)します。 他の課金アカウントにサブスクリプションを作成する場合は、「[Azure portal での追加サブスクリプションの作成](billing-create-subscription.md)」を参照してください。

サブスクリプションを作成するには、**請求書セクションの所有者**、**請求書セクションの共同作成者**、または **Azure サブスクリプションの作成者**である必要があります。 詳細については、「[サブスクリプションの課金ロールとタスク](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)」を参照してください。 お使いの課金アカウントに Azure サブスクリプションを作成する権限を他のユーザーに付与する方法については、「[Azure サブスクリプションを作成する権限を他のユーザーに付与する](#give-others-permission-to-create-azure-subscriptions)」を参照してください。

## <a name="create-a-subscription-in-the-azure-portal"></a>Azure portal にサブスクリプションを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **サブスクリプション**を検索します。

   ![ポータルでのサブスクリプションの検索を示すスクリーン ショット](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. **[追加]** を選択します。

4. 複数の課金アカウントにアクセスできる場合、ご自分の Microsoft 顧客契約の課金アカウントを選択します。

   ![サブスクリプションの作成ページのスクリーンショット](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 課金プロファイルを選択します。 お使いのサブスクリプション料金は、その課金プロファイルの請求書に反映され、それの支払い方法で支払われます。 アクセスできる課金プロファイルが 1 つのみの場合は、この選択範囲はグレー表示されます。

6. 請求書セクションを選択します。 お使いのサブスクリプションの料金は、その課金プロファイルの請求書のこのセクションに反映されます。 アクセスできる請求書セクションが 1 つのみの場合は、この選択範囲はグレー表示されます。

7. サブスクリプションのプランを選択します。 開発またはワークロードのテストにこのサブスクリプションを使用する場合は、**[Dev/Test 用 Microsoft Azure プラン]** を選択します。その他の場合には、**[Microsoft Azure プラン]** を使用します。 アクセスできるプランが 1 つのみの場合は、この選択範囲はグレー表示されます。

8. サブスクリプションの名前を入力します。 この名前を使用すると、Azure portal でサブスクリプションを簡単に識別できます。

9. **作成**を選択します。

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Azure サブスクリプションを作成する権限を他のユーザーに付与する

ユーザーを請求書セクションに Azure サブスクリプションの作成者として追加すると、Azure サブスクリプションを作成する権限を付与できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** で検索します。

   ![ポータルでのサブスクリプションの検索を示すスクリーン ショット](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 請求書セクションに移動します。 お持ちのアクセスによっては、課金アカウントまたは課金プロファイルを選択する必要があります。 課金アカウントまたは課金プロファイルから **[請求書セクション]** を選択し、請求書セクションを選択します。

4. 左上の **[Access Management (IAM)]\(アクセス管理 (IAM)\)** を選択します。

5. ページ上部の **[追加]** を選択します。

6. ロールに **[Azure サブスクリプションの作成者]** を選択します。

   ![ユーザーへの Azure サブスクリプションの作成者ロールの付与を示すスクリーン ショット](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. アクセス権を付与するユーザーの電子メール アドレスを入力します。

8. **[保存]** を選択します。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

- [Azure リソースを作成する権限を組み込みのロールを使用して他のユーザーに付与する](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)
- [Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md)
- [リソースの整理と管理のための管理グループを作成する](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
