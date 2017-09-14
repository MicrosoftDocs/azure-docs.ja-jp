---
title: "Azure Active Directory で Office 365 グループの有効期限をプレビューする | Microsoft Docs"
description: "Azure Active Directory (プレビュー) で Office 365 グループの有効期限を設定する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: f4efac7b74620e68e727292e02a5a904b03d6e2f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---

# <a name="configure-office-365-groups-expiration-preview"></a>Office 365 グループの有効期限の構成 (プレビュー)

選択した Office 365 グループの有効期限を設定することで、Office 365 グループのライフサイクルを管理できます。 この有効期限を設定すると、それらのグループの所有者はグループが引き続き必要であればグループを更新するよう求められます。 更新されない Office 365 グループはすべて削除されます。 削除された Office 365 グループは、30 日以内であればグループの所有者または管理者が復元できます。  


> [!NOTE]
> Office 365 グループにのみ有効期限を設定できます。
>
> Office 365 グループの有効期限を設定するには、次のユーザーに Azure AD Premium ライセンスが割り当てられている必要があります。
>   - テナントの有効期限の設定を構成している管理者
>   - この設定が適用されるように選択されたグループに含まれるすべてのメンバー

## <a name="set-office-365-groups-expiration"></a>Office 365 グループの有効期限の設定

1. Azure AD テナントのグローバル管理者のアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com) を開きます。

2. Azure AD を開き、**[ユーザーとグループ]** を選択します。

3. **[グループ設定]** を選択し、**[有効期限]** を選択して有効期限の設定画面を開きます。
  
  ![[有効期限] ブレード](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. **[有効期限]** ブレードで、次のことができます。

  * グループの有効期間を日数で設定する。 プリセット値かカスタム値 (31 日以上である必要があります) のいずれかを選択する。 
  * グループに所有者がいない場合に、更新と有効期限の通知を送信する電子メール アドレスを指定する。 
  * どの Office 365 グループを有効期限切れにするかを選択する。 **[すべて]** の Office 365 グループに対して有効期限を有効にするか、Office 365 グループの中から選択するか、**[なし]** を選択してすべてのグループに対して有効期限を無効にすることができます。
  * **[保存]** を選択して完了し、設定を保存する。

Microsoft PowerShell モジュールをダウンロードしてインストールし、PowerShell を経由して Office 365 グループの有効期限を構成する方法については、「[Azure Active Directory V2 PowerShell Module - Public Preview Release 2.0.0.137 (Azure Active Directory V2 PowerShell モジュール - パブリック プレビュー リリース 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)」をご覧ください。

次のような電子メール通知が、グループの有効期限の 30 日前、15 日前、1 日前に Office 365 グループの所有者に送信されます。

![有効期限に関する電子メール通知](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

グループの所有者は、**[Renew group]\(グループの更新\)** を選択して Office 365 グループを更新できます。または **[Go to group]\(グループに移動\)** を選択してグループのメンバーとその他の詳細を確認できます。

グループが有効期限切れになると、有効期限日の 1 日後にグループが削除されます。 次のような電子メール通知が Office 365 グループの所有者に送信され、その Office 365 グループの有効期限とその後の削除について通知されます。

![グループの削除に関する電子メール通知](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

**[Restore group]\(グループの復元\)** を選択するか、または「Azure Active Directory で削除された Office 365 グループを復元する」 (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal) に記載されているように PowerShell コマンドレットを使用することで、グループを復元できます。
    
復元するグループにドキュメント、SharePoint サイト、または他の永続的なオブジェクトが含まれている場合は、グループとその内容を完全に復元するまでに最大 24 時間かかることがあります。

> [!NOTE]
> * 有効期限の設定をデプロイするときに、有効期限ウィンドウの期限よりも古いグループがある場合があります。 これらのグループはすぐに削除されることはありませんが、有効期限まで残り 30 日に設定されます。 最初の更新に関する通知電子メールは 1 日以内に送信されます。 たとえば、グループ A は 400 日前に作成され、有効期限の間隔は 180 日に設定されているとします。 有効期限の設定を適用すると、グループ A は所有者が更新しないかぎりは削除まで 30 日に設定されます。
> * 動的なグループを削除し復元する場合、そのグループは新しいグループとみなされ、規則に従って再度追加されます。 このプロセスには最大で 24 時間かかることがあります。

## <a name="next-steps"></a>次のステップ
次の記事では、Azure AD グループに関する追加情報が提供されています。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

