---
title: RBAC を使用して外部ユーザーの Azure リソースへのアクセスを管理する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) を使用して、組織外のユーザーの Azure リソースへのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 91548a4df4a77623978ea4bcb214b76427c026a6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012027"
---
# <a name="manage-access-to-azure-resources-for-external-users-using-rbac"></a>RBAC を使用して外部ユーザーの Azure リソースへのアクセスを管理する

ロールベースのアクセス制御 (RBAC) では、環境内の特定のリソースへのアクセスが必要なものの、インフラストラクチャ全体や課金に関連するスコープへのアクセスが必ずしも必要ではない外部のコラボレーター、ベンダー、フリーランサーと連携している大企業や SMB 向けの、優れたセキュリティ管理を実現できます。 RBAC では、管理者アカウント (サブスクリプション レベルでのサービス管理者ロール) によって管理される 1 つの Azure サブスクリプションの所有者を柔軟に設定できるほか、管理者権限を付与することなく、複数のユーザーを同じサブスクリプションでの業務に招待することができます。

> [!NOTE]
> Office 365 管理センターからプロビジョニングされた Office 365 サブスクリプションまたは Azure Active Directory ライセンス (例:Azure Active Directory へのアクセス) が Microsoft 365 管理センターからプロビジョニングされたものの場合、RBAC の使用資格はありません。

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>RBAC ロールをサブスクリプション スコープで割り当てる

RBAC が使用されるケースとして一般的な 2 つの例を次に示します (ただし、これらに限定されません)。

* 特定のリソースまたはサブスクリプション全体を管理するために組織の外部のユーザー (管理者ユーザーの Azure Active Directory テナントに属していないユーザー) を招待する場合
* 組織の内部のユーザー (管理者ユーザーの Azure Active Directory テナントに属しているユーザー) であるが、サブスクリプション全体または環境内の特定のリソース グループやリソース スコープへの詳細なアクセスを必要とする別のチームまたはグループに属しているユーザーと連携する場合

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Azure Active Directory の外部のユーザーにサブスクリプション レベルでアクセス権を付与する

RBAC ロールは、サブスクリプションの**所有者**のみが付与できます。 そのため、管理者は、このロールが事前に割り当てられているユーザーまたは Azure サブスクリプションを作成したユーザーとしてログインする必要があります。

管理者としてサインインした後、Azure Portal で [サブスクリプション] を選択し、目的のサブスクリプションを選択します。
![Azure Portal のサブスクリプション ブレード](./media/role-assignments-external-users/0.png) 既定では、管理者ユーザーが Azure サブスクリプションを購入している場合、そのユーザーが **[アカウント管理者]** として表示され、これがサブスクリプション ロールとなります。 Azure サブスクリプション ロールの詳細については、「[ Azure サブスクリプション管理者を追加または変更する](../billing/billing-add-change-azure-subscription-administrator.md)」を参照してください。

この例では、ユーザー "alflanigan@outlook.com" が AAD テナント "Default tenant Azure" の "無料試用版" サブスクリプションの**所有者**です。 このユーザーは初期 Microsoft アカウントが "Outlook" (Microsoft アカウント = Outlook、Live など) である Azure サブスクリプションの作成者なので、このテナントに追加される他のすべてのユーザーの既定のドメイン名は **"\@alflaniganuoutlook.onmicrosoft.com"** となります。 仕様により、新しいドメインの構文は、テナントを作成したユーザーのユーザー名とドメイン名を組み合わせ、拡張子 **".onmicrosoft.com"** を追加することで構成されます。
さらに、新しいテナント用にカスタム ドメイン名を追加して確認すると、ユーザーはそのカスタム ドメイン名でサインインできます。 Azure Active Directory テナントのカスタム ドメイン名を確認する方法の詳細については、[ディレクトリへのカスタム ドメイン名の追加](../active-directory/fundamentals/add-custom-domain.md)に関する記事を参照してください。

この例では、"Default tenant Azure" ディレクトリに "\@alflanigan.onmicrosoft.com" というドメイン名のユーザーのみが含まれています。

サブスクリプションの選択後、管理者ユーザーは **[アクセス制御 (IAM)]**、**[新しいロールの追加]** の順にクリックする必要があります。

![Azure Portal の [アクセス制御 (IAM)] 機能](./media/role-assignments-external-users/1.png)

