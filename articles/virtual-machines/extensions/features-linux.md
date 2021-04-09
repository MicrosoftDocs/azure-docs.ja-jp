---
title: Linux 用の Azure VM 拡張機能とその機能
description: Linux 上の Azure 仮想マシンで使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: 8ff7b2940ca9ce3e5dc5913c0e676a32857771e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046880"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Linux 用の仮想マシンの拡張機能とその機能

Azure 仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストールやウイルス対策保護が必要な場合、あるいは、仮想マシン内でスクリプトを実行するために、VM 拡張機能を使用できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Azure Resource Manager テンプレート、および Azure Portal を使って実行できます。 拡張機能は、新しい VM の展開にバンドルすることも、既存の任意のシステムに対して実行することもできます。

この記事では、VM 拡張機能の概要と Azure VM 拡張機能を使用する場合の前提条件を示し、VM 拡張機能を検出、管理、および削除する方法についてのガイダンスを提供します。 構成がそれぞれ固有の VM 拡張機能が多数あるため、この記事では一般的な情報を示します。 拡張機能に固有の詳細情報については、それぞれの拡張機能のドキュメントをご覧ください。

## <a name="use-cases-and-samples"></a>ユース ケースとサンプル

さまざまな Azure VM 拡張機能が存在しますが、そのユース ケースはそれぞれ異なります。 次に例をいくつか示します。

