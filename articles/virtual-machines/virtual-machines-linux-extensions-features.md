---
title: "Linux 用の仮想マシンの拡張機能とその機能 | Microsoft Docs"
description: "Azure 仮想マシンに使用できる拡張機能について、提供または改善される内容ごとにまとめて説明します。"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: ef28ca0a43d7e07fa87d5afdd3f0428f28a310f0
ms.lasthandoff: 03/27/2017


---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Linux 用の仮想マシンの拡張機能とその機能

Azure 仮想マシン拡張機能は、Azure Virtual Machines でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 たとえば、仮想マシンでソフトウェアのインストール、ウイルス対策保護、または Docker 構成が必要な場合、VM 拡張機能を使用して、これらのタスクを実行できます。 Azure VM 拡張機能は、Azure CLI、PowerShell、Azure Resource Manager テンプレート、および Azure Portal を使用して実行できます。 拡張機能は、新しい仮想マシン デプロイにバンドルすることも、既存の任意のシステムに対して実行することもできます。

このドキュメントでは、VM 拡張機能の概要と Azure VM 拡張機能を使用する場合の前提条件を示し、VM 拡張機能を検出、管理、および削除する方法についてのガイダンスを提供します。 構成がそれぞれ固有の VM 拡張機能が多数あるため、このドキュメントでは一般的な情報を示します。 拡張機能に固有の詳細情報については、それぞれの拡張機能のドキュメントをご覧ください。

## <a name="use-cases-and-samples"></a>ユース ケースとサンプル

さまざまな Azure VM 拡張機能が存在しますが、そのユース ケースはそれぞれ異なります。 次に例をいくつか示します。

- Linux 用の DSC 拡張機能を使用して、仮想マシンに PowerShell Desired State Configuration を適用します。 詳細については、「[Azure Desired State configuration extension](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)」(Azure Desired State Configuration 拡張機能) を参照してください。
- Microsoft Monitoring Agent の VM 拡張機能を使用して仮想マシンの監視を構成します。 詳細については、「[Azure VM の監視を有効または無効にする](virtual-machines-linux-vm-monitoring.md)」を参照してください。
- Datadog 拡張機能を使用して Azure インフラストラクチャの監視を構成します。 詳細については、[Datadog のブログ](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)を参照してください。
- Docker VM 拡張機能を使用して Azure 仮想マシンで Docker ホストを構成します。 詳細については、[Docker VM 拡張機能](virtual-machines-linux-dockerextension.md)に関するページを参照してください。

プロセス固有の拡張機能のほか、カスタム スクリプト拡張機能を Windows と Linux の両方の仮想マシンで使用できます。 Linux 用カスタム スクリプト拡張機能では、仮想マシンで実行する任意の Bash スクリプトを使用できます。 カスタム スクリプトは、ネイティブの Azure ツールが提供可能な構成以上の構成を必要とする Azure のデプロイを設計する場合に役立ちます。 詳細については、[Linux VM カスタム スクリプト拡張機能](virtual-machines-linux-extensions-customscript.md)に関するページを参照してください。

