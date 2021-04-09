---
title: Azure Policy を使用して大規模に Azure Monitor をデプロイする
description: Azure Policy を使用して大規模に Azure Monitor 機能をデプロイします。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/08/2020
ms.openlocfilehash: cc55cd17a547b9c63f2c26479d5797fae016d8d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044070"
---
# <a name="deploy-azure-monitor-at-scale-using-azure-policy"></a>Azure Policy を使用して大規模に Azure Monitor をデプロイする
Azure Monitor の一部の機能は構成を 1 回または限られた回数行うだけで済みますが、それ以外の機能については監視するリソースごとに繰り返す必要があります。 この記事では、Azure Policy を使用して Azure Monitor を大規模に実装し、監視がすべての Azure リソースに対して確実に一貫して正確に構成されるようにする方法について説明します。

たとえば、既存のすべての Azure リソースと、自分が作成するそれぞれの新しいリソースに対して、診断設定を作成する必要があるとします。 さらに、仮想マシンを作成するたびにエージェントをインストールして構成する必要があるとします。 PowerShell や CLI などの方法は Azure Monitor のすべての機能に対して使用できるため、それらの方法を使用してこれらのアクションを実行できます。 Azure Policy を使用すると、リソースを作成または変更するたびに適切な構成を自動的に実行するロジックを設定できます。


