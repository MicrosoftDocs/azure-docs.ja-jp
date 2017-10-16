---
title: "ロールベースのアクセス制御のカスタム ロールを作成して Azure の内部および外部ユーザーに割り当てる | Microsoft Docs"
description: "PowerShell と CLI を使用して作成したカスタム RBAC ロールを内部および外部ユーザーに割り当てます"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: cccd0af0c991efe330567c2459717798d116e68f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="intro-on-role-based-access-control"></a>ロールベースのアクセス制御の概要

ロールベースのアクセス制御は Azure Portal 限定の機能です。この機能を利用すると、サブスクリプションの所有者は他のユーザーに詳細なロールを割り当てることができ、そのユーザーは環境内の特定のリソース スコープを管理できるようになります。

RBAC では、環境内の特定のリソースへのアクセスが必要なものの、インフラストラクチャ全体や課金に関連するスコープへのアクセスが必ずしも必要ではない外部のコラボレーター、ベンダー、フリーランサーと連携している大企業や SMB 向けの、優れたセキュリティ管理を実現できます。 RBAC では、管理者アカウント (サブスクリプション レベルでのサービス管理者ロール) によって管理される 1 つの Azure サブスクリプションの所有者を柔軟に設定できるほか、管理者権限を付与することなく、複数のユーザーを同じサブスクリプションでの業務に招待することができます。 管理や課金という観点から、RBAC 機能は、さまざまなシナリオで Azure を利用するための、時間効率と管理効率に優れた方法であることが証明されています。

## <a name="prerequisites"></a>前提条件
Azure 環境で RBAC を使用するには、以下のことが必要です。

* スタンドアロンの Azure サブスクリプションが所有者 (サブスクリプション ロール) としてユーザーに割り当てられている
* Azure サブスクリプションの所有者ロールがある
* [Azure Portal](https://portal.azure.com) にアクセスできる
* ユーザーのサブスクリプションに次のリソース プロバイダーが登録されていることを確認する: **Microsoft.Authorization**。 リソース プロバイダーの登録方法の詳細については、[Resource Manager のプロバイダー、リージョン、API のバージョン、およびスキーマ](/azure-resource-manager/resource-manager-supported-services.md)に関するページを参照してください。
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> O365 ポータルからプロビジョニングされた Office 365 サブスクリプションまたは Azure Active Directory ライセンス (例: Azure Active Directory へのアクセス) に RBAC の使用資格はありません。

## <a name="how-can-rbac-be-used"></a>RBAC の使用方法
RBAC は、Azure の 3 種類のスコープで適用できます。 最上位から最下位までの各スコープは以下のとおりです。

* サブスクリプション (最上位)
* リソース グループ
* リソース スコープ (個々の Azure リソース スコープに対象が設定されたアクセス許可を提供する最下位アクセス レベル)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>RBAC ロールをサブスクリプション スコープで割り当てる
RBAC が使用されるケースとして一般的な 2 つの例を次に示します (ただし、これらに限定されません)。

* 特定のリソースまたはサブスクリプション全体を管理するために組織の外部のユーザー (管理者ユーザーの Azure Active Directory テナントに属していないユーザー) を招待する場合
* 組織の内部のユーザー (管理者ユーザーの Azure Active Directory テナントに属しているユーザー) であるが、サブスクリプション全体または環境内の特定のリソース グループやリソース スコープへの詳細なアクセスを必要とする別のチームまたはグループに属しているユーザーと連携する場合

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Azure Active Directory の外部のユーザーにサブスクリプション レベルでアクセス権を付与する
RBAC ロールを付与できるのは、サブスクリプションの**所有者**のみのため、管理者ユーザーはこのロールが事前に割り当てられているユーザー名か、Azure サブスクリプションを作成したユーザー名でログインする必要があります。

管理者としてサインインした後、Azure Portal で [サブスクリプション] を選択し、目的のサブスクリプションを選択します。
![Azure Portal のサブスクリプション ブレード](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) 既定では、管理者ユーザーが Azure サブスクリプションを購入している場合、そのユーザーが **[アカウント管理者]** として表示され、これがサブスクリプション ロールとなります。 Azure サブスクリプション ロールの詳細については、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](/billing/billing-add-change-azure-subscription-administrator.md)」を参照してください。

