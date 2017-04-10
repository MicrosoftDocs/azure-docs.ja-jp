---
title: "Azure クラシック ポータルでの Azure Active Directory ユーザーのライセンス | Microsoft Docs"
description: "Office 365、Microsoft Intune、Azure Active Directory Premium エディションと Basic エディションを含む、Microsoft Azure Active Directory のライセンス、しくみ、使用方法、ベスト プラクティスの説明"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d769e8c6-7581-43f5-a3b4-de4b1dca2344
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 7df8805139b6e3cc23041a8201b496fb7fce8063
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-microsoft-azure-active-directory-licensing-in-the-azure-classic-portal"></a>Azure クラシック ポータルでの Microsoft Azure Active Directory ライセンスとは

> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-licensing-get-started-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-licensing-what-is.md)
>
>


Azure Active Directory (Azure AD) は、マイクロソフトのサービスとしての ID (IDaaS) ソリューションおよびプラットフォームです。 Azure AD は、Office 365、Dynamics、Microsoft Intune、Azure (このモードでは Azure AD の使用料金は発生しません) などの任意の Microsoft サービスと使用できる Azure AD 無料版から、Enterprise Mobility Suite (EMS)、Azure AD Premium や Basic、Azure Multi-Factor Authentication (MFA) などの Azure AD の有料バージョンまで、さまざま機能と技術のバージョンが提供されています。 多くの Microsoft オンライン サービスと同様、ほとんどの Azure AD の有料バージョンは、Office 365、Microsoft Intune、Azure AD のように、ユーザーごとの資格を通じて提供されます。 このような場合、サービスの購入は&1; つ以上のサブスクリプションで表され、各サブスクリプションには、テナントで事前に購入したライセンスの数が含まれます。 ユーザーごとの資格はライセンスの割り当てによって実現されます。ユーザーと製品が関連付けられ、ユーザーに対してサービス コンポーネントが有効になり、前払いライセンスの&1; つが使用されます。

