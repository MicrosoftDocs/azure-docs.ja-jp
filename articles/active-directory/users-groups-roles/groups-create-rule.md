---
title: 動的グループの作成と状態チェックを行う - Azure Active Directory | Microsoft Docs
description: Azure portal でグループ メンバーシップ ルールの作成と状態チェックを行う方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343acce228c38e38152fc2ea9d8fe0a59d8254d4
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193935"
---
# <a name="create-a-dynamic-group-and-check-status"></a>動的グループの作成と状態チェックを行う

Azure Active Directory (Azure AD) では、ユーザーまたはデバイスのプロパティに基づいてグループ メンバーシップを決定するルールを使用できます。 この記事では、Azure portal で動的グループのルールを設定する方法について説明します。
セキュリティ グループまたは Office 365 グループに対して、動的メンバーシップがサポートされています。 グループ メンバーシップのルールが適用されるときに、ユーザーとデバイスの属性がメンバーシップのルールと一致するかどうかが評価されます。 ユーザーまたはデバイスの属性が変更されると、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 ユーザーとデバイスは、グループの条件を満たす場合に、追加または削除されます。 セキュリティ グループはデバイスとユーザーのどちらにも使用できますが、Office 365 グループはユーザー グループのみが可能です。

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal のルール ビルダー

Azure AD には、重要なルールをすばやく作成したり更新したりできるルール ビルダーが用意されています。 ルール ビルダーでは、最大で 5 つの式の作成がサポートされます。 ルール ビルダーを使用すると、いくつかの単純な式を使ってルールを簡単に作成できますが、ルール ビルダーを使用してすべてのルールを再現することはできません。 作成したいルールがルール ビルダーでサポートされていない場合は、テキスト ボックスを使用できます。

以下に、テキスト ボックスを使用して作成することをお勧めする高度なルールまたは構文の例をいくつか示します。

- 5 つを超える式を持つルール
- 直接の部下のルール
- [演算子の優先順位](groups-dynamic-membership.md#operator-precedence)の設定
- [複雑な式を持つルール](groups-dynamic-membership.md#rules-with-complex-expressions) (例: `(user.proxyAddresses -any (_ -contains "contoso"))`)

> [!NOTE]
> ルール ビルダーは、テキスト ボックスで作成された一部のルールを表示できない場合があります。 ルール ビルダーがルールを表示できない場合は、メッセージが表示されることがあります。 ルール ビルダーは、動的グループ ルールのサポートされている構文、検証、または処理をどのような方法でも変更しません。

![動的グループのメンバーシップのルールを追加する](./media/groups-update-rule/update-dynamic-group-rule.png)

構文の例、メンバーシップ ルールでサポートされているプロパティ、演算子、および値については、「[Azure Active Directory の動的グループ メンバーシップ ルール](groups-dynamic-membership.md)」を参照してください。

## <a name="to-create-a-group-membership-rule"></a>グループ メンバーシップ ルールを作成するには

1. 全体管理者、Intune 管理者、またはテナントのユーザー管理者ロールであるアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてグループ]** を選び、 **[新しいグループ]** を選びます。

   ![新しいグループを追加するコマンドを選択する](./media/groups-create-rule/new-group-creation.png)

1. **[グループ]** ページで、新しいグループの名前と説明を入力します。 ユーザーまたはデバイスのいずれかに対して **[メンバーシップの種類]** を選択し、 **[動的クエリの追加]** を選択します。 ルール ビルダーでは、最大で 5 つの式がサポートされます。 5 つを超える式を追加するには、テキスト ボックスを使用する必要があります。

   ![動的グループのメンバーシップのルールを追加する](./media/groups-create-rule/add-dynamic-group-rule.png)

1. メンバーシップのクエリで使用できるカスタム拡張機能プロパティを表示するには、次のようにします。
   1. **[カスタム拡張機能のプロパティを取得します]** を選択します。
   1. アプリケーション ID を入力し、 **[プロパティの更新]** を選択します。
1. ルールを作成した後、 **[保存]** を選択します。
1. **[新規グループ]** ページで **[作成]** をクリックして、グループを作成します。

入力したルールが有効でない場合は、ルールを処理できなかった理由の説明がポータルの Azure 通知に表示されます。 ルールを修正する方法を理解するには、こちらを注意深くお読みください。

## <a name="turn-on-or-off-welcome-email"></a>ウェルカム メールをオンまたはオフにする

新しい Office 365 グループが作成されると、グループに追加されたユーザー宛にウェルカム通知が送信されます。 後で、ユーザーまたはデバイスのいずれかの属性が変更される場合は、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 追加されたユーザーは、ウェルカム通知も受け取ります。 この動作は、[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps) を使用して無効にすることができます。

## <a name="check-processing-status-for-a-rule"></a>ルールの処理状態をチェックする

グループの **[概要]** ページで、メンバーシップの処理状態と最終更新日を確認できます。
  
  ![動的グループの状態の表示](./media/groups-create-rule/group-status.png)

**[メンバーシップの処理]** の状態には、次の状態メッセージが表示される場合があります。

- **評価中**:グループの変更が受信され、更新プログラムが評価されています。
- **処理中**:更新プログラムが処理されています。
- **更新の完了**:処理が完了し、該当するすべての更新が行われました。
- **処理エラー**:メンバーシップのルールを評価するエラーが原因で、処理を完了できませんでした。
- **更新の一時停止**:動的メンバーシップ規則の更新プログラムが管理者によって一時停止されました。 MembershipRuleProcessingState は、"一時停止" に設定されます。

**[メンバーシップの最終更新日時]** の状態には、次の状態メッセージが表示される場合があります。

- &lt;**日付と時刻**&gt;:メンバーシップが最後に更新された日時。
- **進行中**: 更新は現在進行中です。
- **不明**:最終更新時刻を取得することができません。 新しいグループである可能性があります。

特定のグループのメンバーシップ規則の処理中にエラーが発生すると、そのグループの **[概要]** ページの上部にアラートが表示されます。 24 時間以上、テナント内のすべてのグループに対して保留中の動的メンバーシップの更新が処理できない場合は、 **[すべてグループ]** の上部にアラートが表示されます。

![処理エラー メッセージ アラート](./media/groups-create-rule/processing-error.png)

次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

- [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
- [新しいグループの作成とメンバーの追加](../fundamentals/active-directory-groups-create-azure-portal.md)
- [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)
