---
title: Azure AD ディレクトリを削除する - Azure Active Directory | Microsoft Docs
description: セルフサービス ディレクトリを含む Azure AD ディレクトリを削除するための準備方法について説明します
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607298"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Azure Active Directory でディレクトリを削除する

Azure AD ディレクトリを削除すると、そのディレクトリに含まれるリソースもすべて削除されます。 組織でその関連リソースを最小限にして準備してから、削除してください。 ポータルから Azure AD ディレクトリを削除できるのは、Azure Active Directory (Azure AD) グローバル管理者だけです。

## <a name="prepare-the-directory"></a>ディレクトリを準備する

いくつかのチェックに合格するまで、Azure AD のディレクトリを削除することはできません。 これらのチェックにより、Azure AD ディレクトリの削除がユーザーのアクセス (Office 365 へのサインインや Azure のリソースへのアクセスなど) に悪影響を及ぼすリスクが軽減されます。 たとえば、サブスクリプションに関連付けられたディレクトリが誤って削除された場合、ユーザーはそのサブスクリプションの Azure リソースにアクセスできなくなります。 次の条件がチェックされます。

* ディレクトリを削除するグローバル管理者 1 人を除き、ディレクトリ内にユーザーが存在しない。 ディレクトリを削除するには、他のすべてのユーザーを削除しておく必要があります。 ユーザーがオンプレミスから同期されている場合は、まず同期を無効にする必要があります。また、Azure portal または Azure PowerShell のコマンドレットを使用して、クラウド ディレクトリ内のユーザーを削除する必要があります。
* ディレクトリ内にアプリケーションが存在してはいけません。 ディレクトリを削除できるようにするには、すべてのアプリケーションを削除しておく必要があります。
* そのディレクトリにリンクされる多要素認証プロバイダーが存在しない。
* ディレクトリに関連付けられている、Microsoft Azure、Office 365、Azure AD Premium などの Microsoft Online Services のサブスクリプションが存在してはいけません。 たとえば、Azure で既定のディレクトリが作成されている場合、Azure サブスクリプションが認証にこのディレクトリを引き続き使用していれば、このディレクトリを削除することはできません。 同様に、別のユーザーがディレクトリにサブスクリプションを関連付けている場合、そのディレクトリを削除することはできません。

## <a name="delete-the-directory"></a>ディレクトリを削除する

1. 組織のグローバル管理者であるアカウントを使って、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 削除するディレクトリに切り替えます。
  
   ![削除する前に組織を確認します](./media/directory-delete-howto/delete-directory-command.png)

4. **[ディレクトリの削除]** を選択します。
  
   ![組織を削除するコマンドを選択します](./media/directory-delete-howto/delete-directory-list.png)

5. ディレクトリが 1 つ以上のチェックに合格しなかった場合は、合格する方法に関する詳細情報へのリンクが提示されます。 すべてのチェックに合格したら、**[削除]** を選択してプロセスを完了します。

## <a name="if-you-cant-delete-the-directory"></a>ディレクトリを削除できない場合

Azure AD ディレクトリを構成したときに、Azure AD Premium P2、Office 365 Business Premium、または Enterprise Mobility + Security E5 など、組織のライセンスベースのサブスクリプションもアクティブになっていた可能性があります。 偶発的なデータ損失を回避するため、サブスクリプションが完全に削除されるまではディレクトリを削除できません。 ディレクトリの削除を許可するには、サブスクリプションが**プロビジョニング解除済み**状態である必要があります。 **有効期限切れ**または**キャンセル済み**のサブスクリプションは、**無効** 状態に移行します。そして、最終段階が**プロビジョニング解除済み**状態です。

試用版の Office 365 サブスクリプション (有償のパートナー/CSP、Enterprise Agreement、ボリューム ライセンスは含まれません) の有効期限が切れたときに起こることについては、次の表を参照してください。 Office 365 のデータ保有とサブスクリプションのライフ サイクルの詳細については、「[一般法人向け Office 365 のサブスクリプションが終了したとき、データとアクセスはどうなりますか?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)」を参照してください。 

サブスクリプションの状態 | データ | データへのアクセス
----- | ----- | -----
アクティブ (試用版の 30 日間) | 誰もがアクセスできるデータ | ユーザーは Office 365 のファイルやアプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます 
有効期限切れ (30 日間) | 誰もがアクセスできるデータ| ユーザーは Office 365 のファイルやアプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます
無効 (30 日間) | 管理者だけがアクセスできるデータ | ユーザーは Office 365 のファイルやアプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスできますが、ライセンスを割り当てたり、ユーザーを更新したりすることはできません
プロビジョニング解除 (無効後 30 日間) | 削除されたデータ (使用中の他のサービスがない場合は自動的に削除) | ユーザーは Office 365 のファイルやアプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスして他のサブスクリプションの購入および管理ができます

## <a name="delete-a-subscription"></a>サブスクリプションを削除する

Microsoft 365 管理センターを使用して、サブスクリプションをプロビジョニング解除済み状態にして、3 日で削除されるようにすることができます。

