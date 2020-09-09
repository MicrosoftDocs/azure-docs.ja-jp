---
title: ハイブリッド マシンを大規模に Azure に接続する
description: この記事では、Azure Arc 対応サーバー (プレビュー) を利用する Azure に、サービス プリンシパルを使用してマシンを接続する方法について学習します。
ms.date: 07/23/2020
ms.topic: conceptual
ms.openlocfilehash: 07266ce7fb9579e1d4fb1b65394e0b7fdf7aa13d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211409"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>ハイブリッド マシンを大規模に Azure に接続する

要件に応じていくつかの柔軟なオプションを使用して、環境内の複数の Windows または Linux マシンに対して Azure Arc 対応サーバー (プレビュー) を有効にすることができます。 Microsoft が提供するテンプレート スクリプトを使用すると、Azure Arc への接続の確立を含め、インストールのすべての手順を自動化できます。ただし、ターゲット マシンおよび Azure で管理者特権でのアクセス許可を持つアカウントを使用して、このスクリプトを対話的に実行する必要があります。 マシンを Azure Arc 対応サーバー (プレビュー) に接続するには、特権 ID を使用するのではなく、Azure Active Directory [サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)を使用して、[対話的にマシンを接続](onboard-portal.md)できます。 サービス プリンシパルは、`azcmagent` コマンドを使用してマシンを Azure に接続するために必要な最小限のアクセス許可のみが付与される、特殊な制限付きの管理用 ID です。 これは、テナント管理者のような、より高い特権を持つアカウントを使用するよりも安全で、アクセス制御セキュリティのベスト プラクティスに従っています。 サービス プリンシパルは、オンボード中にのみ使用され、その他の目的には使用されません。  

Connected Machine エージェントをインストールして構成するためのインストール方法を使用するには、使用する自動化された方法に、マシンでの管理者のアクセス許可が付与されている必要があります。 Linux ではルート アカウントを使用し、Windows ではローカルの Administrators グループのメンバーとして実行します。

開始する前に、必ず[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このプロセスの終わりに、ハイブリッド マシンが Azure Arc 対応サーバー (プレビュー) に正しく接続されます。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>大規模なオンボーディング用にサービス プリンシパルを作成する

[Azure PowerShell](/powershell/azure/install-az-ps) を使用して、[New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) コマンドレットでサービス プリンシパルを作成できます。 または、[Azure portal を使用したサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページに記載されている手順に従って、このタスクを完了することができます。

> [!NOTE]
> サービス プリンシパルを作成するときのアカウントは、オンボーディングに使用したいサブスクリプションの所有者またはユーザー アクセス管理者であることが必要です。 ロールの割り当てを作成する十分なアクセス許可がない場合、サービス プリンシパルは作成されますが、マシンをオンボードすることはできません。
>

PowerShell を使用してサービス プリンシパルを作成するには、次の手順を実行します。

1. 次のコマンドを実行します。 [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) コマンドレットの出力を変数に格納しておく必要があります。そうしないと、後の手順で必要になるパスワードを取得できなくなります。

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. `$sp` 変数に格納されているパスワードを取得するため、次のコマンドを実行します。

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 出力で、**password** フィールドのパスワード値を見つけてコピーします。 また、**ApplicationId** フィールドの値も見つけてコピーします。 それらは後で安全な場所に保存します。 サービス プリンシパルのパスワードを忘れたり紛失したりした場合は、[`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) コマンドレットを使用してリセットできます。

以下のプロパティの値は、`azcmagent` に渡されるパラメーターと共に使用されます。

* **ApplicationId** プロパティの値は、`--service-principal-id` パラメーターの値に使用されます
* **password** プロパティの値は、エージェントを接続するために使用される `--service-principal-secret` パラメーターに使用されます。

> [!NOTE]
> **Id** プロパティではなく、サービス プリンシパルの **ApplicationId** プロパティを使用するようにしてください。
>

**Azure Connected Machine のオンボード** ロールには、マシンのオンボードに必要なアクセス許可のみが含まれています。 リソース グループまたはサブスクリプションを含めることをそのスコープに許可するため、サービス プリンシパルのアクセス許可を割り当てることができます。 ロールの割り当てを追加するには、「[Azure RBAC と Azure portal を使用してロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-portal.md)」または「[Azure RBAC と Azure CLI を使用してロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」を参照してください。

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

以下の手順では、スクリプト テンプレートを使用して、ハイブリッド マシンに Connected Machine エージェントをインストールし、構成します。このテンプレートでは、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」という記事で説明されているのと同様の手順が実行されます。 違いは、サービス プリンシパルを使用して `azcmagent` コマンドを実行し、Azure Arc への接続を確立する最後の手順です。

以下に、サービス プリンシパル用に使用する `azcmagent` コマンドを構成する設定を示します。

* `tenant-id`:Azure AD の専用インスタンスを表す一意識別子 (GUID)。
* `subscription-id`:その中でマシンが必要な Azure サブスクリプションのサブスクリプション ID (GUID)。
* `resource-group`:接続されたマシンの所属先にしたいリソース グループの名前。
* `location`:[サポート対象の Azure リージョン](overview.md#supported-regions)に関するページを参照してください。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。
* `resource-name`:(*省略可能*) オンプレミス マシンの Azure リソースの表記に使用されます。 この値を指定しない場合は、マシンのホスト名が使用されます。

`azcmagent` コマンドライン ツールの詳細は、[Azcmagent のリファレンス](./manage-agent.md)に関するページを参照して確認することができます。

### <a name="windows-installation-script"></a>Windows インストール スクリプト

次に示すのは、サービス プリンシパルを使用して、完全に自動化された非対話型のエージェント インストールをサポートするように変更された、Windows インストール スクリプト用の Connected Machine エージェントの例です。

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>このスクリプトは、64 ビット版の Windows PowerShell からの実行のみをサポートしています。
>

### <a name="linux-installation-script"></a>Linux インストール スクリプト

次に示すのは、サービス プリンシパルを使用して、完全に自動化された非対話型のエージェント インストールをサポートするように変更された、Linux インストール スクリプト用の Connected Machine エージェントの例です。

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>**Azcmagent** を実行するには、Linux マシンに対する*ルート* アクセス許可が必要です。

エージェントをインストールし、Azure Arc 対応サーバー (プレビュー) に接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されていることを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

- [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の詳細を確認します。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-intro.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。