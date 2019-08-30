---
title: Azure で Linux VM のカスタム スクリプトを実行する | Microsoft Docs
description: カスタム スクリプト拡張機能 v1 を使用して Linux VM 構成タスクを自動化します
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: e5ef1bde9420104b596c22837048b054f918b3cc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092625"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Linux 仮想マシンで Azure カスタム スクリプト拡張機能 v1 を使用する

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

カスタム スクリプト拡張機能バージョン 1 は、スクリプトをダウンロードし、Azure 仮想マシン上で実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。 スクリプトは、Azure Storage や他のアクセス可能なインターネットの場所からダウンロードできます。または、実行時に拡張機能に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合します。 また、Azure CLI、PowerShell、Azure Portal、または Azure Virtual Machines REST API を使って実行することもできます。

この記事では、Azure CLI からカスタム スクリプト拡張機能を使う方法と、Azure Resource Manager テンプレートを使って拡張機能を実行する方法について説明します。 また、Linux システムでのトラブルシューティング手順も示します。

次の 2 つの Linux カスタム スクリプト拡張機能があります。

* バージョン 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* バージョン 2 - Microsoft.Azure.Extensions.CustomScript

代わりに新しいバージョン ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) を使うには、新規の展開と既存の展開を切り替えてください。 新しいバージョンはドロップイン置換用に設計されています。 したがって、名前とバージョンを変更するだけで簡単に移行でき、拡張機能の構成を変更する必要はありません。

### <a name="operating-system"></a>オペレーティング システム

サポートされている Linux ディストリビューション:

* CentOS 6.5 以降
* Debian 8 以降
  * Debian 8.7 の最新イメージには Python2 が付属していないので、CustomScriptForLinux は使用できません。
* FreeBSD
* OpenSUSE 13.1 以降
* Oracle Linux 6.4 以降
* SUSE Linux Enterprise Server 11 SP3 以降
* Ubuntu 12.04 以降

### <a name="script-location"></a>スクリプトの場所

拡張機能を使用すると、Azure Blob ストレージの資格情報を使用して Azure Blob Storage にアクセスできます。 また、スクリプトは、GitHub や社内ファイル サーバーなどの、そのエンドポイントに VM をルーティングできる限り、任意の場所に保存できます。

### <a name="internet-connectivity"></a>インターネット接続

