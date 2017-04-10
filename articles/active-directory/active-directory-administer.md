---
title: "Azure AD ディレクトリの管理 | Microsoft Docs"
description: "Azure AD テナントの概要、および Azure Active Directory で Azure を管理する方法を説明します。"
services: active-directory
documentationcenter: 
author: curtand
writer: markvi
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce
ms.lasthandoff: 12/08/2016


---
# <a name="administer-your-azure-ad-directory"></a>Azure AD ディレクトリの管理
## <a name="what-is-an-azure-ad-tenant"></a>Azure AD テナントとは
物理的なワークスペースでは、テナントはビルを占めているグループまたは会社と定義できます。 たとえば、組織がビルのオフィス スペースを所有しているとします。 このビルは、他の複数の組織が面している通りにある場合があります。 組織はそのビルのテナントと考えることができます。 このビルは組織の資産であり、セキュリティを提供し、ビジネスを安全に行うことができるようにします。 また、ビルは通りで他の企業から切り離されています。 これにより、組織とその資産は他の組織から分離されます。

クラウド対応のワークスペースでは、テナントはそのクラウド サービスの特定のインスタンスを所有して管理するクライアントまたは組織と定義できます。 Microsoft Azure によって ID プラットフォームを提供されたテナントは、組織が Azure や Office 365 などの Microsoft クラウド サービスにサインアップしたときに受け取って所有する、Azure Active Directory (Azure AD) の専用インスタンスです。

各 Azure AD ディレクトリは、他の Azure AD ディレクトリと区別され分離されています。 会社のオフィス ビルが組織に固有のセキュリティで保護された資産であるのと同様に、Azure AD ディレクトリも特定の組織だけが使用するセキュリティで保護された資産として設計されています。 Azure AD アーキテクチャは、顧客のデータや ID 情報が混合しないよう分離します。 これは、Azure AD ディレクトリのユーザーや管理者が、別のディレクトリのデータに誤ってまたは悪意をもってアクセスすることはできないことを意味します。

