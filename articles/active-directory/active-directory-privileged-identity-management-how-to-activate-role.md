---
title: "ロールをアクティブ化または非アクティブ化する方法 | Microsoft Docs"
description: "Azure Privileged Identity Management アプリケーションで特権 ID のロールをアクティブ化する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a143fd78eae52fda0cbadb7e74fd8209f24629a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法
Azure Active Directory (AD) Privileged Identity Management を使用すると、企業における Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権アクセスの管理が簡略化されます。  

管理ロールの候補者となっている場合は、特権の必要な操作を実行する必要のあるときにロールをアクティブにできます。 たとえば、Office 365 の機能をときどきしか管理しないユーザーは、組織の特権ロール管理者によって永続的なグローバル管理者には設定されない可能性があります。このロールは他のサービスにも影響を与えるからです。 その代わりに、Exchange Online 管理者などの Azure AD ロールが割り当てられます。 このような権限が必要な場合には、ロールをアクティブ化することを要求できます。それにより、事前に定義された期間だけ管理権限が付与されます。

この記事は、Azure AD Privileged Identity Management (PIM) でロールをアクティブ化する必要のある管理者を対象とし、 アクセス許可が必要になったときにロールをアクティブ化して、作業が終わったときにロールを非アクティブ化する手順について説明します。 また、特権ロールの管理者はロールをアクティブ化するための承認を要求できます (プレビュー)。 PIM 承認ワークフローの詳細は[ここ](./privileged-identity-management/azure-ad-pim-approval-workflow.md)にあります。

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management アプリケーションの追加
別のポータルや PowerShell で操作している場合でも、 [Azure Portal](https://portal.azure.com/) で Azure AD Privileged Identity Management アプリケーションを使用してロールのアクティブ化を要求できます。 Azure Portal に Azure AD Privileged Identity Management アプリケーションがない場合は、まず、以下の手順を実行してください。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅に表示されているユーザー名をクリックし、操作するディレクトリを選択します。
3. **[その他のサービス]** を選択し、[フィルター] ボックスを使用して "**Azure AD Privileged Identity Management**" を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

## <a name="activate-a-role"></a>ロールのアクティブ化
ロールが必要な場合は、アクティブ化を要求できます。Azure AD Privileged Identity Management アプリケーションの左ナビゲーション列でナビゲーション オプションの **[自分のロール]** を選択します。

1. [Azure Portal](https://portal.azure.com/) にサインインし、[Azure AD Privileged Identity Management ] タイルをクリックします。
2. **[自分のロール]** を選択します。 割り当てられた資格のあるロールの一覧がページの上部にグループで表示されます。
3. アクティブ化するロールを選択します。
4. **[アクティブ化]**を選択します。 **[ロール アクティブ化要求]** ブレードが表示されます。
5. 一部のロールをアクティブ化するには、多要素認証 (MFA) が必要になります。 認証は、セッションごとに 1 回だけ行う必要があります。
   
    ![ロール アクティブ化前の MFA の確認 - スクリーンショット][2]
6. テキスト フィールドにアクティブ化要求の理由を入力します。  一部のロールでは、トラブル チケット番号を入力するように求められます。
7. **[OK]**を選択します。  ロールで承認が要求されない場合、アクティブ化されています。アクティブなロールの一覧にロールが表示されます (資格のあるロールの割り当ての一覧のすぐ下)。 ロールのアクティブ化に[承認が必要](./privileged-identity-management/azure-ad-pim-approval-workflow.md)な場合、ブラウザーの右隅にトースト通知が一時的に表示され、承認待ちになっていることが通知されます。

    ![要求保留の通知 - スクリーンショット][3]

## <a name="deactivate-a-role"></a>ロールの非アクティブ化
ロールは、アクティブになった後、期限 (資格のある期間) に達すると自動的に非アクティブになります。

管理作業を早期に終了した場合は、Azure AD Privileged Identity Management アプリケーションでロールを手動で非アクティブにすることもできます。  **[自分のロール]** を選択し、**[アクティブなロールの割り当て]** から使用を終えたロールを選択し、**[非アクティブ化]** を選択します。  

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする
承認が要求されるロールのアクティブ化を必要としない場合、保留中の要求をいつでもキャンセルできます。 Azure AD Privileged Identity Management アプリケーションの左ナビゲーション列でナビゲーション オプションの **[自分のロール]** を選択します。

1. [Azure Portal](https://portal.azure.com/) にサインインし、[Azure AD Privileged Identity Management ] タイルをクリックします。
2. **[自分のロール]** を選択します。 割り当てられた資格のあるロールの一覧がページの上部にグループで表示されます。
3. ロールを選択します。
4. [ロールのアクティブ化の詳細] ブレードで **[Activation is pending approval]\(アクティブ化で承認が保留になっています\)** バナーを選択します。
5. **[承認待ち]** ブレードの上部にある **[キャンセル]** を選択します。

   ![要求保留の取り消しのスクリーンショット][4]

## <a name="next-steps"></a>次のステップ
Azure AD Privileged Identity Management の詳細を、次のリンクで確認できます。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
