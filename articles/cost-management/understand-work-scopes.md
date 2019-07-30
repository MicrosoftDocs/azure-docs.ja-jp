---
title: Azure Cost Management のスコープを理解して使用する | Microsoft Docs
description: この記事では、Azure で使用できる課金スコープとリソース管理スコープの概要、およびこれらのスコープを Cost Management と API で使用する方法について説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 699707953ae06afa9cbf3cc7286f94917ba0efca
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490106"
---
# <a name="understand-and-work-with-scopes"></a>スコープを理解して使用する

この記事では、Azure で使用できる課金スコープとリソース管理スコープの概要、およびこれらのスコープを Cost Management と API で使用する方法について説明します。

## <a name="scopes"></a>スコープ

"_スコープ_" とは、Azure リソースの階層内のノードのことで、Azure AD ユーザーがサービスにアクセスして管理するために使用します。 ほとんどの Azure リソースは、リソース グループ内に作成およびデプロイされます (これらはサブスクリプションに含まれています)。 Microsoft では、Azure サブスクリプションの上に、課金データの管理に特化したロールを持つ次の 2 つの階層も用意しています。
- 課金データ (支払いや請求書など)
- クラウド サービス (コストやポリシー ガバナンスなど)

スコープは、お客様が課金データの管理、請求書の表示、およびアカウント全般の管理を行う場所であり、支払い専用のロールを持ちます。 課金とアカウントに関するロールは、[Azure RBAC](../role-based-access-control/overview.md) を使用するリソース管理用のロールとは別々に管理されます。 アクセス制御の違いを含め、各スコープを明確に区別するために、両者をそれぞれ "_課金スコープ_" および "_RBAC スコープ_" と呼びます。

## <a name="how-cost-management-uses-scopes"></a>Cost Management でのスコープの使用方法

Cost Management はリソースよりも上のすべてのスコープで機能するため、請求先アカウント全体であっても 1 つのリソース グループであっても、組織がアクセス権を持つレベルでコストを管理することができます。 課金スコープはお客様の Microsoft 契約 (サブスクリプションの種類) に応じて異なりますが、RBAC スコープの場合はそうではありません。

## <a name="azure-rbac-scopes"></a>Azure RBAC のスコープ

Azure では、リソース管理用の 3 つのスコープがサポートされます。 各スコープでは、アクセスとカバンナスの管理がサポートされます。これにはコスト管理も含まれますが、これに限定されるものではありません。

