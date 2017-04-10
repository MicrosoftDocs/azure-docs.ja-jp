---

title: "Azure Active Directory プレビューのライセンスの概要 | Microsoft Docs"
description: "Office 365、Microsoft Intune、Azure Active Directory Premium エディションと Basic エディションを含む、Azure Active Directory のライセンス、しくみ、使用方法、ベスト プラクティスの説明"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3bad24f35cf7867f1e02e8470c602a7f6a9ce8fb
ms.lasthandoff: 03/09/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>Azure Active Directory プレビューでの自分とユーザーのライセンスの取得

> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-licensing-get-started-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) は、マイクロソフトのサービスとしての ID (IDaaS) ソリューションおよびプラットフォームです。 Azure AD は、Office 365、Dynamics、Microsoft Intune、Azure (このモードでは Azure AD の使用料金は発生しません) などの任意の Microsoft サービスと使用できる Azure AD 無料版から、Enterprise Mobility Suite (EMS)、Azure AD Premium (P1 および P2) や Azure AD Basic、Azure Multi-Factor Authentication (MFA) などの Azure AD の有料バージョンまで、さまざま機能と技術のバージョンが提供されています。 多くの Microsoft オンライン サービスと同様、ほとんどの Azure AD の有料バージョンは、Office 365、Microsoft Intune、Azure AD のように、ユーザーごとの資格を通じて提供されます。 このような場合、サービスの購入は&1; つ以上のサブスクリプションで表され、各サブスクリプションには、テナントで事前に購入したライセンスの数が含まれます。 ユーザーごとの資格はライセンスの割り当てによって実現されます。ユーザーと製品が関連付けられ、ユーザーに対してサービス コンポーネントが有効になり、前払いライセンスの&1; つが使用されます。

