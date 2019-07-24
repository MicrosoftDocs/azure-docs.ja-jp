---
title: Office 365 グループの有効期限の設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Office 365 グループの有効期限を設定する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c19ee3bdd14ee6a2c5b59294f475f6c18b570fa
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660258"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Office 365 グループの有効期限ポリシーの構成

Office 365 グループの有効期限ポリシーを設定して、そのライフサイクルを管理できるようになりました。 有効期限ポリシーを設定できるのは、Azure Active Directory (Azure AD) 内の Office 365 グループのみです。 

グループに有効期限を設定した場合:

- 有効期限が近づくと、グループの更新を促す通知がグループの所有者に送られます
- 更新されないグループはすべて削除されます
- 削除された Office 365 グループは、30 日以内であればグループの所有者または管理者が復元できます

現在、1 つのテナントの Office 365 グループで構成できる有効期限ポリシーは 1 つのみです。

> [!NOTE]
> Office 365 グループの有効期限ポリシーを構成および使用するには、有効期限ポリシーを適用するグループのすべてのメンバーの Azure AD Premium ライセンスが手元に必要です。

Azure AD PowerShell コマンドレットをダウンロードしてインストールする方法については、[Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) に関するページを参照してください。

## <a name="roles-and-permissions"></a>ロールとアクセス許可

Azure AD で Office 365 グループの有効期限を構成および使用できるロールを次に示します。

役割 | アクセス許可
-------- | --------
グローバル管理者またはユーザー管理者 | Office 365 グループの有効期限ポリシー設定の作成、読み取り、更新、または削除が可能です<br>任意の Office 365 グループを更新できます
User | 所有する Office 365 グループを更新できます<br>所有する Office 365 グループを復元できます<br>有効期限ポリシーの設定を読み取ることができます

削除したグループを復元するためのアクセス許可の詳細については、[Azure Active Directory で削除した Office 365 グループの復元](groups-restore-deleted.md)に関するページをご覧ください。

## <a name="set-group-expiration"></a>グループの有効期限の設定

1. Azure AD テナントのグローバル管理者のアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com) を開きます。

2. **[グループ]** を選択し、**[有効期限]** を選択して有効期限の設定を開きます。
  
   ![グループの有効期限の設定](./media/groups-lifecycle/expiration-settings.png)

3. **[有効期限]** ブレードで、次のことができます。

  * グループの有効期間を日数で設定する。 プリセット値かカスタム値 (31 日以上である必要があります) のいずれかを選択する。 
  * グループに所有者がいない場合に、更新と有効期限の通知を送信する電子メール アドレスを指定する。 
  * どの Office 365 グループを有効期限切れにするかを選択する。 **[すべて]** を選択してすべての Office 365 グループの有効期限を有効にするか、**[選択済み]** を選択して、選択された Office 365 グループのみを有効にするか、**[なし]** を選択してすべてのグループの有効期限を無効にできます。
  * **[保存]** を選択して完了し、設定を保存する。

> [!NOTE]
> * 有効期限を初めて設定する場合、有効期限の間隔よりも古いグループは、有効期限まで残り 30 日に設定されます。 最初の更新に関する通知電子メールは 1 日以内に送信されます。 たとえば、グループ A は 400 日前に作成され、有効期限の間隔は 180 日に設定されているとします。 有効期限の設定を適用すると、グループ A は所有者が更新しないかぎりは削除まで 30 日に設定されます。
> * 動的なグループを削除し復元する場合、そのグループは新しいグループとみなされ、規則に従って再度追加されます。 このプロセスには最大で 24 時間かかることがあります。

## <a name="email-notifications"></a>電子メール通知

次のような電子メール通知が、グループの有効期限の 30 日前、15 日前、1 日前に Office 365 グループの所有者に送信されます。 メールの言語は、グループ所有者の優先言語またはテナントの言語によって決まります。 グループの所有者が優先言語を定義している場合、または複数の所有者が同じ優先言語を持っている場合は、その言語が使用されます。 その他のすべてのケースでは、テナントの言語が使用されます。

![有効期限に関する電子メール通知](./media/groups-lifecycle/expiration-notification.png)

グループの所有者は、**グループの更新**通知電子メールから、アクセス パネルのグループ詳細ページに直接アクセスできます。 そこで、ユーザーは、グループについての詳細 (グループの説明、最終更新日時、有効期限が切れる日時など) や、グループを更新する機能についても知ることができます。 グループの詳細ページには、現在、Office 365 グループ リソースへのリンクも含まれているので、グループの所有者がそのグループの内容とアクティビティを簡単に確認できます。