この例では、ユーザー "alflanigan@outlook.com" が AAD テナント "Default tenant Azure" の "無料試用版" サブスクリプションの**所有者**です。 このユーザーは初期 Microsoft アカウントが "Outlook" (Microsoft アカウント = Outlook、Live など) である Azure サブスクリプションの作成者なので、このテナントに追加される他のすべてのユーザーの既定のドメイン名は **"@alflaniganuoutlook.onmicrosoft.com"** となります。 仕様により、新しいドメインの構文は、テナントを作成したユーザーのユーザー名とドメイン名を組み合わせ、拡張子 **".onmicrosoft.com"** を追加することで構成されます。
さらに、新しいテナント用にカスタム ドメイン名を追加して確認すると、ユーザーはそのカスタム ドメイン名でサインインできます。 Azure Active Directory テナントのカスタム ドメイン名を確認する方法の詳細については、[ディレクトリへのカスタム ドメイン名の追加](/active-directory/active-directory-add-domain)に関する記事を参照してください。

この例では、"Default tenant Azure" ディレクトリに "@alflanigan.onmicrosoft.com" というドメイン名のユーザーのみが含まれています。

サブスクリプションの選択後、管理者ユーザーは **[アクセス制御 (IAM)]**、**[新しいロールの追加]** の順にクリックする必要があります。





![Azure Portal の [アクセス制御 (IAM)] 機能](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Azure Portal の [アクセス制御 (IAM)] 機能で新しいユーザーを追加する](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

次の手順では、割り当てるロールと RBAC ロールの割り当て先のユーザーを選択します。 管理者ユーザーの場合、**[ロール]** ドロップダウン メニューには Azure で利用できる組み込み RBAC ロールのみが表示されます。 各ロールとその割り当て可能なスコープの詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](/active-directory/role-based-access-built-in-roles.md)」を参照してください。
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found--->

次に、管理者ユーザーは外部ユーザーのメール アドレスを追加する必要があります。 想定される動作として、既存のテナントに外部ユーザーが表示されません。 外部ユーザーを招待すると、そのユーザーは、**[サブスクリプション] の [アクセス制御 (IAM)]** 内に、現在サブスクリプション スコープで RBAC ロールが割り当てられているすべてのユーザーと共に表示されます。





