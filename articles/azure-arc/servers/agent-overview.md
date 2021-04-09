---
title: Connected Machine エージェントの概要
description: この記事では、ハイブリッド環境でホストされている仮想マシンの監視をサポートする、使用可能な Azure Arc 対応サーバー エージェントの詳細な概要を提供します。
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 1fd863ccacc7768401e35254a98c7bb494b3d358
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470491"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Azure Arc 対応サーバー エージェントの概要

Azure Arc 対応サーバー Connected Machine エージェントを使用すると、企業ネットワークまたは他のクラウド プロバイダー上の Azure の外部でホストされている Windows および Linux コンピューターを管理できます。 この記事では、エージェント、システムとネットワークの要件、およびさまざまなデプロイ方法の概要の詳細を示します。

>[!NOTE]
>2020 年 9 月の Azure Arc 対応サーバーの一般リリース以降では、Azure Connected Machine エージェント (バージョン 1.0 以前のエージェント) のプレリリース バージョンはすべて、**2021 年 2 月 2 日** までに **非推奨** となります。  プレリリースされたエージェントが Azure Arc 対応サーバー サービスと通信できなくなる前に、この期間にバージョン 1.0 以降にアップグレードすることができます。

## <a name="agent-component-details"></a>エージェント コンポーネントの詳細

Azure Connected Machine エージェント パッケージには、まとめてバンドルされているいくつかの論理コンポーネントが含まれています。

* Hybrid Instance Metadata Service (HIMDS) は、Azure への接続と接続されたマシンの Azure ID を管理します。

* ゲスト構成エージェントは、ゲスト内ポリシーとゲスト構成機能 (そのマシンが必要なポリシーに準拠しているかどうかの評価など) を提供します。

    切断されたマシンに対する Azure Policy の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)での次の動作に注意してください。

    * 切断されたマシンを対象とするゲスト構成ポリシー割り当てには影響を与えません。
    * ゲスト割り当ては 14 日間ローカルに格納されます。 14 日の期間内に Connected Machine エージェントがサービスに再接続した場合は、ポリシー割り当てが再適用されます。
    * 割り当ては 14 日後に削除され、14 日の期間の後にマシンに再割り当てされることはありません。

* 拡張エージェントは VM 拡張機能 (インストール、アンインストール、アップグレードなど) を管理します。 拡張機能は Azure からダウンロードされ、Windows では `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` フォルダー、Linux の場合は `/opt/GC_Ext/downloads` にコピーされます。 Windows では、拡張機能はパス `%SystemDrive%\Packages\Plugins\<extension>` にインストールされます。Linux では、拡張機能は `/var/lib/waagent/<extension>` にインストールされます。

## <a name="instance-metadata"></a>インスタンス メタデータ

接続されたコンピューターに関するメタデータ情報は、Connected Machine エージェントが Arc 対応サーバーに登録された後に収集されます。 具体的な内容は次のとおりです。

* オペレーティング システムの名前、種類、バージョン
* コンピューター名
* コンピューターの完全修飾ドメイン名 (FQDN)
* Connected Machine エージェントのバージョン
* Active Directory と DNS の完全修飾ドメイン名 (FQDN)
* UUID (BIOS ID)
* Connected Machine エージェントのハートビート
* Connected Machine エージェントのバージョン
* マネージド ID の公開キー
* ポリシーのコンプライアンスの状態と詳細 (Azure Policy のゲスト構成ポリシーを使用している場合)

次のメタデータ情報は、Azure からエージェントによって要求されます。

* リソースの場所 (リージョン)
* 仮想マシン ID
* Tags
* Azure Active Directory マネージド ID 証明書
* ゲスト構成ポリシーの割り当て
* 拡張要求 - インストール、更新、削除。

## <a name="download-agents"></a>エージェントをダウンロードする

Windows および Linux 用の Azure Connected Machine エージェント パッケージは、以下の場所からダウンロードできます。

