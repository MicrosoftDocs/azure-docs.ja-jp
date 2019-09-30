---
title: PIM で Azure AD ロールの設定を構成する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804443"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>PIM で Azure AD ロールの設定を構成する

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、組織の Azure Active Directory (Azure AD) Privileged Identity Management (PIM) をカスタマイズできます。

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure AD ロールの設定を開きます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

1. **[設定]** をクリックします。

    ![Azure AD ロール - 設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **[ロール]** をクリックします。

1. 設定を構成するロールをクリックします。

    ![Azure AD ロール - ロールの設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    各ロールの設定ページでは、複数の設定を構成できます。 これらの設定の影響を受けるのは、**永続的**な割り当てのユーザーではなく、**有資格**の割り当てのユーザーのみです。

## <a name="activations"></a>アクティブ化

**[アクティブ化]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 ～ 72 時間の範囲の値を指定できます。

## <a name="notifications"></a>通知

**[通知]** スイッチを使用して、ロールがアクティブになったときに管理者がメール通知を受け取るかどうかを指定します。 これは、不正なアクティブ化の検出に役立ちます。

**[有効]** に設定すると、通知が送信されます。

- 特権ロール管理者
- セキュリティ管理者
- グローバル管理者

詳しくは、「[PIM での電子メール通知](pim-email-notifications.md)」をご覧ください。

## <a name="incidentrequest-ticket"></a>インシデント/要求チケット

**[インシデント/要求チケット]** スイッチを使用して、管理者候補に対して、ロールのアクティブ化時にチケット番号の入力を必須とするかどうかを指定できます。 これは、ロール アクセスの監査を行う場合に役立ちます。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**[多要素認証]** スイッチを使用して、ユーザーに対して、ロールのアクティブ化で MFA を使用した ID 確認を必須とするかどうかを指定します。 ユーザーがこの確認を行う必要があるのはセッションごとに 1 回だけです。ロールのアクティブ化のたびに確認を行う必要はありません。 MFA を有効化する場合は次の 2 点に留意してください。

* メール アドレスの Microsoft アカウント (一般には @outlook.com ですが、例外もあります) を持つユーザーは、Azure MFA に登録できません。 Microsoft アカウントを持つユーザーにロールを割り当てるには、ユーザーを永続的な管理者にするか、そのロールの MFA を無効化する必要があります。
* Azure AD および Office365 の高い特権ロールでは、MFA は無効化できません。 これは、こうしたロールを慎重に保護する必要があるために設けられている安全機能です。  
  
  * Azure Information Protection 管理者
  * 課金管理者
  * クラウド アプリケーション管理者
  * コンプライアンス管理者
  * 条件付きアクセス管理者
  * CRM サービス管理者
  * カスタマー ロックボックスのアクセス承認者
  * ディレクトリ ライター
  * Exchange 管理者
  * グローバル管理者
  * Intune サービス管理者
  * Power BI サービス管理者
  * 特権ロール管理者
  * セキュリティ管理者
  * SharePoint サービス管理者
  * Skype for Business 管理者
  * ユーザー管理者

詳細については、[多要素認証 (MFA) と PIN](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="require-approval"></a>承認を要求する

ロールをアクティブ化するには承認を必須にする場合は、次の手順を実行します。

1. **[承認を要求する]** スイッチを **[有効]** に設定します。 ウィンドウが展開され、承認者を選択できるオプションが表示されます。

    ![Azure AD ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    承認者を指定**しない**場合は、特権ロール管理者が既定の承認者になります。 特権ロール管理者が、このロールの**すべて**のアクティブ化要求を承認する必要があります。

1. 承認者を指定するには、 **[承認者の選択]** をクリックします。

    ![Azure AD ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 1 人以上の承認者を選択し、 **[選択]** をクリックします。 ユーザーまたはグループを選択することができます。 承認者を 2 人以上にすることをお勧めします。 自己承認は許可されません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[保存]** をクリックして変更を保存します。


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [PIM で Azure AD ロールに対するセキュリティ アラートを構成する](pim-how-to-configure-security-alerts.md)
