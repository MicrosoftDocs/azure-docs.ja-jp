---
title: Azure Application Insights のリソース、ロール、アクセス制御 | Microsoft Docs
description: 組織の insights の所有者、共同作成者、閲覧者。
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589600"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights のリソース、ロール、アクセス制御

Azure [Application Insights][start] では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) を利用し、データへのアクセス権を読み取りできる人と更新できる人を制御できます。

> [!IMPORTANT]
> リソース自体ではなく、アプリケーション リソースが属する **リソース グループまたはサブスクリプション** でユーザーにアクセスを割り当てます。 **Application Insights コンポーネントの共同作業者** ロールを割り当てます。 これにより、Web テストとアラート、アプリケーション リソースのアクセス制御が統一されます。 [詳細については、こちらを参照してください](#access)。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>リソース、グループ、サブスクリプション

最初に、いくつかの定義を紹介します。

* **リソース** - Microsoft Azure サービスのインスタンス。 Application Insights リソースはアプリケーションから送られてくる利用統計情報データを収集し、分析し、表示します。  その他の種類の Azure リソースには Web アプリ、データベース、VM があります。
  
    リソースを表示するには、[Azure portal][portal] を開き、サインインし、[すべてのリソース] をクリックします。 リソースを見つけるには、フィルター フィールドに名前の一部を入力します。
  
    ![Azure リソースの一覧](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**リソース グループ**][group] - すべてのリソースが 1 つのグループに属します。 関連リソースを管理するとき、特にアクセス制御の場合、グループは便利な方法です。 たとえば、1 つのリソース グループに Web アプリ、そのアプリを監視する Application Insights リソース、エクスポートされたデータを保持する Storage リソースを配置できます。

* [**サブスクリプション**](https://portal.azure.com) - Application Insights またはその他の Azure リソースを使用するには、Azure サブスクリプションにサインインします。 すべてのリソース グループは 1 つの Azure サブスクリプションに属し、そこでは価格パッケージが選択されるほか、会社のサブスクリプションである場合にメンバーとそのアクセス許可が選択されます。
* [**Microsoft アカウント**][account] - Microsoft Azure サブスクリプション、XBox Live、Outlook.com、およびその他の Microsoft サービスへのサインインに使用するユーザー名とパスワード。

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> リソース グループのコントロール制御

アプリケーションに作成したリソースに加え、アラートと Web テストには非表示の個別リソースもあることを理解することが重要です。 これらはご利用の Application Insights リソースと同じ[リソース グループ](#resource-group)に関連付けられています。 Web サイトやストレージなど、他の Azure サービスをそこに配置することもあります。

## <a name="to-provide-access-to-another-user"></a>別のユーザーにアクセスを与えるには

サブスクリプションまたはリソース グループの所有者権限が必要です。

ユーザーは、[Microsoft アカウント][account]を持っているか、または[組織の Microsoft アカウント](../../active-directory/fundamentals/sign-up-organization.md)にアクセスする必要があります。 個人と Azure Active Directory に定義されているユーザー グループにアクセスを供与できます。

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>リソース グループに移動するか、リソース自体に直接移動する

左側のメニューから **[アクセス制御 (IAM)]** を選択します。

![Azure portal のアクセス制御ボタンのスクリーンショット](./media/resources-roles-access-control/0001-access-control.png)

**[ロールの割り当ての追加]** を選択します

![[追加] ボタンが赤で強調表示されたアクセス制御メニューのスクリーンショット](./media/resources-roles-access-control/0002-add.png)

下記の **[アクセス許可の追加]** ビューは、主に Application Insights リソースに固有のものです。リソース グループなどの上位レベルからアクセス制御のアクセス許可を表示すると、Application Insights 中心のロール以外のその他のロールが表示されます。

Azure のロールベースのアクセス制御のすべての組み込みロールについては、[公式のリファレンス コンテンツ](../../role-based-access-control/built-in-roles.md)をご覧ください。

![アクセス制御のユーザー ロールの一覧のスクリーンショット](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>ロールを選択する

該当する場合は、関連する公式のリファレンス ドキュメントにリンクしています。

| Role | In the resource group |
| --- | --- |
| [所有者](../../role-based-access-control/built-in-roles.md#owner) |ユーザー アクセスを含め、あらゆるものを変更できます。 |
| [Contributor](../../role-based-access-control/built-in-roles.md#contributor) |すべてのリソースを含め、あらゆるものを編集できます。 |
| [Application Insights コンポーネントの共同作成者](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Application Insights リソースを編集できます。 |
| [Reader](../../role-based-access-control/built-in-roles.md#reader) |表示はできますが、何も変更することはできません。 |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Application Insights スナップショット デバッガー機能を使用するためのアクセス許可をユーザーに付与します。 このロールは、所有者ロールにも共同作成者ロールにも含まれていないことに注意してください。 |
| Azure Service Deploy リリース管理の共同作成者 | Azure Service Deploy を使用してデプロイするサービスの共同作成者ロール。 |
| [Data Purger](../../role-based-access-control/built-in-roles.md#data-purger) | 個人データを消去するための特殊なロール。 詳細については、[個人データに関するガイダンス](../logs/personal-data-mgmt.md)についての記事をご覧ください。   |
| ExpressRoute 管理者 | Express Route を作成、削除、管理できます。|
| [Log Analytics Contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics 共同作成者は、すべての監視データを読み取り、監視設定を編集できます。 監視設定の編集には、VM 拡張機能の VM への追加、Azure Storage からログの収集を設定できるようにするためのストレージ アカウント キーの読み取り、Automation アカウントの作成と構成、ソリューションの追加、すべての Azure リソースでの Azure Diagnostics の構成が含まれます。  |
| [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics Reader は、すべての監視データの表示と検索、およびすべての Azure リソース上の Azure Diagnostics 構成の表示など、監視設定の表示を行うことができます。 |
| masterreader | ユーザーがあらゆるものを表示できるようにします。ただし、変更することはできません。 |
| [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | すべての監視データを読み取り、監視設定を更新できます。|
| [監視メトリック パブリッシャー](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Azure リソースに対するメトリックの公開を有効にします。 |
| [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) | すべての監視データを読み取ることができます。 |
| リソース ポリシーの共同作成者 (プレビュー) | リソース ポリシーの作成/変更、サポート チケットの作成、リソース/階層の読み取りを実行する権限により、ユーザーを EA からバックフィルしました。  |
| [User Access Administrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) | ユーザーが Azure リソースへの他のユーザーのアクセスを管理できるようにします。|
| [Website Contributor](../../role-based-access-control/built-in-roles.md#website-contributor) | (Web プランではなく) Web サイトを管理できます。ただし、Web サイトにアクセスすることはできません。|

'Editing' includes creating, deleting and updating:

* リソース
* Web テスト
* 警告
* 連続エクスポート

#### <a name="select-the-user"></a>ユーザーを選択する

対象のユーザーがディレクトリにない場合、Microsoft アカウントを持つユーザーを招待できます。
(Outlook.com、OneDrive、Windows Phone、XBox Live などのサービスを利用している場合、Microsoft アカウントを持っています。)

## <a name="related-content"></a>関連コンテンツ

* [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>ロール メンバーシップを特定する PowerShell クエリ

特定のロールを通知や電子メール アラートとリンクできるため、特定のロールに属すユーザーの一覧の生成に役立ちます。 このような一覧の生成に役立つように、ニーズに応じて調整できる次のサンプル クエリを用意しました。

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>管理者ロール + 共同作成者ロールについてサブスクリプション全体にクエリを実行する

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>所有者と共同作成者について特定の Application Insights リソースのコンテキスト内でクエリを実行する

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>所有者と共同作成者について特定の Application Insights リソースのコンテキスト内でクエリを実行する

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
