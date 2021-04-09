---
title: Azure の Linux VM でカスタム スクリプト拡張機能を実行する
description: カスタム スクリプト拡張機能 v2 を使用して Linux VM 構成タスクを自動化する
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 04/25/2018
ms.openlocfilehash: 094e5f4b1bf1611f2d418d3a7b8db15ec5d58878
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563576"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Linux 仮想マシンで Azure カスタム スクリプト拡張機能 v2 を使用する
カスタム スクリプト拡張機能バージョン 2 は、スクリプトをダウンロードし、Azure 仮想マシン上で実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。 スクリプトは、Azure Storage や他のアクセス可能なインターネットの場所からダウンロードできます。または、実行時に拡張機能に提供することもできます。 

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合します。 また、Azure CLI、PowerShell、または Azure Virtual Machines REST API を使って実行することもできます。

この記事では、Azure CLI からカスタム スクリプト拡張機能を使う方法と、Azure Resource Manager テンプレートを使って拡張機能を実行する方法について説明します。 また、Linux システムでのトラブルシューティング手順も示します。


次の 2 つの Linux カスタム スクリプト拡張機能があります。
* バージョン 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* バージョン 2 - Microsoft.Azure.Extensions.CustomScript

新しいバージョン 2 を使用するには、新規および既存の展開を切り替えてください。 新しいバージョンはドロップイン置換用に設計されています。 したがって、名前とバージョンを変更するだけで簡単に移行でき、拡張機能の構成を変更する必要はありません。


### <a name="operating-system"></a>オペレーティング システム

Linux 用カスタム スクリプト拡張機能は、サポートされている拡張機能 OS 上で実行できます。詳細については、この[記事](../linux/endorsed-distros.md)をご覧ください。

### <a name="script-location"></a>スクリプトの場所

拡張機能を使用すると、Azure Blob ストレージの資格情報を使用して Azure Blob Storage にアクセスできます。 また、スクリプトは、GitHub や社内ファイル サーバーなどの、そのエンドポイントに VM をルーティングできる限り、任意の場所に保存できます。

