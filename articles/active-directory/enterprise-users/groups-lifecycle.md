---
title: Microsoft 365 グループの有効期限の設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Microsoft 365 グループの有効期限を設定する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a2a69b5013ab8e1bc4637f21ce484a132d37834
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455714"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Microsoft 365 グループの有効期限ポリシーを構成する

この記事では、Microsoft 365 グループの有効期限ポリシーを設定して、そのライフサイクルを管理する方法について説明します。 有効期限ポリシーを設定できるのは、Azure Active Directory (Azure AD) 内の Microsoft 365 グループのみです。

グループに有効期限を設定した場合:

- ユーザー アクティビティがあるグループは、有効期限が近づくと自動的に更新されます。
- グループが自動更新されない場合、グループの所有者には、グループを更新するように通知されます。
- 更新されないグループはすべて削除されます。
- 削除された Microsoft 365 グループは、30 日以内であればグループの所有者または管理者が復元できます。

現在、Azure AD 組織内のすべての Microsoft 365 グループに対して構成できる有効期限ポリシーは 1 つのみです。

> [!NOTE]
> Microsoft 365 グループの有効期限ポリシーを構成および使用するには、有効期限ポリシーを適用するすべてのグループのメンバーの Azure AD Premium ライセンスを所有している必要がありますが、必ずしも割り当てる必要はありません。

Azure AD PowerShell コマンドレットをダウンロードしてインストールする方法については、[Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) に関するページを参照してください。

## <a name="activity-based-automatic-renewal"></a>アクティビティベースの自動更新

Azure AD インテリジェンスを使うと、最近使用されたかどうかに基づいてグループが自動的に更新されるようになります。 この機能は、Outlook、SharePoint、Teams などの Microsoft 365 サービス全体のグループに含まれるユーザー アクティビティに基づいているため、グループ所有者による手動操作は不要になります。 たとえば、所有者またはグループ メンバーが、SharePoint でドキュメントをアップロードする、Teams チャンネルにアクセスする、Outlook でグループにメールを送信するなどの操作を行うと、グループはその有効期限が終了する 35 日前くらいに自動的に更新され、所有者には更新通知が送信されません。

### <a name="activities-that-automatically-renew-group-expiration"></a>グループの有効期限を自動的に更新するアクティビティ

次のユーザー操作により、グループの自動更新が行われます。

- SharePoint:ファイルの表示、編集、ダウンロード、移動、共有、またはアップロード
- Outlook:グループへの参加、グループ領域からのグループ メッセージの読み取り/書き込み、メッセージへの "いいね!" の追加 (Outlook Web Access)
- Teams:チーム チャネルへのアクセス

### <a name="auditing-and-reporting"></a>監査とレポート

管理者は、Azure AD のアクティビティ監査ログから自動的に更新されたグループの一覧を取得できます。

![アクティビティに基づくグループの自動更新](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>ロールとアクセス許可

Azure AD で Microsoft 365 グループの有効期限を構成および使用できるロールを次に示します。

Role | アクセス許可
-------- | --------
グローバル管理者、グループ管理者、またはユーザー管理者 | Microsoft 365 グループの有効期限ポリシー設定の作成、読み取り、更新、または削除が可能です<br>任意の Microsoft 365 グループを更新できます
User | 自分が所有する Microsoft 365 グループを更新できます<br>自分が所有する Microsoft 365 グループを復元できます<br>有効期限ポリシーの設定を読み取ることができます

削除したグループを復元するためのアクセス許可の詳細については、[Azure Active Directory で削除した Microsoft 365 グループの復元](groups-restore-deleted.md)に関するページを参照してください。

## <a name="set-group-expiration"></a>グループの有効期限の設定

1. Azure AD 組織のグローバル管理者のアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)を開きます。

2. **[グループ]** を選択し、 **[有効期限]** を選択して有効期限の設定を開きます。
  
   ![グループの有効期限の設定](./media/groups-lifecycle/expiration-settings.png)

3. **[有効期限]** ページで、次のことができます。

    - グループの有効期間を日数で設定する。 プリセット値かカスタム値 (30 日以上である必要があります) のいずれかを選択する。
    - グループに所有者がいない場合に、更新と有効期限の通知を送信する電子メール アドレスを指定する。
    - 有効期限が切れる Microsoft 365 グループを選択します。 有効期限は次のように設定できます。
      - **[すべて]** の Microsoft 365 グループ
      - **[選択済み]** の Microsoft 365 グループの一覧
      - **[なし]** : すべてのグループの有効期限を制限しない場合
    - **[保存]** を選択して完了し、設定を保存する。

> [!NOTE]
> - 有効期限を初めて設定すると、有効期限の間隔よりも古いグループは、グループが自動的に更新されるか所有者が更新しない限り、有効期限まで 35 日間に設定されます。
> - 動的なグループを削除し復元する場合、そのグループは新しいグループとみなされ、規則に従って再度追加されます。 このプロセスには最大で 24 時間かかります。
> - Teams で使用されるグループの有効期限の通知は、Teams の所有者フィードに表示されます。

## <a name="email-notifications"></a>メール通知

グループが自動的に更新されない場合、このようなメール通知は、グループの有効期限の 30 日前、15 日前、および 1 日前に Microsoft 365 グループ所有者に送信されます。 メールの言語は、グループ所有者の優先言語または Azure AD の言語設定によって決まります。 グループの所有者が優先言語を定義している場合、または複数の所有者が同じ優先言語を持っている場合は、その言語が使用されます。 それ以外のすべての場合は、Azure AD の言語設定が使用されます。

