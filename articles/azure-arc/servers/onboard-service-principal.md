---
title: ハイブリッド マシンを大規模に Azure に接続する
description: この記事では、サービス プリンシパルを使用して、Azure Arc 対応サーバーを使用する Azure にマシンを接続する方法について説明します。
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175942"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>ハイブリッド マシンを大規模に Azure に接続する

要件に応じたいくつかの柔軟なオプションを使用して、環境内の複数の Windows または Linux マシンに対して Azure Arc 対応サーバーを有効にすることができます。 Microsoft が提供するテンプレート スクリプトを使用すると、Azure Arc への接続の確立を含め、インストールのすべての手順を自動化できます。ただし、ターゲット マシンおよび Azure で管理者特権でのアクセス許可を持つアカウントを使用して、このスクリプトを対話的に実行する必要があります。

Azure Arc 対応サーバーにマシンを接続するには、特権 ID を使用するのではなく、Azure Active Directory [サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)を使用して[対話的にマシンを接続する](onboard-portal.md)ことができます。 サービス プリンシパルは、`azcmagent` コマンドを使用してマシンを Azure に接続するために必要な最小限のアクセス許可のみが付与される、特殊な制限付きの管理用 ID です。 これは、テナント管理者のような、より高い特権を持つアカウントを使用するよりも安全で、アクセス制御セキュリティのベスト プラクティスに従っています。 サービス プリンシパルは、オンボード中にのみ使用され、その他の目的には使用されません。  

Connected Machine エージェントをインストールして構成するためのインストール方法を使用するには、使用する自動化された方法に、マシンでの管理者のアクセス許可が付与されている必要があります。 Linux ではルート アカウントを使用し、Windows ではローカルの Administrators グループのメンバーとして実行します。

開始する前に、必ず[前提条件](agent-overview.md#prerequisites)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 サポートされているリージョン、および関連するその他の考慮事項については、 [サポート対象の Azure リージョン](overview.md#supported-regions) に関する記事を参照してください。 また、設計と展開の条件、および管理と監視に関する推奨事項については、[規模拡大に関する計画ガイド](plan-at-scale-deployment.md)を参照してください。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>大規模なオンボーディング用にサービス プリンシパルを作成する

[Azure PowerShell](/powershell/azure/install-az-ps) を使用して、[New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) コマンドレットでサービス プリンシパルを作成できます。 または、[Azure portal を使用したサービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md)に関するページに記載されている手順に従って、このタスクを完了することができます。

> [!NOTE]
> サービス プリンシパルを作成する前に、アカウントは、オンボーディングに使用するサブスクリプションの **所有者** または **ユーザー アクセス管理者** ロールのメンバーである必要があります。 ロールの割り当てを構成する十分なアクセス許可がない場合、サービス プリンシパルは作成されますが、マシンをオンボードすることはできません。
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

**Azure Connected Machine のオンボード** ロールには、マシンのオンボードに必要なアクセス許可のみが含まれています。 リソース グループまたはサブスクリプションを含めることをそのスコープに許可するため、サービス プリンシパルのアクセス許可を割り当てることができます。 ロールの割り当てを追加するには、[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)方法、または [Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)方法に関する記事を参照してください。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure portal からインストール スクリプトを生成する

ダウンロードとインストールを自動化し、Azure Arc との接続を確立するスクリプトは、Azure portal から入手できます。 そのプロセスを完了するには、次の手順を実行します。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

1. **[サーバー - Azure Arc]** ページで、左上の **[追加]** を選択します。

1. **[メソッドを選択してください]** ページで、 **[Add multiple servers]\(複数のサーバーを追加\)** タイルを選択し、 **[スクリプトの生成]** を選択します。

1. **[スクリプトの生成]** ページで、Azure 内でマシンを管理するサブスクリプションとリソース グループを選択します。 マシンのメタデータが格納される Azure の場所を選択します。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

1. **[Prerequisites]\(前提条件\)** ページで情報を確認し、 **[次: リソースの詳細]** を選択します。

1. **[リソースの詳細]** ページで、以下を設定します。

    1. **[リソース グループ]** ドロップダウンの一覧で、マシンを管理するリソース グループを選択します。
    1. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。
    1. **[オペレーティング システム]** ドロップダウン リストで、スクリプトを実行するように構成されているオペレーティング システムを選択します。
    1. マシンがプロキシ サーバー経由でインターネットに接続して通信を行っている場合は、プロキシ サーバーの IP アドレス、またはマシンでプロキシ サーバーとの通信に使用されている名前とポート番号を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。
    1. **[次へ: 認証]** を選択します。

1. **[認証]** ページの **[サービス プリンシパル]** ドロップダウン リストで、 **[Arc-for-servers]** を選択します。  次に、 **[次へ: タグ]** を選択します。

1. **[タグ]** ページで、提案されている既定の **物理的な場所のタグ** を確認し、値を入力するか、**カスタム タグ** を 1 つ以上指定して標準をサポートします。

1. **[Next:Download and run script]\(次: スクリプトをダウンロードして実行する\)** を選択します。

1. **[Download and run script]\(次: スクリプトをダウンロードして実行する\)** ページで概要情報を確認し、 **[ダウンロード]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

Windows の場合は `OnboardingScript.ps1`、Linux の場合は `OnboardingScript.sh` をコンピューターに保存するよう求められます。

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

先ほど作成したスクリプト テンプレートがあれば、組織で利用中の自動化ツールを使用して、Connected Machine エージェントを複数のハイブリッド Linux コンピューターおよび Windows コンピューターにインストールして構成できます。 このスクリプトでは、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」に説明されているものに似た手順が実行されます。 違いは、サービス プリンシパルを使用して `azcmagent` コマンドを実行し、Azure Arc への接続を確立する最後の手順です。

以下に、サービス プリンシパル用に使用する `azcmagent` コマンドを構成する設定を示します。

* `service-principal-id`: サービス プリンシパルのアプリケーション ID を表す一意の識別子 (GUID)。
* `service-principal-secret` | サービス プリンシパルのパスワード。
* `tenant-id`:Azure AD の専用インスタンスを表す一意識別子 (GUID)。
* `subscription-id`:その中でマシンが必要な Azure サブスクリプションのサブスクリプション ID (GUID)。
* `resource-group`:接続されたマシンの所属先にしたいリソース グループの名前。
* `location`:[サポート対象の Azure リージョン](overview.md#supported-regions)に関するページを参照してください。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。
* `resource-name`:(*省略可能*) オンプレミス マシンの Azure リソースの表記に使用されます。 この値を指定しない場合は、マシンのホスト名が使用されます。

`azcmagent` コマンドライン ツールの詳細は、[Azcmagent のリファレンス](./manage-agent.md)に関するページを参照して確認することができます。

>[!NOTE]
>Windows PowerShell スクリプトは、64 ビット版の Windows PowerShell からの実行のみをサポートしています。
>

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

![成功したサーバー接続](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>次のステップ

- トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

- [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)の詳細を確認します。 Azure Monitor for VMs を使用してオペレーティング システムとワークロードの監視データを収集したい場合、それを Automation Runbook や機能 (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-introduction.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。
