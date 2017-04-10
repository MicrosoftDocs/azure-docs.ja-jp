---
title: "Azure Active Directory で他のディレクトリまたはパートナー会社からユーザーを追加する | Microsoft Docs"
description: "Azure Active Directory で、外部ユーザーとゲスト ユーザーを含む、ユーザーの追加方法またはユーザー情報の変更方法を説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 7b506668b3025774edec25b6a37b83a47d702cb0
ms.openlocfilehash: b2eaddc631087d8b7b94e40f508bdd5da5229736
ms.lasthandoff: 01/31/2017


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Azure Active Directory で他のディレクトリまたはパートナー会社からユーザーを追加する
> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-users-create-external-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-create-users-external.md)
>
>

この記事では、Azure Active Directory の他のディレクトリからユーザーを追加する方法、またはパートナー会社からユーザーを追加する方法について説明します。 組織内の新しいユーザーの追加、および Microsoft アカウントを持つユーザーの追加については、 [Azure Active Directory への新しいユーザーの追加](active-directory-create-users.md)に関するページを参照してください。 既定では、追加されたユーザーに管理者のアクセス許可はありませんが、ロールはいつでも割り当てることができます。

## <a name="add-a-user"></a>ユーザーの追加
1. ディレクトリのグローバル管理者となっているアカウントで [Azure クラシック ポータル](https://manage.windowsazure.com) にサインインします。
2. **[Active Directory]**を選択し、ディレクトリを開きます。
3. **[ユーザー]** タブを選択し、コマンド バーの **[ユーザーの追加]** をクリックします。
4. **[このユーザーに関する情報の入力]** ページの **[ユーザーの種類]** で次のいずれかを選択します。

   * **[別の Microsoft Azure AD ディレクトリのユーザー]** - 別の Azure AD ディレクトリに属しているユーザー アカウントを対象のディレクトリに追加します。 別のディレクトリでユーザーを選択できるのは、そのディレクトリでもメンバーになっている場合のみです。
   * **[パートナー会社のユーザー]** - ディレクトリにパートナー会社のユーザーを招待して承認します ( [Azure Active Directory B2B コラボレーション](active-directory-b2b-what-is-azure-ad-b2b.md)に関するページを参照)。 [電子メール アドレスを指定して CSV ファイルをアップロードすること](active-directory-b2b-references-csv-file-format.md)が必要になります。
5. ユーザーの **[プロファイル]** ページで、姓と名およびユーザー フレンドリ名を入力し、**[ロール]** ボックスの一覧からユーザー ロールを選択します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。 ユーザーの **Multi-Factor Authentication を有効にする** かどうかを指定します。
6. **[一時パスワードの取得]** ページで、**[作成]** を選択します。

> [!IMPORTANT]
> 組織が複数のドメインを使用している場合は、ユーザー アカウントを追加するときに起きる、次の問題について理解しておく必要があります。
>
> * 複数のドメインで同じユーザー プリンシパル名 (UPN) を持つユーザー アカウントを追加するには、たとえば、**最初に** geoffgrisso@contoso.onmicrosoft.com, を追加し、**その後に** geoffgrisso@contoso.com を追加します。
> * geoffgrisso@contoso.onmicrosoft.com を追加する前に geoffgrisso@contoso.com を追加**しないでください**。 この順序は重要であり、なかなか元に戻すことができない場合があります。
>
>

オンプレミスの Active Directory サービスに ID が同期されているユーザーの情報を変更する場合、Azure クラシック ポータルでユーザー情報を変更することはできません。 このユーザー情報を変更するには、オンプレミスの Active Directory 管理ツールを使用してください。

## <a name="add-external-users"></a>外部ユーザーの追加
自分が属している別の Azure AD ディレクトリからユーザーを追加することや、CSV ファイルをアップロードしてパートナー会社からユーザーを追加することもできます。 外部ユーザーを追加するには、**[ユーザーの種類]** で **[別の Microsoft Azure AD ディレクトリのユーザー]** または **[パートナー会社のユーザー]** を指定します。

どちらの種類のユーザーも別のディレクトリに属しており、**外部ユーザー**として追加されます。 外部ユーザーは、ディレクトリ内の他のユーザーと共同作業できます。新しいアカウントや資格情報を追加する必要はありません。 外部ユーザーは、サインイン時に自分のホーム ディレクトリで認証されます。この認証は、外部ユーザーが追加された他のすべてのディレクトリで有効になります。

## <a name="external-user-management-and-limitations"></a>外部ユーザーの管理と制限事項
ディレクトリに別のディレクトリのユーザーを追加すると、そのユーザーは追加先のディレクトリでは外部ユーザーとなります。 表示名とユーザー名がユーザーのホーム ディレクトリからコピーされ、追加先のディレクトリ内の外部ユーザーに使用されます。 それ以降、外部ユーザー アカウントのプロパティは完全に独立します。 ユーザーのホーム ディレクトリでプロパティを変更しても、その変更は追加先のディレクトリ内の外部ユーザー アカウントに反映されません。

2 つのアカウント間の関連は、ユーザーが常に自分のホーム ディレクトリにより、または Microsoft アカウントを使って認証されるということのみとなります。 このため、パスワードをリセットするオプション、または外部ユーザーの多要素認証を有効にするオプションは表示されません。 現時点で、ホーム ディレクトリまたは Microsoft アカウントの認証ポリシーは、ユーザーがサインインする際に評価される唯一のポリシーとなっています。

> [!NOTE]
> ディレクトリ内の外部ユーザーを無効にすることもできます。無効にすると、ディレクトリへのアクセスはブロックされます。
>
>

ユーザーがホーム ディレクトリ内で削除されたか、または Microsoft アカウントが取り消されたとしても、外部ユーザーは追加先のディレクトリ内に引き続き残ります。 ただし、ディレクトリ内のユーザーはホーム ディレクトリでの認証や Microsoft アカウントを使った認証ができないため、リソースにアクセスできません。

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>現時点で Azure AD の外部ユーザーによるアクセスをサポートしているサービス
* **Azure クラシック ポータル**: 複数のディレクトリの管理者である外部ユーザーは、その各ディレクトリを管理できます。
* **SharePoint Online**: 外部共有が有効になっている場合、外部ユーザーは SharePoint Online で承認されたリソースにアクセスできます。
* **Dynamics CRM**: 外部ユーザーは、PowerShell によってライセンスが付与された場合、Dynamics CRM の承認されたリソースにアクセスできます。
* **Dynamics AX**: 外部ユーザーは、PowerShell によってライセンスが付与された場合、Dynamics AX の承認されたリソースにアクセスできます。 [Azure AD 外部ユーザー](#known-limitations-of-azure-ad-external-users) の制限事項は、Dynamics AX の外部ユーザーにも適用されます。

### <a name="known-limitations-of-azure-ad-external-users"></a>Azure AD 外部ユーザーの既知の制限事項
* 外部ユーザーが管理者である場合は、パートナー会社のユーザーを、自分のホーム ディレクトリ以外のディレクトリ (B2B コラボレーション) に追加することはできません。
* 外部ユーザーは、自分のホーム ディレクトリ以外のディレクトリ内のマルチテナント アプリケーションに同意できません。
* PowerBI では、現在、外部ユーザーによるアクセスはサポートされていません。
* Office ポータルでは、外部ユーザーへのライセンス付与をサポートしていません。
* Azure AD PowerShell では、外部ユーザーは自分のホーム ディレクトリにログインした状態であり、自分が外部ユーザーであるディレクトリを管理できません。
* Dynamics CRM Outlook プラグインを構成するときに、外部ユーザーは、[CRM Online] を選択するのではなく、Dynamics CRM Online の URL を手動で入力することが必要になる場合があります。

## <a name="whats-next"></a>参照トピック
* [Azure Active Directory に新しいユーザーを追加する](active-directory-create-users.md)
* [Administer your Azure AD directory](active-directory-administer.md)
* [Manage passwords in Azure AD](active-directory-manage-passwords.md)
* [Manage groups in Azure AD](active-directory-manage-groups.md)