![有効期限に関する電子メール通知](./media/groups-lifecycle/expiration-notification.png)

グループの所有者は、**グループの更新** 通知電子メールから、[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/applications)のグループ詳細ページに直接アクセスできます。 そこで、ユーザーは、グループについての詳細 (グループの説明、最終更新日時、有効期限が切れる日時など) や、グループを更新する機能についても知ることができます。 グループの詳細ページには、現在、Microsoft 365 グループ リソースへのリンクも含まれているので、グループの所有者がそのグループの内容とアクティビティを簡単に確認できます。

グループが有効期限切れになると、有効期限日の 1 日後にグループが削除されます。 次のような電子メール通知が Microsoft 365 グループの所有者に送信され、その Microsoft 365 グループの有効期限とその後の削除について通知されます。

![グループの削除に関する電子メール通知](./media/groups-lifecycle/deletion-notification.png)

削除してから 30 日以内であれば、 **[Restore group]\(グループの復元\)** を選択するか、または「[Azure Active Directory で削除された Microsoft 365 グループを復元する](groups-restore-deleted.md)」に記載されている PowerShell コマンドレットを使用することで、グループを復元できます。 30 日間のグループの復元期間はカスタマイズできないことに注意してください。

復元するグループにドキュメント、SharePoint サイト、または他の永続的なオブジェクトが含まれている場合は、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Microsoft 365 グループの有効期限日を取得する方法

Microsoft 365 グループの有効期限は、ユーザーが有効期限や最終更新日などのグループの詳細を表示できるアクセス パネルに加え、Microsoft Graph REST API ベータ版からも取得できます。 Microsoft Graph ベータ版では、グループ プロパティとして expirationDateTime が有効になりました。 これは GET 要求で取得できます。 詳細については、[こちらの例](/graph/api/group-get?view=graph-rest-beta#example)を参照してください。

> [!NOTE]
> アクセス パネルでグループのメンバーシップを管理するには、Azure Active Directory の [Groups General Setting]\(グループの全般設定\) で [Restrict access to Groups in Access Panel]\(アクセス パネルのグループへのアクセスを制限する\) を [いいえ] に設定する必要があります。

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Microsoft 365 グループの有効期限と訴訟ホールドのメールボックスの連携

グループの有効期限が切れてグループが削除されると、削除の 30 日後に、グループのアプリ (Planner、サイト、チームなど) のデータが完全に削除されます。ただし、訴訟ホールドにあるグループ メールボックスは保持され、完全に削除されることはありません。 管理者は、Exchange コマンドレットを使用してメールボックスを復元し、データをフェッチできます。

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Microsoft 365 グループの有効期限とアイテム保持ポリシーの連携

アイテム保持ポリシーは、セキュリティ/コンプライアンス センターを使用して構成されます。 Microsoft 365 グループに対してアイテム保持ポリシーを設定した場合は、グループの有効期限が切れてグループが削除されると、グループ メールボックス内のグループのメッセージ交換とグループ サイト内のファイルは、アイテム保持ポリシーで定義されている特定の日数の間、保持コンテナーに保持されます。 有効期限が過ぎた後、ユーザーにはグループやその内容が表示されませんが、E-Discovery を使用してサイトとメールボックスのデータを復旧できます。

## <a name="powershell-examples"></a>PowerShell の例

PowerShell コマンドレットを使用して、Azure AD 組織の Microsoft 365 グループの有効期限の設定を構成する方法の例を次に示します。

1. PowerShell v2.0 モジュールをインストールし、PowerShell プロンプトでサインインします。

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 有効期限の設定を構成します。New-AzureADMSGroupLifecyclePolicy コマンドレットを使用して、Azure AD 組織内のすべての Microsoft 365 グループの有効期間を 365 日間に設定します。 所有者がいない Microsoft 365 グループの更新通知は、"emailaddress@contoso.com" に送信されます
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 既存のポリシーを取得する Get-AzureADMSGroupLifecyclePolicy:このコマンドレットにより、現在の構成済み Microsoft 365 グループの有効期限の設定が取得されます。 この例では、次のものを確認できます。

   - ポリシー ID
   - Azure AD 組織内のすべての Microsoft 365 グループの有効期間が 365 日に設定されていること
   - 所有者がいない Microsoft 365 グループの更新通知は、"emailaddress@contoso.com" に送信されます
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 既存のポリシーを更新する Get-AzureADMSGroupLifecyclePolicy:このコマンドレットは、既存のポリシーの更新に使用されます。 次の例では、グループの既存ポリシーの有効期間が 365 日から 180 日に変更されます。
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 特定のグループをポリシーに追加する Add-AzureADMSLifecyclePolicyGroup:このコマンドレットにより、グループがライフサイクル ポリシーに追加されます。 例
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 既存のポリシーを削除する Remove-AzureADMSGroupLifecyclePolicy:このコマンドレットにより、Microsoft 365 グループの有効期限の設定が削除されます。ただし、ポリシー ID が必要です。 このコマンドレットを実行すると、Microsoft 365 グループの有効期限が無効になります。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
次のコマンドレットは、ポリシーをさらに細かく構成するために使用できます。 詳細については、[PowerShell のドキュメント](/powershell/module/azuread/?view=azureadps-2.0-preview#groups)を参照してください。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>次のステップ

次の記事では、Azure AD グループに関する追加情報が提供されています。

- [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
- [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [グループのメンバーの管理](../fundamentals/active-directory-groups-members-azure-portal.md)
- [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)
