---
title: "Azure で Linux VM のカスタム スクリプトを実行する | Microsoft Docs"
description: "カスタム スクリプト拡張機能を使用して Linux VM 構成タスクを自動化する"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 09c1ace33461eff30fbd4b277e1397934fe5a98a
ms.lasthandoff: 01/31/2017


---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Azure カスタム スクリプト拡張機能と Linux 仮想マシンの使用
カスタム スクリプト拡張機能は、Azure 仮想マシンでスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージやその他のアクセス可能なインターネットの場所からダウンロードできます。また、実行時に拡張機能に提供することもできます。 カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

このドキュメントでは、Azure CLI からカスタム スクリプト拡張機能を使用する方法と、Azure Resource Manager テンプレートを使用する方法について詳しく説明します。また、Linux システムでのトラブルシューティング手順についても詳しく説明します。

## <a name="extension-configuration"></a>拡張機能の構成
カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、コマンド ラインで指定する構成ファイルや、Azure Resource Manager テンプレートに格納できます。 機密データは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

### <a name="public-configuration"></a>パブリック構成
スキーマ:

* **commandToExecute**: (必須、文字列) 実行するエントリ ポイント スクリプト
* **fileUris**: (省略可能、文字列の配列) ダウンロードするファイルの URL。
* **timestamp** : (省略可能、整数) このフィールドの値を変更してスクリプトの再実行をトリガーする場合にだけ、このフィールドを使用します。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>保護された構成
スキーマ:

* **commandToExecute**: (省略可能、文字列) 実行するエントリ ポイント スクリプト。 コマンドにパスワードなどの機密情報が含まれている場合は、代わりにこのフィールドを使用します。
* **storageAccountName**: (省略可能、文字列) ストレージ アカウントの名前。 ストレージの資格情報を指定する場合は、すべての fileUris が Azure BLOB の URL である必要があります。
* **storageAccountKey**: (省略可能、文字列) ストレージ アカウントのアクセス キー。

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
カスタム スクリプト拡張機能を実行するために Azure CLI を使用している場合は、少なくともファイルの URI およびスクリプト実行コマンドが含まれている構成ファイルを作成します。

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version --public-config-path /script-config.json
```

必要に応じて、`--public-config` および `--private-config` オプションを使用してコマンドを実行できます。そのようにすると、実行中に構成を指定することができ、個別の構成ファイルが不要になります。

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version \
  --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI の例
**例 1** - スクリプト ファイルを持つパブリック構成。

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Azure CLI コマンド:

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version --public-config-path /public.json
```

**例 2** - スクリプト ファイルを持たないパブリック構成。

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI コマンド:

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version --public-config-path /public.json
```

**例 3** - スクリプト ファイルの URI を指定するためにパブリック構成ファイルを使用し、実行するコマンドを指定するために、保護された構成ファイルを使用します。

パブリック構成ファイル:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

保護された構成ファイル:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI コマンド:

```azurecli
azure vm extension set myResourceGroup myVM CustomScript Microsoft.Azure.Extensions 2.0 \
  --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure カスタム スクリプト拡張機能は、Resource Manager テンプレートを使用して、仮想マシンのデプロイ時に実行できます。 そうするには、適切な形式の JSON をデプロイ テンプレートに追加します。

### <a name="resource-manager-examples"></a>Resource Manager の例
**例 1** - パブリック構成。

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**例 2** - 保護された構成内の実行コマンド。

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

完全な例については、 [.Net Core Music Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
カスタム スクリプト拡張機能を実行すると、スクリプトが次の例のようなディレクトリに作成されるかダウンロードされます。 コマンドの出力も、このディレクトリの `stdout` および `stderr` ファイルに保存されます。

```bash
/var/lib/waagent/custom-script/download/0/
```

Azure スクリプト拡張機能ではログが生成され、そのログもここに保存されます。

```bash
/var/log/azure/custom-script/handler.log
```

カスタム スクリプト拡張機能の実行状態も、Azure CLI で取得できます。

```azurecli
azure vm extension get myResourceGroup myVM
```

出力は次のテキストのようになります。

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>次のステップ
他の VM スクリプト拡張機能については、[Linux 用の Azure スクリプト拡張機能の概要](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。