GitHub または Azure Storage などスクリプトを外部でダウンロードする必要がある場合は、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。 たとえば、スクリプトが Azure ストレージにある場合、[ストレージ](../../virtual-network/security-overview.md#service-tags)の Azure NSG サービス タグを使用してアクセスを許可できます。

スクリプトがローカル サーバー上にある場合も、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。

### <a name="tips-and-tricks"></a>ヒントとコツ

* この拡張機能が失敗する原因として最もよく挙げられるものはスクリプトの構文エラーであるため、スクリプトを実行してもエラーが発生しないことをテストしたり、失敗した個所を簡単に特定できるロギング機能をスクリプトに追加してください。
* 誤って複数回実行されたときも、システムに変更が加えられないよう、べき等のスクリプトを作成してください。
* スクリプトの実行時に、ユーザー入力を必要としないように作成してください。
* スクリプトの実行に許されているのは 90 分間で、これを超えると拡張機能へのプロビジョニングが失敗します。
* スクリプトの中に再起動を組み込まないでください。これを守らないと、インストールされているその他の拡張で問題が発生し、再起動後にその拡張は実行されなくなります。 
* 再起動が必要なスクリプトの場合は、アプリケーションをインストールしてから、スクリプトを実行するなどしてください。Cron ジョブを使用して、あるいは DSC、Chef、Puppet 拡張機能などのツールを使用して再起動をスケジュールしてください。
* 拡張機能ではスクリプトは 1 度だけ実行するか、あるいは、起動するたびに実行できます。この場合、[cloud-init イメージ](../linux/using-cloud-init.md)と、[Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) モジュールを使用できます。 または、Systemd サービス ユニットを作成するためにスクリプトを使用することができます。
* スクリプトを実行する時期をスケジュールする場合は、拡張機能を使用して Cron ジョブを作成する必要があります。
* スクリプトが実行されている場合は、Azure Portal または CLI には拡張機能の状態が「移行中」とのみ表示されます。 実行中のスクリプトのステータスをより高い頻度で更新するには、独自のソリューションを作成する必要があります。
* カスタム スクリプト拡張機能では、プロキシ サーバーはネイティブではサポートされていませんが、*Curl* などの、プロキシ サーバーをサポートするファイル転送ツールをスクリプト内で使用することができます。
* スクリプトまたはコマンドで使用している既定以外のディレクトリの場所に注意し、これを処理するロジックを用意してください。

## <a name="extension-schema"></a>拡張機能のスキーマ

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、構成ファイルに格納したり、コマンド ラインで指定したり、Azure Resource Manager テンプレートで指定したりできます。 

機微なデータは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

これらの項目は機密データとして扱う必要があり、拡張機能の保護された構成設定で指定する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | string |
| type | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (例) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (例) | python MyPythonScript.py \<my-param1\> | string |
| enableInternalDNSCheck | true | ブール値 |
| storageAccountName (例) | examplestorageacct | string |
| storageAccountKey (例) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>プロパティ値の詳細

* `fileUris`: (省略可能、文字列配列) スクリプトの URI リスト
* `enableInternalDNSCheck`: (省略可能、ブール値) 既定値は True、DNS チェックを無効にする場合 False に設定。
* `commandToExecute`: (省略可能、文字列) 実行するエントリ ポイント スクリプト
* `storageAccountName`: (省略可能、文字列) ストレージ アカウントの名前
* `storageAccountKey`: (省略可能、文字列) ストレージ アカウントのアクセス キー

パブリックまたはプロテクトのいずれかの設定に、次の値を設定することができます。パブリックとプロテクトの両方でこれらの値を設定してはなりません。

* `commandToExecute`

デバッグには、パブリック設定を使用することが便利な場合もありますが、保護された設定を使用することを強くお勧めします。

パブリック設定は、スクリプトを実行する仮想マシンにクリア テキストで送信されます。  保護された設定は、Azure と VM のみが知っているキーを使用して暗号化されます。 設定は、送信された VM に保存されます。このため、設定が暗号化されていた場合は、VM 上に暗号化された状態で保存されます。 暗号化された値を復号化するために使用する証明書は、VM に格納され、実行時の設定 (必要な場合) の暗号化を解除するためにも使用されます。

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時にカスタム スクリプト拡張機能を実行できます。

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使ってカスタム スクリプト拡張機能を実行するときは、1 つまたは複数の構成ファイルを作成します。 少なくとも 'commandToExecute' が存在する必要があります。

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

必要に応じて、コマンドで JSON 形式の文字列として設定を指定できます。 そのようにすると、実行中に構成を指定することができ、個別の構成ファイルが不要になります。

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI の例

#### <a name="public-configuration-with-no-script-file"></a>スクリプト ファイルのないパブリック構成

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>保護されたパブリック構成ファイル

スクリプト ファイルの URI を指定するには、パブリック構成ファイルを使います。 実行するコマンドを指定するには、保護された構成ファイルを使います。

パブリック構成ファイル:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

保護された構成ファイル:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>トラブルシューティング

カスタム スクリプト拡張機能を実行すると、スクリプトが次の例のようなディレクトリに作成されるかダウンロードされます。 コマンドの出力も、このディレクトリの `stdout` および `stderr` ファイルに保存されます。

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

トラブルシューティングする場合は、まず Linux エージェントのログをチェックして、拡張機能が実行されていることを確認します。

```bash
/var/log/waagent.log
```

次のような、拡張機能の実行を特定します。

```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

注意：

1. Enable は、コマンドの実行が開始されたときです。
1. Download は、FileUris に指定されたスクリプト ファイルではなく、Azure からの CustomScript 拡張機能パッケージのダウンロードに関連します。
1. また、ファイルの書き出し先となるログ ファイル (`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`) も確認できます

次にログ ファイルを調べます。次のような形式です。

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

次のような各実行を特定します。

```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

ここで、

* このログでは、Enable コマンドが開始されています。
* 拡張機能に設定が渡されています。
* その結果、拡張機能がファイルをダウンロードしています。
* コマンドの実行とその結果。

また、Azure CLI を使って、カスタム スクリプト拡張機能の実行状態を取得できます。

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

出力は次のテキストのようになります。

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>次の手順

コード、現在の問題、およびバージョンについては、[カスタム スクリプト拡張機能 リポジトリ](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)をご覧ください。
