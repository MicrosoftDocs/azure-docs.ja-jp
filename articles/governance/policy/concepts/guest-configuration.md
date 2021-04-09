---
title: 仮想マシンのコンテンツの監査を学習する
description: Azure Policy がゲスト構成クライアントを使用して仮想マシン内の設定を監査するしくみについて説明します。
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 33a492eb3c8c175bfcdc6a13cb467ed2f180c1e1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702880"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy のゲストの構成の理解


Azure Policy では、Azure 内で実行するマシンと [Arc に接続されたマシン](../../../azure-arc/servers/overview.md)の両方に対して、マシン内の設定を監査できます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを介した拡張機能によって、次のような設定が検証されます。

- オペレーティング システムの構成
- アプリケーションの構成または存在
- 環境設定

現時点では、ほとんどの Azure Policy ゲスト構成ポリシーの定義では、コンピューター内の設定の監査のみが行われます。 構成は適用されません。 例外は、[以下で参照されている](#applying-configurations-using-guest-configuration) 1 つの組み込みポリシーです。

[このドキュメントのビデオ チュートリアルを利用できます](https://youtu.be/Y6ryD3gTHOs)。

## <a name="enable-guest-configuration"></a>ゲスト構成を有効にする

環境内のマシン (Azure 内のマシンと Arc に接続されたマシンを含む) の状態を監査するには、以下の詳細を確認してください。

## <a name="resource-provider"></a>リソース プロバイダー

ゲストの構成を使用するには、リソース プロバイダーを登録する必要があります。 ゲスト構成ポリシーの割り当てがポータルを通じて行われた場合、またはサブスクリプションが Azure Security Center に登録されている場合は、リソース プロバイダーが自動的に登録されます。 [ポータル](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して手動で登録できます。

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure 仮想マシンの要件をデプロイする

マシン内の設定を監査するには、[仮想マシン拡張機能](../../../virtual-machines/extensions/overview.md)を有効にします。また、マシンにはシステム マネージド ID が必要です。 拡張機能は、適用可能なポリシーの割り当てと、対応する構成定義をダウンロードします。 ID は、ゲスト構成サービスに対して読み取りと書き込みを行うときに、マシンを認証するために使用されます。 Arc に接続されたマシンは Arc に接続されたマシンのエージェントに含まれているため、この拡張機能は必要ありません。

> [!IMPORTANT]
> Azure の仮想マシンを監査するには、ゲスト構成拡張機能とマネージド ID が必要です。 拡張機能を大規模にデプロイするには、次のポリシー イニシアチブを割り当てます。
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>拡張機能に設定されている制限

拡張機能のマシン内で実行されているアプリケーションへの影響を制限するために、ゲスト構成が CPU の 5% を超えることは許可されていません。 この制限は、組み込み定義とカスタム定義の両方に存在します。 これは、Arc に接続されたマシンのエージェントのゲスト構成サービスにも当てはまります。

### <a name="validation-tools"></a>検証ツール

マシン内では、ゲスト構成クライアントによりローカル ツールを使用して監査が実行されます。

次の表では、サポートされている各オペレーティング システム上で使用されるローカル ツールの一覧を示します。 組み込みコンテンツの場合、ゲスト構成によってこれらのツールの読み込みが自動的に処理されます。

|オペレーティング システム|検証ツール|Notes|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| Azure Policy でのみ使用されるフォルダーにサイドローディングされます。 Windows PowerShell DSC と競合しません。 PowerShell Core はシステム パスに追加されません。|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Chef InSpec バージョン 2.2.61 が既定の場所にインストールされ、システム パスに追加されます。 Ruby と Python を含む InSpec パッケージの依存関係もインストールされます。 |

### <a name="validation-frequency"></a>検証の頻度

ゲスト構成クライアントは、新しいコンテンツを 5 分ごとにチェックします。 ゲスト割り当てを受信すると、その構成の設定が 15 分間隔でチェックされます。 監査が完了すると、結果がゲスト構成リソース プロバイダーに送信されます。 ポリシー[評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)が発生すると、マシンの状態がゲスト構成リソース プロバイダーに書き込まれます。 この更新により、Azure Policy によって Azure Resource Manager のプロパティが評価されます。 オンデマンドの Azure Policy 評価により、ゲスト構成リソース プロバイダーから最新の値が取得されます。 ただし、マシン内の構成の新しい監査はトリガーされません。

## <a name="supported-client-types"></a>サポートされているクライアントの種類

ゲスト構成ポリシー定義には、新しいバージョンが含まれます。 ゲスト構成クライアントに互換性がない場合、Azure Marketplace で入手できる古いバージョンのオペレーティング システムは除外されます。 次の表は、Azure イメージでサポートされているオペレーティング システムの一覧を示します。

|Publisher|名前|バージョン|
|-|-|-|
|Canonical|Ubuntu Server|14.04 - 20.04|
|Credativ|Debian|8 - 10|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Windows クライアント|Windows 10|
|OpenLogic|CentOS|7.3 -8|
|Red Hat|Red Hat Enterprise Linux|7.4 - 8|
|Suse|SLES|12 SP3-SP5、15|

カスタム仮想マシン イメージについては、上記の表にあるいずれかのオペレーティング システムであれば、ゲスト構成ポリシー定義でサポートされます。

## <a name="network-requirements"></a>ネットワークの要件

Azure の仮想マシンは、ローカル ネットワーク アダプターまたは Private Link を使用して、ゲスト構成サービスと通信できます。

Azure Arc マシンは、オンプレミスのネットワーク インフラストラクチャを使用して接続し、Azure サービスにアクセスして、コンプライアンスの状態を報告します。

### <a name="communicate-over-virtual-networks-in-azure"></a>Azure の仮想ネットワークを介して通信する

通信に仮想ネットワークを使用する仮想マシンでは、ポート `443` で Azure データセンターへの発信アクセスが必要です。 アウトバウンド トラフィックが許可されないプライベート仮想ネットワークを Azure で使用している場合は、ネットワーク セキュリティ グループ規則で例外を構成する必要があります。 サービス タグ "GuestAndHybridManagement" を使用して、ゲスト構成サービスを参照できます。

### <a name="communicate-over-private-link-in-azure"></a>Azure で Private Link を介して通信する

仮想マシンは、ゲスト構成サービスとの通信に [Private Link](../../../private-link/private-link-overview.md) を使用できます。 この機能を有効にするには、名前 `EnablePrivateNeworkGC` (Network の "t" はありません) と値 `TRUE` を使用してタグを適用します。 タグは、ゲスト構成ポリシー定義をマシンに適用する前または後に適用できます。

Azure [仮想パブリック IP アドレス](../../../virtual-network/what-is-ip-address-168-63-129-16.md)を使用してトラフィックがルーティングされて、Azure プラットフォーム リソースとの、セキュリティで保護された認証済みチャネルが確立されます。

### <a name="azure-arc-connected-machines"></a>Azure Arc の接続されたマシン

Azure Arc によって接続されている Azure の外部にあるノードでは、ゲスト構成サービスへの接続が必要です。 ネットワークとプロキシの要件に関する詳細は、[Azure Arc のドキュメント](../../../azure-arc/servers/overview.md)で提供されています。

Azure のゲスト構成リソース プロバイダーと通信するには、マシンはポート **443** で Azure データセンターに対してアウトバウンド アクセスを行う必要があります。 Azure 内のネットワークで送信トラフィックが許可されていない場合は、[ネットワーク セキュリティ グループ](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)の規則で例外を構成します。 [サービス タグ](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" を使用して、ゲスト構成サービスを参照できます。

プライベート データセンターの Arc 接続サーバーでは、次のパターンを使用してトラフィックを許可します。

- ポート: 送信インターネット アクセスには TCP 443 のみが必要です
- グローバル URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>マネージド ID の要件

イニシアチブ「_仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする_」のポリシー定義を使用すると、システムによって割り当てられたマネージド ID が有効になります (まだ存在しない場合)。 ID の作成を管理するイニシアチブには、2 つのポリシー定義があります。 ポリシー定義内の IF 条件により、Azure のマシン リソースの現在の状態に基づいて正しい動作が保証されます。

現時点でマシンにマネージド ID がない場合、有効なポリシーは次のようになります。[ID のない仮想マシンでゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

現時点でマシンにユーザー割り当てのシステム ID がある場合、有効なポリシーは次のようになります。[ユーザー割り当て ID がある VM でゲスト構成の割り当てを有効にするためにシステム割り当てマネージド ID を追加する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>ゲスト構成定義の要件

ゲスト構成ポリシー定義では、**AuditIfNotExists** 効果が使用されます。 定義が割り当てられると、バックエンド サービスでは、`Microsoft.GuestConfiguration` Azure リソース プロバイダーのすべての要件のライフサイクルが自動的に処理されます。

**AuditIfNotExists** ポリシー定義は、マシン上のすべての要件が満たされるまで、コンプライアンスの結果を返しません。 要件については、「[Azure 仮想マシンの要件をデプロイする](#deploy-requirements-for-azure-virtual-machines)」セクションをご覧ください

> [!IMPORTANT]
> 以前のリリースのゲスト構成では、**DeployIfNoteExists** 定義と **AuditIfNotExists** 定義を組み合わせるためにイニシアチブが必要でした。 **DeployIfNotExists** 定義は不要になりました。 定義とイニシアチブには `[Deprecated]` ラベルが付けられていますが、既存の割り当ては引き続き機能します。 詳細については、ブログ記事の「[ゲスト構成の監査ポリシーに関してリリースされた重要な変更](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)」をご覧ください。

### <a name="what-is-a-guest-assignment"></a>ゲストの割り当てとは

Azure Policy が割り当てられている場合で、それが "Guest Configuration" カテゴリに属している場合は、ゲスト割り当てを記述するために含められたメタデータが存在します。
ゲスト割り当ては、コンピューターと Azure Policy シナリオとの間のリンクだと考えてください。
たとえば、次のスニペットでは、Azure Windows ベースライン構成が、最小バージョンを `1.0.0` として、ポリシーのスコープ内のすべてのコンピューターに関連付けられています。 既定では、ゲスト割り当てはコンピューターの監査のみを実行します。

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

ゲスト割り当ては、ゲスト構成サービスによってマシンごとに自動で作成されます。 リソースの種類は `Microsoft.GuestConfiguration/guestConfigurationAssignments` です。
Azure Policy は、ゲスト割り当てリソースの **complianceStatus** プロパティを使用して、コンプライアンスの状態を報告します。 詳細については、[コンプライアンス データを取得する](../how-to/get-compliance-data.md)を参照してください。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>業界の基準に従ってオペレーティング システムの設定を監査する

Azure Policy の 1 つのイニシアティブでは、"ベースライン" に従ってオペレーティング システムの設定を監査します。 _\[[プレビュー]\]: Windows マシンはAzure セキュリティ ベースラインの要件を満たす必要がある_”　という定義には、Active Directory グループ ポリシーに基づく一連の規則が含まれています。

ほとんどの設定は、パラメーターとして使用できます。 パラメーターを使用すると、監査対象をカスタマイズできます。
要件に合わせてポリシーを調整するか、またはポリシーを業界の規制標準などのサード パーティ情報にマップします。

一部のパラメーターは、整数の値範囲をサポートしています。 たとえば、[パスワードの有効期間] の設定では、有効なグループ ポリシー設定を監査できます。 "1,70" の範囲では、ユーザーがパスワードを 1 ～ 70 日ごとに変更する必要があることを確認します。

Azure Resource Manager テンプレート (ARM テンプレート) を使用してポリシーを割り当てる場合は、パラメーター ファイルを使用して例外を管理します。 これらのファイルを Git などのバージョン管理システムにチェックインします。 ファイル変更に関するコメントによって、なぜ割り当てが予測される値の例外であるかの根拠が示されます。

#### <a name="applying-configurations-using-guest-configuration"></a>ゲスト構成を使用して構成を適用する

_[Windows コンピューターでタイムゾーンを構成]_ する定義のみが、タイムゾーンを構成することによって、マシンに変更を加えます。 コンピューター内の設定を構成するためのカスタム ポリシー定義はサポートされていません。

_[構成]_ で始まる定義を割り当てるとき、 _[前提条件を展開して Windows VM でゲスト構成ポリシーを有効にする]_ 定義も割り当てる必要があります。 必要に応じて、これらの定義をイニシアチブで結合させることができます。

> [!NOTE]
> 組み込みのタイム ゾーン ポリシーは、マシン内の設定の構成をサポートする唯一の定義であり、マシン内の設定を構成するカスタム ポリシー定義はサポートされていません。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 外部のコンピューターにポリシーを割り当てる

ゲスト構成に使用できる監査ポリシー定義には、**Microsoft.HybridCompute/machines** リソースの種類が含まれます。 ポリシー割り当てのスコープ内にある[サーバー向け Azure Arc](../../../azure-arc/servers/overview.md) にオンボードされているすべてのマシンが自動的に追加されます。

## <a name="troubleshooting-guest-configuration"></a>ゲスト構成のトラブルシューティング

ゲスト構成のトラブルシューティングの詳細については、[Azure Policy のトラブルシューティング](../troubleshoot/general.md)に関する記事を参照してください。

### <a name="multiple-assignments"></a>複数の割り当て

ポリシー割り当てで異なるパラメーターが使用されている場合でも、現在、ゲスト構成ポリシー定義では、同じゲスト割り当てはマシンごとに一度の割り当てのみがサポートされています。

### <a name="client-log-files"></a>クライアントのログ ファイル

ゲスト構成拡張機能は、ログ ファイルを次の場所に書き込みます。

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>ログのリモート収集

ゲスト構成の構成またはモジュールのトラブルシューティングでは最初に、[Windows 用のカスタム ゲスト構成監査ポリシーを作成する](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)方法の手順に従って `Test-GuestConfigurationPackage` コマンドレットを使用する必要があります。
それでうまくいかない場合は、クライアント ログを収集すると問題の診断に役立ちます。

#### <a name="windows"></a>Windows

[Azure VM の実行コマンド](../../../virtual-machines/windows/run-command.md)を使用して、ログ ファイルから情報をキャプチャします。次の PowerShell スクリプトの例が役立ちます。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

[Azure VM の実行コマンド](../../../virtual-machines/linux/run-command.md)を使用して、ログ ファイルから情報をキャプチャします。次の Bash スクリプトの例が役立ちます。

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>クライアント ファイル

ゲスト構成のクライアントは、コンテンツ パッケージをマシンにダウンロードし、その内容を抽出します。
どのコンテンツがダウンロードされ、保管されたか確認するには、下に示すフォルダーの場所を参照します。

Windows: `c:\programdata\guestconfig\configurations`

Linux: `/var/lib/guestconfig/configurations`

## <a name="guest-configuration-samples"></a>ゲスト構成のサンプル

ゲスト構成の組み込みのポリシー サンプルは、次の場所で入手できます。

- [組み込みのポリシー定義 - ゲスト構成](../samples/built-in-policies.md#guest-configuration)
- [組み込みのイニシアチブ - ゲスト構成](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy サンプルの GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>ビデオの概要

次に示す Azure Policy ゲスト構成の概要は、ITOps Talks 2021 で配信されたものです。

[Azure Policy のゲスト構成を使用したハイブリッド サーバー環境でのベースラインの管理](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>次のステップ

- [ゲスト構成のコンプライアンス ビュー](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)から各設定の詳細を表示する方法を学習します。
- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](./definition-structure.md)」を確認します。
- 「[Policy の効果について](./effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