[今すぐ Azure AD premium をお試しください。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [!NOTE]
> Azure AD 管理ポータルは、Azure クラシック ポータルの一部です。 Azure AD を使用するうえで Azure の購入は不要ですが、このポータルにアクセスするにはアクティブな Azure サブスクリプションまたは [Azure の試用版サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)が必要です。
>
>

Azure AD サービスの機能の概要については、「 [Azure AD とは](active-directory-whatis.md)」を参照してください。
[Azure AD サービス レベルの詳細](https://azure.microsoft.com/support/legal/sla/)

> [!NOTE]
> Azure の従量課金制のサブスクリプションは、ディレクトリで表され、Azure リソースを作成でき、支払い方法にマップされる点が異なります。 この場合、サブスクリプションに関連付けられているライセンス数は&0; です。 ユーザーのサブスクリプションとの関連付け、つまり管理するサブスクリプション リソースへのアクセスは、サブスクリプションにマップされている Azure のリソースを操作するためのアクセス許可が付与されることによって実現されます。
>
>

## <a name="how-does-azure-ad-licensing-work"></a>Azure AD ライセンスのしくみ
ライセンス ベース (資格ベース) の Azure AD サービスは、Azure AD ディレクトリ/サービスのテナントのサブスクリプションをアクティブにすることで有効になります。 サブスクリプションがアクティブになると、サービスの機能は、ディレクトリ管理者またはサービス管理者が管理し、ライセンスを付与されたユーザーが使用できるようになります。

Enterprise Mobility Suite、Azure AD Premium、Azure AD Basic を購入またはアクティブにすると、有効期間や前払いライセンスを含め、ディレクトリとサブスクリプションが更新されます。 状態、次のライフサイクル イベント、割り当てられているライセンスの数、使用可能なライセンスの数などのサブスクリプション情報は、Azure クラシック ポータルの該当するディレクトリの [ライセンス] タブから入手できます。 この場所は、ライセンスの割り当ての管理にも最適です。

各サブスクリプションは&1; つ以上のサービス プランで構成され、各サービス プランはそのサービスの種類に含まれる機能レベル (Azure AD、Azure MFA、Microsoft Intune、Exchange Online、SharePoint Online など) に対応します。 Azure AD のライセンス管理では、サービス プラン レベルの管理は不要です。 これは、含まれるサービスのアクセス管理に詳細な構成モードを利用する Office 365 とは異なります。 Azure AD では、機能の有効化と個々のアクセス許可の管理には、使用中の構成を利用します。

一般に、Azure AD のサブスクリプション情報は、Azure クラシック ポータルの該当するディレクトリの [ライセンス] タブで管理されます。 Azure AD Premium 以外の Azure AD サブスクリプションは Office ポータルに表示されません。

> [!IMPORTANT]
> Azure AD Premium と Azure AD Basic は、Enterprise Mobility Suite サブスクリプションと同様に、プロビジョニングされたディレクトリまたはテナントに限定されます。 サブスクリプションをディレクトリ間で分割することや、他のディレクトリ内のユーザーに資格を与えるために使用することはできません。 ディレクトリ間でサブスクリプションを移動することはできますが、直接購入の場合は、サポート チケットの送信、または取り消しと再購入が必要になります。
>
> Azure AD または Enterprise Mobility Suite をボリューム ライセンス サブスクリプションを通して購入したときに、契約に他の Microsoft Online サービス (Office 365 など) が含まれている場合、アクティブ化は自動的に行われます。
>
>

Azure AD の有料の機能は、そのディレクトリ全体で利用できます。 たとえば、次のようになります。

* アプリケーションへのグループ ベースの割り当ては、管理している特定のアプリケーションで有効になります。
* セルフ サービスによる詳細なグループ管理機能は、ディレクトリの構成または特定のグループ内で使用できます。
* プレミアム セキュリティ レポートは [レポート] タブにあります。
* クラウド アプリケーションの検出は、Azure Portal の ID の下に表示されます。

### <a name="assigning-licenses"></a>ライセンスの割り当て
有料機能を構成するうえで必要なことはサブスクリプションの入手だけですが、Azure AD の有料機能を使用するには、適切なユーザーにライセンスを配布する必要があります。 一般に、Azure AD の有料機能へのアクセスが必要なユーザーや Azure AD の有料機能で管理されているユーザーには、ライセンスを割り当てる必要があります。 ライセンスの割り当てとは、ユーザーと Azure AD Premium、Basic、Enterprise Mobility Suite などの購入済みのサービスとのマッピングです。

ディレクトリ内のどのユーザーにライセンスを付与する必要があるかは簡単に管理できます。 これは、Azure AD 管理ポータルを使用してグループに割り当てて割り当てルールを作成するか、ポータル、PowerShell、または API を使用して適切なユーザーに直接ライセンスを割り当てることで実現できます。 ライセンスをグループに割り当てると、グループ メンバー全員にライセンスが割り当てられます。 グループに追加されたユーザーには該当するライセンスが割り当てられ、グループから削除されたユーザーは該当するライセンスが削除されます。 グループの割り当てには、使用可能な任意のグループ管理を利用できます。これは、アプリケーションへのグループ ベースの割り当てと一貫しています。 この方法を使用して、ディレクトリ内のすべてのユーザーが自動的に割り当てられるようにルールを設定することや、適切な役職を持つすべてのユーザーにライセンスを確実に付与することができます。さらには、組織内の他の管理者に決定を委任することもできます。

グループ ベースのライセンスの割り当てを使用すると、利用場所が指定されていないユーザーは、割り当て中にディレクトリの場所を継承します。 この場所は、管理者がいつでも変更できます。 エラーのために自動割り当てが失敗した場合、そのライセンスの種類が割り当てられるユーザーの情報にその状態が反映されます。

## <a name="getting-started-with-azure-ad-licensing"></a>Azure AD ライセンスの使用
Azure AD を使用するのは簡単です。Azure の無料評価版にサインアップすると、いつでもディレクトリを作成できます。 [組織としてのサインアップの詳細については、こちらを参照してください](sign-up-organization.md)。 次の情報は、お使いのディレクトリが、使用する可能性がある、または使用する予定の他の Microsoft サービスと、サービスの取得目的に最適かどうかを確認するのに役立ちます。

ここでベスト プラクティスをいくつか紹介します。

* マイクロソフトが提供するいずれかの組織向けサービスを既に使用している場合は、Azure AD ディレクトリを既に使用しています。 この場合、他のサービスでも同じディレクトリを使用し続けて、プロビジョニングやハイブリッド SSO など、中心的な ID 管理をそれらのサービスでも利用できるようにする必要があります。 ユーザーは、単一のログオン エクスペリエンスで、さまざまなサービスの豊富な機能を利用できるようになります。 そのため、従業員用に Azure AD の有料サービスの購入を決定した場合は、これを実現するために同じディレクトリを使用することをお勧めします。
* 別のユーザー (パートナーや顧客など) が Azure AD を使用する予定がある場合、運用環境のサービスから分離して Azure AD サービスを評価する場合、または自社のサービス用にサンドボックス環境を設定する予定の場合は、まず、Azure クラシック ポータルを使用して、新しいディレクトリを作成することをお勧めします。 [Azure クラシック ポータルでの新しい Azure AD ディレクトリの作成方法の詳細については、こちらをご覧ください](active-directory-licensing-directory-independence.md)。 新しいディレクトリは、グローバル管理者のアクセス許可を持つ外部ユーザーとしてのアカウントと共に作成されます。 このアカウントで Azure クラシック ポータルにサインインすると、このディレクトリが表示され、ディレクトリのすべての管理タスクにアクセスすることができます。 (Azure クラシック ポータル経由でアクセスできない) 他の Microsoft サービスを管理する場合は、適切な権限を持つローカル アカウントを作成することをお勧めします。 [Azure AD にユーザー アカウントを作成する方法については、こちらを参照してください](active-directory-create-users.md)。

> [!NOTE]
> Azure AD では "外部ユーザー" をサポートしています。外部ユーザーとは、Microsoft アカウント (MSA) または別のディレクトリの Azure AD の ID を使用して作成された Azure AD のインスタンス内のユーザー アカウントです。 この機能を Microsoft の組織向けのすべてのサービスに拡張することに取り組んでいますが、現在、これらのアカウントは一部のサービスでサポートされていません。たとえば、Office 365 の管理ポータルでは、現在これらのユーザーをサポートしていません。 その結果、Microsoft アカウントを持つ外部ユーザーは Office 365 の管理ポータルに一切アクセスできず、他の Azure AD ディレクトリの外部ユーザーは無視されます。 後者の場合、ユーザーが最初に作成された Azure AD または Office 365 ディレクトリのユーザーのローカル アカウントのみが、これらの環境にアクセスできます。
>
>

次に示すように、Azure AD にはさまざまな有料バージョンがあります。 これらのバージョンは、購入の可否に若干の違いがあります。

| 製品 | EA/VL | 開く | CSP | MPN 使用権 | 直接購入 | 試用版 |
| --- | --- | --- | --- | --- | --- | --- |
| Enterprise Mobility Suite |○ |○ |○ |○ | |○ |
| Azure AD Premium |○ |○ |○ | |○ |○ |
| Azure AD Basic |○ |○ |○ |○ |<br /> |<br /> |

### <a name="select-one-or-more-license-trials"></a>1 つ以上のライセンスの評価版を選択する
 どの場合でも、ディレクトリの [ライセンス] タブで特定の試用版を選択して、Azure AD Premium または Enterprise Mobility Suite の試用版のサブスクリプションをアクティブにできます。 どちらの試用版にも、30 日間のサブスクリプションと 100 ライセンスが含まれています。

![Azure Active Directory 評価版のライセンス プラン](./media/active-directory-licensing-what-is/trial_plans.png)

![Enterprise Mobility Suite 評価版のライセンス プラン](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![有効な評価版のライセンス プラン](./media/active-directory-licensing-what-is/active_license_trials.png)

### <a name="assign-licenses"></a>ライセンスを割り当てる
サブスクリプションがアクティブになったら、自分自身にライセンスを割り当てて、ブラウザーを更新してすべての機能が表示されることを確認する必要があります。 次の手順では、Azure AD の有料機能へのアクセスが必要なユーザーや有料機能に含める必要があるユーザーにライセンスを割り当てます。 上の「ライセンスの割り当て」で説明したように、一番良い方法は、目的の対象ユーザーを表すグループを特定し、そのグループにライセンスを割り当てることです。この方法では、そのライフサイクルにおいて、グループに追加されたユーザーにはライセンスが割り当てられ、グループから削除されたユーザーはライセンスが削除されます。

グループまたは個々のユーザーにライセンスを割り当てるには、割り当てるライセンス プランを選択し、コマンド バーの **[割り当て]** をクリックします。

![有効な評価版のライセンス プラン](./media/active-directory-licensing-what-is/assign_licenses.png)

選択したプランの [割り当て] ダイアログ ボックスで、ユーザーを選択し、右側の **[割り当て]** 列に追加することができます。 ユーザーの一覧のページを移動したり、ユーザー グリッドの右上にある虫眼鏡を使用して特定のユーザーを検索したりできます。 グループを割り当てるには、 **[表示]** メニューの [グループ] を選択し、右側のチェック マークをクリックして表示されている割り当てを最新の情報に更新します。

![ライセンスをグループに割り当てる](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

これで、グループを検索するかページを移動し、同じ方法でグループを **[割り当て]** 列に追加できるようになりました。 これらの方法を使用して、1 回の操作でユーザーとグループの組み合わせを割り当てることができます。 割り当て処理を完了するには、ページの右下隅にあるチェック マークをクリックします。

![ライセンス割り当ての進行状況メッセージ](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

グループが割り当てられると、そのメンバーは 30 分以内にライセンスを継承します。ただし、通常は 1 ～ 2 分以内です。

Azure AD ライセンスの割り当て中に割り当てエラーが発生する可能性がありますが、発生するのは比較的まれです。 割り当てで発生する可能性があるエラーは次の内容に限定されます。

* "割り当て競合" とは、ユーザーが、現在のライセンスと互換性のないライセンスに以前から割り当てられている状況です。 この場合は、新しいライセンスを割り当てるために前のライセンスを削除する必要があります。
* "利用可能なライセンス数の上限を超えました" は、割り当てられているグループ内のユーザーの数が利用可能なライセンスを超えたときに、ライセンスの不足のため、ユーザーの割り当ての状態に割り当ての失敗が反映されます。

### <a name="view-assigned-licenses"></a>割り当てられたライセンスを表示する
使用可能、割り当て済み、次のサブスクリプション ライフサイクル イベントなど、割り当て済みのライセンスの概要ビューが **[ライセンス]** タブに表示されます。

![割り当てられたライセンスの確認](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

(直接または&1; つ以上のグループから継承された) 割り当ての状態とパスを含む、割り当て済みのユーザーとグループの詳細な一覧は、ライセンス プランに移動すると確認できます。

![ライセンスプランに割り当てられたライセンスの詳細](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

ライセンスの削除は、割り当てと同じくらい簡単です。 ユーザーに直接割り当てられているかグループに割り当てられている場合は、ライセンスの種類を選択し、 **[削除]**を選択して、削除する一覧にユーザーまたはグループを追加し、アクションを確認することで、ライセンスを削除できます。 または、ライセンスの種類を開き、該当するユーザーまたはグループを選択し、コマンド バーの **[削除]** をタップします。 グループからユーザーへのライセンスの継承を終了するには、グループからそのユーザーを削除するだけです。

### <a name="extending-trials"></a>試用版の拡張
顧客向けの評価版の拡張機能は、Office 365 ポータルからセルフ サービスで使用できます。 顧客の管理者は、 [Office ポータル](https://portal.office.com/#Billing) (Office ポータルに対するアクセス許可によってアクセス権は異なります) に移動し、Azure AD Premium 評価版を選択できます。 **[評価版の拡張]** リンクをクリックし、指示に従います。 クレジット カード情報を入力する必要がありますが、課金されません。

![Office ポータルにて評価版を拡張](./media/active-directory-licensing-what-is/extend_license_trial.png)

顧客は、サポート要求を送信して、評価版の拡張機能を要求することもできます。 顧客の管理者は、Office 365 ポータル [サポート ページ](http://aka.ms/extendAADtrial) に移動できます (アクセス権は、Office サポート ページに対するアクセス許可によって異なります)。 このページで [機能] の [Subscriptions and Trials (サブスクリプションと試用版)] と [症状] の [Trial questions (試用版の質問)] を選択します。 最後に、環境に関する情報を入力します。

![サポート リクエストによる評価版の拡張](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>次のステップ
Azure AD Premium の一部の機能を構成し、使用する準備ができました。

* [セルフサービスのパスワード リセット](active-directory-manage-passwords.md)
* [セルフサービスのグループ管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [アプリケーションへのグループの割り当て](active-directory-manage-groups.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure AD Premium ライセンスの直接購入](http://aka.ms/buyaadp)

