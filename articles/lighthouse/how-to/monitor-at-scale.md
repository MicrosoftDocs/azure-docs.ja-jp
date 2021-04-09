---
title: 委任されたリソースを大規模に監視する
description: 管理下にある顧客テナント全体を対象に、スケーラブルな方法で効率よく Azure Monitor ログを使用する方法について説明します。
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 98fd984492276dbdfbc2f8001bca19560764a2a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742587"
---
# <a name="monitor-delegated-resources-at-scale"></a>委任されたリソースを大規模に監視する

サービス プロバイダーは、[Azure Lighthouse](../overview.md) に複数の顧客テナントをオンボードしている場合があります。 Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数のテナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

このトピックでは、管理下にある顧客テナント全体を対象に、スケーラブルな方法で [Azure Monitor ログ](../../azure-monitor/logs/data-platform-logs.md)を使用する方法について説明します。 このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

> [!NOTE]
> 管理テナントのユーザーには、委任された顧客サブスクリプションで [Log Analytics ワークスペースの管理に必要なロール](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)が付与されていることを確認してください。

## <a name="create-log-analytics-workspaces"></a>Log Analytics ワークスペースの作成

データを収集するには、Log Analytics ワークスペースを作成する必要があります。 これらの Log Analytics ワークスペースは、Azure Monitor によって収集されたデータのための特別な環境です。 各ワークスペースには、独自のデータ リポジトリと構成があり、データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。

これらのワークスペースは、直接顧客テナントに作成することをお勧めします。 そうすることで、データを自分の環境にエクスポートするのではなく、各顧客のテナント内に留めることができます。 また、Log Analytics でサポートされるリソースまたはサービスの監視を一元化し、監視対象となるデータの種類について、より柔軟な運用が可能となります。

> [!TIP]
> Log Analytics ワークスペースからデータにアクセスする目的で使用される Automation アカウントは、ワークスペースと同じテナントで作成する必要があります。

Log Analytics ワークスペースは、[Azure portal](../../azure-monitor/logs/quick-create-workspace.md)、[Azure CLI](../../azure-monitor/logs/quick-create-workspace-cli.md)、[Azure PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md) のいずれかを使用して作成できます。

> [!IMPORTANT]
> すべてのワークスペースが顧客テナントに作成されている場合でも、管理テナントのサブスクリプションには、Microsoft.Insights リソース プロバイダーも登録する必要があります。

## <a name="deploy-policies-that-log-data"></a>データのログ記録ポリシーをデプロイする

Log Analytics ワークスペースを作成したら、診断データが各テナントの適切なワークスペースに送信されるよう、顧客階層構造全体に [Azure Policy](../../governance/policy/index.yml) をデプロイできます。 実際にデプロイするポリシーは、監視したいリソースの種類によって異なる場合があります。

ポリシーの作成について詳しくは、「[チュートリアル: コンプライアンスを強制するポリシーの作成と管理」](../../governance/policy/tutorials/create-and-manage.md)を参照してください。 特定の種類のリソースを選んで監視するポリシーが簡単に作成できるスクリプトを[コミュニティ ツール](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator)を通じて入手できます。

デプロイするポリシーが決まったら、[委任されたサブスクリプションに対してそれらを大規模にデプロイする](policy-at-scale.md)ことができます。

## <a name="analyze-the-gathered-data"></a>生成されたデータを分析する

ポリシーをデプロイすると、各顧客テナントに作成した Log Analytics ワークスペースにデータがログされます。 管理下にある全顧客の分析情報を入手したければ、[Azure Monitor Workbooks](../../azure-monitor/visualize/workbooks-overview.md) などのツールを使用して、複数のデータ ソースから情報を収集し、分析してください。

## <a name="view-alerts-across-customers"></a>顧客間のアラートを表示する

管理対象の顧客テナントの委任されたサブスクリプションの[アラート](../../azure-monitor/alerts/alerts-overview.md)を表示できます。

管理対象のテナントから、Azure portal または API と管理ツールを使用して、[アクティビティ ログ アラートを作成、表示、および管理](../../azure-monitor/alerts/alerts-activity-log.md)できます。

複数の顧客にわたってアラートを自動的に更新するには、[Azure Resource Graph](../../governance/resource-graph/overview.md) クエリを使用して、アラートをフィルター処理します。 クエリをダッシュボードにピン留めし、すべての適切な顧客とサブスクリプションを選択することができます。 たとえば、次のクエリを使用すると、重大度が 0 および 1 のアラートが表示され、60 分ごとに更新されます。

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>次のステップ

- GitHub の「[ドメインごとのアクティビティ ログ](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain)」ブックを試してみます。
- 複数の Log Analytics ワークスペースに [Update Management ログを問い合わせる](../../automation/update-management/query-logs.md)ことで、パッチ コンプライアンス レポートを追跡するこの [MVP ビルト サンプル ワークブック](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)を試してみます。 
- 他の[テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。