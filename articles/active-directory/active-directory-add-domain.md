---
title: "Azure Active Directory へのカスタム ドメイン名の追加 | Microsoft Docs"
description: "Azure Active Directory に会社のドメイン名を追加する方法とドメイン名を検証する方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/30/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: f1f3d6fa1ba29b0d43d29c7d9cccfc430fdd6f1e
ms.openlocfilehash: 9803b4e41223bc979e4c0b0a4f22586a441c28df
ms.lasthandoff: 02/22/2017


---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>Azure Active Directory へのカスタム ドメイン名の追加
> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-domains-add-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-add-domain.md)
> 
> 

組織にはビジネス向けに使用する&1; つ以上のドメイン名があり、ユーザーはその会社のドメイン名を使用してその会社のネットワークにサインインします。 Azure Active Directory (Azure AD) を利用していると、会社のドメイン名を Azure AD にも追加することができます。 これにより、ディレクトリ内で "alice@contoso.com" のようなユーザーにとって馴染みのあるユーザー名を割り当てることができます。 この場合のプロセスは単純です。

1. カスタム ドメイン名をディレクトリに追加する
2. ドメイン名レジストラーでドメイン名の DNS エントリを追加する
3. Azure AD でカスタム ドメイン名を検証する

> [!NOTE]
> カスタム ドメイン名を企業ネットワーク上の Active Directory フェデレーション サービス (AD FS) またはそれ以外のセキュリティ トークン サービス (STS) で使用するように構成する場合は、 [Azure Active Directory でのフェデレーション用ドメインの追加と構成](active-directory-add-domain-federated.md)に関する記事の手順を実施します。 企業のディレクトリから Azure AD にユーザーを同期したいが、 [パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md) では実現できないような場合に便利です。
> 
> 

## <a name="add-a-custom-domain-name-to-your-directory"></a>カスタム ドメイン名をディレクトリに追加する
1. Azure AD ディレクトリのグローバル管理者のユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。
2. **[Active Directory]** でディレクトリを開き、**[ドメイン]** タブをクリックします。
3. コマンド バーで、 **[追加]**を選択します。 "contoso.com" など、カスタム ドメインの名前を入力します。 .com や .net など、トップレベルの拡張子を必ず付け、シングル サインオン (フェデレーション) に関するチェック ボックスはオフのままにします。
4. **[追加]**を選択します。
5. ドメインの追加ウィザードの&2; ページ目で、組織がそのカスタム ドメイン名を所有していることを Azure AD が検証するために使用する DNS エントリを取得します。

ドメイン名を追加したので、Azure AD では、そのドメイン名が組織に所有されていることを検証する必要があります。 Azure AD でこの検証を実行するには、ドメイン名の DNS ゾーン ファイルに DNS エントリを追加する必要があります。 この作業は、ドメイン名のドメイン名レジストラーの Web サイトで実行できます。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>ドメインのドメイン名レジストラーで DNS エントリを追加する
Azure AD でカスタム ドメイン名を使用できるようにするために、次の手順としてドメインの DNS ゾーン ファイルを更新します。 これにより、組織がそのカスタム ドメイン名を所有していることを Azure AD で検証できます。

1. ドメインのドメイン名レジストラーにサインインします。 DNS エントリを更新するアクセス権がない場合は、アクセス権を持つ人物またはチームに連絡し、手順 2. の実行と、完了後の連絡を依頼します。
2. Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。 この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。 メール ルーティングや Web ホスティングなどの動作は変更されません。

DNS エントリの追加に関するヘルプについては、 [一般的な DNS レジストラーで DNS エントリを追加する手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Azure AD との間でドメイン名を検証する
DNS エントリを追加すると、Azure AD でドメイン名を検証することができるようになります。

**ドメインの追加**ウィザードがまだ開いている場合は、ウィザードの&3; ページ目で **[確認]** を選択します。 **[確認]**を選択すると、Azure AD はドメインの DNS ゾーン ファイル内の DNS エントリを探します。 DNS レコードが反映された後でないと、Azure AD はドメイン名を検証できません。 反映には数秒しかかからない場合がほとんどですが、1 時間以上かかる場合もあります。 最初から検証が機能しない場合は、後でもう一度やり直してください。

**ドメインの追加** ウィザードがまだ開いていない場合は、 [Azure クラシック ポータル](https://manage.windowsazure.com/)内でドメインを検証することができます。

1. Azure AD ディレクトリのグローバル管理者であるユーザー アカウントでサインインします。
2. ディレクトリを開き、 **[ドメイン]** タブをクリックします。
3. 検証するドメイン名を選択し、コマンド バーの **[確認]** を選択します。
4. ダイアログ ボックスの **[確認]** を選択し、検証を完了します。

これで [カスタム ドメイン名を含んだユーザー名を割り当てる](active-directory-add-domain-add-users.md)ことができます。

## <a name="troubleshooting"></a>トラブルシューティング
カスタム ドメイン名を検証できない場合は、次のことを試してください。 以下では、最も一般的な原因から順に列挙します。

1. **1 時間待つ**。 DNS レコードは、Azure AD がドメインを検証する前に反映されている必要があります。 これに&1; 時間以上かかる場合があります。
2. **DNS レコードが正しく入力されていることを確認する**。 ドメインのドメイン名レジストラーの Web サイトで、この手順を実行します。 Azure AD は、DNS エントリが DNS ゾーン ファイルに存在しない場合、または Azure AD によって提供された DNS エントリと完全に一致しない場合は、ドメイン名を検証できません。 ドメインの DNS レコードをドメイン名レジストラーで更新するアクセス権がない場合は、組織内でそのアクセス権を持つ人物またはチームと DNS エントリを共有し、DNS エントリの追加を依頼します。
3. **Azure AD の他のディレクトリから、そのドメイン名を削除する**。 ドメイン名は、1 つのディレクトリ内だけでしか検証できません。 ドメイン名が以前に他のディレクトリで検証されていた場合は、新しいディレクトリで検証する前に、前のディレクトリ内から削除されている必要があります。 ドメイン名の削除については、[カスタム ドメイン名の管理](active-directory-add-manage-domain-names.md)に関する記事を参照してください。

## <a name="add-more-custom-domain-names"></a>カスタム ドメイン名を追加する
複数のカスタム ドメイン名 (たとえば "contoso.com" と "contosobank.com" など) を使用する場合、ドメイン名は最大 900 個まで使用することができます。 この記事の同じ手順に従って各ドメイン名を追加してください。

## <a name="next-steps"></a>次のステップ
* [カスタム ドメイン名を含むユーザー名を割り当てる](active-directory-add-domain-add-users.md)
* [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)
* [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)
* [ユーザーがサインインしたときに会社のブランドを表示する](active-directory-add-company-branding.md)
* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


