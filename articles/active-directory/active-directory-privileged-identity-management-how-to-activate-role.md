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
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: f70fec829b273de97fc49e61ba42d511b6b141b3


---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でロールをアクティブ化または非アクティブ化する方法
Azure Active Directory (AD) Privileged Identity Management を使用すると、企業における Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権アクセスの管理が簡略化されます。  

管理ロールの候補者となっている場合は、そのロールが必要なタスクを実行する必要のあるときにロールをアクティブにできます。 たとえば、Office 365 の機能をときどきしか管理しないユーザーは、組織の特権ロール管理者によって永続的なグローバル管理者には設定されない可能性があります。このロールは他のサービスにも影響を与えるからです。 その代わりに、Exchange Online 管理者などの Azure AD ロールが割り当てられます。 このような権限が必要な場合には、ロールをアクティブ化することを要求できます。それにより、事前に定義された期間だけ管理権限が付与されます。

この記事は、Azure AD Privileged Identity Management (PIM) でロールをアクティブ化する必要のある管理者を対象とし、 アクセス許可が必要になったときにロールをアクティブ化して、作業が終わったときにロールを非アクティブ化する手順について説明します。

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management アプリケーションの追加
別のポータルや PowerShell で操作している場合でも、 [Azure Portal](https://portal.azure.com/) で Azure AD Privileged Identity Management アプリケーションを使用してロールのアクティブ化を要求できます。 Azure Portal に Azure AD Privileged Identity Management アプリケーションがない場合は、まず、以下の手順を実行してください。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅に表示されているユーザー名をクリックし、操作するディレクトリを選択します。
3. **[その他のサービス]** を選択し、[フィルター] ボックスを使用して "**Azure AD Privileged Identity Management**" を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

## <a name="activate-a-role"></a>ロールのアクティブ化
ロールが必要な場合は、Azure AD Privileged Identity Management アプリケーションの **[ロールをアクティブ化する]** ボタンを使用してアクティブ化を要求できます。

1. [Azure Portal](https://portal.azure.com/) にサインインし、[Azure AD Privileged Identity Management ] タイルをクリックします。
2. **[ロールをアクティブ化する]**をクリックします。 割り当てられているロールの一覧が表示されます。
3. アクティブ化するロールを選択します。
4. **[アクティブ化]**を選択します。 **[ロール アクティブ化要求]** ブレードが表示されます。
5. 一部のロールをアクティブ化するには、多要素認証 (MFA) が必要になります。 認証は、セッションごとに&1; 回だけ行う必要があります。
   
    ![ロール アクティブ化前の MFA の確認 - スクリーンショット][2]
6. テキスト フィールドにアクティブ化要求の理由を入力します。  一部のロールでは、トラブル チケット番号を入力するように求められます。
7. **[OK]**を選択します。  ロールがアクティブ化され、ロールの変更が Microsoft Online Services に表示されます。

## <a name="deactivate-a-role"></a>ロールの非アクティブ化
ロールは、アクティブになった後、期限に達すると自動的に非アクティブになります。

早期に終了した場合は、Azure AD Privileged Identity Management アプリケーションでロールを手動で非アクティブにすることもできます。  **[ロールをアクティブ化する]** をクリックし、使用しなくなったロールを選択して **[非アクティブ化]** をクリックします。  

## <a name="next-steps"></a>次のステップ
Azure AD Privileged Identity Management の詳細を、次のリンクで確認できます。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png



<!--HONumber=Feb17_HO1-->


