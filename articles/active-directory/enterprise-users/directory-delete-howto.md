---
title: Azure AD 組織 (テナント) を削除する - Azure Active Directory | Microsoft Docs
description: セルフサービス組織を含む Azure AD 組織 (テナント)を削除するための準備方法について説明します
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edc6fb98359c5360836bc369e5ae1928464df92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861032"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>Azure Active Directory でテナントを削除する

Azure AD 組織 (テナント) を削除すると、その組織に含まれるリソースもすべて削除されます。 組織でその関連リソースを最小限にして準備してから、削除してください。 ポータルから Azure AD 組織を削除できるのは、Azure Active Directory (Azure AD) グローバル管理者だけです。

## <a name="prepare-the-organization"></a>組織を準備する

いくつかのチェックに合格するまで、Azure AD の組織を削除することはできません。 これらのチェックにより、Azure AD 組織の削除がユーザーのアクセス (Microsoft 365 へのサインインや Azure のリソースへのアクセスなど) に悪影響を及ぼすリスクが軽減されます。 たとえば、サブスクリプションに関連付けられた組織が誤って削除された場合、ユーザーはそのサブスクリプションの Azure リソースにアクセスできなくなります。 次の条件がチェックされます。

* 組織を削除するグローバル管理者 1 人を除き、ユーザーが Azure AD 組織 (テナント) 内に存在しないこと。 組織を削除するには、他のすべてのユーザーを削除しておく必要があります。 ユーザーがオンプレミスから同期されている場合は、まず同期を無効にする必要があります。また、Azure portal または Azure PowerShell のコマンドレットを使用して、クラウド組織内のユーザーを削除する必要があります。
* 組織内にアプリケーションが存在しないこと。 組織を削除できるようにするには、すべてのアプリケーションを削除しておく必要があります。
* その組織にリンクされる多要素認証プロバイダーが存在しない。
* 組織に関連付けられている、Microsoft Online Services のサブスクリプション (Microsoft Azure、Microsoft 365、Azure AD Premium など) が存在しないこと。 たとえば、Azure で既定の Azure AD 組織が作成されている場合、Azure サブスクリプションが認証にこの組織を引き続き使用していれば、この組織を削除することはできません。 同様に、別のユーザーが組織にサブスクリプションを関連付けている場合、その組織を削除することはできません。

## <a name="delete-the-organization"></a>組織を削除する

1. 組織のグローバル管理者であるアカウントを使って、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 削除する組織に切り替えます。
  
   ![削除する前に組織を確認します](./media/directory-delete-howto/delete-directory-command.png)

4. **[テナントの削除]** を選択します。
  
   ![組織を削除するコマンドを選択します](./media/directory-delete-howto/delete-directory-list.png)

5. 組織が 1 つ以上のチェックに合格しなかった場合は、合格する方法に関する詳細情報へのリンクが提示されます。 すべてのチェックに合格したら、 **[削除]** を選択してプロセスを完了します。

## <a name="if-you-cant-delete-the-organization"></a>組織を削除できない場合

Azure AD 組織を構成したときに、Azure AD Premium P2、Microsoft 365 Business Standard、または Enterprise Mobility + Security E5 など、組織のライセンスベースのサブスクリプションもアクティブになっていた可能性があります。 偶発的なデータ損失を回避するため、サブスクリプションが完全に削除されるまでは組織を削除できません。 組織の削除を許可するには、サブスクリプションが **プロビジョニング解除済み** 状態である必要があります。 **有効期限切れ** または **キャンセル済み** のサブスクリプションは、**無効** 状態に移行します。そして、最終段階が **プロビジョニング解除済み** 状態です。

試用版の Microsoft 365 サブスクリプション (有償のパートナー/CSP、Enterprise Agreement、ボリューム ライセンスは含まれません) の有効期限が切れたときに起こることについては、次の表を参照してください。 Microsoft 365 のデータ保有とサブスクリプションのライフ サイクルの詳細については、「[一般法人向け Microsoft 365 のサブスクリプションが終了したとき、データとアクセスはどうなりますか?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)」を参照してください。 