- [**管理グループ**](../governance/management-groups/index.md) - Azure サブスクリプションを整理するための階層型コンテナー (最大 8 つ)。

    リソースの種類:[Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **サブスクリプション** - Azure リソース用のプライマリ コンテナー。

    リソースの種類:[Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**リソース グループ**](../azure-resource-manager/resource-group-overview.md#resource-groups) -同じライフサイクルを共有する Azure ソリューションの関連するリソースを論理的にグループ化したもの。 たとえば、一緒にデプロイおよび削除されるリソースなどです。

    リソースの種類:[Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

管理グループを使用すると、サブスクリプションを階層に整理できます。 たとえば、管理グループを使用して論理的な組織階層を作成したとします。 その後、実稼働および開発/テスト用のワークロードに使用するサブスクリプションをチームに割り当てます。 そして、各サブシステムまたはコンポーネントを管理するためのリソース グループをサブスクリプション内に作成します。

組織階層を作成することで、コストとポリシーへの準拠を組織的にロールアップすることができます。 そして、各リーダーは自分達の現在のコストを確認して分析することができます。 さらに、予算を作成して不適切な支出パターンを制限し、Advisor の推奨事項を取り入れてコストを最低レベルに最適化することができます。

コストを表示し、必要に応じてコストの構成 (予算やエクスポートなど) を管理するアクセス権を付与するには、ガバナンス スコープで Azure RBAC を使用します。 Azure RBAC を使用して、特定のスコープ以下のロールに定義されている一連の定義済みアクションを実行するアクセス権を、Azure AD ユーザーとグループに付与します。 たとえば、管理グループのスコープに割り当てられているロールによって、入れ子になったサブスクリプションとリソース グループにも同じアクセス権が付与されます。

Cost Management の各スコープでは、次の組み込みロールがサポートされています。

- [**所有者**](../role-based-access-control/built-in-roles.md#owner) – コストを表示し、コストの構成を含めたすべてを管理することができます。
- [**共同作成者**](../role-based-access-control/built-in-roles.md#contributor) – コストを表示し、コストの構成を含めたすべてを管理できますが、アクセスの制御はできません。
- [**閲覧者**](../role-based-access-control/built-in-roles.md#reader) – コストのデータと構成を含めたすべてを表示できますが、変更を加えることはできません。
- [**Cost Management 共同作成者**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – コストの表示、コストの構成の管理、および推奨事項の表示を実行できます。
- [**Cost Management 閲覧者**](../role-based-access-control/built-in-roles.md#cost-management-reader) – コスト データの表示、コストの構成、および推奨事項の表示を実行できます。

Cost Management 共同作成者は、最低限の特権を持つ推奨ロールです。 予算を作成および管理し、コストをより効果的に監視してレポートできるようにエクスポートすることができます。 Cost Management 共同作成者は、エンド ツー エンドのコスト管理シナリオをサポートする追加のロールが必要になることがあります。 次のシナリオで考えてみましょう。

- **予算を超過したときに対応する** – Cost Management 共同作成者は、予算超過に自動的に対応するために、アクション グループを作成および管理するアクセス権も必要です。 予算のしきい値を超えたときに使用するアクション グループを含むリソース グループに対して、[監視の共同作成者](../role-based-access-control/built-in-roles.md#monitoring-contributor)を付与することを検討してください。 特定のアクションを自動化するには、使用する特定のサービス (Automation や Azure Functions など) の追加のロールが必要です。
- **コスト データのエクスポートをスケジュールする** – Cost Management 共同作成者は、ストレージ アカウントにデータをコピーするエクスポートをスケジュールするために、ストレージ アカウントを管理するアクセス権も必要です。 コスト データのエクスポート先となるストレージ アカウントを含むリソース グループに対して、[ストレージ アカウントの共同作成者](../role-based-access-control/built-in-roles.md#storage-account-contributor)を付与することを検討してください。
- **コスト削減の推奨事項を表示する** – Cost Management 閲覧者と Cost Management 共同作成者は、コストの推奨事項を*表示する*ためのアクセス権を既定で所有しています。 ただし、コストの推奨事項に従って操作するためのアクセスでは、個々のリソースに対するアクセス権が必要です。 コストの推奨事項に従って操作したい場合は、[サービス固有のロール](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)の付与を検討してください。

## <a name="enterprise-agreement-scopes"></a>Enterprise Agreement のスコープ

Enterprise Agreement (EA) の課金アカウント (加入契約とも呼ばれます) には、次のスコープがあります。

- [**課金アカウント**](../billing/billing-view-all-accounts.md) -EA 加入契約を表します。 請求書はこのスコープで生成されます。 使用量ベースではない購入 (Marketplace や予約など) は、このスコープでのみ表示されます。 これらは、部門や登録アカウントでは表示されません。

    リソースの種類: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部門** - 任意の登録アカウントをグループ化したものです。

    リソースの種類: `Billing/billingAccounts/departments`

- **登録アカウント** - 1 人のアカウント所有者を表します。 複数のユーザーにアクセス権を付与することはできません。

    リソースの種類: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

ガバナンス スコープは 1 つのディレクトリにバインドされますが、EA の課金スコープは違います。 EA の課金アカウントは、任意の数の Azure AD ディレクトリにまたがるサブスクリプションを持つことができます。

EA の課金スコープでは、次のロールがサポートされます。

- **エンタープライズ管理者** – 課金アカウントの設定とアクセスを管理し、すべてのコストを表示し、コストの構成を管理することができます。 たとえば、予算やエクスポートなどです。 この EA 課金スコープは、[Cost Management 共同作成者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-contributor)と同じ機能を持ちます。
- **エンタープライズ読み取り専用ユーザー** – 課金アカウントの設定、コスト データ、およびコストの構成を表示することができます。 たとえば、予算やエクスポートなどです。 この EA 課金スコープは、[Cost Management 閲覧者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-reader)と同じ機能を持ちます。
- **部門管理者** – 部門の設定 (コスト センターなど) を管理し、すべてのコストにアクセスして表示し、コストの構成を管理することができます。 たとえば、予算やエクスポートなどです。  部門管理者と読み取り専用ユーザーがコストを表示できるように、 **[DA ビューの請求額]** の課金アカウント設定を有効にする必要があります。 **[DA ビューの請求額]** が無効になっている場合、部門のユーザーは、たとえアカウントやサブスクリプションの所有者であっても、いずれのレベルでもコストを表示することはできません。
- **部門の読み取り専用ユーザー** – 部門の設定、コスト データ、およびコストの構成を表示することができます。 たとえば、予算やエクスポートなどです。 **[DA ビューの請求額]** が無効になっている場合、部門のユーザーは、たとえアカウントやサブスクリプションの所有者であっても、いずれのレベルでもコストを表示することはできません。
- **アカウント所有者** – 登録アカウントの設定 (コスト センターなど) を管理し、すべてのコストを表示し、登録アカウントのコストの構成 (予算やエクスポートなど) を管理することができます。 アカウント所有者と RBAC ユーザーがコストを表示できるように、 **[AO ビューの請求額]** の課金アカウント設定を有効にする必要があります。

EA の課金アカウントのユーザーは、請求書に直接アクセスすることができません。 外部のボリューム ライセンス システムから請求書を入手できます。

Azure サブスクリプションは、加入契約アカウントの下に入れ子になっています。 課金ユーザーは、それぞれのスコープの下にあるサブスクリプションとリソース グループのコスト データにアクセスすることができます。 Azure portal でリソースを表示したり管理したりすることはできません。 課金ユーザーがコストを表示するには、Azure portal のサービスの一覧で **[コストの管理と請求]** に移動します。 次に、コストにフィルターを適用し、レポートする必要がある特定のサブスクリプションとリソース グループに絞り込みます。

課金ユーザーは、特定の課金アカウントに明示的に属しているわけではないため、管理グループにアクセスすることはできません。 アクセス権は、管理グループに対して明示的に付与する必要があります。 管理グループでは、入れ子になったすべてのサブスクリプションのコストがロールアップされます。 ただし、含まれるのは使用量ベースの購入のみです。 予約やサード パーティの Marketplace オファリングなどの購入は含まれません。 これらのコストを表示するには、EA の課金アカウントを使用します。

## <a name="individual-agreement-scopes"></a>個々の契約のスコープ

従量課金制などの個々のプランと無料試用版や開発テスト用プランなどの関連する種類から作成された Azure サブスクリプションには、課金アカウントの明示的なスコープはありません。 代わりに、EA アカウント所有者のように、各サブスクリプションにアカウント所有者またはアカウント管理者がいます。

- [**課金アカウント**](../billing/billing-view-all-accounts.md) - 1 つまたは複数の Azure サブスクリプションの 1 人のアカウント所有者を表します。 現在、複数のユーザーにアクセス権を付与することや、集計されたコスト ビューにアクセスすることはできません。

    リソースの種類:適用不可

個々の Azure サブスクリプションのアカウント管理者は、[Azure アカウント センター](https://account.azure.com/subscriptions)から、請求や支払いなどの課金データを表示し、管理することができます。 しかし、Azure portal でコスト データを表示したり、リソースを管理したりすることはできません。 アカウント管理者にアクセス権を付与するには、前述の Cost Management のロールを使用します。

EA とは異なり、個々の Azure サブスクリプションのアカウント管理者は、Azure portal で請求書を表示できます。 Cost Management 閲覧者と Cost Management 共同作成者のロールでは、請求書にアクセスできないことに注意してください。 詳細については、[請求書へのアクセス権を付与する方法](../billing/billing-manage-access.md##give-read-only-access-to-billing)に関する記事をご覧ください。

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft Customer Agreement のスコープ

Microsoft 顧客契約の課金アカウントには、次のスコープがあります。

- **課金アカウント** - 複数の Microsoft 製品およびサービスの顧客契約を表します。 顧客契約の課金アカウントの機能は、EA 加入契約と同じではありません。 EA 加入契約は、課金プロファイルとより密接に関連しています。

    リソースの種類: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **課金プロファイル** - 請求書に含まれるサブスクリプションを定義します。 課金プロファイルは、請求書が生成されるスコープなので、機能的に EA 加入契約に相当します。 同様に、使用量ベースではない購入 (Marketplace や予約など) は、このスコープでのみ表示されます。 これらは、請求書セクションには含まれません。

    リソースの種類: `Microsoft.Billing/billingAccounts/billingProfiles`

- **請求書セクション** - 請求書または課金プロファイルでのサブスクリプションのグループを表します。 請求書セクションは部門と似ています。つまり、複数のユーザーが請求書セクションにアクセスすることができます。

    リソースの種類: `Microsoft.Billing/billingAccounts/invoiceSections`

EA の課金スコープとは異なり、顧客契約の課金アカウントは 1 つのディレクトリにバインドされ "_ます_"。複数の Azure AD ディレクトリにまたがるサブスクリプションを持つことはできません。

顧客契約の課金スコープでは、次のロールがサポートされます。

- **所有者** – 課金設定を管理し、すべてのコストにアクセスして表示し、コストの構成を管理することができます。 たとえば、予算やエクスポートなどです。 この顧客契約の課金スコープは、[Cost Management 共同作成者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-contributor)と同じ機能を持ちます。
- **共同作成者** – 課金設定を管理できますが、すべてのコストにアクセスして表示したり、コストの構成を管理したりすることはできません。 たとえば、予算やエクスポートなどです。 この顧客契約の課金スコープは、[Cost Management 共同作成者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-contributor)と同じ機能を持ちます。
- **閲覧者** – 課金設定、コスト データ、およびコストの構成を表示できます。 たとえば、予算やエクスポートなどです。 この顧客契約の課金スコープは、[Cost Management 閲覧者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-reader)と同じ機能を持ちます。
- **請求書管理者** – 請求書の表示と支払い、コストのデータと構成の表示ができます。 たとえば、予算やエクスポートなどです。 この顧客契約の課金スコープは、[Cost Management 閲覧者の Azure RBAC ロール](../role-based-access-control/built-in-roles.md#cost-management-reader)と同じ機能を持ちます。
- **Azure サブスクリプション作成者** – Azure サブスクリプションの作成、コストの表示、およびコストの構成の管理ができます。 たとえば、予算やエクスポートなどです。 この顧客契約の課金スコープは、EA 加入契約のアカウント所有者のロールと同じ機能を持ちます。

Azure サブスクリプションは、EA 登録アカウントの下に入れ子になっているのと同様に、請求書セクションの下に入れ子になっています。 課金ユーザーは、それぞれのスコープの下にあるサブスクリプションとリソース グループのコスト データにアクセスすることができます。 しかし、Azure portal でリソースを表示したり管理したりすることはできません。 課金ユーザーがコストを表示するには、Azure portal のサービスの一覧で **[コストの管理と請求]** に移動します。 次に、コストにフィルターを適用し、レポートする必要がある特定のサブスクリプションとリソース グループに絞り込みます。

課金ユーザーは、課金アカウントに明示的に属しているわけではないため、管理グループにアクセスすることはできません。 ただし、組織で管理グループが有効になっている場合、すべてのサブスクリプションのコストは、課金アカウントおよびルート管理グループにロールアップされます (どちらも 1 つのディレクトリに制限されているため)。 管理グループには、使用量ベースの購入のみが含まれます。 予約やサード パーティの Marketplace オファリングなどの購入は、管理グループには含まれません。 そのため、課金アカウントとルート管理グループで合計額が異なる場合があります。 これらのコストを表示するには、課金アカウントまたはそれぞれの課金プロファイルを使用します。

## <a name="cloud-solution-provider-csp-scopes"></a>クラウド ソリューション プロバイダー (CSP) のスコープ

クラウド ソリューション プロバイダー (CSP) パートナーは、現在 Cost Management ではサポートされていません。 代わりに、[パートナー センター](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)を使用できます。

## <a name="switch-between-scopes-in-cost-management"></a>Cost Management でスコープを切り替える

Azure portal のすべての Cost Management ビューには、ビューの左上に **[スコープ]** 選択ピルがあります。 これを使ってすばやくスコープを変更できます。 **[スコープ]** ピルをクリックして、スコープ選択ツールを開きます。 課金アカウント、ルート管理グループ、およびルート管理グループの下に入れ子になっていないすべてのサブスクリプションが表示されます。 スコープを選択するには、背景をクリックして強調表示し、下部にある **[選択]** をクリックします。 入れ子になったスコープを表示するには、サブスクリプション内のリソース グループの場合と同様に、スコープ名のリンクをクリックします。 入れ子になった任意のレベルの親スコープを選択するには、スコープ選択ツールの上部にある **[この &lt;スコープ&gt; の選択]** をクリックします。

## <a name="identify-the-resource-id-for-a-scope"></a>スコープのリソース ID を特定する

Cost Management API を使用するときは、スコープを特定することが非常に重要です。 次の情報を使用して、Cost Management API の適切なスコープ URI を構築してください。

### <a name="billing-accounts"></a>課金アカウント

1. Azure portal を開き、サービスの一覧にある **[コストの管理と請求]** に移動します。
2. 課金アカウントのメニューで **[プロパティ]** を選択します。
3. 課金アカウント ID をコピーします。
4. スコープは次のようになります。`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>課金プロファイル

1. Azure portal を開き、サービスの一覧にある **[コストの管理と請求]** に移動します。
2. 課金アカウントのメニューで **[課金プロファイル]** を選択します。
3. 目的の課金プロファイルの名前をクリックします。
4. 課金プロファイルのメニューで **[プロパティ]** を選択します。
5. 課金アカウントと課金プロファイルの ID をコピーします。
6. スコープは次のようになります。`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>請求書セクション

1. Azure portal を開き、サービスの一覧にある **[コストの管理と請求]** に移動します。
2. 課金アカウントのメニューで **[請求書セクション]** を選択します。
3. 目的の請求書セクションの名前をクリックします。
4. 請求書セクションのメニューで **[プロパティ]** を選択します。
5. 課金アカウントと請求書セクションの ID をコピーします。
6. スコープは次のようになります。`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA の部門

1. Azure portal を開き、サービスの一覧にある **[コストの管理と請求]** に移動します。
2. 課金アカウントのメニューで **[部門]** を選択します。
3. 目的の部門の名前をクリックします。
4. 部門のメニューで **[プロパティ]** を選択します。
5. 課金アカウントと部門の ID をコピーします。
6. スコープは次のようになります。`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA の登録アカウント

1. Azure portal を開き、サービスの一覧にある **[コストの管理と請求]** に移動します。
2. 課金アカウントのメニューで **[登録アカウント]** を選択します。
3. 目的の登録アカウントの名前をクリックします。
4. 登録アカウントのメニューで **[プロパティ]** を選択します。
5. 課金アカウントと登録アカウントの ID をコピーします。
6. スコープは次のようになります。`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理グループ

1. Azure portal を開き、サービスの一覧にある **[管理グループ]** に移動します。
2. 目的の管理グループに移動します。
3. 管理グループ ID を表からコピーします。
4. スコープは次のようになります。`"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>サブスクリプション

1. Azure portal を開き、サービスの一覧にある **[サブスクリプション]** に移動します。
2. サブスクリプション ID を表からコピーします。
3. スコープは次のようになります。`"/subscriptions/{id}"`

### <a name="resource-groups"></a>リソース グループ

1. Azure portal を開き、サービスの一覧にある **[リソース グループ]** に移動します。
2. 目的のリソース グループの名前をクリックします。
3. リソース グループのメニューで **[プロパティ]** を選択します。
4. リソース ID フィールドの値をコピーします。
5. スコープは次のようになります。`"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management は現在 [Azure Global](https://management.azure.com) と [Azure Government](https://management.usgovcloudapi.net) でサポートされています。 Azure Government の詳細については、[Azure Global および Government API のエンドポイント](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_に関する記事をご覧ください。_

## <a name="next-steps"></a>次の手順

- Cost Management の最初のクイック スタートをまだ完了していない場合は、[コスト分析の開始](quick-acm-cost-analysis.md)に関する記事をご覧ください。
