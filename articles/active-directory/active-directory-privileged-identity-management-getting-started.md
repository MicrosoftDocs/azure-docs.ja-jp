---
title: "Azure AD Privileged Identity Management の使用 | Microsoft Docs"
description: "Azure ポータルの Azure Active Directory Privileged Identity Management アプリケーションで特権 ID を管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 0f9f09ca8fb30d494433ed8d26b808d1b5d4d0b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management の使用開始

組織内のアクセス権は、Azure Active Directory (AD) Privileged Identity Management で管理、制御、監視することができます。 その対象には、Azure リソース、Azure AD、Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) へのアクセスが含まれます。

この記事では、Azure AD PIM アプリを Azure ポータル ダッシュボードに追加する方法を説明します。

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management アプリケーションの追加

Azure AD Privileged Identity Management を使用するには、まず Azure ポータル ダッシュボードにアプリケーションを追加する必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/) にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure Portal の右上隅に表示されているユーザー名を選択し、 PIM を使用するディレクトリを選択します。
3. **[その他のサービス]** を選択し、[フィルター] ボックスを使用して "**Azure AD Privileged Identity Management**" を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、そのディレクトリの **[セキュリティ管理者]** ロールと **[特権ロール管理者]** ロールが自動的に割り当てられます。 ユーザーの Azure AD ディレクトリ ロールの割り当てを管理できるのは特権ロール管理者だけです。 [セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md)を実行して、 画面の指示に従って初回の検出と割り当てを体験することもできます。

## <a name="navigate-to-your-tasks"></a>タスクへの移動

Azure AD Privileged Identity Management を設定すると、アプリケーションを開くたびにナビゲーション ブレードが表示されます。 このブレードを使用して、ID 管理タスクを実行します。

![Top-level tasks for PIM - screenshot](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **[自分のロール]** には、自分に割り当てられている適格でアクティブなロールの一覧が表示されます。 ここでは、割り当てられている適格なロールをアクティブにできます。
- **[Approve Requests (Preview)]\(申請の承認 (プレビュー)\)** には、ディレクトリ内のユーザーによる適格な Azure AD ディレクトリ ロールのアクティブ化要求のうち、自分が承認するものの一覧が表示されます。 [詳細情報。](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **[Pending Requests (Preview)]\(保留中の要求 (プレビュー)\)** には、適格なロール割り当てのアクティブ化の保留中要求が表示されます。
- **[アクセスのレビュー]** には、自分に完了が割り当てられているアクティブなアクセス レビューが一覧表示されます (自分自身のアクセスをレビューするものと、他のユーザーのアクセスをレビューするものの両方)。
- 左側のナビゲーション メニューの管理セクションにある **[Azure AD ディレクトリ ロール]** には、特権ロール管理者がロール割り当ての管理、ロールのアクティブ化設定の変更、アクセス レビューの開始などを行うためのダッシュボードが表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。
- 左側のナビゲーション メニューの管理セクションにある **[Azure Resource roles (Preview)]\(Azure リソース ロール (プレビュー)\)** には、ロールの割り当てを選択してあるサブスクリプション リソースの一覧が表示されます。 

## <a name="next-steps"></a>次のステップ
[Azure AD Privileged Identity Management の概要](active-directory-privileged-identity-management-configure.md) に関するページに、組織で管理アクセスを管理する方法の詳細が記載されています。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