![Azure Portal の [アクセス制御 (IAM)] 機能で新しいユーザーを追加する](./media/role-assignments-external-users/2.png)

次の手順では、割り当てるロールと RBAC ロールの割り当て先のユーザーを選択します。 管理者ユーザーの場合、**[ロール]** ドロップダウン メニューには Azure で利用できる組み込み RBAC ロールのみが表示されます。 各ロールとその割り当て可能なスコープの詳細については、[Azure リソースの組み込みロール](built-in-roles.md)に関するページを参照してください。

次に、管理者ユーザーは外部ユーザーのメール アドレスを追加する必要があります。 想定される動作として、既存のテナントに外部ユーザーが表示されません。 外部ユーザーを招待すると、そのユーザーは、**[サブスクリプション] の [アクセス制御 (IAM)]** 内に、現在サブスクリプション スコープで RBAC ロールが割り当てられているすべてのユーザーと共に表示されます。

![新しい RBAC ロールにアクセス許可を追加する](./media/role-assignments-external-users/3.png)

![サブスクリプション レベルでの RBAC ロールの一覧](./media/role-assignments-external-users/4.png)

ユーザー "chessercarlton@gmail.com" は、"無料試用版" サブスクリプションの**所有者**になるよう招待されています。 招待を送信すると、外部ユーザーにはアクティブ化リンクが含まれた確認メールが届きます。
![RBAC ロールについての招待メール](./media/role-assignments-external-users/5.png)

組織の外部のユーザーである新しいユーザーは、"Default tenant Azure" ディレクトリ内に既存の属性がありません。 属性は、外部ユーザーがディレクトリ内に記録されることに対して同意した後に作成されます。このディレクトリとは、外部ユーザーがロールを割り当てられたサブスクリプションに関連付けられているディレクトリです。

![RBAC ロールについての招待メールのメッセージ](./media/role-assignments-external-users/6.png)

この時点から外部ユーザーは Azure Active Directory テナントに外部ユーザーとして表示され、Azure Portal で確認できるようになります。

![ユーザー ブレード Azure Active Directory (Azure Portal)](./media/role-assignments-external-users/7.png)

**[ユーザー]** ビューでは、Azure Portal の異なるアイコンの種類によって外部ユーザーを認識できます。

ただし、**所有者**または**共同作成者**のアクセス権を**サブスクリプション** スコープで外部ユーザーに付与しても、**全体管理者**が許可しない限り、管理者ユーザーのディレクトリに外部ユーザーがアクセスすることはできません。 ユーザーのプロパティでは、**[メンバー]** と **[ゲスト]** という 2 つの共通パラメーターがある **[ユーザー タイプ]** を確認できます。 メンバーはディレクトリに登録されているユーザーであるのに対し、ゲストは外部ソースからディレクトリに招待されているユーザーです。 詳細については、「[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](../active-directory/active-directory-b2b-admin-add-users.md)」を参照してください。

> [!NOTE]
> ポータルで資格情報を入力した後に、外部ユーザーが正しいディレクトリを選択してサインインしていることを確認してください。 同じユーザーは、複数のディレクトリにアクセスすることができるほか、Azure Portal の右上にあるユーザー名をクリックすることで、ドロップダウン リストから適切なディレクトリを 1 つ選択することができます。

外部ユーザーは、ディレクトリのゲストとなっている間、Azure サブスクリプションのすべてのリソースを管理できますが、ディレクトリにはアクセスできません。

![Azure Active Directory へのアクセス制限 (Azure portal)](./media/role-assignments-external-users/9.png)

Azure Active Directory と Azure サブスクリプションには、他の Azure リソース (例: 仮想マシン、仮想ネットワーク、Web アプリ、ストレージなど) と Azure サブスクリプションとの間にあるような子と親の関係はありません。 後者はすべて Azure サブスクリプションの下で作成、管理、課金されますが、Azure ディレクトリへのアクセスの管理には Azure サブスクリプションが使用されます。 詳細については、[Azure サブスクリプションを Azure AD に関連付ける方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)に関するページを参照してください。

すべての組み込み RBAC ロールのうち、**所有者**と**共同作成者**は環境内の全リソースへの完全な管理アクセスが可能ですが、共同作成者は新しい RBAC ロールを作成および削除できないという違いがあります。 **仮想マシン共同作成者**のような他の組み込みロールは、リソースの作成先となっている**リソース グループ**に関係なく、名前によって示されるリソースのみへの完全な管理アクセスが可能です。