- Linux 用の DSC 拡張機能を使って、VM に PowerShell Desired State Configuration を適用します。 詳細については、「[Azure Desired State configuration extension](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)」(Azure Desired State Configuration 拡張機能) を参照してください。
- Microsoft Monitoring Agent の VM 拡張機能を使用して VM の監視を構成します。 詳細については、[Linux VM の監視方法](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)に関する記事を参照してください。
- Chef または Datadog 拡張機能を使って Azure インフラストラクチャの監視を構成します。 詳細については、[Chef のドキュメント](https://docs.chef.io/azure_portal.html)または [Datadog のブログ](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)を参照してください。

プロセス固有の拡張機能のほか、カスタム スクリプト拡張機能を Windows と Linux の両方の仮想マシンで使用できます。 Linux 用カスタム スクリプト拡張機能では、VM で実行する任意の Bash スクリプトを使用できます。 カスタム スクリプトは、ネイティブの Azure ツールが提供可能な構成以上の構成を必要とする Azure のデプロイを設計する場合に役立ちます。 詳細については、[Linux VM カスタム スクリプト拡張機能](custom-script-linux.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

VM 上で拡張機能を処理するには、Azure Linux エージェントがインストールされている必要があります。 一部の個々の拡張機能には、リソースまたは依存関係へのアクセスなどの前提条件があります。

### <a name="azure-vm-agent"></a>Azure VM エージェント

Azure VM エージェントは、Azure VM と Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure VM のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure Marketplace イメージにプレインストールされており、サポートされているオペレーティング システムに手動でインストールすることができます。 Linux 用 Azure VM エージェントは Linux エージェントと呼ばれます。

サポートされているオペレーティング システムおよびインストール手順については、「[仮想マシンのエージェントおよび拡張機能について](agent-linux.md)」を参照してください。

#### <a name="supported-agent-versions"></a>サポートされているエージェントのバージョン

最適なエクスペリエンスを提供するために、エージェントの最小バージョンが用意されています。 詳細については、 [こちらの記事](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)を参照してください。

#### <a name="supported-oses"></a>サポート対象の OS

Linux エージェントは複数の OS で実行されますが、拡張機能フレームワークでは拡張を行う OS に対して制限があります。 詳細については、 [こちらの記事](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
)を参照してください。

一部の拡張機能は、すべての OS ではサポートされず、"*エラー コード 51、'OS がサポートされていません'* " が出力される場合があります。 サポートの可否については、個々の拡張機能のドキュメントを確認してください。

#### <a name="network-access"></a>ネットワーク アクセス

拡張機能パッケージは、Azure Storage 拡張機能リポジトリからダウンロードされ、拡張機能ステータスのアップロードが Azure Storage に転記されます。 [サポートされている](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)バージョンのエージェントを使用する場合、エージェント通信用の Azure ファブリック コントローラーに通信をリダイレクトするためにエージェントを使用できるので、VM リージョン内の Azure Storage へのアクセスを許可する必要はありません。 サポートされていないバージョンのエージェントを使用する場合は、VM からそのリージョン内の Azure Storage への送信アクセスを許可する必要があります。

> [!IMPORTANT]
> ゲスト ファイアウォールを使用して *168.63.129.16* へのアクセスをブロックした場合、上記のアクセス許可とは関係なく、拡張機能はエラーになります。

エージェントは、拡張機能パッケージおよびレポート ステータスをダウンロードするためだけに使用できます。 たとえば、拡張機能のインストール時に GitHub からスクリプトをダウンロードする必要がある場合 (カスタム スクリプト)、または Azure Storage へのアクセスが必要な場合 (Azure Backup) は、追加のファイアウォール/ネットワーク セキュリティ グループ ポートが開かれている必要があります。 拡張機能はそれぞれ、独自のアプリケーションになっているため、要件も異なります。 たとえば、拡張機能が Azure Storage へのアクセスを必要とする場合、[ストレージ](../../virtual-network/network-security-groups-overview.md#service-tags)の Azure NSG サービス タグを使用してアクセスを許可できます。

エージェントのトラフィック要求をリダイレクトするために、Linux エージェントはプロキシ サーバーのサポートを備えています。 ただし、このプロキシ サーバーのサポートは拡張機能には適用されません。 プロキシを使用するには、個々の拡張機能を構成する必要があります。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出

Azure VM と共に、多くのさまざまな VM 拡張機能を使用できます。 完全な一覧を確認するには、[az vm extension image list](/cli/azure/vm/extension/image#az-vm-extension-image-list) を使用します。 次の例では、*westus* の場所で利用できるすべての拡張機能が表示されています:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM 拡張機能の実行

Azure VM 拡張機能は既存の VM で実行できます。これは、構成の変更や、デプロイ済みの VM での接続の回復が必要な場合に便利です。 また、VM 拡張機能を Azure Resource Manager テンプレートのデプロイにバンドルすることもできます。 Resource Manager テンプレートで拡張機能を使用すると、Azure VM をデプロイし、デプロイ後の操作を行わずに構成できます。

既存の VM に対して拡張機能を実行するには、次の方法を使用します。

### <a name="azure-cli"></a>Azure CLI

[az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) コマンドにより、Azure VM 拡張機能を既存の VM に対して実行できます。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という名前の VM に対して Custom Script 拡張機能を実行します。 例のリソース グループ名、VM 名、および実行するスクリプト (https:\//raw.githubusercontent.com/me/project/hello.sh) を実際の情報に置き換えます。 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

拡張機能が正しく実行されると、出力は次の例のようになります:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure portal

VM 拡張機能は、Azure Portal から既存の VM に適用できます。 ポータルで VM を選択し、 **[拡張機能]** を選択してから、 **[追加]** を選択します。 利用可能な拡張機能の一覧から目的の拡張機能を選択し、ウィザードの手順に従います。

次の図は、Azure Portal からの Linux カスタム スクリプト拡張機能のインストールを示しています:

![カスタム スクリプト拡張機能のインストール](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使用して実行できます。 テンプレートを使用して拡張機能をデプロイする場合は、完全に構成された Azure デプロイを作成できます。 たとえば、次の JSON は、負荷分散された一連の VM と Azure SQL Database をデプロイした後、各 VM に .NET Core アプリケーションをインストールする Resource Manager テンプレートからの抜粋です。 VM 拡張機能はソフトウェアのインストールに対応します。

詳細については、[Resource Manager テンプレート](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)全体を参照してください。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Resource Manager テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成](../windows/template-description.md#extensions)」を参照してください。

## <a name="secure-vm-extension-data"></a>VM 拡張機能のデータの保護

VM 拡張機能の実行時には、資格情報、ストレージ アカウント名、ストレージ アカウント アクセス キーなどの機密情報を含めなければならない場合があります。 多くの VM 拡張機能には、データを暗号化し、ターゲットの VM 内でのみ暗号化を解除する保護された構成が含まれます。 拡張機能には、それぞれ固有の保護された構成スキーマがあります。スキーマについては、拡張機能のドキュメントで詳しく説明されています。

次の例は、Linux 用カスタム スクリプト拡張機能のインスタンスを示しています。 実行するコマンドには、一連の資格情報が含まれています。 この例では、実行するコマンドは暗号化されません。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

**command to execute** プロパティを **protected** 構成に移動すると、次の例に示すように実行文字列が保護されます。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>エージェントと拡張機能を更新する方法

エージェントと拡張機能は、同じ更新メカニズムを共有します。 一部の更新プログラムでは、追加のファイアウォール規則を必要としません。

更新プログラムが利用できる場合で、拡張機能への変更があり、次のような他の VM モデルが変更されるときは、更新プログラムは VM にのみインストールされます。

- データ ディスク
- 拡張機能
- ブート診断コンテナー
- ゲスト OS のシークレット
- VM サイズ
- ネットワーク プロファイル

別のリージョンの VM は異なるバージョン上に搭載できるので、パブリッシャーは別のタイミングで、更新プログラムをリージョンで使用できるようにします。

#### <a name="agent-updates"></a>エージェントの更新プログラム

Linux VM エージェントには、分離できない 1 つのパッケージに *プロビジョニング エージェント コード* と *拡張機能処理コード* が含まれています。 Azure 上で cloud-init を使用してプロビジョニングを行いたい場合、*プロビジョニング エージェント* を無効化できます。 これを行うには、「[cloud-init を使用する](../linux/using-cloud-init.md)」を参照してください。

エージェントのサポートされているバージョンでは自動更新を使用できます。 更新可能な唯一のコードは *拡張機能処理コード* であり、プロビジョニング コードではありません。 *プロビジョニング エージェント コードは* 一度だけ実行されるコードです。

*拡張機能処理コード* は、Azure ファブリックとの通信を担い、インストール、ステータスのレポート、個々の拡張機能の更新と削除などの VM 拡張機能の操作を処理します。 更新プログラムには、*拡張機能処理コード* に対するセキュリティ修正プログラム、バグ修正プログラム、および拡張機能が含まれます。

エージェントがインストールされると、親デーモンが作成されます。 この親はその後、拡張機能の処理に使用される子プロセスを生成します。 エージェントの更新プログラムがある場合、更新プログラムがダウンロードされ、親は子プロセスを停止し、アップグレードし、再起動します。 更新プログラムに問題がある場合、親プロセスは前の子のバージョンにロール バックします。

親プロセスは自動更新できません。 親は distro パッケージの更新によってのみ更新できます。

実行中のバージョンを確認するには、次のように `waagent` を確認します:

```bash
waagent --version
```

出力は次の例のようになります。

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

上記の出力例で、親または 'パッケージのデプロイ済みバージョン' は *WALinuxAgent-2.2.17* です

'Goal state agent' は自動更新バージョンです。

エージェントの自動更新を常に有効にする ([AutoUpdate.Enabled=y](./update-linux-agent.md)) ことを強くお勧めします。 有効にしていない場合、エージェントを手動で更新し続ける必要があり、バグおよびセキュリティの修正プログラムは提供されません。

#### <a name="extension-updates"></a>拡張機能の更新プログラム

拡張機能の更新プログラムが使用可能な場合、Linux エージェントではこれをダウンロードして、拡張機能をアップグレードします。 拡張機能の自動更新プログラムは、"*マイナー*" または "*修正プログラム*" のどちらかです。 拡張機能の "*マイナー*" 更新プログラムは、拡張機能をプロビジョニングするときに、選択または除外できます。 次の例では、*autoUpgradeMinorVersion": true,'* を使って、Resource Manager テンプレートのマイナー バージョンを自動でアップグレードする方法を示しています。

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

最新のマイナー リリースのバグ修正プログラムを取得するには、拡張機能のデプロイで常に自動更新を選択することを強くお勧めします。 セキュリティまたは主要なバグの修正を提供する修正プログラムは、除外できません。

### <a name="how-to-identify-extension-updates"></a>拡張機能の更新プログラムを識別する方法

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>拡張機能が autoUpgradeMinorVersion を使って VM 上に設定されているかどうかを識別する

拡張機能が 'autoUpgradeMinorVersion' を使ってプロビジョニングされたかどうかは、VM モデルから判断できます。 確認するには、[az vm show](/cli/azure/vm#az-vm-show) を使用して、次のようにリソース グループと VM の名前を指定します:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

次の例の出力は、*autoUpgradeMinorVersion* が *true* に設定されていることを示しています。

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>autoUpgradeMinorVersion が行われたタイミングを識別する

拡張機能への更新がいつ行われたかを判断するには、 */var/log/waagent.log* で VM 上のエージェント ログを確認します。

次の例では、VM には *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* がインストールされていました。 *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027* の修正プログラムが入手可能でした:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>エージェントのアクセス許可

タスクを実行するには、エージェントが *root* として実行される必要があります。

## <a name="troubleshoot-vm-extensions"></a>VM 拡張機能のトラブルシューティング

各 VM 拡張機能には、固有のトラブルシューティング手順が存在する場合があります。 たとえば、カスタム スクリプト拡張機能を使用する場合、スクリプト実行の詳細は、拡張機能が実行された VM のローカルに保存されます。 拡張機能に固有のトラブルシューティング手順は、拡張機能のドキュメントで詳しく説明されています。

次のトラブルシューティング手順は、すべての VM 張機能に適用されます。

1. Linux エージェント ログをチェックするには、 */var/log/waagent.log* で拡張機能がプロビジョニングされたときのアクティビティを確認します

2. */var/log/azure/\<extensionName>* で、実際の拡張機能のログをさらに詳しくチェックします

3. エラーコードや既知の問題などについて、拡張機能固有のドキュメントのトラブルシューティングのセクションを確認します。

3. システム ログを確認します。 排他的なパッケージ マネージャーのアクセスを必要とする別のアプリケーションのインストールが長時間実行されている場合など、拡張機能に干渉する可能性のある他の操作をチェックします。

### <a name="common-reasons-for-extension-failures"></a>拡張機能のエラーの一般的な理由

1. 拡張機能の実行には 20 分かかります (例外は CustomScript 拡張機能、Chef、および DSC であり、これらは 90 分かかります)。 デプロイにかかる時間がこれを超過する場合、タイムアウトとしてマークされます。 これは、拡張機能がプロビジョニングを試行しているときの、VM のリソース低減や他の VM 構成/起動タスクによる大量のリソース消費に起因する可能性があります。

2. 最小限の前提条件が満たされていない。 一部の拡張機能では、HPC イメージなど、VM SKU 上に依存関係があります。 拡張機能は、Azure Storage やパブリック サービスとの通信など、特定のネットワーク アクセス要件を必要とする場合があります。 その他の例としては、パッケージ リポジトリへのアクセス、ディスク領域の不足、セキュリティ上の制限事項が考えられます。

3. 排他的なパッケージ マネージャーのアクセス。 一部のケースでは、長時間にわたる VM 構成の実行が拡張機能のインストールと競合し、どちらもパッケージ マネージャーへの排他的なアクセスを必要とする状況になる場合があります。

### <a name="view-extension-status"></a>拡張機能の状態表示

VM 拡張機能が VM に対して実行された後は、次のように、拡張機能のステータスを返す [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) を使用します:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

出力は次の例のようになります。

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

拡張機能の実行の状態は、Azure Portal で確認することもできます。 拡張機能の状態を表示するには、VM を選択し、 **[拡張機能]** を選択して、目的の拡張機能を選択します。

### <a name="rerun-a-vm-extension"></a>VM 拡張機能の再実行

VM 拡張機能の再実行が必要な場合があります。 拡張機能を再実行するには、その拡張機能を削除し、その後任意の実行方法で拡張機能を再実行します。 拡張機能を削除するには、次のように [az vm extension delete](/cli/azure/vm/extension#az-vm-extension-delete) を使用します:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

また、次の手順を使用して、Azure Portal で拡張機能を削除することもできます。

1. VM を選択します。
2. **[拡張機能]** を選択します。
3. 目的の拡張機能を選択します。
4. **[アンインストール]** を選択します。

## <a name="common-vm-extension-reference"></a>一般的な VM 拡張機能のリファレンス

| 拡張機能の名前 | 説明 | 詳細情報 |
| --- | --- | --- |
| Linux 用カスタム スクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します。 |[Linux 用カスタム スクリプト拡張機能](custom-script-linux.md) |
| VM アクセス拡張機能 |Azure 仮想マシンへのアクセスを回復します。 |[VM アクセス拡張機能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics 拡張機能 |Azure Diagnostics を管理します |[Azure Diagnostics 拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM アクセス拡張機能 |ユーザーと資格情報を管理します。 |[Linux 用 VM アクセス拡張機能](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>次のステップ

VM 拡張機能の詳細については、「[Azure 仮想マシンの拡張機能と機能の概要](overview.md)」をご覧ください。