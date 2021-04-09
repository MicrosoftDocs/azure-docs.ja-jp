---
title: 動的グループを作成または編集し、状態を取得する - Azure AD | Microsoft Docs
description: Azure portal でグループ メンバーシップ ルールを作成または更新し、その処理状態を確認する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8525e9a76fa2439692cdb26b36c0bb761d63177c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550231"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Azure Active Directory で動的グループを作成または更新する

Azure Active Directory (Azure AD) では、ユーザーまたはデバイスのプロパティに基づいてグループ メンバーシップを決定するルールを使用できます。 この記事では、Azure portal で動的グループのルールを設定する方法について説明します。
セキュリティ グループまたは Microsoft 365 グループに対して、動的メンバーシップがサポートされています。 グループ メンバーシップのルールが適用されるときに、ユーザーとデバイスの属性がメンバーシップのルールと一致するかどうかが評価されます。 ユーザーまたはデバイスの属性が変更されると、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 ユーザーとデバイスは、グループの条件を満たす場合に、追加または削除されます。 セキュリティ グループはデバイスとユーザーのどちらにも使用できますが、Microsoft 365 グループはユーザー グループのみが可能です。 動的グループを使用するには、Azure AD プレミアム P1 ライセンスが必要です。 詳細については、[グループの動的メンバーシップ ルール](./groups-dynamic-membership.md)に関するページを参照してください。 

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal のルール ビルダー

Azure AD には、重要なルールをすばやく作成したり更新したりできるルール ビルダーが用意されています。 ルール ビルダーでは、最大で 5 つの式の作成がサポートされます。 ルール ビルダーを使用すると、いくつかの単純な式を使ってルールを簡単に作成できますが、すべてのルールの再現に使用することはできません。 作成したいルールがルール ビルダーでサポートされていない場合は、テキスト ボックスを使用できます。

以下に、テキスト ボックスを使用して構築することをお勧めする高度なルールまたは構文の例をいくつか示します。

- 5 つを超える式を持つルール
- 直接の部下のルール
- [演算子の優先順位](groups-dynamic-membership.md#operator-precedence)の設定
- [複雑な式を持つルール](groups-dynamic-membership.md#rules-with-complex-expressions) (例: `(user.proxyAddresses -any (_ -contains "contoso"))`)

> [!NOTE]
> ルール ビルダーは、テキスト ボックスで作成された一部のルールを表示できない場合があります。 ルール ビルダーがルールを表示できない場合は、メッセージが表示されることがあります。 ルール ビルダーは、動的グループ ルールのサポートされている構文、検証、または処理をどのような方法でも変更しません。

![[動的メンバーシップ ルール] ページを示すスクリーンショット。[ルールの構成] タブで [式の追加] アクションが選択されています。](./media/groups-create-rule/update-dynamic-group-rule.png)

構文の例、メンバーシップ ルールでサポートされているプロパティ、演算子、および値については、「[Azure Active Directory の動的グループ メンバーシップ ルール](groups-dynamic-membership.md)」を参照してください。

## <a name="to-create-a-group-membership-rule"></a>グループ メンバーシップ ルールを作成するには

1. グローバル管理者、Intune 管理者、または Azure AD 組織のユーザー管理者ロールであるアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を検索して選択します。
1. **[すべてグループ]** を選び、 **[新しいグループ]** を選びます。

   ![新しいグループを追加するコマンドを選択する](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. **[グループ]** ページで、新しいグループの名前と説明を入力します。 ユーザーまたはデバイスのいずれかに対して **[メンバーシップの種類]** を選択し、 **[動的クエリの追加]** を選択します。 ルール ビルダーでは、最大で 5 つの式がサポートされます。 5 つを超える式を追加するには、テキスト ボックスを使用する必要があります。

   ![[すべてのグループ] ページを示すスクリーンショット。[新しいグループ] アクションが選択されています。](./media/groups-create-rule/add-dynamic-group-rule.png)

1. メンバーシップのクエリで使用できるカスタム拡張機能プロパティを表示するには、次のようにします。
   1. **[カスタム拡張機能のプロパティを取得します]** を選択します。
   1. アプリケーション ID を入力し、 **[プロパティの更新]** を選択します。
1. ルールを作成した後、 **[保存]** を選択します。
1. **[新規グループ]** ページで **[作成]** をクリックして、グループを作成します。

入力したルールが有効でない場合は、ルールを処理できなかった理由の説明がポータルの Azure 通知に表示されます。 ルールを修正する方法を理解するには、こちらを注意深くお読みください。

## <a name="to-update-an-existing-rule"></a>既存のロールを更新するには

1. グローバル管理者、グループ管理者、Intune 管理者、または Azure AD 組織のユーザー管理者ロールであるアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. グループを選択して、そのプロファイルを開きます。
1. グループのプロファイル ページで、 **[動的メンバーシップ ルール]** を選択します。 ルール ビルダーでは、最大で 5 つの式がサポートされます。 5 つを超える式を追加するには、テキスト ボックスを使用する必要があります。

   ![動的グループのメンバーシップのルールを追加する](./media/groups-create-rule/update-dynamic-group-rule.png)

1. メンバーシップのルールで使用できるカスタム拡張機能プロパティを表示するには:
   1. **[カスタム拡張機能のプロパティを取得します]** を選択します。
   1. アプリケーション ID を入力し、 **[プロパティの更新]** を選択します。
1. ルールを更新した後、 **[保存]** を選択します。

## <a name="turn-on-or-off-welcome-email"></a>ウェルカム メールをオンまたはオフにする

新しい Microsoft 365 グループが作成されると、グループに追加されたユーザー宛にメールのウェルカム通知が送信されます。 後で、ユーザーまたはデバイスのいずれかの属性が変更される場合は、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 追加されたユーザーは、ウェルカム通知も受け取ります。 この動作は、[Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup) を使用してオフにすることができます。

## <a name="check-processing-status-for-a-rule"></a>ルールの処理状態をチェックする

グループの **[概要]** ページで、メンバーシップの処理状態と最終更新日を確認できます。
  
  ![動的グループの状態の表示](./media/groups-create-rule/group-status.png)

**[メンバーシップの処理]** の状態には、次の状態メッセージが表示される場合があります。

- **評価中**:グループの変更が受信され、更新プログラムが評価されています。
- **処理**: 更新プログラムが処理されています。
- **更新の完了**:処理が完了し、該当するすべての更新が行われました。
- **処理エラー**:メンバーシップのルール評価におけるエラーが原因で、処理を完了できませんでした。
- **更新の一時停止**:動的メンバーシップ規則の更新プログラムが管理者によって一時停止されました。 MembershipRuleProcessingState は、"一時停止" に設定されます。

**[メンバーシップの最終更新日時]** の状態には、次の状態メッセージが表示される場合があります。

- &lt;**日付と時刻**&gt;:メンバーシップが最後に更新された日時。
- **進行中**: 更新は現在進行中です。
- **不明**:最終更新時刻を取得することができません。 新しいグループである可能性があります。

特定のグループのメンバーシップ規則の処理中にエラーが発生すると、そのグループの **[概要]** ページの上部にアラートが表示されます。 24 時間以上、組織内のすべてのグループに対して保留中の動的メンバーシップの更新が処理できない場合は、 **[すべてグループ]** の上部にアラートが表示されます。

![処理エラー メッセージ アラート](./media/groups-create-rule/processing-error.png)

次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

- [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
- [新しいグループの作成とメンバーの追加](../fundamentals/active-directory-groups-create-azure-portal.md)
- [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)