---
title: "Azure AD Privileged Identity Management の使用 | Microsoft Docs"
description: "Azure ポータルの Azure Active Directory Privileged Identity Management アプリケーションで特権 ID を管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management の使用開始
組織内のアクセス権は、Azure Active Directory (AD) Privileged Identity Management で管理、制御、監視することができます。 その対象には、Azure AD をはじめとする Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) 内のリソースへのアクセスが含まれます。

この記事では、Azure AD PIM アプリを Azure ポータル ダッシュボードに追加する方法を説明します。

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management アプリケーションの追加
Azure AD Privileged Identity Management を使用するには、まず Azure ポータル ダッシュボードにアプリケーションを追加する必要があります。

1. ディレクトリのグローバル管理者として [Azure ポータル](https://portal.azure.com/) にサインインします。
2. 組織に複数のディレクトリがある場合は、Azure Portal の右上隅に表示されているユーザー名を選択し、 PIM を使用するディレクトリを選択します。
3. **[その他のサービス]** を選択し、[フィルター] ボックスを使用して "**Azure AD Privileged Identity Management**" を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

ディレクトリ内で Azure AD Privileged Identity Management を使用する最初のユーザーには、そのディレクトリの **[セキュリティ管理者]** ロールと **[特権ロール管理者]** ロールが自動的に割り当てられます。 ユーザーのロールの割り当てを管理できるのは特権ロール管理者だけです。 [セキュリティ ウィザード](active-directory-privileged-identity-management-security-wizard.md)を実行して、 画面の指示に従って初回の検出と割り当てを体験することもできます。

## <a name="navigate-to-your-tasks"></a>タスクへの移動
Azure AD Privileged Identity Management を設定すると、アプリケーションを開くたびにナビゲーション ブレードが表示されます。 このブレードを使用して、ID 管理タスクを実行します。

![Top-level tasks for PIM - screenshot](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* **[自分のロール]** を使用すると、自分に割り当てられているロールの一覧が表示されます。 このセクションで、権限がある任意のロールをアクティブ化できます。
* **[申請の承認 (プレビュー)]** には、ディレクトリ内ユーザーからの保留中のアクティブ化申請が一覧表示されます。 [詳細情報。](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* **[保留中の要求 (プレビュー)]** には、アクティブ化された最新の要求が表示されます。
* **[アクセスのレビュー]** では、完了する必要がある保留中のすべてのアクセス レビューが表示されます。自分自身のアクセスをレビューすることも、他のユーザーのアクセスをレビューすることもできます。
* [管理] セクションにある **[Azure AD ディレクトリ ロール]** は、特権ロール管理者がロール割り当ての管理、ロールのアクティブ化設定の変更、アクセス レビューの開始などを行うためのダッシュボードです。 このダッシュボードのオプションは、特権ロール管理者以外に対しては無効になっています。

## <a name="next-steps"></a>次のステップ
[Azure AD Privileged Identity Management の概要](active-directory-privileged-identity-management-configure.md) に関するページに、組織で管理アクセスを管理する方法の詳細が記載されています。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