![新しい RBAC ロールにアクセス許可を追加する](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![サブスクリプション レベルでの RBAC ロールの一覧](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

ユーザー "chessercarlton@gmail.com" は、"無料試用版" サブスクリプションの**所有者**になるよう招待されています。 招待を送信すると、外部ユーザーにはアクティブ化リンクが含まれた確認メールが届きます。
![RBAC ロールについての招待メール](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

組織の外部のユーザーである新しいユーザーは、"Default tenant Azure" ディレクトリ内に既存の属性がありません。 属性は、外部ユーザーがディレクトリ内に記録されることに対して同意した後に作成されます。このディレクトリとは、外部ユーザーがロールを割り当てられたサブスクリプションに関連付けられているディレクトリです。





![RBAC ロールについての招待メールのメッセージ](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

この時点から外部ユーザーは Azure Active Directory テナントに外部ユーザーとして表示され、Azure Portal とクラシック ポータルの両方で確認できるようになります。





![ユーザー ブレード Azure Active Directory (Azure Portal)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![ユーザー ブレード Azure Active Directory (Azure クラシック ポータル)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

両方のポータルの **[ユーザー]** ビューでは、以下の点で外部ユーザーを識別できます。

* Azure Portal でのアイコンの種類の違い
* クラシック ポータルでのソース ポイントの違い

ただし、**所有者**または**共同作成者**のアクセス権を**サブスクリプション** スコープで外部ユーザーに付与しても、**全体管理者**が許可しない限り、管理者ユーザーのディレクトリに外部ユーザーがアクセスすることはできません。 ユーザーのプロパティでは、**[メンバー]** と **[ゲスト]** という 2 つの共通パラメーターがある **[ユーザー タイプ]** を確認できます。 メンバーはディレクトリに登録されているユーザーであるのに対し、ゲストは外部ソースからディレクトリに招待されているユーザーです。 詳細については、「[Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](/active-directory/active-directory-b2b-admin-add-users)」を参照してください。
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> ポータルで資格情報を入力した後に、外部ユーザーが正しいディレクトリを選択してサインインしていることを確認してください。 同じユーザーは、複数のディレクトリにアクセスすることができるほか、Azure Portal の右上にあるユーザー名をクリックすることで、ドロップダウン リストから適切なディレクトリを 1 つ選択することができます。

外部ユーザーは、ディレクトリのゲストとなっている間、Azure サブスクリプションのすべてのリソースを管理できますが、ディレクトリにはアクセスできません。





![Azure Active Directory へのアクセス制限 (Azure portal)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory と Azure サブスクリプションには、他の Azure リソース (例: 仮想マシン、仮想ネットワーク、Web アプリ、ストレージなど) と Azure サブスクリプションとの間にあるような子と親の関係はありません。 後者はすべて Azure サブスクリプションの下で作成、管理、課金されますが、Azure ディレクトリへのアクセスの管理には Azure サブスクリプションが使用されます。 詳細については、「[Azure サブスクリプションと Azure AD の関連性](/active-directory/active-directory-how-subscriptions-associated-directory)」を参照してください。

すべての組み込み RBAC ロールのうち、**所有者**と**共同作成者**は環境内の全リソースへの完全な管理アクセスが可能ですが、共同作成者は新しい RBAC ロールを作成および削除できないという違いがあります。 **仮想マシン共同作成者**のような他の組み込みロールは、リソースの作成先となっている**リソース グループ**に関係なく、名前によって示されるリソースのみへの完全な管理アクセスが可能です。

**仮想マシン共同作成者**の組み込み RBAC ロールをサブスクリプション レベルで割り当てると、そのロールが割り当てられたユーザーは次のようになります。

* デプロイ日や属しているリソース グループに関係なく、すべての仮想マシンを表示できる
* サブスクリプション内の仮想マシンへの完全な管理アクセスができる
* サブスクリプション内の他の種類のリソースを表示できない
* 課金の観点からはどのような変更も加えることができない

> [!NOTE]
> RBAC は Azure Portal 限定の機能であり、クラシック ポータルへのアクセス権を付与することはできません。

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>組み込み RBAC ロールを外部ユーザーに割り当てる
このテストの別のシナリオ向けに、外部ユーザー "alflanigan@gmail.com" が**仮想マシン共同作成者**として追加されています。




![[仮想マシン共同作成者] 組み込みロール](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

この組み込みロールが割り当てられた外部ユーザーは、通常、仮想マシンと、それに隣接するデプロイ時に必要な Resource Manager 限定のリソースのみを表示および管理できます。 仕様により、このような制限付きロールでは、一部のリソース (例: 仮想マシン) がクラシック ポータルにデプロイされたままであっても、Azure Portal 内に作成された、ロールに対応するリソースにしかアクセスできません。





![Azure Portal の仮想マシン共同作成者ロールの概要](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>同じディレクトリのユーザーにサブスクリプション レベルでアクセス権を付与する
このプロセスのフローは、RBAC ロールを付与する管理者の観点でも、ロールへのアクセス権を付与されるユーザーの観点でも、外部ユーザーを追加するプロセスと同じです。 ここでの違いは、サインイン後にサブスクリプション内のすべてのリソース スコープがダッシュボードで利用できるようになるため、招待されるユーザーに招待メールが届かないことです。

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>リソース グループ スコープで RBAC ロールを割り当てる
RBAC ロールを**リソース グループ** スコープで割り当てるプロセスは、外部ユーザーと内部ユーザー (同じディレクトリに属しているユーザー)、どちらの種類のユーザーの場合でも、サブスクリプション レベルでロールを割り当てるプロセスと同じです。 RBAC ロールを割り当てられているユーザーの環境では、そのユーザーにアクセス権が割り当てられているリソース グループのみを、Azure Portal の **[リソース グループ]** アイコンから表示できます。

## <a name="assign-rbac-roles-at-the-resource-scope"></a>リソース スコープで RBAC ロールを割り当てる
Azure のリソース スコープで RBAC ロールを割り当てるプロセスは、サブスクリプション レベルやリソース グループ レベルでロールを割り当てるプロセスと同じであり、両方のシナリオと同じワークフローに従います。 ここでも、RBAC ロールが割り当てられているユーザーが表示できるのは、**[すべてのリソース]** タブに表示する場合も、ダッシュボードに直接表示する場合も、そのユーザーにアクセス権が割り当てられている項目のみです。

リソース グループ スコープまたはリソース スコープでの RBAC に共通する重要な点は、ユーザーが正しいディレクトリにサインインすることです。





![Azure Portal でのディレクトリ ログイン](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>RBAC ロールを Azure Active Directory グループに割り当てる
Azure の 3 種類のスコープで RBAC を使用するすべてのシナリオでは、割り当て済みユーザーとして各種リソースを管理およびデプロイする特権が与えられ、個人のサブスクリプションを管理する必要がありません。 割り当て済みユーザーによってそれ以降に作成されたすべてのリソースは、RBAC ロールが割り当てられているサブスクリプション、リソース グループ、またはリソース スコープに関係なく、ユーザーがアクセスできる 1 つの Azure サブスクリプションの下で課金されます。 この方法により、リソースの管理者がだれであっても、その Azure サブスクリプション全体について課金管理者のアクセス許可を付与されているユーザーが、使用量の概要を全体的に把握できます。

より大きな組織の場合は、RBAC ロールを同じ方法で Azure Active Directory グループに適用できます。これは、管理者ユーザーの観点で、各ユーザーに対して個別にではなく、チームまたは部門全体に対して詳細なアクセス権を付与する必要があると考えると、極めて時間効率および管理効率に優れた方法です。 この例を示すために、**共同作成者**ロールがテナント内の 1 つのグループにサブスクリプション レベルで追加されています。





![AAD グループ用の RBAC ロールを追加する](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

これらのグループは、Azure Active Directory 内でのみプロビジョニングされ、管理されるセキュリティ グループです。

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>サポート要求を開くためのカスタム RBAC ロールを PowerShell を使用して作成する
Azure で利用できる組み込み RBAC ロールでは、環境内の使用可能なリソースに基づき一定のアクセス許可レベルが保証されます。 ただし、これらのロールがいずれも管理者ユーザーのニーズに合わない場合は、カスタム RBAC ロールを作成して、さらにアクセスを制限する方法もあります。

カスタム RBAC ロールを作成するには、組み込みロールを 1 つ取得し、それを編集してから元の環境内にインポートする必要があります。 ロールのダウンロードとアップロードは PowerShell または CLI を使用して管理されます。

サブスクリプション レベルで詳細なアクセス権を付与できるほか、招待されたユーザーが柔軟にサポート要求を開くこともできるカスタム ロールを作成するうえで、その前提条件を理解しておくことが重要です。

この例では、すべてのリソース スコープを閲覧できるものの、その編集や新しいリソースの作成はできないアクセス権をユーザーに付与する、組み込みロールの**閲覧者**がカスタマイズされ、ユーザーがサポート要求を開くこともできるようになっています。

最初の操作である**閲覧者**ロールのエクスポートは、管理者特権のアクセス許可を使用し、管理者として実行した PowerShell で行う必要があります。

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![閲覧者 RBAC ロールの PowerShell スクリーンショット](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

次に、ロールの JSON テンプレートを抽出する必要があります。





![カスタム閲覧者 RBAC ロールの JSON テンプレート](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

標準的な RBAC ロールは、**Actions**、**NotActions**、**AssignableScopes** という 3 つのメイン セクションから構成されています。

**Actions** セクションには、このロールに許可されているすべての操作が記述されています。 各アクションがリソース プロバイダーから割り当てられている点を理解しておくことが重要です。 この場合、サポート チケットの作成のために **Microsoft.Support** リソース プロバイダーが記述されている必要があります。

利用可能かつサブスクリプションに登録されているすべてのリソース プロバイダーを表示するには、PowerShell を使用します。
```
Get-AzureRMResourceProvider

```
さらに、リソース プロバイダーの管理に利用できるすべての PowerShell コマンドレットを確認することもできます。
    ![リソース プロバイダーの管理に関する PowerShell スクリーンショット](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

特定の RBAC ロールのすべての操作を制限するには、リソース プロバイダーが **NotActions** セクションに記述されます。
最後に、RBAC ロールには、ロールが使用されるサブスクリプションの明示的なサブスクリプション ID が含まれていなければなりません。 サブスクリプション ID は **AssignableScopes** に記述されています。記述されていない場合は、ロールをサブスクリプションにインポートすることができません。

RBAC ロールを作成してカスタマイズしたら、それを元の環境にインポートする必要があります。

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

この例で作成した RBAC ロールのカスタム名は "Reader support tickets access level" であり、ユーザーはサブスクリプション内のすべてのリソースを表示できるほか、サポート要求を開くこともできます。

> [!NOTE]
> サポート要求を開く操作が許可される組み込み RBAC ロールは、**所有者**と**共同作成者**の 2 つだけです。 ユーザーがサポート要求を開けるようにするには、そのユーザーにサブスクリプション スコープ限定で RBAC ロールを割り当てる必要があります。これは、すべてのサポート要求が Azure サブスクリプションに基づき作成されるためです。

この新しいカスタム ロールは同じディレクトリのユーザーに割り当てられています。





![Azure Portal にインポートされたカスタム RBAC ロールのスクリーンショット](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![同じディレクトリのユーザーに対するインポート済みカスタム RBAC ロールの割り当てのスクリーンショット](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![インポート済みカスタム RBAC ロールのアクセス許可のスクリーンショット](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

このカスタム RBAC ロールの以下のような制限を強調するために、例をさらに詳しく説明しました。
* 新しいサポート要求を作成できる
* 新しいリソース スコープ (例: 仮想マシン) を作成できない
* 新しいリソース グループを作成できない





![カスタム RBAC ロールのスクリーンショット (サポート要求を作成している)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![カスタム RBAC ロールのスクリーンショット (VM を作成できない)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![カスタム RBAC ロールのスクリーンショット (新しい RG を作成できない)](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>サポート要求を開くためのカスタム RBAC ロールを Azure CLI を使用して作成する
Mac で作業しているため PowerShell にアクセスできないという場合は、Azure CLI を使用します。

カスタム ロールの作成手順は同じですが、CLI を使用した場合、ロールを JSON テンプレートでダウンロードできないという唯一の例外があります。ただし、CLI で表示することはできます。

この例では、**バックアップ リーダー**という組み込みロールを選択しました。

```

azure role show "backup reader" --json

```





![CLI のスクリーンショット: バックアップ リーダー ロールの表示](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

JSON テンプレート内のプロパティをコピーしてから Visual Studio でロールを編集し、**Microsoft.Support** リソース プロバイダーが **Actions** セクションに追加されています。これにより、このユーザーはバックアップ コンテナーの閲覧者のままでサポート要求を開くことができます。 ここでも、このロールが使用されるサブスクリプションの ID を **AssignableScopes** セクションに追加する必要があります。

```

azure role create --inputfile <path>

```





![CLI のスクリーンショット: カスタム RBAC ロールのインポート](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

これで、新しいロールが Azure Portal で使用可能になりました。割り当てプロセスは前述の例と同じです。





![Azure Portal のスクリーンショット: CLI 1.0 を使用して作成されたカスタム RBAC ロール](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

最新の Build 2017 の時点では、Azure Cloud Shell が一般提供されています。 Azure Cloud Shell は IDE と Azure Portal を補完するサービスです。 このサービスにより、コンピューターに CLI をインストールする代わりに、Azure 内で認証およびホストされるブラウザーベースのシェルを利用できます。





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