グループが有効期限切れになると、有効期限日の 1 日後にグループが削除されます。 次のような電子メール通知が Office 365 グループの所有者に送信され、その Office 365 グループの有効期限とその後の削除について通知されます。

![グループの削除に関する電子メール通知](./media/groups-lifecycle/deletion-notification.png)

削除してから 30 日以内であれば、**[Restore group]\(グループの復元\)** を選択するか、または「[Azure Active Directory で削除された Office 365 グループを復元する](groups-restore-deleted.md)」に記載されている PowerShell コマンドレットを使用することで、グループを復元できます。 30 日間のグループの復元期間はカスタマイズできないことに注意してください。
    
復元するグループにドキュメント、SharePoint サイト、または他の永続的なオブジェクトが含まれている場合は、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Office 365 グループの有効期限を取得する方法
Office 365 グループの有効期限は、ユーザーが有効期限や最終更新日などのグループの詳細を表示できるアクセス パネルに加え、Microsoft Graph REST API ベータ版からも取得できます。 Microsoft Graph ベータ版では、グループ プロパティとして expirationDateTime が有効になりました。 これは GET 要求で取得できます。 詳細については、[こちらの例](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example)を参照してください。

> [!NOTE]
> アクセス パネルでグループのメンバーシップを管理するには、Azure Active Directory の [Groups General Setting]\(グループの全般設定\) で [Restrict access to Groups in Access Panel]\(アクセス パネルのグループへのアクセスを制限する\) を [いいえ] に設定する必要があります。


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 グループの有効期限と訴訟ホールドのメールボックスの連携
グループの有効期限が切れてグループが削除されると、削除の 30 日後に、グループのアプリ (Planner、サイト、チームなど) のデータが完全に削除されます。ただし、訴訟ホールドにあるグループ メールボックスは保持され、完全に削除されることはありません。 管理者は、Exchange コマンドレットを使用してメールボックスを復元し、データをフェッチできます。 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 グループの有効期限とアイテム保持ポリシーの連携
アイテム保持ポリシーは、セキュリティ/コンプライアンス センターを使用して構成されます。 Office 365 グループに対してアイテム保持ポリシーを設定した場合は、グループの有効期限が切れてグループが削除されると、グループ メールボックス内のグループのメッセージ交換とグループ サイト内のファイルは、アイテム保持ポリシーで定義されている特定の日数の間、保持コンテナーに保持されます。 有効期限が過ぎた後、ユーザーにはグループやその内容が表示されませんが、E-Discovery を使用してサイトとメールボックスのデータを復旧できます。

## <a name="powershell-examples"></a>PowerShell の例
PowerShell コマンドレットを使用して、テナントの Office 365 グループの有効期限の設定を構成する方法の例を次に示します。

1. PowerShell v2.0 プレビュー モジュール (2.0.0.137) をインストールし、PowerShell プロンプトでサインインします。
   ```powershell
   Install-Module -Name AzureADPreview
   connect-azuread 
   ```
2. 有効期限の設定を構成する New-AzureADMSGroupLifecyclePolicy:このコマンドレットにより、テナント内のすべての Office 365 グループの有効期間が 365 日間に設定されます。 所有者がいない Office 365 グループの更新通知は、"emailaddress@contoso.com" に送信されます。
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. 既存のポリシーを取得する Get-AzureADMSGroupLifecyclePolicy:このコマンドレットにより、現在の構成済み Office 365 グループの有効期限の設定が取得されます。 この例では、次のものを確認できます。
   * ポリシー ID 
   * テナント内のすべての Office 365 グループの有効期間が 365 日に設定されていること
   * 所有者がいない Office 365 グループの更新通知は、"emailaddress@contoso.com" に送信されます
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. 既存のポリシーを更新する Get-AzureADMSGroupLifecyclePolicy:このコマンドレットは、既存のポリシーの更新に使用されます。 次の例では、グループの既存ポリシーの有効期間が 365 日から 180 日に変更されます。 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. 特定のグループをポリシーに追加する Add-AzureADMSLifecyclePolicyGroup:このコマンドレットにより、グループがライフサイクル ポリシーに追加されます。 例
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. 既存のポリシーを削除する Remove-AzureADMSGroupLifecyclePolicy:このコマンドレットにより、Office 365 グループの有効期限の設定が削除されます。ただし、ポリシー ID が必要です。 これで、Office 365 グループの有効期限が無効になります。 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
次のコマンドレットは、ポリシーをさらに細かく構成するために使用できます。 詳細については、[PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)を参照してください。

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>次の手順
次の記事では、Azure AD グループに関する追加情報が提供されています。

* [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](../fundamentals/active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)
