---
title: "Azure Active Directory Domain Services: パスワード同期を有効にする | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 4b6da997f44860dccb2aa2571ce099ab2d0231f3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services とのパスワード同期を有効にする
前のタスクでは、Azure Active Directory (Azure AD) テナントに対して Azure Active Directory Domain Services を有効にしました。 次のタスクでは、NT LAN Manager (NTLM) および Kerberos 認証に必要な資格情報ハッシュを Azure AD Domain Services との間で同期できるようにします。 資格情報の同期が設定されると、ユーザーは自社の資格情報を使用して、管理対象ドメインにサインインできます。

対象となるユーザー アカウントがクラウド専用のアカウントであるか、オンプレミス ディレクトリとの間で Azure AD Connect を使って同期されたアカウントであるかによって、必要な手順は異なります。  Azure AD テナントにクラウドのみのユーザーとオンプレミス AD からのユーザーとが混在している場合、両方の手順を実行する必要があります。

<br>

> [!div class="op_single_selector"]
> * **クラウド専用ユーザー アカウント**: [クラウド専用ユーザー アカウントのパスワードを管理対象ドメインとの間で同期する](active-directory-ds-getting-started-password-sync.md)
> * **オンプレミスのユーザー アカウント**: [オンプレミス AD との間で同期されたユーザー アカウントのパスワードを管理対象ドメインとの間で同期する](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>タスク 5: 管理対象ドメインとの間でクラウド専用ユーザー アカウントのパスワード同期を有効にする
Azure Active Directory Domain Services では、管理対象ドメインでユーザーを認証するために、NTLM および Kerberos 認証に適した形式の資格情報ハッシュが必要です。 テナントに対して Azure Active Directory Domain Services を有効にしない限り、Azure AD で NTLM または Kerberos 認証に必要な形式の資格情報ハッシュが生成または保存されることはありません。 明らかなセキュリティ上の理由から、Azure AD はパスワード資格情報をクリア テキスト形式でも保存しません。 そのため、Azure AD には、これらの NTLM または Kerberos 資格情報ハッシュをユーザーの既存の資格情報に基づいて自動的に生成する方法がありません。

> [!NOTE]
> 組織にクラウド専用ユーザー アカウントが存在する場合、Azure Active Directory Domain Services を使用する必要があるユーザーは、自分のパスワードを変更しなければなりません。 クラウド専用ユーザー アカウントとは、Azure Portal または Azure AD PowerShell コマンドレットを使って Azure AD ディレクトリに作成されたアカウントです。 そのようなユーザー アカウントは、オンプレミス ディレクトリとの間で同期されません。
>
>

このパスワード変更プロセスにより、Azure Active Directory Domain Services での Kerberos および NTLM 認証に必要な資格情報ハッシュが Azure AD 内に生成されます。 パスワードの変更を促す方法として、Azure Active Directory Domain Services を使用する必要があるテナント内の全ユーザーのパスワードを有効期限切れにすることも、それらのユーザーにパスワードを変更するように指示することもできます。

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>NTLM と Kerberos の資格情報ハッシュの生成を有効にする (クラウド専用ユーザー アカウント)
ユーザーがパスワードを変更できるように、以下の手順をユーザーに伝える必要があります。

1. 組織の [Azure AD アクセス パネル](http://myapps.microsoft.com)のページに移動します。

    ![Azure AD アクセス パネルの起動](./media/active-directory-domain-services-getting-started/access-panel.png)

2. 右上隅に表示される自分の名前をクリックし、メニューから **[プロファイル]** を選択します。

    ![プロファイルの選択](./media/active-directory-domain-services-getting-started/select-profile.png)

3. **[プロファイル]** ページの **[パスワードの変更]** をクリックします。

    ![[パスワードの変更] をクリック](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > アクセス パネル ウィンドウに **[パスワードの変更]** オプションが表示されない場合は、組織で [Azure AD でのパスワード管理](../active-directory/active-directory-passwords-getting-started.md)を構成済みであることを確認します。
   >
   >
4. **[パスワードの変更]** ページで既存の (古い) パスワードを入力し、新しいパスワードを入力して、それを確認します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. **[送信]**をクリックします。

パスワードを変更すると、数分後に Azure Active Directory Domain Services で新しいパスワードを使用できるようになります。 また、さらに数分 (通常は約 20 分) 後には、新しく変更したパスワードを使用して、管理対象ドメインに参加しているコンピューターにサインインできるようになります。

## <a name="related-content"></a>関連コンテンツ
* [自分のパスワードを更新する方法](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Azure AD でのパスワード管理の概要](../active-directory/active-directory-passwords-getting-started.md)
* [Azure Active Directory Domain Services とのパスワード同期を有効にする (同期された Azure AD テナントの場合)](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Azure Active Directory Domain Services で管理されているドメインを管理する](active-directory-ds-admin-guide-administer-domain.md)
* [Azure Active Directory Domain Services で管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure Active Directory Domain Services で管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

