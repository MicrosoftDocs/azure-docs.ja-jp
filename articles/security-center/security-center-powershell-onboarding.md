---
title: PowerShell を使用して Azure Security Center にオンボードする
description: このドキュメントでは、PowerShell コマンドレットを使用して Azure Security Center を有効にする手順について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 25639509cfb689e025ea4388f302378648af8ba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439495"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>PowerShell を使用した Azure Security Center へのオンボードの自動化

Azure ワークロードは、Azure Security Center の PowerShell モジュールを使用して、プログラムでセキュリティ保護できます。
PowerShell を使用することで、タスクを自動化し、手動タスクに固有の人的エラーを回避できます。 これは、すべて最初からセキュリティ保護する必要がある何百何千というリソースがある数十のサブスクリプションが関係した大規模な展開で特に便利です。

PowerShell を使用して Azure Security Center にオンボードすると、Azure リソースの使用開始と管理をプログラムで自動化し、必要なセキュリティ コントロールを追加できます。

この記事では、変更して環境内サブスクリプション全体に Security Center をロールアウトするために使用できる、PowerShell スクリプトのサンプルを提供します。 

この例では、Security Center を ID: d07c0080-170c-4c24-861d-9c817742786c のサブスクリプションで有効にし、Azure Defender を有効にして、高レベルの保護を提供する推奨設定を適用します。これにより高度な脅威防止および検出機能が提供されます。

1. [Azure Defender](azure-defender.md) を有効にする。 
 
2. サブスクリプションに関連付けられている VM 上で Log Analytics エージェントが収集するデータの送信先である Log Analytics ワークスペースを設定します。この例では、既存のユーザーが定義したワークスペースです (myWorkspace)。

3. [Log Analytics エージェントをデプロイ](security-center-enable-data-collection.md#auto-provision-mma)する Security Center の自動エージェント プロビジョニングをアクティブ化します。

5. 組織の [CISO を Security Center のアラートと注目すべきイベントのセキュリティ連絡先として](security-center-provide-security-contact-details.md)設定します。

6. Security Center の[既定のセキュリティ ポリシー](tutorial-security-policy.md)を割り当てます。

## <a name="prerequisites"></a>前提条件

Security Center のコマンドレットを実行する前に、これらの手順を実行する必要があります。

1. PowerShell を管理者として実行します。

1. PowerShell で次のコマンドを実行します。
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>PowerShell を使用したセキュリティ センターへのオンボード

1. Security Center のリソース プロバイダーに次のようにサブスクリプションを登録します。

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. 省略可能:サブスクリプションのカバレッジレベル (Azure Defender のオン/オフ) を設定します。 未定義の場合、Defender はオフになります。

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. エージェントのレポート先の Log Analytics ワークスペースを構成します。 サブスクリプションの VM のレポート先になる、事前に作成済みの Log Analytics ワークスペースが必要です。 同じワークスペースをレポート先にする複数のサブスクリプションを定義できます。 定義されていない場合、既定のワークスペースが使用されます。

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Azure VM 上での Log Analytics エージェントの自動プロビジョニング インストール:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Azure 仮想マシンが Azure Security Center によって必ず自動的に保護されるように、自動プロビジョニングを有効にすることをお勧めします。
    >

1. 省略可能:オンボードするサブスクリプションのセキュリティ連絡先の詳細を定義することを強くお勧めします。その連絡先は Security Center により生成されるアラートと通知の受信者として使用されます。

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. 既定の Security Center ポリシーのイニシアティブを割り当てます。

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

PowerShell を使用した Azure Security Center のオンボードが正常に行われました。

自動化スクリプトでこれらの PowerShell コマンドレットを使用して、サブスクリプションとリソースをプログラムで繰り返し処理できるようになりました。 これにより、時間が節約され、人的ミスの可能性が減ります。 これは参照用の[サンプル スクリプト](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)として使用できます。




## <a name="see-also"></a>関連項目
PowerShell を使用して Security Center へのオンボードを自動化する方法の詳細については、次の記事を参照してください。

* [Az.Security](/powershell/module/az.security)

Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。