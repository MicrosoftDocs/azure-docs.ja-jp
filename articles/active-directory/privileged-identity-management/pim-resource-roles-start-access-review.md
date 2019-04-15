---
title: PIM で Azure リソース ロールのアクセス レビューを開始する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに対するアクセス レビューを開始する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46903967b375d882dc3c7a62cd0b7f8b6059f8b3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579497"
---
# <a name="start-an-access-review-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールのアクセス レビューを開始する
ユーザーが持っているアクセス特権がユーザーには不要になった場合、そのロールの割り当ては "古く" なっています。 このような古くなったロールの割り当てに関連するリスクを軽減するために、特権ロール管理者はロールを定期的に確認する必要があります。 このドキュメントでは、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) でアクセス レビューを開始する手順を説明します。

PIM アプリケーションのメイン ページで、以下に移動します。

* **[アクセス レビュー]** > **[追加]**

![アクセス レビューを追加する](media/azure-pim-resource-rbac/rbac-access-review-home.png)

**[追加]** ボタンを選択すると、**[アクセス レビューの作成]** ブレードが表示されます。 このブレードでは、名前と時間制限を使用してレビューを構成し、確認するロールを選択し、レビューを実行するユーザーを決定します。

![アクセス レビューの作成](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>レビューを構成する
アクセス レビューを作成するには、まず名前を付けてから、開始日と終了日を設定する必要があります。

![レビューを構成する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

ユーザーが完了するための十分なレビューの期間の長さを作成します。 終了日の前に完了したら、常にレビューを早く停止できます。

### <a name="choose-a-role-to-review"></a>確認するロールを選択する
各レビューでは、1 つのロールに重点を置いています。 特定のロールのブレードからアクセス レビューを開始した場合を除き、ここでロールを選択する必要があります。

1. **[ロール メンバーシップをレビュー]** に移動します。
   
    ![ロール メンバーシップを確認する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. 一覧から 1 つのロールを選択します。

### <a name="decide-who-will-perform-the-review"></a>レビューを実行するユーザを決定する
レビューを実行するための 3 つのオプションがあります。 他のユーザーにレビューを割り当てて完了してもらうか、自分でレビューするか、または各ユーザーが自身のアクセスを確認できます。

1. いずれかのオプションを選択してください。
   
   * **[選択したユーザー]**: アクセスする必要があるユーザーがわからない場合は、このオプションを使用します。 このオプションでは、リソース所有者またはグループ マネージャーにレビューを割り当て、完了してもらうことができます。
   * **[割り当て済み (セルフ)]**: ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。
   
2. **[レビュー担当者を選択する]** に移動します。
   
    ![レビュー担当者を選択する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>レビューを開始する
最後に、アクセスを承認する理由をユーザーが入力することを必須にすることができます。 必要に応じて、レビューの説明を追加します。 **[開始]** を選択します。

待機しているアクセス レビューがあることをユーザーに通知し、[アクセス レビューを実行する方法](pim-resource-roles-perform-access-review.md)を示します。

## <a name="manage-the-access-review"></a>アクセスレビューを管理する
レビュー担当者がレビューを完了すると、PIM Azure リソース ダッシュボードで進行状況状況を追跡できます。 ディレクトリのアクセス権は、[レビューが完了する](pim-resource-roles-complete-access-review.md)まで変更されません。

レビュー期間が終了するまで、レビューの完了をユーザーに通知するか、または [アクセス レビュー] セクションからレビューを早めに停止することができます。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールのアクセス レビューを完了する](pim-resource-roles-complete-access-review.md)
- [PIM で自分の Azure リソース ロールのアクセス レビューを実行する](pim-resource-roles-perform-access-review.md)
- [PIM で Azure AD ロールのアクセス レビューを開始する](pim-how-to-start-security-review.md)