![Azure Active Directory の管理][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Azure AD ディレクトリを取得する方法
Azure AD は、ほとんどの Microsoft クラウド サービスの背後にある、次のようなコア ディレクトリおよび ID 管理機能を提供します。

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM オンライン
* Microsoft Intune

これらの Microsoft クラウド サービスのいずれかにサインアップすると、Azure AD ディレクトリが提供されます。 必要に応じて、追加のディレクトリを作成できます。 たとえば、最初のディレクトリを運用ディレクトリとして保持し、テストまたはステージング用に別のディレクトリを作成できます。

> [!NOTE]
> 最初のサービスにサインアップした後は、他の Microsoft クラウド サービスにサインアップするときに、組織に関連付けられている同じ管理者アカウントを使用することをお勧めします。
> 
> 

Microsoft クラウド サービスに初めてサインアップするときには、組織と組織のインターネット ドメイン名登録の詳細情報を提供するよう求められます。 その後、この情報を使用して、組織の新しい Azure AD ディレクトリ インスタンスが作成されます。 複数の Microsoft クラウド サービスにサブスクライブしている場合でも、サインイン試行の認証にその同じディレクトリが使用されます。

その他のサービスは、組織のオンプレミス ID インフラストラクチャと Azure AD 間で効率を高めるために、ユーザーが構成した既存のユーザー アカウント、ポリシー、設定、またはオンプレミス ディレクトリ統合を最大限に活用します。

たとえば、最初は Microsoft Intune サブスクリプションにサインアップし、ディレクトリ同期サーバーやシングル サインオン サーバーをデプロイすることによって、オンプレミス Active Directory と Azure AD ディレクトリの統合を強化するために必要な手順を完了した場合、Office 365 などの別の Microsoft クラウド サービスにサインアップすると、Microsoft Intune で現在使用しているディレクトリ統合の同じメリットをそのサービスでも活用できます。

オンプレミス ディレクトリと Azure AD の統合の詳細については、「 [ディレクトリ統合](active-directory-aadconnect.md)」をご覧ください。

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>Azure AD ディレクトリを新しい Azure サブスクリプションに関連付ける
新しい Azure サブスクリプションは、既存の Office 365 または Microsoft Intune サブスクリプションへのサインインを認証する同じディレクトリに関連付けることができます。 職場または学校アカウントを使用して、Microsoft Azure 管理ポータルにサインインします。 管理ポータルに、そのアカウントのサブスクリプションが見つからなかったことを示すメッセージが表示されます。 **[Azure にサインアップ]**を選択すると、ディレクトリをポータル内での管理に使用できるようになります。 詳細については、「 [Azure での Office 365 サブスクリプションのディレクトリの管理](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure)」をご覧ください。

Azure AD の使用方法に関するよくある質問のビデオについては、「 [Azure Active Directory - Common Sign-up, sign-in and usage questions (Azure Active Directory - サインアップ、サインイン、使用方法に関するよくある質問)](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions)」をご覧ください。

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>組織として Microsoft クラウド サービスにサインアップして Azure AD ディレクトリを作成する
Microsoft クラウド サービスのサブスクリプションをまだ持っていない場合は、次のリンクのいずれかを使用してサインアップします。 最初のサービスにサインアップすると、Azure AD ディレクトリが自動的に作成されます。

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Azure によってプロビジョニングされた既定のディレクトリを管理する
現在は、Azure にサインアップするとディレクトリが自動的に作成され、そのディレクトリにサブスクリプションが関連付けられます。 ただし、Azure に最初にサインアップしたのが 2013 年 10 月より前の場合、ディレクトリは自動的には作成されませんでした。 その場合、Azure がアカウントの既定のディレクトリをプロビジョニングすることによって、アカウントに対して "バックフィル" が実行された可能性があります。 サブスクリプションは、その既定のディレクトリに関連付けられています。

ディレクトリのバックフィルは、Azure のセキュリティ モデルの全体的な改善の一環として、2013 年 10 月に実行されました。 バックフィルにより、組織の ID 機能をすべての Azure ユーザーに提供し、ディレクトリ内のユーザーのコンテキストですべての Azure リソースにアクセスできるようになります。 ディレクトリなしで Azure を使用することはできません。 Azure を使用できるようにするために、2013 年 7 月 7 日より前にサインアップし、ディレクトリを持っていなかったユーザーには、ディレクトリを作成してもらう必要がありました。 ディレクトリを既に作成していた場合は、そのディレクトリにサブスクリプションが関連付けられています。

Azure AD を使用するのにコストは一切かかりません。 ディレクトリは無料のリソースです。 別途ライセンスが必要ですが、会社のブランド化やセルフサービスのパスワード リセットなどの追加機能を提供する Azure Active Directory Premium サービスも用意されています。

ディレクトリの表示名を変更するには、ポータルでディレクトリをクリックし、**[構成]** をクリックします。 このトピックの後の方で説明するように、新しいディレクトリを追加したり、不要になったディレクトリを削除したりすることができます。 サブスクリプションを別のディレクトリに関連付けるには、左側のナビゲーションで **[設定]** 拡張機能をクリックし、**[サブスクリプション]** ページの下部で **[ディレクトリの編集]** をクリックします。 既定の *.onmicrosoft.com ドメインではなく、登録済みの DNS 名を使用してカスタム ドメインを作成することもできます。これは、SharePoint Online などのサービスを利用している場合にお勧めします。

## <a name="how-can-i-manage-directory-data"></a>ディレクトリ データを管理する方法
1 つ以上の Microsoft クラウド サービス サブスクリプションの管理者は、Microsoft Azure 管理ポータル、Microsoft Intune アカウント ポータル、または Office 365 管理センターを使用して、組織のディレクトリ データを管理できます。 また、 [Windows PowerShell 用 Microsoft Azure Active Directory モジュール](https://msdn.microsoft.com/library/azure/jj151815.aspx) のコマンドレットをダウンロードして実行することで、Azure AD に格納されているデータを管理することもできます。

これらのポータル (またはコマンドレット) から、次の操作が可能です。

* ユーザーとグループのアカウントを作成および管理する
* 組織がサブスクライブしている関連クラウド サービスを管理する
* ディレクトリ サービスとのオンプレミス統合をセットアップする

次の図に示すように、Microsoft Azure 管理ポータル、Office 365 管理センター、Microsoft Intune アカウント ポータル、Azure AD コマンドレットはすべて、組織のディレクトリに関連付けられている Azure AD の 1 つの共有インスタンスとの間で読み取りと書き込みを行います。 このようにして、ポータル (またはコマンドレット) はディレクトリ データを取得または変更するフロントエンド インターフェイスとして機能します。

![][2]

ユーザーとグループの管理に使用する、これらのアカウント ポータルと関連する Azure AD PowerShell コマンドレットは、Azure AD プラットフォームの上に構築されています。

これらのサービスのいずれかのコンテキストでサインインしているときに、いずれかのポータル (またはコマンドレット) を使用して組織のデータを変更した場合、このデータはサブスクライブしている Microsoft クラウド サービス間で共有されるため、そのサービスのコンテキストで次回サインインしたときに、他のポータルでも変更が表示されます。
たとえば、Office 365 管理センターを使用して、ユーザーがサインインできないようにブロックした場合、そのユーザーは組織が現在サブスクライブしている他のどのサービスにもサインインできなくなります。 Microsoft Intune アカウント ポータルのコンテキストで、その同じユーザーのアカウントを取得すると、そのユーザーがブロックされていることがわかります。

## <a name="how-can-i-add-and-manage-multiple-directories"></a>複数のディレクトリを追加および管理する方法
Azure AD ディレクトリは、Microsoft Azure 管理ポータルで追加できます。 左側で **[Active Directory]** 拡張機能を選択し、**[追加]** をクリックします。

各ディレクトリは、完全に独立したリソースとして管理できます。つまり、各ディレクトリは対等であり、フル機能を備え、管理対象の他のディレクトリから論理的に独立しています。ディレクトリ間に親子関係はありません。 このディレクトリ間の独立には、リソースの独立、管理上の独立、同期の独立があります。

* **リソースの独立**。 後述する外部ユーザーの一部の例外を除き、あるディレクトリでリソースを作成または削除しても、別のディレクトリのリソースには影響しません。 あるディレクトリで "contoso.com" というカスタム ドメインを使用している場合、このドメインを他のディレクトリで使用することはできません。
* **管理上の独立**。  "Contoso" ディレクトリの管理者以外のユーザーが、"Test" というテスト ディレクトリを作成した場合、次のようになります。
  
  * データを 1 つの AD フォレストと同期するディレクトリ同期ツール。
  * "Test" の管理者が管理者特権を明示的に付与した場合を除き、"Contoso" ディレクトリの管理者には、"Test" ディレクトリに対する直接的な管理者特権はありません。 "Contoso" の管理者は、"Test" を作成したユーザー アカウントの制御により、"Test" ディレクトリへのアクセスを制御できます。
    
    また、あるディレクトリでユーザーの管理者ロールを変更 (追加または削除) した場合、変更は、別のディレクトリでそのユーザーに割り当てられている可能性のある管理者ロールには影響しません。
* **同期の独立**。 次のいずれかの 1 つのインスタンスからデータが同期されるように、各 Azure AD を個別に構成できます。
  
  * データを 1 つの AD フォレストと同期するディレクトリ同期ツール。
  * データを 1 つ以上のオンプレミス フォレストや非 AD データ ソースと同期する、Azure Active Directory Connector for Forefront Identity Manager。

他の Azure リソースとは異なり、ディレクトリは Azure サブスクリプションの子リソースではないことにも注意してください。 そのため、Azure サブスクリプションを取り消したり、期限切れを許可した場合でも、Azure AD PowerShell、Azure Graph API、または Office 365 管理センターなどの他のインターフェイスを使用して、ディレクトリ データに引き続きアクセスできます。 また、ディレクトリに別のサブスクリプションを関連付けることもできます。

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Azure AD ディレクトリを削除する方法
グローバル管理者は、ポータルから Azure AD ディレクトリを削除できます。 ディレクトリを削除すると、そのディレクトリに含まれるリソースもすべて削除されます。そのため、ディレクトリを削除する前に、そのディレクトリが不要であることを確認する必要があります。

> [!NOTE]
> ユーザーが職場または学校アカウントを使用してサインインしている場合、そのユーザーは自分のホーム ディレクトリを削除することはできません。 たとえば、ユーザーが joe@contoso.onmicrosoft.com, としてサインインしている場合、そのユーザーは既定のドメインが contoso.onmicrosoft.com であるディレクトリを削除することはできません。
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Azure AD ディレクトリを削除するために満たす必要がある条件
Azure AD でディレクトリを削除するには、特定の条件を満たす必要があります。 これにより、ディレクトリの削除がユーザーやアプリケーションに悪影響を及ぼす (ユーザーが Office 365 にサインインできない、Azure のリソースにアクセスできないなど) リスクが軽減されます。 たとえば、サブスクリプションのディレクトリが誤って削除された場合、ユーザーはそのサブスクリプションの Azure リソースにアクセスできなくなります。

次の条件がチェックされます。

* ディレクトリ内のユーザーは、ディレクトリを削除するグローバル管理者に限られます。 ディレクトリを削除するには、他のすべてのユーザーを削除しておく必要があります。 ユーザーがオンプレミスから同期されている場合は、同期を無効にする必要があります。また、管理ポータルまたは Windows PowerShell 用 Azure モジュールを使用して、クラウド ディレクトリでユーザーを削除する必要があります。 グループまたは連絡先 (Office 365 管理センターから追加された連絡先など) を削除する要件はありません。
* ディレクトリ内にアプリケーションが存在してはいけません。 ディレクトリを削除するには、すべてのアプリケーションを削除しておく必要があります。
* ディレクトリに関連付けられている、Microsoft Azure、Office 365、Azure AD Premium などの Microsoft Online Services のサブスクリプションが存在してはいけません。 たとえば、Azure で既定のディレクトリが作成されている場合、Azure サブスクリプションが認証にこのディレクトリを引き続き使用していれば、このディレクトリを削除することはできません。 同様に、別のユーザーがディレクトリにサブスクリプションを関連付けている場合、そのディレクトリを削除することはできません。 サブスクリプションを別のディレクトリに関連付けるには、Microsoft Azure 管理ポータルにサインインし、左側のナビゲーションで **[設定]** をクリックします。 次に、**[サブスクリプション]** ページの下部にある **[ディレクトリの編集]** をクリックします。 Azure サブスクリプションの詳細については、「 [How Azure subscriptions are associated with Azure AD (Azure サブスクリプションを Azure AD に関連付ける方法)](active-directory-how-subscriptions-associated-directory.md)」をご覧ください。

> [!NOTE]
> ユーザーが職場または学校アカウントを使用してサインインしている場合、そのユーザーは自分のホーム ディレクトリを削除することはできません。 たとえば、ユーザーが joe@contoso.onmicrosoft.com, としてサインインしている場合、そのユーザーは既定のドメインが contoso.onmicrosoft.com であるディレクトリを削除することはできません。
> 
> 

* Multi-Factor Authentication プロバイダーをディレクトリにリンクすることはできません。

## <a name="additional-resources"></a>その他のリソース
* [Azure AD フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [StackOverflow](http://stackoverflow.com/questions/tagged/azure)
* [Azure への組織としてのサインアップ](sign-up-organization.md)
* [Windows PowerShell による Azure AD の管理](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

