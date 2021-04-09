---
title: PIM で Azure AD ロールの設定を構成する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ロールの設定を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06eb7e8d1be2e58fd6cf87167a585a2ffeee0d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708485"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management で Azure AD ロールの設定を構成する

特権ロール管理者は、ロール候補の割り当てをアクティブ化しているユーザーの操作性を変更するなど、Azure Active Directory (Azure AD) 組織の Privileged Identity Management (PIM) をカスタマイズできます。

## <a name="determine-your-version-of-pim"></a>PIM のバージョンを判断する

2019 年 11 月以降、Privileged Identity Management の Azure AD ロール部分は、Azure リソース ロールのエクスペリエンスと一致する新しいバージョンに更新されます。 これによって機能が追加され、[既存の API の変更](azure-ad-roles-features.md#api-changes)が行われます。 新しいバージョンのロールアウト中、この記事で実行する手順は、現在お使いになっている Privileged Identity Management のバージョンによって異なります。 このセクションの手順に従って、お使いになっている Privileged Identity Management のバージョンを確認してください。 Privileged Identity Management のバージョンを確認したら、この記事に記載されている手順のうち、そのバージョンに一致するものを選択することができます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)のロールであるユーザーで [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。 概要ページの上部にバナーが表示されている場合は、この記事の **[新しいバージョン]** タブの指示に従ってください。 それ以外の場合は、 **[以前のバージョン]** タブの指示に従ってください。

  [![[Azure AD] > [Privileged Identity Management] を選択します。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Azure AD ロールに対する要求を承認または拒否するには、この記事の手順に従ってください。

# <a name="new-version"></a>[新しいバージョン](#tab/new)

## <a name="open-role-settings"></a>ロールの設定を開く

次の手順を実行して、Azure AD ロールの設定を開きます。

1. [特権ロール管理者](../roles/permissions-reference.md#privileged-role-administrator)ロールのユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** &gt; **[Azure AD ロール]** &gt; **[ロールの設定]** を開きます。

    ![Azure AD ロールを一覧表示する [ロールの設定] ページ](./media/pim-how-to-change-default-settings/role-settings.png)

1. 設定を構成するロールを選択します。

    ![複数の割り当てとアクティブ化の設定を一覧表示する [ロールの設定の詳細] ページ](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. **[編集]** を選択して [ロールの設定] ページを開きます。

    ![割り当てとアクティブ化の設定を更新するためのオプションがある [Edit role settings]/(ロールの設定の編集/) ページ](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    各ロールの [ロールの設定] ウィンドウでは、複数の設定を構成できます。

## <a name="assignment-duration"></a>割り当て期間

ロールの設定を構成するときに、各割り当ての種類 (対象とアクティブ) に 2 つの割り当て期間オプションから選択できます。 これらのオプションは、Privileged Identity Management でユーザーがロールに割り当てられるときの既定の最大期間になります。

このような **資格のある** 割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続的に資格のある割り当てを許可する** | 全体管理者と特権ロール管理者は、永続的に資格のある割り当てを割り当てることができます。 |
| **次の後に、資格のある割り当ての有効期限が切れる** | 全体管理者と特権ロール管理者は、すべての資格のある割り当てに、開始日と終了日の指定を必須にすることができます。 |

また、このような **アクティブな** 割り当て期間のオプションからいずれかを選択することができます。

| | |
| --- | --- |
| **永続するアクティブな割り当てを許可する** | 全体管理者と特権ロール管理者は、永続的にアクティブな割り当てを割り当てることができます。 |
| **アクティブ割り当ては次の期間後に期限切れになる** | 全体管理者と特権ロール管理者は、すべてのアクティブな割り当てに、開始日と終了日の指定を必須にすることができます。 |

> [!NOTE]
> 全体管理者と特権ロール管理者は､終了日時が指定されているすべての割り当てを更新することができます｡ これに対し､ユーザーは[ロールの割り当てを延長または更新する](pim-resource-roles-renew-extend.md)セルフサービス要求を開始することができます｡

## <a name="require-multi-factor-authentication"></a>多要素認証が必要です

Privileged Identity Management では、2 つの異なるシナリオで、Azure AD Multi-factor Authentication の強制を選択できます。

### <a name="require-multi-factor-authentication-on-active-assignment"></a>アクティブな割り当てに多要素認証が必要

場合によっては、ユーザーをロールに短期間 (たとえば 1 日) 割り当てる必要があります。 この場合、割り当てられたユーザーがアクティブ化を要求する必要はありません。 このシナリオでは、ユーザーは割り当てられた時点からそのロールでアクティブになるため、各自がそのロールの割り当てを使用するときに、Privileged Identity Management で Multi-Factor Authentication を強制することができません。

割り当てを実行する管理者が本人であることを確認するために、 **[アクティブな割り当てに多要素認証が必要]** チェックボックスをオンにして、アクティブな割り当てに Multi-Factor Authentication を強制できます。

### <a name="require-multi-factor-authentication-on-activation"></a>アクティブ化に Multi-Factor Authentication を要求する

ロールの資格を持つユーザーに対して、アクティブ化する前に Azure AD Multi-Factor Authentication を使用していることを証明するように要求することができます。 Multi-Factor Authentication により、ユーザーが、確かに本人が言うような人物であることが合理的かつ確実に保証されます。 このオプションを強制すると、ユーザー アカウントが侵害された可能性がある状況で、重要なリソースが保護されます。

アクティブ化の前に多要素認証を要求するには、 **[ロールの編集] 設定** の [割り当て] タブで **[アクティブ化には Multi-Factor Authentication が必要です]** チェックボックスをオンにします。

詳細については、[Multi-Factor Authentication と Privileged Identity Management](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="activation-maximum-duration"></a>アクティブ化の最大期間

**[アクティブ化の最大期間]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 から 24 時間の範囲の値を指定できます。

## <a name="require-justification"></a>理由を必須にする

ユーザーがアクティブ化するときに、業務上の正当な理由を入力するように要求できます。 理由を必須にするには、 **[アクティブな割り当てに理由が必要]** チェックボックスまたは **[アクティブ化に理由が必要]** チェックボックスをオンにします。

## <a name="require-approval-to-activate"></a>アクティブ化の承認を必須にする

複数の承認者を設定した場合、承認は、承認者が 1 人でも承認または拒否すると直ちに完了します。 少なくとも 2 人のユーザーから承認を要求することはできません。 ロールをアクティブ化するために承認を必須にする場合は、次の手順を実行します。

1. **[アクティブにするには承認が必要です]** チェックボックスをオンにします。

1. **[承認者の選択]** を選択します。

    ![承認者を選択するための [ユーザーまたはグループの選択] ペイン](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 少なくとも 1 人のユーザーを選択し、 **[選択]** をクリックします。 少なくとも 1 人の承認者を選択する必要があります。 既定の承認者はいません。

    選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[更新]** を選択して変更を保存します。

# <a name="previous-version"></a>[以前のバージョン](#tab/previous)

## <a name="open-role-settings-previous-version"></a>ロール設定を開く (以前のバージョン)

次の手順を実行して、Azure AD ロールの設定を開きます。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

1. **[設定]** を選択します。

    ![Azure AD ロール - 設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. **[役割]** を選びます。

1. 設定を構成するロールを選択します。

    ![Azure AD ロール - ロールの設定](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    各ロールの設定ページでは、複数の設定を構成できます。 これらの設定の影響を受けるのは、**永続的** な割り当てのユーザーではなく、**有資格** の割り当てのユーザーのみです。

## <a name="activations"></a>アクティブ化

**[アクティブ化]** スライダーを使用して、ロールの有効期限が切れるまでの最大時間 (時間単位) を設定します。 1 ～ 72 時間の範囲の値を指定できます。

## <a name="notifications"></a>通知

**[通知]** スイッチを使用して、ロールがアクティブになったときに管理者がメール通知を受け取るかどうかを指定します。 この通知は、不正なアクティブ化の検出に役立ちます。

**[有効]** に設定すると、通知が送信されます。

- 特権ロール管理者
- セキュリティ管理者
- 全体管理者

詳細については、[Privileged Identity Management でのメール通知](pim-email-notifications.md)に関するページを参照してください。

## <a name="incidentrequest-ticket"></a>インシデント/要求チケット

**[インシデント/要求チケット]** スイッチを使用して、管理者候補に対して、ロールのアクティブ化時にチケット番号の入力を要求します。 このプラクティスでは、ロール アクセスの監査をより効果的に行うことができます。

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

**[多要素認証]** スイッチを使用して、ユーザーに対して、ロールのアクティブ化で MFA を使用した ID 確認を必須とするかどうかを指定します。 ユーザーが自分の ID を確認する必要があるのは、ロールのアクティブ化のたびではなく、セッションごとに 1 回だけです。 MFA を有効化する場合は次の 2 点に留意してください。

- メール アドレス (一般には @outlook.com ですが、例外もあります) の Microsoft アカウントを持つユーザーは、Azure AD Multi-Factor Authentication に登録できません。 Microsoft アカウントを持つユーザーにロールを割り当てるには、ユーザーを永続的な管理者にするか、そのロールの Multi-Factor Authentication を無効化する必要があります。
- Azure AD および Microsoft 365 の高い特権ロールに対して、Azure AD Multi-Factor Authentication を無効化することはできません。 この安全機能は、次のロールの保護に役立ちます。  
  
  - Azure Information Protection 管理者
  - 課金管理者
  - クラウド アプリケーション管理者
  - コンプライアンス管理者
  - 条件付きアクセス管理者
  - Dynamics 365 管理者
  - カスタマー ロックボックスのアクセス承認者
  - ディレクトリ ライター
  - Exchange 管理者
  - 全体管理者
  - Intune 管理者
  - Power BI 管理者
  - 特権ロール管理者
  - セキュリティ管理者
  - SharePoint 管理者
  - Skype for Business 管理者
  - ユーザー管理者

詳細については、[Multi-Factor Authentication と Privileged Identity Management](pim-how-to-require-mfa.md) に関するページを参照してください。

## <a name="require-approval"></a>承認を要求する

ロールのアクティブ化に必要な承認を委任する場合は、次の手順に従います。

1. **[承認を要求する]** スイッチを **[有効]** に設定します。 ウィンドウが展開され、承認者を選択できるオプションが表示されます。

    ![[承認を要求する] スイッチを示すスクリーンショット。[有効化] が選択されています。](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    承認者を指定しない場合、特権ロール管理者は既定の承認者になり、このロールに対するすべてのアクティブ化要求を承認する必要があります。

1. 承認者を追加するには、 **[承認者の選択]** をクリックします。

    ![Azure AD ロール - 設定 - 承認を要求する](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. 特権ロール管理者に加えて、1 つまたは複数の承認者を選択し、 **[選択]** をクリックします。 少なくとも 2 人の承認者を追加することをお勧めします。 自分自身を承認者として追加した場合でも、ロールのアクティブ化を自己承認することはできません。 選択した承認者の一覧に選択内容が表示されます。

1. すべてのロール設定を指定したら、 **[保存]** を選択して変更を保存します。

---

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で Azure AD ロールに対するセキュリティ アラートを構成する](pim-how-to-configure-security-alerts.md)
