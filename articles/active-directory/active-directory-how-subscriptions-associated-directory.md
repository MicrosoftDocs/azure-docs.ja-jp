---
title: "Azure サブスクリプションを Azure Active Directory に関連付ける方法 | Microsoft Docs"
description: "Microsoft Azure へのサインインのほか、Azure サブスクリプションと Azure Active Directory の関係などの関連する問題。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: 4eee1b3f30c9e47983af5c1dfa60fe0deb8c2cc8


---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Azure サブスクリプションを Azure Active Directory に関連付ける方法
この記事では、Microsoft Azure へのサインインに関する情報と、Azure サブスクリプションと Azure Active Directory (Azure AD) の関係など、関連する問題について説明します。

## <a name="accounts-that-you-can-use-to-sign-in"></a>サインインに使用できるアカウント
サインインに使用できるアカウントから始めます。 Microsoft アカウント (旧称 Microsoft Live ID) と、Azure AD に格納されるアカウントである職場または学校アカウントの&2; 種類があります。

| Microsoft アカウント | Azure AD アカウント |
| --- | --- |
| Microsoft によって実行されるコンシューマー ID システム |Microsoft によって実行されるビジネス ID システム |
| Hotmail や MSN などのコンシューマー指向サービスに対する認証 |Office 365 などのビジネス指向サービスに対する認証 |
| コンシューマーは、電子メールにサインアップするときなどに、専用の Microsoft アカウントを作成します |企業および組織は、専用の職場または学校アカウントを作成および管理します |
| ID が作成されて Microsoft アカウント システムに格納されます |Azure または Office 365 などの他のサービスを使用して ID が作成され、組織に割り当てられている Azure AD インスタンスに格納されます |

当初、Azure には Microsoft アカウント ユーザーのみがアクセスできましたが、現在では *両方の* システムのユーザーがアクセスできます。 これは、すべての Azure プロパティが認証時に Azure AD を信頼し、Azure AD が組織のユーザーを認証し、Azure AD が Microsoft アカウント コンシューマー ID システムを信頼してコンシューマー ユーザーを認証するフェデレーション関係を作成することによって実現されました。 その結果、Azure AD は「ゲスト」 Microsoft アカウントだけでなく「ネイティブ」 Azure AD アカウントも認証できます。

たとえば、ここでは Microsoft アカウントを使用するユーザーが Azure クラシック ポータルにサインインしています。

> [!NOTE]
> Azure クラシック ポータルにサインインするには、msmith@hotmail.com に Azure のサブスクリプションが必要です。 アカウントは、サービス管理者またはサブスクリプションの共同管理者である必要があります。
>
>

![][1]

この Hotmail アドレスはコンシューマー アカウントであるため、サインインは Microsoft アカウント コンシューマー ID システムによって認証されます。 Azure AD の ID システムは、Microsoft アカウント システムによって行われた認証を信頼し、Azure サービスにアクセスするためのトークンを発行します。

## <a name="how-an-azure-subscription-is-related-to-azure-ad"></a>Azure サブスクリプションと Azure AD の関連性
すべての Azure サブスクリプションには、Azure AD インスタンスとの間に信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは&1; つのディレクトリだけを信頼します。 サブスクリプションが信頼しているディレクトリは、[設定] タブで確認できます。 [サブスクリプションの設定を編集](active-directory-understanding-resource-access.md) して、信頼するディレクトリを変更できます。

このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。 一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。

同様に、サブスクリプション内の Azure AD 拡張機能は、Azure クラシック ポータルの他の拡張機能のように機能しません。 Azure クラシック ポータルの他の拡張機能は、 Azure サブスクリプションをその有効範囲としています。 Azure AD 拡張機能では、サブスクリプションに応じた表示の変化はありません。サインインしているユーザーに基づくディレクトリだけがサブスクリプションによって表示されます。

すべてのユーザーにはそのユーザーを認証する&1; つのホーム ディレクトリがありますが、ユーザーは他のディレクトリのゲストになることもできます。 Azure AD 拡張機能では、ユーザー アカウントがメンバーとなっているディレクトリがすべて表示されます。 アカウントがメンバーではないディレクトリは表示されません。 ディレクトリは、Azure AD の職場または学校アカウントに対して、または Microsoft アカウント ユーザーに対して (Azure AD は Microsoft アカウント システムとフェデレーションされているため)、トークンを発行できます。

次の図では、Contoso の職場アカウントを使用してサインアップした後の Michael Smith のサブスクリプションを示します。