[今すぐ Azure AD premium をお試しください。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Azure AD サービスの機能の概要については、「 [Azure AD とは](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/)」を参照してください。 詳細については、サービス レベル アグリーメントのページを参照してください。

> [!NOTE]
> Azure の従量課金制のサブスクリプションは、ディレクトリで表され、Azure リソースを作成でき、支払い方法にマップされる点が異なります。 この場合、サブスクリプションに関連付けられているライセンス数は&0; です。 ユーザーのサブスクリプションとの関連付け、つまり管理するサブスクリプション リソースへのアクセスは、サブスクリプションにマップされている Azure のリソースを操作するためのアクセス許可が付与されることによって実現されます。

## <a name="how-does-azure-ad-licensing-work"></a>Azure AD ライセンスのしくみ

ライセンス ベースの Azure AD サービスは、Azure AD ディレクトリ/サービスのテナントのサブスクリプションをアクティブにすることで有効になります。 サブスクリプションがアクティブになると、サービスの機能は、ディレクトリ管理者またはサービス管理者が管理し、ライセンスを付与されたユーザーが使用できるようになります。

Enterprise Mobility Suite、Azure AD Premium、Azure AD Basic を購入またはアクティブにすると、有効期間や前払いライセンスを含め、ディレクトリとサブスクリプションが更新されます。 割り当てられているライセンスの数や使用可能なライセンスの数などのサブスクリプション情報は、Azure Portal の該当するディレクトリで、Azure Active Directory &gt; **[ライセンス]** タイルから入手できます。 この場所は、ライセンスの割り当ての管理にも最適です。

各サブスクリプションは&1; つ以上のサービス プランで構成され、各サービス プランはそのサービスの種類に含まれる機能レベル (Azure AD、Azure MFA、Microsoft Intune、Exchange Online、SharePoint Online など) に対応します。  Azure AD のライセンス管理では、サービス プラン レベルの管理は不要です。 これは、含まれるサービスのアクセス管理に詳細な構成モードを利用する Office 365 とは異なります。 Azure AD では、機能の有効化と個々のアクセス許可の管理には、使用中の構成を利用します。

> [!IMPORTANT]
> Azure AD Premium と Azure AD Basic は、Enterprise Mobility Suite サブスクリプションと同様に、プロビジョニングされたディレクトリまたはテナントに限定されます。 サブスクリプションをディレクトリ間で分割することや、他のディレクトリ内のユーザーに資格を与えるために使用することはできません。 ディレクトリ間でサブスクリプションを移動することはできますが、直接購入の場合は、サポート チケットの送信、または取り消しと再購入が必要になります。
>
> Azure AD または Enterprise Mobility Suite をボリューム ライセンス サブスクリプションを通して購入したときに、契約に他の Microsoft Online サービス (Office 365 など) が含まれている場合、アクティブ化は自動的に行われます。

### <a name="assigning-licenses"></a>ライセンスの割り当て

有料機能を構成するうえで必要なことはサブスクリプションの入手だけですが、Azure AD の有料機能を使用するには、適切なユーザーにライセンスを配布する必要があります。 一般に、Azure AD の有料機能へのアクセスが必要なユーザーや Azure AD の有料機能で管理されているユーザーには、ライセンスを割り当てる必要があります。 ライセンスの割り当てとは、ユーザーと Azure AD Premium、Basic、Enterprise Mobility Suite などの購入済みのサービスとのマッピングです。

ディレクトリ内のどのユーザーにライセンスを付与する必要があるかは簡単に管理できます。 これは、Azure ポータルを使用してグループにライセンスを割り当てるか、ポータル、PowerShell、または API を使用して適切なユーザーに直接ライセンスを割り当てることで実現できます。 ライセンスをグループに割り当てると、グループ メンバー全員にライセンスが割り当てられます。 グループに追加されたユーザーには該当するライセンスが割り当てられ、グループから削除されたユーザーは該当するライセンスが削除されます。 グループの割り当てには、使用可能な任意のグループ管理を利用できます。これは、アプリケーションへのグループ ベースの割り当てと一貫しています。 この方法を使用して、ディレクトリ内のすべてのユーザーが自動的に割り当てられるようにルールを設定することや、適切な役職を持つすべてのユーザーにライセンスを確実に付与することができます。さらには、組織内の他の管理者に決定を委任することもできます。 

高度なシナリオや Office 365 ライセンスのシナリオを含め、グループにライセンス割り当てる方法の詳細については、[この記事](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-assignment-azure-portal)を参照してください。

## <a name="getting-started-with-azure-ad-licensing"></a>Azure AD ライセンスの使用

Azure AD を使用するのは簡単です。Azure の無料評価版にサインアップすると、いつでもディレクトリを作成できます。 [組織としてのサインアップの詳細については、こちらを参照してください](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/)。 次の情報は、お使いのディレクトリが、使用する可能性がある、または使用する予定の他の Microsoft サービスと、サービスの取得目的に最適かどうかを確認するのに役立ちます。

ここでベスト プラクティスをいくつか紹介します。

- マイクロソフトが提供するいずれかの組織向けサービスを既に使用している場合は、Azure AD ディレクトリを既に使用しています。 この場合、他のサービスでも同じディレクトリを使用し続けて、プロビジョニングやハイブリッド シングル サインオン (SSO) など、中心的な ID 管理をそれらのサービスでも利用できるようにする必要があります。 ユーザーは、単一のサインイン エクスペリエンスで、さまざまなサービスの豊富な機能を利用できるようになります。 そのため、従業員用に Azure AD の有料サービスの購入を決定した場合は、これを実現するために同じディレクトリを使用することをお勧めします。

- 別のユーザー (パートナーや顧客など) が Azure AD を使用する予定がある場合、運用環境のサービスから分離して Azure AD サービスを評価する場合、または自社のサービス用にサンドボックス環境を設定する予定の場合は、まず、Azure クラシック ポータルを使用して、新しいディレクトリを作成することをお勧めします。 Azure クラシック ポータルでの新しい Azure AD ディレクトリの作成方法についてさらに説明します。 新しいディレクトリは、グローバル管理者のアクセス許可を持つ外部ユーザーとしてのアカウントと共に作成されます。 このアカウントで Azure Portal にサインインすると、このディレクトリが表示され、ディレクトリのすべての管理タスクにアクセスすることができます。

> [!NOTE]
> Azure AD では "外部ユーザー" をサポートしています。外部ユーザーとは、Microsoft アカウント (MSA) または別のディレクトリの Azure AD の ID を使用して作成された Azure AD のインスタンス内のユーザー アカウントです。 この機能を Microsoft の組織向けのすべてのサービスに拡張することに取り組んでいますが、現在、これらのアカウントは一部のサービスでサポートされていません。たとえば、Office 365 の管理ポータルでは、現在これらのユーザーをサポートしていません。 その結果、Microsoft アカウントを持つ外部ユーザーは Office 365 の管理ポータルに一切アクセスできず、他の Azure AD ディレクトリの外部ユーザーは無視されます。 後者の場合、ユーザーが最初に作成された Azure AD または Office 365 ディレクトリのユーザーのローカル アカウントのみが、これらの環境にアクセスできます。

次に示すように、Azure AD にはさまざまな有料バージョンがあります。 これらのバージョンは、購入の可否に若干の違いがあります。

|  製品       |              EA/VL  | 開く  | CSP |  MPN 使用権  | 直接購入 |  試用版 |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | ○   |    ○  |    ○  |     ○ | &nbsp;  | ○ |
|  Azure AD Premium P2     |    ○    |   ○   |   ○   | &nbsp;  |  ○  |   ○  
|  Azure AD Premium P1     |    ○   |    ○    |  ○   |  &nbsp; |  ○  |  &nbsp; |             
|  Azure AD Basic          |    ○   |    ○   |  ○  |  ○  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>1 つ以上のライセンスの評価版を選択する

Azure Active Directory &gt; **[クイック スタート]** で、Azure AD Premium または Enterprise Mobility Suite 試用版サブスクリプションをアクティブ化することができます。

![select a license trial](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

現在、試用版ライセンスは **[ライセンス]** ブレードから入手できます。

### <a name="assign-licenses-to-users-and-groups"></a>ユーザーとグループにライセンスを割り当てる

サブスクリプションがアクティブになったら、自分自身にライセンスを割り当てて、ブラウザーを更新してすべての機能が表示されることを確認する必要があります。 次の手順では、Azure AD の有料機能へのアクセスが必要なユーザーや有料機能に含める必要があるユーザーにライセンスを割り当てます。 上の「[ライセンスの割り当て](#assigning-licenses)」で説明したように、一番良い方法は、目的の対象ユーザーを表すグループを特定し、そのグループにライセンスを割り当てることです。この方法では、そのライフサイクルにおいて、グループに追加されたユーザーにはライセンスが割り当てられ、グループから削除されたユーザーはライセンスが削除されます。

> [!NOTE]
> 一部の Microsoft サービスは場所によっては使用できません。ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定する必要があります。 これは、Azure Portal の [ユーザー] &gt; [プロファイル] &gt; [設定] セクションで行います。 グループ ライセンス割り当てを使用するとき、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。

ライセンスをグループまたは個々のユーザーに割り当てるには、Azure Active Directory &gt; [ライセンス] &gt; [All Products (すべての製品)] で、1 つ以上の製品を選択し、コマンド バーの **[割り当て]** ボタンをクリックします。

![select a license to assign](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

新しいブレードが表示され、ここで複数のユーザーまたはグループを選択できます。必要に応じて製品のサービス プランを無効にすることもできます。 上部の検索バーを使用すると、ユーザーやグループの名前を検索できます。

![select a user or group for license assignment](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

ライセンスをグループに割り当てるとき、グループ内のユーザー数によっては、すべてのユーザーがライセンスを継承するまでに少し時間がかかることがあります。 処理の状態は、**[ライセンス]** タイルの下のグループのブレードで確認できます。

![license assignment status](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Azure AD および EMS 製品を管理しているとき、Azure AD ライセンスの割り当て中に割り当てエラーが発生する可能性がありますが、発生するのは比較的まれです。 割り当てで発生する可能性があるエラーは次の内容に限定されます。
- 割り当て競合: ユーザーが、現在のライセンスと互換性のないライセンスに以前から割り当てられている状況です。 この場合は、新しいライセンスを割り当てるために現在のライセンスを削除する必要があります。
- 利用可能なライセンス数の上限を超えました: 割り当てられているグループ内のユーザーの数が利用可能なライセンスを超えたときに、ライセンスの不足のため、ユーザーの割り当ての状態に割り当ての失敗が反映されます。

グループのライセンス割り当ての詳細についてはこの記事で説明しています。

### <a name="view-assigned-licenses"></a>割り当てられたライセンスを表示する

割り当て済みライセンスと割り当て可能なライセンスの概要ビューが、Azure Active Directory &gt; **[ライセンス]** &gt; **[All products (すべての製品)]** に表示されます。

![view license summary](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

特定の製品をクリックすると、割り当てられたユーザーとグループの詳しい一覧を表示できます。 **[ライセンスされているユーザー]** に、ライセンスがユーザーに直接割り当てられたか、グループから継承されているかにかかわらず、現在ライセンスを使用しているすべてのユーザーが表示されます。

![view license details](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同様に、**[ライセンスされているグループ]** には、ライセンスが割り当てられているすべてのグループが表示されます。 それらのビューでユーザーまたはグループをクリックすると、**[ライセンス]** ブレードが開き、そのオブジェクトに割り当てられたすべてのライセンスが表示されます。

### <a name="removing-a-license"></a>ライセンスを削除する

ライセンスを削除するには、ユーザーまたはグループに移動して、**[ライセンス]** タイルを開きます。 ライセンスを選択し、**[削除]** をクリックします。

![remove a license](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

ユーザーがグループから継承したライセンスを直接削除することはできません。 代わりに、ライセンスを継承したグループからユーザーを削除します。

### <a name="extending-trials"></a>試用版の拡張

顧客向けの評価版の拡張機能は、Office 365 ポータルからセルフ サービスで使用できます。 顧客の管理者は、Office ポータル (Office ポータルに対するアクセス許可によってアクセス権は異なります) に移動し、Azure AD Premium 試用版を選択できます。 **[Extend trial (試用期間を延長する)]** リンクをクリックすると延長プロセスが開始します。 クレジット カードが必要ですが請求されません。

![extend a trial in the Azure portal](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>次のステップ

グループを使用したライセンス管理の高度なシナリオについてはこの記事をご覧ください。

Azure AD Premium の一部の機能を構成し、使用する準備ができました。

* [セルフサービスのパスワード リセット](active-directory-manage-passwords.md)
* [セルフサービスのグループ管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [アプリケーションへのグループの割り当て](active-directory-manage-groups.md)
* [グループへのライセンスの割り当て](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure AD Premium ライセンスの直接購入](http://aka.ms/buyaadp)