**仮想マシン共同作成者**の組み込み RBAC ロールをサブスクリプション レベルで割り当てると、そのロールが割り当てられたユーザーは次のようになります。

* デプロイ日や属しているリソース グループに関係なく、すべての仮想マシンを表示できる
* サブスクリプション内の仮想マシンへの完全な管理アクセスができる
* サブスクリプション内の他の種類のリソースを表示できない
* 課金の観点からはどのような変更も加えることができない

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>組み込み RBAC ロールを外部ユーザーに割り当てる

このテストの別のシナリオ向けに、外部ユーザー "alflanigan@gmail.com" が**仮想マシン共同作成者**として追加されています。

![[仮想マシン共同作成者] 組み込みロール](./media/role-assignments-external-users/11.png)

この組み込みロールが割り当てられた外部ユーザーは、通常、仮想マシンと、それに隣接するデプロイ時に必要な Resource Manager 限定のリソースのみを表示および管理できます。 仕様により、これらの制限されたロールは、Azure Portal で作成された対応するリソースのみへのアクセスを提供します。

![Azure Portal の仮想マシン共同作成者ロールの概要](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>同じディレクトリのユーザーにサブスクリプション レベルでアクセス権を付与する

このプロセスのフローは、RBAC ロールを付与する管理者の観点でも、ロールへのアクセス権を付与されるユーザーの観点でも、外部ユーザーを追加するプロセスと同じです。 ここでの違いは、サインイン後にサブスクリプション内のすべてのリソース スコープがダッシュボードで利用できるようになるため、招待されるユーザーに招待メールが届かないことです。

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>リソース グループ スコープで RBAC ロールを割り当てる

RBAC ロールを**リソース グループ** スコープで割り当てるプロセスは、外部ユーザーと内部ユーザー (同じディレクトリに属しているユーザー)、どちらの種類のユーザーの場合でも、サブスクリプション レベルでロールを割り当てるプロセスと同じです。 RBAC ロールを割り当てられているユーザーの環境では、そのユーザーにアクセス権が割り当てられているリソース グループのみを、Azure Portal の **[リソース グループ]** アイコンから表示できます。

## <a name="assign-rbac-roles-at-the-resource-scope"></a>リソース スコープで RBAC ロールを割り当てる

Azure のリソース スコープで RBAC ロールを割り当てるプロセスは、サブスクリプション レベルやリソース グループ レベルでロールを割り当てるプロセスと同じであり、両方のシナリオと同じワークフローに従います。 ここでも、RBAC ロールが割り当てられているユーザーが表示できるのは、**[すべてのリソース]** タブに表示する場合も、ダッシュボードに直接表示する場合も、そのユーザーにアクセス権が割り当てられている項目のみです。

リソース グループ スコープまたはリソース スコープでの RBAC に共通する重要な点は、ユーザーが正しいディレクトリにサインインすることです。

![Azure Portal でのディレクトリ ログイン](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>RBAC ロールを Azure Active Directory グループに割り当てる

Azure の 3 種類のスコープで RBAC を使用するすべてのシナリオでは、割り当て済みユーザーとして各種リソースを管理およびデプロイする特権が与えられ、個人のサブスクリプションを管理する必要がありません。 割り当て済みユーザーによってそれ以降に作成されたすべてのリソースは、RBAC ロールが割り当てられているサブスクリプション、リソース グループ、またはリソース スコープに関係なく、ユーザーがアクセスできる 1 つの Azure サブスクリプションの下で課金されます。 この方法により、リソースの管理者がだれであっても、その Azure サブスクリプション全体について課金管理者のアクセス許可を付与されているユーザーが、使用量の概要を全体的に把握できます。

より大きな組織の場合は、RBAC ロールを同じ方法で Azure Active Directory グループに適用できます。これは、管理者ユーザーの観点で、各ユーザーに対して個別にではなく、チームまたは部門全体に対して詳細なアクセス権を付与する必要があると考えると、極めて時間効率および管理効率に優れた方法です。 この例を示すために、**共同作成者**ロールがテナント内の 1 つのグループにサブスクリプション レベルで追加されています。

![AAD グループ用の RBAC ロールを追加する](./media/role-assignments-external-users/14.png)

これらのグループは、Azure Active Directory 内でのみプロビジョニングされ、管理されるセキュリティ グループです。