![][2]

## <a name="how-to-manage-a-subscription-and-a-directory"></a>サブスクリプションとディレクトリを管理する方法
Azure サブスクリプションの管理ロールは、Azure サブスクリプションに関連付けられているリソースを管理します。 サブスクリプションの管理に関するこれらのロールとベスト プラクティスについては、「 [Azure Active Directory での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

既定では、サインアップすると、サービス管理者ロールを割り当てられます。 他のユーザーが同じサブスクリプションを使用してサインインし、サービスにアクセスする必要がある場合は、共同管理者として追加できます。 サービス管理者および共同管理者になることができるのは、Azure サブスクリプションが関連付けられているディレクトリの Microsoft アカウントか、職場または学校アカウントです。

Azure AD には、ディレクトリおよび ID 関連の機能を管理するために異なる管理ロールのセットがあります。 たとえば、ディレクトリのグローバル管理者は、ディレクトリにユーザーとグループを追加でき、ユーザーに多要素認証を要求できます。 ディレクトリを作成するユーザーはグローバル管理者ロールに割り当てられ、他のユーザーに管理者ロールを割り当てることができます。

サブスクリプション管理者と同様、Azure AD 管理ロールには Microsoft アカウントか、職場または学校アカウントを使用できます。 Azure AD 管理ロールは、Office 365 や Microsoft Intune などの他のサービスによっても使用されます。 詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

ここで重要なポイントは、Azure サブスクリプション管理者と Azure AD ディレクトリ管理者は&2; つの異なる概念であるということです。 Azure サブスクリプション管理者は、Azure のリソースを管理でき、Azure クラシック ポータルで Active Directory 拡張機能を表示できます (Azure クラシック ポータルは Azure のリソースであるため)。 ディレクトリ管理者は、ディレクトリ内のプロパティを管理できます。

1 人のユーザーが両方のロールになることができますが、これは必須ではありません。 ユーザーをディレクトリ グローバル管理者ロールに割り当てることはできますが、Azure サブスクリプションのサービス管理者または共同管理者として割り当てることはできません。 サブスクリプションの管理者にならなければ、このユーザーは Azure クラシック ポータルにサインインできません。 ただし、そのユーザーは Azure AD PowerShell や Office 365 管理センターなどの他のツールを使用して、ディレクトリ管理タスクを実行できます。

## <a name="why-cant-i-manage-the-directory-with-my-current-user-account"></a>現在のユーザー アカウントでディレクトリを管理できない理由
ユーザーが Azure サブスクリプションにサインアップする前に職場または学校アカウントを使用して Azure クラシック ポータルにサインインしようとする場合があります。 そのような場合、ユーザーはそのアカウントにはサブスクリプションがないというメッセージを受け取ります。 メッセージには、無料評価版サブスクリプションを開始するためのリンクが含まれます。

無料試用版にサインアップした後は、ユーザーは Azure クラシック ポータルで組織のディレクトリを見ることはできますが、ディレクトリ グローバル管理者ではないため、ディレクトリを管理することはできません (つまり、ユーザーの追加、既存ユーザーのプロパティの編集はできません)。 サブスクリプションにより、ユーザーは Azure クラシック ポータルを使用して Azure Active Directory 拡張機能を表示することはできますが、ディレクトリを管理するにはグローバル管理者の追加権限が必要です。

## <a name="using-your-work-or-school-account-to-manage-an-azure-subscription-that-was-created-by-using-a-microsoft-account"></a>Microsoft アカウントを使って作成された Azure サブスクリプションを、職場または学校アカウントを使って管理
ベスト プラクティスでは、 [組織として Azure にサインアップ](sign-up-organization.md) し、職場または学校アカウントを使用して Azure のリソースを管理する必要があります。 職場または学校アカウントが推奨されるのは、それらを発行した組織によって一元的に管理でき、Microsoft アカウントより多くの機能があり、Azure AD によって直接認証されるためです。 同じアカウントが、企業や組織に提供される Office 365 や Microsoft Intune などの他の Microsoft オンライン サービスへのアクセスを提供します。 既に他のプロパティで使用するアカウントがある場合は、Azure でその同じアカウントを使用できます。 また、これらのプロパティに対する Active Directory インスタンスが既にあって、それを Azure サブスクリプションで信頼することもできます。

職場または学校アカウントは、Microsoft アカウントよりも多くの方法で管理することもできます。 たとえば、管理者は職場または学校アカウントのパスワードをリセットでき、多要素認証を要求できます。

場合によっては、組織のユーザーがコンシューマー Microsoft アカウントの Azure サブスクリプションに関連付けられているリソースを管理できます。 サブスクリプションまたはディレクトリの管理を異なるアカウントに移行する方法の詳細については、「 [Azure における Office 365 サブスクリプションのディレクトリの管理](#manage-the-directory-for-your-office-365-subscription-in-azure)」」に関する記事を参照してください。

## <a name="signing-in-when-you-used-your-work-email-for-your-microsoft-account"></a>Microsoft アカウントに会社の電子メールを使用していた場合のサインイン
過去のある時点で、ユーザー ID として仕事の電子メールを使用してコンシューマー Microsoft アカウントを作成した場合、Microsoft Azure アカウント システムまたは Microsoft アカウント システムのいずれかを選択するよう求めるページが表示される可能性があります。

![][3]

Azure AD とコンシューマー Microsoft アカウント システムに同じ名前のユーザー アカウントがあります。 使用する Azure サブスクリプションに関連付けるアカウントを選択する必要があります。 このユーザーに対してサブスクリプションが存在しないというエラーが表示される場合は、正しくないオプションを選択した可能性があります。 サインアウトして、もう一度やり直してください。 サインインを妨げる可能性のあるエラーの詳細については、「 [Troubleshooting "We were unable to find any subscriptions associated with your account" errors ("アカウントに関連付けられたサブスクリプションはありませんでした" というエラーのトラブルシューティング)](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement)」を参照してください。

## <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Azure での Office 365 サブスクリプションのディレクトリの管理
Azure にサインアップする前に Office 365 にサインアップしたものとします。 Office 365 サブスクリプションのディレクトリを Azure クラシック ポータルで管理することになりました。 その場合、Azure にサインアップしているかどうかに応じた&2; つの方法があります。

### <a name="i-do-not-have-a-subscription-for-azure"></a>Azure のサブスクリプションがない場合
この場合、必要なのは Office 365 へのサインインに使用している会社または学校のアカウントを使用して、 [にサインアップする](sign-up-organization.md)ことだけです。 Office 365 アカウントからの関連情報が、Azure のサインアップ フォームに事前に入力されます。 アカウントは、サブスクリプションのサービス管理者ロールに割り当てられます。  

### <a name="i-do-have-a-subscription-for-azure-using-my-microsoft-account"></a>Microsoft アカウントを使用する Azure のサブスクリプションがある場合
職場または学校アカウントを使用して Office 365 にサインアップした後、Microsoft アカウントを使用して Azure にサインアップした場合は、2 つのディレクトリがあります。1 つは職場または学校用で、もう 1 つは Azure にサインアップしたときに作成された既定のディレクトリです。

Azure クラシック ポータルで両方のディレクトリを管理するには、以下の手順を実行します。

> [!NOTE]
> この手順は、ユーザーが Microsoft アカウントでサインインしているときにのみ実行できます。 ユーザーが職場または学校アカウントでサインインしている場合は、ホーム ディレクトリ (つまり、職場または学校アカウントが格納されていて、職場または学校によって所有されているディレクトリ) によってのみアカウントを認証できるので、 **[既存のディレクトリの使用]** オプションは使用できません。
>
>

1. Microsoft アカウントを使用して、Azure クラシック ポータルにサインインします。
2. **[新規]** > **[App Services]** > **[Active Directory]** > **[ディレクトリ]** > **[カスタム作成]** をクリックします。
3. **[既存のディレクトリの使用]** をクリックして、**[サインアウトする準備ができました]** をオンにし、チェック マークをクリックして操作を完了します。
4. 職場または学校のディレクトリのグローバル管理者権限を持つアカウントを使用して、Azure クラシック ポータルにサインインします。
5. **[Azure で Contoso ディレクトリを使用しますか?]** というメッセージが表示されたら、**[続行]** をクリックします。
6. **[今すぐサインアウト]**をクリックします。
7. Microsoft アカウントを使用して、Azure クラシック ポータルに再度サインインします。 両方のディレクトリが Active Directory 拡張機能に表示されます。

## <a name="next-steps"></a>次のステップ
* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../billing/billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)
* Azure AD で役割を割り当てる方法の詳細については、「 [Azure Active Directory での管理者ロールの割り当て](active-directory-assign-admin-roles.md)
* [Azure への組織としてのサインアップ](sign-up-organization.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG



<!--HONumber=Feb17_HO2-->