VM 拡張機能がエンド ツー エンドのアプリケーションのデプロイで使用される例については、「[Azure Virtual Machines へのアプリケーションのデプロイを自動化する](virtual-machines-linux-dotnet-core-1-landing.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

仮想マシン拡張機能には、それぞれ独自の前提条件があります。 たとえば、Docker VM 拡張機能には、サポートされている Linux ディストリビューションの前提条件があります。 個々の拡張機能の要件については、その拡張機能のドキュメントで詳しく説明されています。

### <a name="azure-vm-agent"></a>Azure VM エージェント

Azure VM エージェントは、Azure 仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。 VM エージェントは、Azure Virtual Machines のデプロイと管理の多くの機能面を担当します (VM 拡張機能の実行など)。 Azure VM エージェントは、Azure Marketplace イメージにプレインストールされており、サポートされているオペレーティング システムに手動でインストールすることができます。

サポートされているオペレーティング システムおよびインストール手順については、「[仮想マシンのエージェントおよび拡張機能について](linux/classic/agents-and-extensions.md)」を参照してください。

## <a name="discover-vm-extensions"></a>VM 拡張機能の検出

Azure Virtual Machines と共に、多くのさまざまな VM 拡張機能を使用できます。 完全な一覧を表示するには、Azure CLI で次のコマンドを実行します。コマンド内の場所は、実際の場所に置き換えます。

```azurecli
azure vm extension-image list westus
```

## <a name="run-vm-extensions"></a>VM 拡張機能の実行

Azure 仮想マシン拡張機能は既存の仮想マシンで実行できます。これは、構成の変更や、デプロイ済みの VM での接続の回復が必要な場合に便利です。 また、VM 拡張機能を Azure Resource Manager テンプレートのデプロイにバンドルすることもできます。 Resource Manager テンプレートで拡張機能を使用すると、Azure 仮想マシンをデプロイし、デプロイ後の操作を行わずに構成できます。

既存の仮想マシンに対して拡張機能を実行するには、次の方法を使用します。

### <a name="azure-cli"></a>Azure CLI

`azure vm extension set` コマンドを使用すると、既存の仮想マシンに対して Azure 仮想マシン拡張機能を実行できます。 次の例では、仮想マシンに対してカスタム スクリプト拡張機能を実行します。

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version \
  --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

このスクリプトでは、次のような出力が表示されます。

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure ポータル

VM 拡張機能は、Azure Portal から既存の仮想マシンに適用できます。 そのためには、仮想マシンを選択し、**[拡張機能]** を選択して **[追加]** をクリックします。 利用可能な拡張機能の一覧から必要な拡張機能を選択し、ウィザードの手順に従います。

次の図は、Azure Portal からの Linux カスタム スクリプト拡張機能のインストールを示しています。

![カスタム スクリプト拡張機能のインストール](./media/virtual-machines-linux-extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレート

VM 拡張機能を Azure Resource Manager テンプレートに追加し、テンプレートのデプロイを使用して実行できます。 テンプレートを使用して拡張機能をデプロイする場合は、完全に構成された Azure デプロイを作成できます。 たとえば、次の JSON は Resource Manager テンプレートからの抜粋です。 このテンプレートは負荷分散された一連の仮想マシンと Azure SQL データベースをデプロイし、各 VM に .NET Core アプリケーションをインストールします。 VM 拡張機能はソフトウェアのインストールに対応します。

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

詳細については、「[Linux VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](virtual-machines-linux-extensions-authoring-templates.md)」を参照してください。

## <a name="secure-vm-extension-data"></a>VM 拡張機能のデータの保護

VM 拡張機能の実行時には、資格情報、ストレージ アカウント名、ストレージ アカウント アクセス キーなどの機密情報を含めなければならない場合があります。 多くの VM 拡張機能には、データを暗号化し、ターゲットの仮想マシン内でのみ暗号化を解除する保護された構成が含まれます。 拡張機能には、それぞれ固有の保護された構成スキーマがあります。スキーマについては、拡張機能のドキュメントで詳しく説明されています。

次の例は、Linux 用カスタム スクリプト拡張機能のインスタンスを示しています。 実行するコマンドに一連の資格情報が含まれていることに注意してください。 この例では、実行するコマンドは暗号化されません。


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

**command to execute** プロパティを **protected** 構成に移動すると、実行文字列が保護されます。

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

## <a name="troubleshoot-vm-extensions"></a>VM 拡張機能のトラブルシューティング

各 VM 拡張機能には、固有のトラブルシューティング手順が存在する場合があります。 たとえば、カスタム スクリプト拡張機能を使用する場合、スクリプト実行の詳細は、拡張機能が実行された仮想マシンのローカルに保存されます。 拡張機能に固有のトラブルシューティング手順は、拡張機能のドキュメントで詳しく説明されています。

次のトラブルシューティング手順は、すべての仮想マシン拡張機能に適用されます。

### <a name="view-extension-status"></a>拡張機能の状態表示

仮想マシンに対して仮想マシン拡張機能を実行した後で、次の Azure CLI コマンドを使用して、拡張機能の状態を返します。 次のコマンドでは、パラメーター名を独自の値に置き換えてください。

```azurecli
azure vm extension get myResourceGroup myVM
```

出力は次のテキストのようになります。

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "myVM"
data:    Publisher                   Name             Version  State
data:    --------------------------  ---------------  -------  ---------
data:    Microsoft.Azure.Extensions  DockerExtension  1.0      Succeeded
info:    vm extension get command OK         :
```

拡張機能の実行の状態は、Azure Portal で確認することもできます。 拡張機能の状態を表示するには、仮想マシンを選択し、**[拡張機能]** を選択して目的の拡張機能を選択します。

### <a name="rerun-a-vm-extension"></a>VM 拡張機能の再実行

仮想マシン拡張機能の再実行が必要な場合があります。 拡張機能を再実行するには、その拡張機能を削除し、その後任意の実行方法で拡張機能を再実行します。 拡張機能を削除するには、Azure CLI を使用して次のコマンドを実行します。 次のコマンドでは、パラメーター名を独自の値に置き換えてください。

```azurecli
azure vm extension set myResourceGroup myVM --uninstall CustomScript Microsoft.Azure.Extensions 2.0
```

Azure Portal で次の手順を使用して拡張機能を削除できます。

1. 仮想マシンを選択します。
2. **[拡張機能]** を選択します。
3. 目的の拡張機能を選択します。
4. **[アンインストール]** を選択します。

## <a name="common-vm-extension-reference"></a>一般的な VM 拡張機能のリファレンス
| 拡張機能の名前 | 説明 | 詳細情報 |
| --- | --- | --- |
| Linux 用カスタム スクリプト拡張機能 |Azure 仮想マシンに対してスクリプトを実行します。 |[Linux 用カスタム スクリプト拡張機能](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker 拡張機能 |リモートの Docker コマンドをサポートする Docker デーモンをインストールします。 |[Docker VM 拡張機能](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM アクセス拡張機能 |Azure 仮想マシンへのアクセスを回復します。 |[VM アクセス拡張機能](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure 診断拡張機能 |Azure 診断を管理します |[Azure 診断拡張機能](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM アクセス拡張機能 |ユーザーと資格情報を管理します。 |[Linux 用 VM アクセス拡張機能](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