* Microsoft ダウンロード センターから [Windows エージェント Windows インストーラー パッケージ](https://aka.ms/AzureConnectedMachineAgent)。

* Linux エージェント パッケージは、Microsoft の[パッケージ リポジトリ](https://packages.microsoft.com/)から、ディストリビューションに適切なパッケージ形式 (.RPM または .DEB) を使用して配布されます。

Windows および Linux 用の Azure Connected Machine エージェントは、要件に応じて、手動または自動で最新リリースにアップグレードできます。 詳細については、[このページ](manage-agent.md)を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="supported-environments"></a>サポートされている環境

Arc 対応サーバーでは、Azure の *外部* でホストされている任意の物理サーバーと仮想マシンに対する Connected Machine エージェントのインストールがサポートされています。 これには、VMware、Azure Stack HCI、その他のクラウド環境などのプラットフォームで実行されている仮想マシンが含まれます。 Arc 対応サーバーは Azure VM として既にモデル化されているため、Azure 内で実行されている仮想マシン、または Azure Stack Hub または Azure Stack Edge 上で実行されている仮想マシンへの、エージェントのインストールはサポートされていません。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Azure Connected Machine エージェントでは、次のバージョンの Windows および Linux オペレーティング システムが正式にサポートされています。

- Windows Server 2008 R2、Windows Server 2012 R2 以上 (Server Core を含む)
- Ubuntu 16.04 および 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Linux ホスト名または Windows コンピューター名では、予約語や商標を名前に使用できません。使用した場合、接続されているマシンを Azure に登録しようとすると失敗します。 予約語の一覧については、「[予約されたリソース名のエラーを解決する](../../azure-resource-manager/templates/error-reserved-resource-name.md)」を参照してください。

### <a name="required-permissions"></a>必要なアクセス許可

* マシンをオンボードするには、リソース グループの **Azure Connected Machine のオンボード** または[共同作成者](../../role-based-access-control/built-in-roles.md#contributor) ロールのメンバーである必要があります。

* マシンの読み取り、変更、および削除を行うには、リソース グループの **Azure Connected Machine のリソース管理者** ロールのメンバーである必要があります。

* **スクリプトの生成** メソッドを使用するときにドロップダウン リストからリソース グループを選択するには、少なくともそのリソース グループの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールのメンバーである必要があります。

### <a name="azure-subscription-and-service-limits"></a>Azure サブスクリプションとサービスの制限

Azure Arc 対応サーバーでコンピューターを構成する前に、Azure Resource Manager の[サブスクリプションの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)と[リソース グループの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)を確認して、接続されるコンピューターの数を計画してください。

Azure Arc 対応サーバーでは、1 つのリソース グループで最大 5, 000 個のマシン インスタンスがサポートされます。

### <a name="transport-layer-security-12-protocol"></a>トランスポート層セキュリティ 1.2 プロトコル

Azure に転送中のデータのセキュリティを確保するには、トランスポート層セキュリティ (TLS) 1.2 を使用するようにマシンを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。

|プラットフォーム/言語 | サポート | 詳細情報 |
| --- | --- | --- |
|Linux | Linux ディストリビューションでは、TLS 1.2 のサポートに関して [OpenSSL](https://www.openssl.org) に依存する傾向があります。 | [OpenSSL の Changelog](https://www.openssl.org/news/changelog.html) を参照して、使用している OpenSSL のバージョンがサポートされていることを確認してください。|
| Windows Server 2012 R2 以降 | サポートされています。既定で有効になっています。 | [既定の設定](/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには。|

### <a name="networking-configuration"></a>ネットワーク構成

Linux と Windows 用の Connected Machine エージェントは、TCP ポート 443 を介して安全に Azure Arc へのアウトバウンド通信を行います。 インターネット経由で通信するためにマシンがファイアウォールやプロキシ サーバーを介して接続されている場合は、次を確認してネットワーク構成の要件を把握してください。

> [!NOTE]
> Arc 対応サーバーでは、Connected Machine エージェントのプロキシとして [Log Analytics ゲートウェイ](../../azure-monitor/agents/gateway.md)を使用することはサポートされていません。
>

アウトバウンド接続がファイアウォールやプロキシ サーバーによって制限されている場合は、以下に示す URL がブロックされていないことを確認してください。 エージェントがサービスと通信するために必要な IP 範囲またはドメイン名のみを許可する場合は、次のサービス タグおよび URL へのアクセスを許可する必要があります。

サービス タグ:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

URL:

| エージェントのリソース | 説明 |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |ゲスト構成|
|`*.his.arc.azure.com`|ハイブリッド ID サービス|
|`www.office.com`|Office 365|

プレビュー エージェント (バージョン 0.11 以下) でも、次の URL へのアクセスが必要になります。

| エージェントのリソース | 説明 |
|---------|---------|
|`agentserviceapi.azure-automation.net`|ゲスト構成|
|`*-agentservice-prod-1.azure-automation.net`|ゲスト構成|

各サービス タグ/リージョンの IP アドレスの一覧については、「[Azure IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)」という JSON ファイルを参照してください。 Microsoft では、各 Azure サービスとそれが使用する IP 範囲を含む更新プログラムを毎週発行しています。 JSON ファイル内のこの情報は、各サービス タグに対応する現在の特定時点の IP 範囲の一覧です。 IP アドレスは変更される可能性があります。 ファイアウォール構成に IP アドレス範囲が必要な場合は、**AzureCloud** サービス タグを使用して、すべての Azure サービスへのアクセスを許可してください。 これらの URL のセキュリティ監視または検査を無効にせず、他のインターネット トラフィックと同様に許可してください。

詳細については、[サービス タグの概要](../../virtual-network/service-tags-overview.md)に関するページをご確認ください。

### <a name="register-azure-resource-providers"></a>Azure リソースプロバイダーを登録する

Azure Arc 対応サーバーは、このサービスを使用するために、サブスクリプション内の次の Azure リソース プロバイダーに依存しています。

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

これらが登録されていない場合は、次のコマンドを使って登録できます。

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

「[Azure portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」の手順に従って、Azure portal でリソースプロバイダーを登録することもできます。

## <a name="installation-and-configuration"></a>インストールと構成

要件に応じたさまざまな方法を使用して、ハイブリッド環境内のマシンを直接 Azure に接続することができます。 次の表は、どの方法が組織にとって最も効果的であるかを判断するために各方法について説明しています。

> [!IMPORTANT]
> Connected Machine エージェントを Azure Windows 仮想マシンにインストールすることはできません。 インストールを試みた場合は、インストールによってこの操作が検出され、ロールバックされます。

| Method | 説明 |
|--------|-------------|
| 対話型 | [Azure portal からマシンを接続する方法](onboard-portal.md)に関するページの手順に従って、1 台のマシンまたは少数のマシンにエージェントを手動でインストールします。<br> Azure portal からスクリプトを生成し、マシン上で実行することによって、エージェントのインストールおよび構成手順を自動化することができます。|
| 大規模 | [サービス プリンシパルを使用したマシンの接続](onboard-service-principal.md)に関するページに従って、複数のマシン用にエージェントをインストールして構成します。<br> この方法では、非対話形式でマシンを接続するためのサービス プリンシパルが作成されます。|
| 大規模 | [Windows PowerShell DSC の使用](onboard-dsc.md)方法に関するページの説明に従って複数のマシンにエージェントをインストールして構成します。<br> この方法では、サービス プリンシパルを使用し、PowerShell DSC から非対話形式でマシンを接続します。 |

## <a name="connected-machine-agent-technical-overview"></a>Connected Machine エージェントの技術概要

### <a name="windows-agent-installation-details"></a>Windows エージェントのインストールの詳細

Windows 用 Connected Machine エージェントは、次の 3 つの方法のいずれかを使用してインストールできます。

* `AzureConnectedMachineAgent.msi` ファイルをダブルクリックする。
* コマンド シェルから Windows インストーラー パッケージ `AzureConnectedMachineAgent.msi` を実行して手動で。
* スクリプト化されたメソッドを使用して PowerShell セッションから。

Windows 用 Connected Machine エージェントをインストールした後、次のシステム全体の構成変更が適用されます。

* 次のインストール フォルダーは、セットアップ中に作成されます。

    |Folder |説明 |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |エージェント サポート ファイルが含まれている既定のインストール パス。|
    |%ProgramData%\AzureConnectedMachineAgent |エージェント構成ファイルが含まれています。|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |取得したトークンが含まれています。|
    |%ProgramData%\AzureConnectedMachineAgent\Config |サービスへの登録情報を記録する `agentconfig.json` エージェント構成ファイルが含まれています。|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | ゲスト構成エージェント ファイルを含むインストール パス。 |
    |%ProgramData%\GuestConfig |Azure からの (適用された) ポリシーが含まれています。|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | 拡張機能は Azure からダウンロードされ、ここにコピーされます。|

* エージェントのインストール中に、次の Windows サービスがターゲット マシン上に作成されます。

    |[サービス名] |Display name |[処理名] |説明 |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |このサービスは、Azure への接続と接続されたマシンの Azure ID を管理するために Azure Instance Metadata Service (IMDS) を実装します。|
    |GCArcService |ゲスト構成 Arc サービス |gc_service |マシンの必要な状態構成を監視します。|
    |ExtensionService |ゲスト構成拡張機能サービス | gc_service |マシンをターゲットとする必要な拡張機能をインストールします。|

* 次の環境変数は、エージェントのインストール中に作成されます。

    |名前 |既定値 |説明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* トラブルシューティングに使用できるログ ファイルがいくつかあります。 これらについては、次の表で説明します。

    |ログ |説明 |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |エージェント (HIMDS) サービスや Azure との対話の詳細を記録します。|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |詳細 (-v) 引数が使用されている場合は、azcmagent ツール コマンドの出力が含まれます。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |DSC サービス アクティビティの詳細を記録します<br> (特に、HIMDS サービスと Azure Policy の間の接続)。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |DSC サービス テレメトリと詳細ログの詳細を記録します。|
    |%ProgramData%\GuestConfig\ext_mgr_logs|拡張エージェント コンポーネントに関する詳細を記録します。|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|インストールされた拡張機能の詳細を記録します。|

* ローカル セキュリティ グループの **ハイブリッド エージェント拡張アプリケーション** が作成されます。

* エージェントのアンインストール中に、次の成果物は削除されません。

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent とサブディレクトリ
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux エージェントのインストールの詳細

Linux 用の Connected Machine エージェントは、Microsoft [パッケージ リポジトリ](https://packages.microsoft.com/)でホストされたディストリビューション (.RPM または .DEB) に適したパッケージ形式で提供されます。 エージェントは、シェル スクリプト バンドルの [Install_linux_azcmagent.sh](https://aka.ms/azcmagent) を使用してインストールおよび構成されます。

Linux 用 Connected Machine エージェントをインストールした後、次のシステム全体の構成変更が適用されます。

* 次のインストール フォルダーは、セットアップ中に作成されます。

    |Folder |説明 |
    |-------|------------|
    |/var/opt/azcmagent/ |エージェント サポート ファイルが含まれている既定のインストール パス。|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | ゲスト構成エージェント ファイルを含むインストール パス。|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |取得したトークンが含まれています。|
    |/var/lib/GuestConfig |Azure からの (適用された) ポリシーが含まれています。|
    |/opt/GC_Ext/downloads|拡張機能は Azure からダウンロードされ、ここにコピーされます。|

* 次のデーモンは、エージェントのインストール中にターゲット マシン上に作成されます。

    |[サービス名] |Display name |[処理名] |説明 |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent サービス。 |himds |このサービスは、Azure への接続と接続されたマシンの Azure ID を管理するために Azure Instance Metadata Service (IMDS) を実装します。|
    |gcad.servce |GC Arc サービス |gc_linux_service |マシンの必要な状態構成を監視します。 |
    |extd.service |拡張機能サービス |gc_linux_service | マシンをターゲットとする必要な拡張機能をインストールします。|

* トラブルシューティングに使用できるログ ファイルがいくつかあります。 これらについては、次の表で説明します。

    |ログ |説明 |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |エージェント (HIMDS) サービスや Azure との対話の詳細を記録します。|
    |/var/opt/azcmagent/log/azcmagent.log |詳細 (-v) 引数が使用されている場合は、azcmagent ツール コマンドの出力が含まれます。|
    |/opt/logs/dsc.log |DSC サービス アクティビティの詳細を記録します<br> (特に、himds サービスと Azure Policy 間の接続)。|
    |/opt/logs/dsc.telemetry.txt |DSC サービス テレメトリと詳細ログの詳細を記録します。|
    |/var/lib/GuestConfig/ext_mgr_logs |拡張エージェント コンポーネントに関する詳細を記録します。|
    |/var/lib/GuestConfig/extension_logs|インストールされた拡張機能の詳細を記録します。|

* 次の環境変数は、エージェントのインストール中に作成されます。 これらの変数は `/lib/systemd/system.conf.d/azcmagent.conf` に設定されます。

    |名前 |既定値 |説明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* エージェントのアンインストール中に、次の成果物は削除されません。

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>次のステップ

* Azure Arc 対応サーバーの評価を開始するには、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」の記事に従ってください。

* トラブルシューティング情報は、[Connected Machine エージェントのトラブルシューティング ガイド](troubleshoot-agent-onboard.md)に関する記事を参照してください。