### <a name="internet-connectivity"></a>インターネット接続
GitHub または Azure Storage などスクリプトを外部でダウンロードする必要がある場合は、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。 たとえば、スクリプトが Azure ストレージにある場合、[ストレージ](../../virtual-network/network-security-groups-overview.md#service-tags)の Azure NSG サービス タグを使用してアクセスを許可できます。

スクリプトがローカル サーバー上にある場合も、ファイアウォールやネットワーク セキュリティ グループのポートを追加で開く必要があります。

### <a name="tips-and-tricks"></a>ヒントとコツ
* この拡張機能が失敗する原因として最もよく挙げられるものはスクリプトの構文エラーであるため、スクリプトを実行してもエラーが発生しないことをテストしたり、失敗した個所を簡単に特定できるロギング機能をスクリプトに追加してください。
* 誤って複数回実行されたときも、システムに変更が加えられないよう、べき等のスクリプトを作成してください。
* スクリプトの実行時に、ユーザー入力を必要としないように作成してください。
* スクリプトの実行に許されているのは 90 分間で、これを超えると拡張機能へのプロビジョニングが失敗します。
* スクリプトの中に再起動を組み込まないでください。これを守らないと、インストールされているその他の拡張で問題が発生し、再起動後にその拡張は実行されなくなります。 
* VM エージェントの停止または更新の原因となるスクリプトを実行することはお勧めしません。 これにより、拡張機能が遷移状態になり、タイムアウトにつながることがあります。
* 再起動が必要なスクリプトの場合は、アプリケーションをインストールしてから、スクリプトを実行するなどしてください。Cron ジョブを使用して、あるいは DSC、Chef、Puppet 拡張機能などのツールを使用して再起動をスケジュールしてください。
* 拡張機能ではスクリプトは 1 度だけ実行するか、あるいは、起動するたびに実行できます。この場合、[cloud-init イメージ](../linux/using-cloud-init.md)と、[Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) モジュールを使用できます。 または、SystemD サービス ユニットを作成するためにスクリプトを使用することができます。
* VM に適用できる拡張機能のバージョンは 1 つだけです。 2 つ目のカスタム スクリプトを実行するために、既存の拡張機能を新しい構成で更新できます。 または、カスタム スクリプト拡張機能を削除し、更新されたスクリプトを使用してそれを再適用できます。
* スクリプトを実行する時期をスケジュールする場合は、拡張機能を使用して Cron ジョブを作成する必要があります。 
* スクリプトが実行されている場合は、Azure Portal または CLI には拡張機能の状態が「移行中」とのみ表示されます。 実行中のスクリプトのステータスをより高い頻度で更新するには、独自のソリューションを作成する必要があります。
* カスタム スクリプト拡張機能では、プロキシ サーバーはネイティブではサポートされていませんが、*Curl* などの、プロキシ サーバーをサポートするファイル転送ツールをスクリプト内で使用することができます。 
* スクリプトまたはコマンドで使用している既定以外のディレクトリの場所に注意し、これを処理するロジックを用意してください。
*  カスタム スクリプトを運用環境の VMSS インスタンスにデプロイする場合は、json テンプレートを使用してデプロイし、SAS トークンを制御できるスクリプト ストレージ アカウントを格納することをお勧めします。 


## <a name="extension-schema"></a>拡張機能のスキーマ

カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、構成ファイルに格納したり、コマンド ラインで指定したり、Azure Resource Manager テンプレートで指定したりできます。 

機微なデータは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

これらの項目は機密データとして扱う必要があり、拡張機能の保護された構成設定で指定する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> managedIdentity プロパティ を storageAccountName プロパティまたは storageAccountKey プロパティと組み合わせて使用することは **できません**

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.1 | INT |
| fileUris (例) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (例) | python MyPythonScript.py \<my-param1> | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (例:) | false | boolean |
| timestamp (例:) | 123456789 | 32-bit integer |
| storageAccountName (例) | examplestorageacct | string |
| storageAccountKey (例) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity (例) | { } or { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } or { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | JSON オブジェクト |

### <a name="property-value-details"></a>プロパティ値の詳細
* `apiVersion`:最新の apiVersion は、[リソース エクスプローラー](https://resources.azure.com/)を使用するか、Azure CLI から次のコマンドを使用して見つけることができます`az provider list -o json`
* `skipDos2Unix`: (省略可能、ブール値) スクリプトベースのファイル URL またはスクリプトの dos2unix 変換を省略します。
* `timestamp` (省略可能、32 ビットの整数) このフィールドは、このフィールドの値を変更することによりスクリプトの再実行をトリガーする場合のみ使用します。  任意の整数値が使用できますが、前の値と異なる必要があります。
* `commandToExecute`: (スクリプトが設定されていない場合は **必須**、文字列)  スクリプトの実行のエントリ ポイント。 コマンドにパスワードなどの機密情報が含まれている場合は、代わりにこのフィールドを使用します。
* `script`: (commandToExecute が設定されていない場合は **必須**、文字列) /bin/sh によって実行される、base64 でエンコードされた (または GZip 圧縮された) スクリプト。
* `fileUris`: (省略可能、文字列の配列) ファイルをダウンロードする URL。
* `storageAccountName`: (省略可能、文字列) ストレージ アカウントの名前。 ストレージの資格情報を指定する場合は、すべての `fileUris` が Azure BLOB の URL である必要があります。
* `storageAccountKey`: (省略可能、文字列) ストレージ アカウントのアクセス キー
* `managedIdentity`: (省略可能、json オブジェクト) ファイルをダウンロードするための[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)
  * `clientId`: (省略可能、文字列) マネージド ID のクライアント ID
  * `objectId`: (省略可能、文字列) マネージド ID のオブジェクト ID


パブリックまたはプロテクトのいずれかの設定に、次の値を設定することができます。拡張機能では、パブリックおよびプロテクトの両方の設定に以下の値が設定された場合、構成が拒否されます。
* `commandToExecute`
* `script`
* `fileUris`

デバッグには、パブリック設定を使用することが便利な場合もありますが、保護された設定を使用することを強くお勧めします。

パブリック設定は、スクリプトを実行する仮想マシンにクリア テキストで送信されます。  保護された設定は、Azure と VM のみが知っているキーを使用して暗号化されます。 設定は、送信された VM に保存されます。このため、設定が暗号化されていた場合は、VM 上に暗号化された状態で保存されます。 暗号化された値を復号化するために使用する証明書は、VM に格納され、実行時の設定 (必要な場合) の暗号化を解除するためにも使用されます。

#### <a name="property-skipdos2unix"></a>プロパティ: skipDos2Unix

既定値は false で、dos2unix 変換が **実行されます**。

前のバージョンの CustomScript、Microsoft.OSTCExtensions.CustomScriptForLinux では、`\r\n` を `\n` に変換することによって DOS ファイルが UNIX ファイルに自動的に変換されます。 この変換がまだ存在し、既定ではオンに設定されています。 この変換は、fileUris からダウンロードされた、または、次の条件のいずれかに基づいてスクリプトが設定されたすべてのファイルに適用されます。

* 拡張子が `.sh`、`.txt`、`.py`、`.pl` のいずれかの場合は、変換されます。 スクリプトは /bin/sh で実行され、VM 上に script.sh として保存されることが前提とされているため、スクリプトの設定は常にこの条件に一致します。
* ファイルが `#!` で始まる場合。

dos2unix 変換は、skipDos2Unix を true に設定することで省略できます。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>プロパティ: script

CustomScript では、ユーザー定義のスクリプトの実行がサポートされています。 commandToExecute と fileUris を 1 つの設定に結合するスクリプト設定。 Azure ストレージまたは GitHub gist からダウンロードするファイルを設定する代わりに、設定としてスクリプトをエンコードできます。 スクリプトは、commandToExecute と fileUris を置き換えるために使用できます。

スクリプトは、base64 エンコードする **必要があります**。  スクリプトは **必要に応じて** GZip 圧縮できます。 スクリプトの設定は、パブリックまたは保護された設定で使用できます。 スクリプト パラメーターのデータの最大サイズは、256 KB です。 このサイズを超えるスクリプトは実行されません。

たとえば、/script.sh/ ファイルに次のスクリプトが保存されているとします。

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

正しい CustomScript スクリプト設定は、次のコマンドの出力を実行して構築されます。

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

または、GZip 圧縮してさらにスクリプトのサイズを削減することもできます (ほとんどの場合)。 (CustomScript では GZip 圧縮の使用は自動的に検出されます。)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript では、次のアルゴリズムを使用して、スクリプトを実行します。

 1. スクリプトの値の長さが 256 KB を超えないことをアサートします。
 1. スクリプトの値を base64 にデコードする
 1. base64 にデコードされた値の GunZip 圧縮を _試行_ します。
 1. デコード (および必要に応じて圧縮解除) された値をディスクに書き込む (/var/lib/waagent/custom-script/#/script.sh)
 1. _/bin/sh-c/var/lib/waagent/custom-script/#/script.sh を使用して、スクリプトを実行します。

####  <a name="property-managedidentity"></a>プロパティ: managedIdentity
> [!NOTE]
> このプロパティは、保護された設定でのみ指定する **必要があります**。

CustomScript (バージョン 2.1 以降) では、"fileUris" 設定で指定された URL からファイルをダウンロードするための[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) がサポートされています。 これにより、ユーザーが SAS トークンやストレージ アカウント キーなどのシークレットを渡さなくとも、CustomScript で Azure Storage プライベート BLOB またはコンテナーにアクセスできるようになります。

この機能を使用するには、ユーザーが、[システムによって割り当てられたか](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)または[ユーザーが割り当てた](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) ID を CustomScript が実行されると想定される VM または VMSS に追加し、[Azure Storage コンテナーまたは BLOB にマネージド ID のアクセス権を付与する必要があります](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)。

ターゲット VM/VMSS でシステムによって割り当てられた ID を使用するには、"managedidentity" フィールドを空の JSON オブジェクトに設定します。 

> 例:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

ターゲット VM/VMSS でユーザーが割り当てた ID を使用するには、"managedidentity" フィールドを、マネージド ID のクライアント ID またはオブジェクト ID で構成します。

> 例 :
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity プロパティ を storageAccountName プロパティまたは storageAccountKey プロパティと組み合わせて使用することは **できません**

## <a name="template-deployment"></a>テンプレートのデプロイ
Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時にカスタム スクリプト拡張機能を実行できます。 カスタム スクリプト拡張機能を含むサンプル テンプレートは、[GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux) で入手できます。


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

## <a name="azure-cli"></a>Azure CLI
Azure CLI を使ってカスタム スクリプト拡張機能を実行するときは、1 つまたは複数の構成ファイルを作成します。 少なくとも 'commandToExecute' が存在する必要があります。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

必要に応じて、コマンドで JSON 形式の文字列として設定を指定できます。 そのようにすると、実行中に構成を指定することができ、個別の構成ファイルが不要になります。

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI の例

#### <a name="public-configuration-with-script-file"></a>スクリプト ファイルのあるパブリック構成

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>トラブルシューティング
カスタム スクリプト拡張機能を実行すると、スクリプトが次の例のようなディレクトリに作成されるかダウンロードされます。 コマンドの出力も、このディレクトリの `stdout` および `stderr` ファイルに保存されます。

```bash
/var/lib/waagent/custom-script/download/0/
```

トラブルシューティングする場合は、まず Linux エージェントのログをチェックして、拡張機能が実行されていることを確認します。

```bash
/var/log/waagent.log 
```

次のような、拡張機能の実行を特定します。

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

注意：
1. Enable は、コマンドの実行が開始されたときです。
2. Download は、FileUris に指定されたスクリプト ファイルではなく、Azure からの CustomScript 拡張機能パッケージのダウンロードに関連します。


Azure スクリプト拡張機能が生成するログも、ここにあります。

```bash
/var/log/azure/custom-script/handler.log
```

次のような各実行を特定します。

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

ここで、
* このログでは、Enable コマンドが開始されています。
* 拡張機能に設定が渡されています。
* その結果、拡張機能がファイルをダウンロードしています。
* コマンドの実行とその結果。

また、Azure CLI を使用して `commandToExecute` として実際に渡された引数など、カスタム スクリプト拡張機能の実行状態を取得することもできます。

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

出力は次のテキストのようになります。

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>次のステップ
コード、現在の問題およびバージョンについては、[custom-script-extension-linux rep](https://github.com/Azure/custom-script-extension-linux) を参照してください。