## <a name="azure-policy"></a>Azure Policy
このセクションでは、最小限の労力で Azure サブスクリプションまたは管理グループ全体にわたって組織標準を評価および適用できる [Azure Policy](../governance/policy/overview.md) について簡単に紹介します。 詳細については、[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

Azure Policy を使用すると、作成されるすべてのリソースに対して構成要件を指定し、準拠していないリソースを特定したり、リソースの作成をブロックしたりできるほか、必要な構成を追加したりすることもできます。 これは、新しいリソースの作成や既存のリソースの変更のための呼び出しをインターセプトすることによって機能します。 ポリシー定義で想定されるプロパティと一致しない場合に要求を拒否したり、非準拠のフラグを設定したりするなどの効果で応答できます。また、関連するリソースをデプロイすることもできます。 **deployIfNotExists** または **modify** ポリシー定義を使用して、既存のリソースを修正できます。

Azure Policy は、次の表に示されるオブジェクトで構成されます。 それぞれの詳細な説明については、「[Azure Policy のオブジェクト](../governance/policy/overview.md#azure-policy-objects)」を参照してください。

| Item | 説明 |
|:---|:---|
| ポリシー定義 | リソースのコンプライアンス条件と、条件が満たされた場合に実行する効果が記述されます。 特定の種類のすべてのリソース、または特定のプロパティに一致するリソースのみを対象にすることができます。 その効果として、リソースにコンプライアンスのフラグを設定したり、関連するリソースをデプロイしたりできます。 ポリシー定義は、「[Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)」に説明されているように、JSON を使用して記述されます。 効果については、「[Azure Policy の効果について](../governance/policy/concepts/effects.md)」で説明されています。
| ポリシー イニシアティブ | まとめて適用する必要があるポリシー定義のグループ。 たとえば、1 つはリソース ログを Log Analytics ワークスペースに送信し、もう 1 つはリソース ログをイベント ハブに送信するポリシー定義があるとします。 両方のポリシー定義を含むイニシアティブを作成し、個々のポリシー定義ではなくそのイニシアティブをリソースに適用します。 イニシアティブは、[Azure Policy のイニシアティブの構造](../governance/policy/concepts/initiative-definition-structure.md)に関するページで説明されているように、JSON を使用して記述されます。 |
| 割り当て | ポリシー定義またはイニシアティブは、スコープに割り当てられるまで有効になりません。 たとえば、ポリシーをリソース グループに割り当てて、そのリソースに作成されたすべてのリソースに適用するか、サブスクリプションに適用してそのサブスクリプションのすべてのリソースに適用します。  詳細については、「[Azure Policy の割り当ての構造](../governance/policy/concepts/assignment-structure.md)」を参照してください。 |

## <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 用の組み込みポリシー定義
Azure Policy には、Azure Monitor に関連するいくつかの定義があらかじめ組み込まれています。 これらのポリシー定義は、既存のサブスクリプションに割り当てることも、独自のカスタム定義を作成するための基礎として使用することもできます。 **[監視]** カテゴリの組み込みポリシーの完全な一覧については、[Azure Monitor 用の Azure Policy 組み込みポリシー定義](.//policy-reference.md)に関するページを参照してください。

監視に関連する組み込みポリシー定義を表示するには、次の手順を実行します。

1. Azure portal で **Azure Policy** に移動します。
2. **[定義]** を選択します。
3. **[種類]** で *[組み込み]* を選択し、 **[カテゴリ]** で *[監視]* を選択します。

  ![監視カテゴリおよび組み込みの種類のポリシー定義の一覧を示す Azure portal 内の [Azure Policy Definitions]\(Azure Policy 定義\) ページのスクリーンショット。](media/deploy-scale/builtin-policies.png)


## <a name="diagnostic-settings"></a>診断設定
[診断設定](essentials/diagnostic-settings.md)を使用して、リソース ログとメトリックを Azure リソースから複数の場所 (通常は Log Analytics ワークスペース) に収集します。これにより、[ログ クエリ](logs/log-query-overview.md)と[ログ アラート](alerts/alerts-log.md)を使用してデータを分析できます。 Policy を使用して、リソースを作成するたびに診断設定を自動的に作成します。

Azure リソースの種類にはそれぞれ、診断設定に一覧表示する必要がある一意のカテゴリのセットがあります。 このため、リソースの種類ごとに別個のポリシー定義が必要です。 一部のリソースの種類には、変更せずに割り当てることができる組み込みのポリシー定義があります。 その他のリソースの種類については、カスタム定義を作成する必要があります。

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 用の組み込みポリシー定義
リソースの種類ごとに 2 つの組み込みポリシー定義があります。1 つは Log Analytics ワークスペースへの送信用、もう 1 つはイベント ハブへの送信用です。 必要な場所が 1 つだけの場合は、そのリソースの種類用のポリシーを割り当てます。 両方が必要な場合は、そのリソース用の両方のポリシー定義を割り当てます。

たとえば、次の画像は、Data Lake Analytics の組み込みの診断設定ポリシー定義を示しています。

  ![Data Lake Analytics 用の 2 つの組み込み診断設定ポリシー定義を示す [Azure Policy Definitions]\(Azure Policy 定義\) ページの部分的なスクリーンショット。](media/deploy-scale/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>カスタム ポリシー定義
組み込みポリシーがないリソースの種類の場合、カスタム ポリシー定義を作成する必要があります。 これは、既存の組み込みポリシーをコピーし、リソースの種類に合わせて変更することによって、Azure portal で手動で行えます。 ただし、PowerShell ギャラリーにあるスクリプトを使用して、プログラムによってポリシーを作成する方が効率的です。

[Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) スクリプトを使用すると、PowerShell または CLI を使用してインストールできる特定のリソースの種類用ポリシー ファイルを作成できます。 診断設定のカスタム ポリシー定義を作成するには、次の手順を使用します。


1. [Azure PowerShell](/powershell/azure/install-az-ps) がインストールされていることを確認します。
2. 次のコマンドを使用して、スクリプトをインストールします。
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. ログの送信先を指定するパラメーターを使用してスクリプトを実行します。 サブスクリプションとリソースの種類を指定するように求められます。 たとえば、Log Analytics ワークスペースとイベント ハブに送信するポリシー定義を作成するには、次のコマンドを使用します。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

4. または、コマンドでサブスクリプションとリソースの種類を指定することもできます。 たとえば、Azure SQL Server データベースの Log Analytics ワークスペースとイベント ハブに送信するポリシー定義を作成するには、次のコマンドを使用します。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. このスクリプトでは、ポリシー定義ごとに、azurepolicy.json、azurepolicy.rules.json、azurepolicy.parameters.json という名前の 3 つのファイルを含む個別のフォルダーが作成されます。 Azure portal でポリシーを手動で作成する場合は、ポリシー定義全体が含まれている azurepolicy.json の内容をコピーして貼り付けることができます。 PowerShell または CLI で他の 2 つのファイルを使用して、コマンド ラインからポリシー定義を作成します。

    次の例は、PowerShell と CLI の両方からポリシー定義をインストールする方法を示しています。 それぞれには、組み込みポリシー定義を使用して新しいポリシー定義をグループ化するために、**監視** カテゴリを指定するメタデータが含まれています。

      ```azurepowershell
      New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
      ```

      ```azurecli
      az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
      ```

### <a name="initiative"></a>イニシアティブ
一般的な戦略では、ポリシー定義ごとに割り当てを作成する代わりに、各 Azure サービスの診断設定を作成するためのポリシー定義を含むイニシアティブを作成します。 お使いの環境の管理方法に応じて、イニシアティブと管理グループ、サブスクリプション、またはリソース グループの間に割り当てを作成します。 この戦略には次の利点があります。

- リソースの種類ごとに複数の割り当てを作成する代わりに、イニシアティブに対して単一の割り当てを作成します。 同じイニシアティブを複数の監視グループ、サブスクリプション、またはリソース グループに使用します。
- 新しいリソースの種類または送信先を追加する必要が生じたら、イニシアティブを変更します。 たとえば、最初の要件は Log Analytics ワークスペースにのみデータを送信することであったのに後でイベント ハブを追加する必要が生じた場合などです。 新しい割り当てを作成するのではなく、イニシアティブを変更します。

イニシアティブの作成の詳細については、「[定義の作成と割り当て](../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)」を参照してください。 次の推奨事項を検討してください。

- **[カテゴリ]** を **[監視]** に設定して、関連する組み込みポリシー定義およびカスタム ポリシー定義を使用してこれをグループ化します。
- イニシアティブに含まれるポリシー定義の Log Analytics ワークスペースとイベント ハブの詳細を指定する代わりに、共通のイニシアティブ パラメーターを使用します。 これにより、すべてのポリシー定義に共通の値を簡単に指定し、必要に応じてその値を変更することができます。

![イニシアチブ定義](media/deploy-scale/initiative-definition.png)

### <a name="assignment"></a>割り当て 
監視対象のリソースのスコープに応じて、イニシアティブを Azure 管理グループ、サブスクリプション、またはリソース グループに割り当てます。 [管理グループ](../governance/management-groups/overview.md)は、特に組織に複数のサブスクリプションがある場合に、ポリシーのスコープ設定に特に役立ちます。

![Azure portal 内の [Diagnostic settings to Log Analytics workspace]\(Log Analytics ワークスペースに対する診断設定\) の [イニシアティブの割り当て] セクションにある [基本] タブの設定のスクリーンショット。](media/deploy-scale/initiative-assignment.png)

イニシアティブ パラメーターを使用すると、イニシアティブ内のすべてのポリシー定義に対して 1 回でワークスペースまたはその他の詳細を指定することができます。 

![イニシアチブ パラメーター](media/deploy-scale/initiative-parameters.png)

### <a name="remediation"></a>Remediation
イニシアティブは、作成時に各仮想マシンに適用されます。 [修復タスク](../governance/policy/how-to/remediate-resources.md)では、イニシアティブ内のポリシー定義を既存のリソースにデプロイします。これにより、既に作成されているすべてのリソースの診断設定を作成できます。 Azure portal を使用して割り当てを作成するときに、修復タスクを同時に作成することができます。 修復の詳細については、「[Azure Policy を使って準拠していないリソースを修復する](../governance/policy/how-to/remediate-resources.md)」を参照してください。

![イニシアティブの修復](media/deploy-scale/initiative-remediation.png)


## <a name="vm-insights"></a>VM 分析情報
[VM 分析情報](vm/vminsights-overview.md)は、仮想マシンを監視するための Azure Monitor の主要なツールです。 VM 分析情報を有効にすると、Log Analytics エージェントと Dependency Agent の両方がインストールされます。 これらのタスクを手動で実行するのではなく、Azure Policy を使用して、作成時に各仮想マシンが構成されるようにします。

> [!NOTE]
> VM 分析情報には、環境内の準拠していない VM を検出して修復することを可能にする **VM 分析情報ポリシー カバレッジ** と呼ばれる機能が含まれています。 Azure VM 用と Azure Arc に接続されているハイブリッド仮想マシン用の Azure Policy を直接操作する代わりに、この機能を使用できます。Azure 仮想マシン スケール セットの場合は、Azure Policy を使用する割り当てを作成する必要があります。
 

VM 分析情報には、両方のエージェントをインストールして完全な監視を可能にする、次の組み込みのイニシアチブが含まれています。 

|名前 |説明 |
|:---|:---|
|VM 分析情報の有効化 | Azure Arc に接続されている Azure VM とハイブリッド VM に、Log Analytics エージェントと依存関係エージェントをインストールします。 |
|仮想マシン スケール セットに対して Azure Monitor を有効にする | Azure 仮想マシン スケール セットに Log Analytics エージェントと依存関係エージェントをインストールします。 |


### <a name="virtual-machines"></a>仮想マシン
Azure Policy インターフェイスを使用してこれらのイニシアティブの割り当てを作成する代わりに、VM 分析情報の機能を使用して、各スコープ内の仮想マシンの数を調べてイニシアティブが適用されているかどうかを判断することができます。 その後、ワークスペースを構成し、そのインターフェイスを使用して必要な割り当てを作成できます。

このプロセスの詳細については、「[Azure Policy を使用してVM 分析情報を有効にする](./vm/vminsights-enable-policy.md)」を参照してください。

![VM 分析情報ポリシー](media/deploy-scale/vminsights-policy.png)

### <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット
Azure Policy を使用して仮想マシン スケール セットの監視を有効にするには、監視するリソースのスコープに応じて、Azure 管理グループ、サブスクリプション、またはリソース グループに対して、**仮想マシン スケール セットに対して Azure Monitor を有効にする** イニシアティブを割り当てます。 [管理グループ](../governance/management-groups/overview.md)は、特に組織に複数のサブスクリプションがある場合に、ポリシーのスコープ設定に特に役立ちます。

![Azure portal 内の [イニシアティブの割り当て] ページのスクリーンショット。 [イニシアティブ定義] は [Virtual Machine Scale Sets 用の Azure Monitor の有効化] に設定されています。](media/deploy-scale/virtual-machine-scale-set-assign-initiative.png)

データの送信先となるワークスペースを選択します。 「[VM 分析情報の Log Analytics ワークスペースの構成](vm/vminsights-configure-workspace.md)」で説明されているように、このワークスペースには *VM 分析情報* ソリューションがインストールされている必要があります。

![ワークスペースを選択](media/deploy-scale/virtual-machine-scale-set-workspace.png)

このポリシーを割り当てる必要がある既存の仮想マシン スケール セットがある場合は、修復タスクを作成します。

![修復タスク](media/deploy-scale/virtual-machine-scale-set-remediation.png)

### <a name="log-analytics-agent"></a>Log Analytics エージェント
シナリオによっては、Log Analytics エージェントはインストールするが、依存関係エージェントをインストールする必要がない場合があります。 エージェントに組み込みのイニシアチブはありませんが、VM 分析情報に用意されている組み込みのポリシー定義に基づいて独自に作成できます。

> [!NOTE]
> Azure Monitor へのデータ配信に Log Analytics エージェントが必要なため、依存関係エージェントを独自にデプロイする理由がありません。


|名前 |説明 |
|-----|------------|
|Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS) |VM イメージ (OS) が一覧で定義されておらず、このエージェントがインストールされていない場合は、VM を非準拠として報告します。 |
|Linux VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、Log Analytics エージェントがインストールされていない場合は、Linux VM にこのエージェントをデプロイします。 |
|Windows VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、Log Analytics エージェントがインストールされていない場合は、Windows VM にこのエージェントをデプロイします。 |
| [プレビュー]\:Log Analytics エージェントは Linux Azure Arc マシンにインストールされる必要がある |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、ハイブリッド Azure Arc マシンを Linux VM に準拠しないものとして報告します。 |
| [プレビュー]\:Log Analytics エージェントは Windows Azure Arc マシンにインストールされる必要がある |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、ハイブリッド Azure Arc マシンを Windows VM に準拠しないものとして報告します。 |
| [プレビュー]\:Linux Azure Arc マシンに Log Analytics エージェントをデプロイする |VM イメージ (OS) が一覧で定義されており、Linux ハイブリッド Azure Arc マシン用の Log Analytics エージェントがインストールされていない場合は、このエージェントをデプロイします。 |
| [プレビュー]\:Windows Azure Arc マシンに Log Analytics エージェントをデプロイする |VM イメージ (OS) が一覧で定義されており、Windows ハイブリッド Azure Arc マシン用の Log Analytics エージェントがインストールされていない場合は、このエージェントをデプロイします。 |
|仮想マシン スケール セットにおける依存関係エージェントのデプロイの監査 - 一覧にない VM イメージ (OS) |VM イメージ (OS) が一覧で定義されておらず、このエージェントがインストールされていない場合は、仮想マシン スケール セットを非準拠として報告します。 |
|仮想マシン スケール セットにおける Log Analytics エージェントのデプロイの監査 - 一覧にない VM イメージ (OS) |VM イメージ (OS) が一覧で定義されておらず、このエージェントがインストールされていない場合は、仮想マシン スケール セットを非準拠として報告します。 |
|Linux 仮想マシン スケール セット用の Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、Linux 仮想マシン スケール セット用の Log Analytics エージェントがインストールされていない場合は、このエージェントをデプロイします。 |
|Windows 仮想マシン スケール セット用の Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、Windows 仮想マシン スケール セット用の Log Analytics エージェントがインストールされていない場合は、このエージェントをデプロイします。 |


## <a name="next-steps"></a>次のステップ

- [Azure Policy](../governance/policy/overview.md) の詳細を確認する。
- [診断設定](essentials/diagnostic-settings.md)の詳細を確認する。