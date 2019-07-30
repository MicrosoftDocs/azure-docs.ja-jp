---
title: 仮想マシンのコンテンツを監査する方法の概要
description: Azure Policy でゲスト構成を使用して、Azure の仮想マシン内で設定を監査する方法を理解します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c79a4907e277c337509bd362653cfb100c4bd39c
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137436"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy のゲストの構成を理解します。

Azure のリソースを監査し、[修復](../how-to/remediate-resources.md)することに加えて、Azure Policy は、仮想マシン内の設定を監査することができます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを使用して、拡張機能は、オペレーティング システムの構成、アプリケーションの構成またはプレゼンス、環境の設定などの設定を検証します。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>拡張機能とクライアント

仮想マシン内部の設定を監査するために、[仮想マシン拡張機能](../../../virtual-machines/extensions/overview.md)を有効にします。 拡張機能は、適用可能なポリシーの割り当てと、対応する構成定義をダウンロードします。

### <a name="register-guest-configuration-resource-provider"></a>ゲスト構成リソース プロバイダーの登録

ゲストの構成を使用するには、リソース プロバイダーを登録する必要があります。 ポータルまたは PowerShell を使用して登録できます。 ポータルを使用してゲスト構成ポリシーを割り当てる場合、リソース プロバイダーは自動的に登録されます。

#### <a name="registration---portal"></a>登録ポータル

Azure portal からゲストの構成用のリソース プロバイダを登録するには、次の手順を実行します。

1. Azure portal を起動し、 **[すべてのサービス]** をクリックします。 **サブスクリプション**を検索して選択します。

1. ゲスト構成を有効にするために必要とするサブスクリプションを検索し、クリックします。

1. **サブスクリプション** ページ上の左側のメニューで、**リソース プロバイダー** をクリックします。

1. **Microsoft.GuestConfiguration** が見つかるまでフィルター処理するか、またはスクロールして、同じ行に**登録**をクリックします。

#### <a name="registration---powershell"></a>登録 - PowerShell

PowerShell を使ってゲスト構成用にリソース プロバイダーを登録するには、次のコマンドを実行します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>検証ツール

仮想マシン内で、ゲスト構成クライアントはローカル ツールを使用して監査を実行します。

次の表では、サポートされている各オペレーティング システムで使用するローカルのツールの一覧を示します。

|オペレーティング システム|検証ツール|メモ|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| ゲスト構成拡張機能によって、Ruby、Python がインストールされます。 |

### <a name="validation-frequency"></a>検証の頻度

ゲスト構成クライアントは、新しいコンテンツを 5 分ごとにチェックします。 ゲスト割り当てを受信すると、設定が 15 分間隔でチェックされます。 監査が完了するとすぐに、結果がゲスト構成リソース プロバイダーに送信されます。 ポリシー[評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)が発生すると、マシンの状態がゲスト構成リソース プロバイダーに書き込まれます。 これにより、Azure Policy が Azure Resource Manager のプロパティを評価します。 オンデマンドの Azure Policy 評価により、ゲスト構成リソース プロバイダーから最新の値が取得されます。 ただし、仮想マシン内の構成の新しい監査はトリガーされません。

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、Azure イメージでサポートされているオペレーティング システムの一覧を示します。

|Publisher|Name|バージョン|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、2012 R2 Datacenter、2016 Datacenter、2019 Datacenter|
|Microsoft|Windows クライアント|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> ゲスト構成は、サポートされる OS を実行しているノードを監査できます。 カスタム イメージを使用する仮想マシンを監査する場合は、**DeployIfNotExists** の定義を複製し、**If** セクションを変更してイメージのプロパティを含める必要があります。

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

Windows Server Nano Server はどのバージョンでもサポートされていません。

### <a name="guest-configuration-extension-network-requirements"></a>ゲスト構成拡張機能のネットワーク要件

Azure のゲスト構成リソース プロバイダーと通信するには、仮想マシンはポート **443** を介して Azure データセンターに送信方向でアクセスできる必要があります。 Azure でプライベート仮想ネットワークを使用し、送信トラフィックを許可しない場合は、[ネットワーク セキュリティ グループ](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則を使用して例外を構成する必要があります。 現時点では、Azure Policy ゲスト構成用のサービス タグはありません。

IP アドレス一覧では、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。 VM がデプロイされているリージョンの IP への送信アクセスのみを許可する必要があります。

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。 更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 Azure ExpressRoute ユーザーは、このファイルが、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために使用されることに注意してください。

## <a name="guest-configuration-definition-requirements"></a>ゲスト構成定義の要件

ゲスト構成によって実行される各監査には、**DeployIfNotExists** 定義と **Audit** 定義の 2 つのポリシー定義が必要です。 **DeployIfNotExists** 定義は、[検証ツール](#validation-tools)をサポートするためにゲスト構成エージェントおよびその他のコンポーネントを使用して仮想マシンを準備するために使用されます。

**DeployIfNotExists** ポリシー定義が検証し、次の項目を修正します。

- 仮想マシンに、評価する構成が割り当てられていることを検証します。 割り当てが現在存在しない場合は、割り当てを取得し、次を実行して、仮想マシンを準備します。
  - [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) を使用して、仮想マシンへの認証を行う
  - **Microsoft.GuestConfiguration** 拡張機能の最新バージョンをインストールする
  - 必要とする場合、[検証ツール](#validation-tools)と依存関係をインストールする

**DeployIfNotExists** の割り当てが準拠していない場合は、[修復タスク](../how-to/remediate-resources.md#create-a-remediation-task)を使用できます。

**DeployIfNotExists** の割り当てが準拠している場合は、**Audit** ポリシーの割り当てはローカル検証ツールを使用して、構成の割り当てが準拠しているかどうかを判断します。
この検証ツールは、結果をゲスト構成クライアントに提供します。 クライアントは、その結果をゲストの拡張機能に転送します。それにより、その結果がゲスト構成のリソース プロバイダー全体で使用可能になります。

Azure Policy は、ゲスト構成リソースプロバイダーの **complianceStatus** プロパティを使用して**コンプライアンス** ノードでコンプライアンスを報告します。 詳細については、[コンプライアンス データを取得する](../how-to/getting-compliance-data.md)を参照してください。

> [!NOTE]
> **Audit** ポリシーから結果を返すには、**DeployIfNotExists** ポリシーが必要です。
> **DeployIfNotExists** がない場合、**Audit** ポリシーは状態として"0 of 0" のリソースを示します。

割り当てで使用するための定義をグループ化するためのイニシアティブには、ゲストの構成のすべての組み込みポリシーが含まれます。 *[プレビュー]: Linux および Windows の仮想マシン内での監査のパスワード セキュリティ設定*という名前の組み込みイニシアティブには 18 のポリシーが含まれています。 Windows 用に **DeployIfNotExists** と **Audit** の 6 つのペアがあり、Linux 用に 3 つのペアがあります。 いずれの場合も、定義内のロジックは、[ポリシー規則](definition-structure.md#policy-rule)の定義に基づいてターゲット オペレーティング システムのみが評価されることを検証します。

## <a name="client-log-files"></a>クライアントのログ ファイル

ゲスト構成拡張機能は、ログ ファイルを次の場所に書き込みます。

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

`<version>` は現在のバージョン番号を示します。

## <a name="guest-configuration-samples"></a>ゲスト構成のサンプル

ポリシー ゲスト構成のサンプルは、以下を参照してください。

- [サンプル インデックス - ゲスト構成](../samples/index.md#guest-configuration)
- [Azure Policy サンプルの GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](definition-structure.md)」を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/getting-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/index.md)」で、管理グループとは何かを確認します。