1. 組織のグローバル管理者であるアカウントを使用して、[Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。 既定の初期ドメイン contoso.onmicrosoft.com を持つ "Contoso" ディレクトリを削除する場合は、admin@contoso.onmicrosoft.com などの UPN を使用してサインインします。

2. **[Billing]\(課金\)** をクリックし、**[サブスクリプション]** を選択してから、キャンセルするサブスクリプションを選択します。 **[キャンセル]** クリックすると、ページが更新されます。
  
   ![サブスクリプションを削除するための [削除] リンク](./media/directory-delete-howto/delete-command.png)
  
3. **[削除]** を選択してサブスクリプションを削除し、使用条件に同意します。 すべてのデータは 3 日以内に完全に削除されます。 気が変わった場合は、3 日の間にサブスクリプションを再アクティブ化することができます。
  
   ![使用条件を慎重に読みます](./media/directory-delete-howto/delete-terms.png)

4. サブスクリプションの状態が変更され、サブスクリプションが削除対象としてマークされています。 このサブスクリプションは、72 時間後に**プロビジョニング解除済み**状態になります。

5. ディレクトリのサブスクリプションを削除して 72 時間が経過すると、Azure AD 管理センターに再度サインインすることができます。要求されているアクションや、ディレクトリの削除をブロックしているサブスクリプションはなくなっているはずです。 Azure AD ディレクトリを正常に削除できます。
  
   ![削除画面でサブスクリプションのチェックに合格](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>削除をブロックする試用版サブスクリプションがある

Microsoft Power BI、Rights Management サービス、Microsoft Power Apps、Dynamics 365 などの[セルフサービス サインアップ製品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)では、個々のユーザーが Office 365 を通じてサインアップできるだけでなく、Azure AD ディレクトリ内に認証用のゲスト ユーザーを作成することもできます。 これらのセルフサービスの製品では、データ損失を回避するために、ディレクトリから完全に削除されるまでディレクトリの削除がブロックされます。 これらを削除できるのは、ユーザーが個別にサインアップしたか、製品を割り当てられたかに関係なく、Azure AD 管理者のみです。

セルフサービス サインアップ製品の割り当て方法は 2 とおりあります。 

* 組織レベルの割り当て: Azure AD 管理者が組織全体に製品を割り当てます。ユーザーは、個別にライセンスされていなくても、この組織レベルの割り当てにより、サービスを積極的に使用できます。
* ユーザー レベルの割り当て: セルフサービス サインアップ中は、基本的に個々のユーザーが管理者なしで自分自身に製品を割り当てています。組織が管理者によって管理されるようになると ([管理者による非管理対象ディレクトリの引き継ぎ](domains-admin-takeover.md)に関する記事を参照)、管理者はセルフサービス サインアップなしで直接ユーザーに製品を割り当てることができます。  

セルフサービス サインアップ製品の削除を開始すると、データは完全に削除され、そのサービスへのユーザー アクセスもすべて削除されます。 その後、個別または組織レベルでオファーが割り当てられたユーザーは、サインインしたり既存のデータにアクセスしたりできないようにブロックされます。 [Microsoft Power BI ダッシュボード](https://docs.microsoft.com/power-bi/service-export-to-pbix)や [Rights Management サービス ポリシー構成](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)などのセルフサービス サインアップ製品でのデータ損失を防止したい場合は、データを別の場所にバックアップして保存しておくようにしてください。

現在使用可能なセルフサービス サインアップ製品およびサービスの詳細については、「[利用可能なセルフサービス プログラム](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)」を参照してください。

試用版の Office 365 サブスクリプション (有償のパートナー/CSP、Enterprise Agreement、ボリューム ライセンスは含まれません) の有効期限が切れたときに起こることについては、次の表を参照してください。 Office 365 のデータ保持とサブスクリプションのライフサイクルの詳細については、「 [一般法人向け Office 365 のサブスクリプションが終了したとき、データとアクセスはどうなりますか?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)」を参照してください。

製品の状態 | データ | データへのアクセス
------------- | ---- | --------------
アクティブ (試用版の 30 日間) | 誰もがアクセスできるデータ | ユーザーはセルフサービス サインアップの製品、ファイル、アプリへの通常のアクセス権を持ちます<br>管理者は Microsoft 365 管理センターとリソースへの通常のアクセス権を持ちます
Deleted | 削除されたデータ | ユーザーはセルフサービス サインアップの製品、ファイル、アプリにアクセスできません<br>管理者は Microsoft 365 管理センターにアクセスして他のサブスクリプションの購入および管理ができます

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Azure portal でセルフサービス サインアップ製品を削除する方法を教えてください

Microsoft Power BI や Azure Rights Management サービスなどのセルフサービス サインアップ製品は、**[削除]** 状態にすると、Azure AD ポータル内ですぐに削除できます。

1. 組織のグローバル管理者であるアカウントを使用して、 [Azure AD 管理センター](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) にサインインします。 既定の初期ドメイン contoso.onmicrosoft.com を持つ "Contoso" ディレクトリを削除する場合は、admin@contoso.onmicrosoft.com などの UPN を使用してサインオンします。

2. **[ライセンス]** を選択し、 **[Self-service sign-up products]\(セルフサービス サインアップ製品\)** を選択します。 シートベースのサブスクリプションから、すべてのセルフサービス サインアップ製品を個別に確認できます。 完全に削除する製品を選択します。 Microsoft Power BI での例を次に示します。

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/licenses-page.png)

3.  **[削除]**  を選択して製品を削除し、データがすぐに完全に削除されることを示す条項に同意します。 この削除操作により、すべてのユーザーが削除され、組織から製品へのアクセス権も削除されます。 [はい] をクリックして削除を進めます。  

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/delete-product.png)

4. **[はい]** を選択すると、セルフサービス製品の削除が開始されます。 削除が進行中であることを示す通知が表示されます。  

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/progress-message.png)

5. これで、セルフサービス サインアップ製品の状態が **[削除済み]** に変更されました。 ページを最新の情報に更新すると、製品が **[Self-service sign-up products]\(セルフサービス サインアップ製品\)** ページから削除されます。  

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/product-deleted.png)

6. すべての製品を削除した後に Azure AD 管理センターにもう一度サインインすると、要求されているアクションや、ディレクトリの削除をブロックしている製品はなくなっているはずです。 Azure AD ディレクトリを正常に削除できます。

    ![ユーザー名が間違って入力されたか、ユーザー名が見つかりません](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>次の手順

[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory/)