サブスクリプションの状態 | Data | データへのアクセス
----- | ----- | -----
アクティブ (試用版の 30 日間) | 誰もがアクセスできるデータ | ユーザーは Microsoft 365 のファイルやアプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます 
有効期限切れ (30 日間) | 誰もがアクセスできるデータ| ユーザーは Microsoft 365 のファイルやアプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます
無効 (30 日間) | 管理者だけがアクセスできるデータ | ユーザーは Microsoft 365 のファイルやアプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスできますが、ライセンスを割り当てたり、ユーザーを更新したりすることはできません
プロビジョニング解除 (無効後 30 日間) | 削除されたデータ (使用中の他のサービスがない場合は自動的に削除) | ユーザーは Microsoft 365 のファイルやアプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスして他のサブスクリプションの購入および管理ができます

## <a name="delete-a-subscription"></a>サブスクリプションを削除する

Microsoft 365 管理センターを使用して、サブスクリプションを **プロビジョニング解除済み** 状態にして、3 日で削除されるようにすることができます。

1. 組織のグローバル管理者であるアカウントを使用して、[Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。 既定の初期ドメイン contoso.onmicrosoft.com を持つ "Contoso" 組織を削除する場合は、admin@contoso.onmicrosoft.com などの UPN を使用してサインインします。

2. **[新しい管理センターを使ってみる]** トグルが有効になっていることを確認し、新しい Microsoft 365 管理センターをプレビューします。

   ![新しい M365 管理センターのエクスペリエンスをプレビューする](./media/directory-delete-howto/preview-toggle.png)

3. 新しい管理センターを有効にしたら、サブスクリプションを削除する前に、取り消す必要があります。 **[課金]** を選択し、 **[製品とサービス]** を選択してから、キャンセルするサブスクリプションの **[サブスクリプションのキャンセル]** を選択します。 フィードバック ページが表示されます。

   ![取り消すサブスクリプションを選択する](./media/directory-delete-howto/cancel-choose-subscription.png)

4. フィードバック フォームに入力し、 **[サブスクリプションのキャンセル]** を選択してサブスクリプションをキャンセルします。

   ![サブスクリプション プレビューのキャンセル コマンド](./media/directory-delete-howto/cancel-command.png)

5. これで、サブスクリプションを削除できます。 削除するサブスクリプションの **[削除]** を選択します。 **[製品とサービス]** ページでサブスクリプションが見つからない場合は、 **[サブスクリプションの状態]** を **[すべて]** に設定してあることを確認してください。

   ![サブスクリプションを削除するための [削除] リンク](./media/directory-delete-howto/delete-command.png)

6. **[サブスクリプションの削除]** を選択してサブスクリプションを削除し、使用条件に同意します。 すべてのデータは 3 日以内に完全に削除されます。 気が変わった場合は、3 日の間に[サブスクリプションを再アクティブ化する](/office365/admin/subscriptions-and-billing/reactivate-your-subscription)ことができます。
  
   ![使用条件を慎重に読みます](./media/directory-delete-howto/delete-terms.png)

7. サブスクリプションの状態が変更され、サブスクリプションが削除対象としてマークされています。 このサブスクリプションは、72 時間後に **プロビジョニング解除済み** 状態になります。

8. 組織のサブスクリプションを削除して 72 時間が経過すると、Azure AD 管理センターに再度サインインすることができます。要求されているアクションや、組織の削除をブロックしているサブスクリプションはなくなっているはずです。 Azure AD 組織を正常に削除できます。
  
   ![削除画面でサブスクリプションのチェックに合格](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>削除をブロックする試用版サブスクリプションがある

Microsoft Power BI、Rights Management サービス、Microsoft Power Apps、Dynamics 365 などの[セルフサービス サインアップ製品](/office365/admin/misc/self-service-sign-up)では、個々のユーザーが Microsoft 365 を通じてサインアップできるだけでなく、Azure AD 組織内に認証用のゲスト ユーザーを作成することもできます。 これらのセルフサービスの製品では、データ損失を回避するために、組織から製品が完全に削除されるまでディレクトリの削除がブロックされます。 これらを削除できるのは、ユーザーが個別にサインアップしたか、製品を割り当てられたかに関係なく、Azure AD 管理者のみです。

セルフサービス サインアップ製品の割り当て方法は 2 とおりあります。 

* 組織レベルの割り当て: Azure AD 管理者が組織全体に製品を割り当てます。ユーザーは、個別にライセンスされていなくても、この組織レベルの割り当てにより、サービスを積極的に使用できます。
* ユーザー レベルの割り当て: セルフサービス サインアップ中は、基本的に個々のユーザーが管理者なしで自分自身に製品を割り当てています。組織が管理者によって管理されるようになると ([管理者による非管理対象組織の引き継ぎ](domains-admin-takeover.md)に関する記事を参照)、管理者はセルフサービス サインアップなしで直接ユーザーに製品を割り当てることができます。  

セルフサービス サインアップ製品の削除を開始すると、データは完全に削除され、そのサービスへのユーザー アクセスもすべて削除されます。 その後、個別または組織レベルでオファーが割り当てられたユーザーは、サインインしたり既存のデータにアクセスしたりできないようにブロックされます。 [Microsoft Power BI ダッシュボード](/power-bi/service-export-to-pbix)や [Rights Management サービス ポリシー構成](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)などのセルフサービス サインアップ製品でのデータ損失を防止したい場合は、データを別の場所にバックアップして保存しておくようにしてください。

現在使用可能なセルフサービス サインアップ製品およびサービスの詳細については、「[利用可能なセルフサービス プログラム](/office365/admin/misc/self-service-sign-up#available-self-service-programs)」を参照してください。

試用版の Microsoft 365 サブスクリプション (有償のパートナー/CSP、Enterprise Agreement、ボリューム ライセンスは含まれません) の有効期限が切れたときに起こることについては、次の表を参照してください。 Microsoft 365 のデータ保有とサブスクリプションのライフ サイクルの詳細については、「[一般法人向け Microsoft 365 のサブスクリプションが終了したとき、データとアクセスはどうなりますか?](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires)」を参照してください。

製品の状態 | Data | データへのアクセス
------------- | ---- | --------------
アクティブ (試用版の 30 日間) | 誰もがアクセスできるデータ | ユーザーはセルフサービス サインアップの製品、ファイル、アプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます
Deleted | 削除されたデータ | ユーザーはセルフサービス サインアップの製品、ファイル、アプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスして他のサブスクリプションの購入および管理ができます

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure portal でセルフサービス サインアップ製品を削除する方法を教えてください

Microsoft Power BI や Azure Rights Management サービスなどのセルフサービス サインアップ製品は、 **[削除]** 状態にすると、Azure AD ポータル内ですぐに削除できます。

1. 組織のグローバル管理者アカウントで、[Azure AD 管理センター](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。 既定の初期ドメイン contoso.onmicrosoft.com を持つ "Contoso" 組織を削除する場合は、admin@contoso.onmicrosoft.com などの UPN を使用してサインインします。

2. **[ライセンス]** を選択し、 **[Self-service sign-up products]\(セルフサービス サインアップ製品\)** を選択します。 シートベースのサブスクリプションから、すべてのセルフサービス サインアップ製品を個別に確認できます。 完全に削除する製品を選択します。 Microsoft Power BI での例を次に示します。

    ![[ライセンス - セルフサービス サインアップ製品] ページを示すスクリーンショット。](./media/directory-delete-howto/licenses-page.png)

3. **[削除]** を選択して製品を削除し、データがすぐに完全に削除されることを示す条項に同意します。 この削除操作により、すべてのユーザーが削除され、組織から製品へのアクセス権も削除されます。 [はい] をクリックして削除を進めます。  

    ![[ライセンス - セルフサービス サインアップ製品] ページで開いた [セルフサービス サインアップ製品の削除] ウィンドウを示すスクリーンショット。](./media/directory-delete-howto/delete-product.png)

4. **[はい]** を選択すると、セルフサービス製品の削除が開始されます。 削除が進行中であることを示す通知が表示されます。  

    ![[ライセンス - セルフサービス サインアップ製品] ページで表示された "削除の進行中" の通知を示すスクリーンショット。](./media/directory-delete-howto/progress-message.png)

5. これで、セルフサービス サインアップ製品の状態が **[削除済み]** に変更されました。 ページを最新の情報に更新すると、製品が **[Self-service sign-up products]\(セルフサービス サインアップ製品\)** ページから削除されます。  

    ![[ライセンス - セルフサービス サインアップ製品] ページの右側の "セルフサービス サインアップ製品が削除されました" というウィンドウを示すスクリーンショット。](./media/directory-delete-howto/product-deleted.png)

6. すべての製品を削除した後に Azure AD 管理センターにもう一度サインインすると、要求されているアクションや、組織の削除をブロックしている製品はなくなっているはずです。 Azure AD 組織を正常に削除できます。

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>次のステップ

[Azure Active Directory のドキュメント](../index.yml)
