---
title: Azure Image Builder テンプレートを作成する (プレビュー)
description: Azure Image Builder で使用するテンプレートを作成する方法について説明します。
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: aaaabe758b036335062907c8e5549ae876c63997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594735"
---
# <a name="preview-create-an-azure-image-builder-template"></a>プレビュー:Azure Image Builder テンプレートを作成する 

Azure Image Builder では、.json ファイルを使って Image Builder サービスに情報を渡します。 この記事では、独自のテンプレートを作成できるように、json ファイルの各セクションについて説明します。 完全な .json ファイルの例を確認するには、[Azure Image Builder の GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts) をご覧ください。

テンプレートの基本的な形式を次に示します。

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>種類と API のバージョン

`type` はリソースの種類であり、`"Microsoft.VirtualMachineImages/imageTemplates"` にする必要があります。 `apiVersion` は API の変化に合わせて時間と共に変わりますが、プレビューでは `"2020-02-14"` にする必要があります。

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>場所

location は、カスタム イメージを作成するリージョンです。 Image Builder プレビューでは、次のリージョンがサポートされています。

- 米国東部
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2
- 北ヨーロッパ
- 西ヨーロッパ


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
既定では Image Builder は "Standard_D1_v2" ビルド VM を使用しますが、これはオーバーライドできます。たとえば GPU VM 用にイメージをカスタマイズする場合は、GPU VM サイズが必要になります。 これは省略可能です。

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

既定では Image Builder はイメージのサイズを変更しません。ソース イメージのサイズが使用されます。 OS ディスク (Win および Linux) のサイズは増やすこと **だけ** が可能です。これは省略可能であり、値 0 はソース イメージと同じサイズを維持することを意味します。 OS ディスクのサイズをソース イメージのサイズより小さくすることはできません。

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
VNET プロパティを指定しない場合、Image Builder によって独自の VNET、パブリック IP、および NSG が作成されます。 パブリック IP は、サービスがビルド VM と通信するときに使用されますが、パブリック IP が不要な場合、または Image Builder が既存の VNET リソース (構成サーバー (DSC、Chef、Puppet、Ansible)、ファイル共有など) にアクセスできるようにする場合は、VNET を指定できます。 詳細については、[ネットワークに関するドキュメント](image-builder-networking.md)をご確認ください。これは省略可能です。

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tags

これらは、生成されるイメージに対して指定できるキー/値ペアです。

## <a name="depends-on-optional"></a>依存関係 (省略可能)

この省略可能なセクションを使って、先に進む前に、依存関係が揃っていることを確認できます。 

```json
    "dependsOn": [],
```

詳しくは、「[リソースの依存関係を定義する](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)」をご覧ください。

## <a name="identity"></a>ID

必須 - スクリプトで Azure Storage から読み取られたイメージを読み書きするアクセスが Image Builder に許可されるようにするには、個々のリソースに対するアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Image Builder のアクセス許可のしくみと関連する手順の詳細については、[ドキュメント](image-builder-user-assigned-identity.md)を参照してください。


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Image Builder によるユーザー割り当て ID のサポート:
* 単一の ID のみがサポートされています
* カスタム ドメインはサポートされていません

詳しくは、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。
この機能のデプロイについて詳しくは、「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)」をご覧ください。

## <a name="properties-source"></a>プロパティ: source

`source` セクションには、Image Builder によって使われるソース イメージについての情報が含まれます。 現在 Image Builder でネイティブにサポートされているのは、Azure Shared Image Gallery (SIG) への Hyper-V 第 1 世代 (Gen1) のイメージ、またはマネージド イメージの作成のみです。 Gen2 イメージを作成する場合は、ソース Gen2 イメージを使用し、VHD に配布する必要があります。 その後、VHD からマネージド イメージを作成し、これを Gen2 イメージとして SIG に挿入する必要があります。

API ではイメージ ビルド用のソースを定義する "SourceType" が必要であり、現在は次の 3 つの種類があります。
- PlatformImage - ソース イメージが Marketplace イメージであることを示します。
- ManagedImage - 標準のマネージド イメージから始めるときは、これを使います。
- SharedImageVersion - ソースとして共有イメージ ギャラリー内のイメージのバージョンを使うときは、これを使います。


> [!NOTE]
> 既存の Windows カスタム イメージを使用する場合は、単一の Windows イメージで Sysprep コマンドを最大で 8 回実行できます。詳細については、[sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) に関するドキュメントを参照してください。

### <a name="platformimage-source"></a>PlatformImage ソース 
Azure Image Builder では、Windows Server とクライアント、および Linux Azure Marketplace のイメージがサポートされます。完全な一覧については、[こちら](../image-builder-overview.md#os-support)を参照してください。 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


ここでのプロパティは VM の作成に使われるものと同じであり、プロパティを取得するには AZ CLI を使って以下を実行します。 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

"最新" バージョンを使用できますが、バージョンは、テンプレートが送信されるときではなく、イメージのビルドが行われるときに評価されます。 Shared Image Gallery 送信先でこの機能を使用する場合、テンプレートを再送信するのは避け、間隔を置いてイメージ ビルドを再実行します。これにより、最新のイメージから、ご自身のイメージが再作成されます。

#### <a name="support-for-market-place-plan-information"></a>マーケットプレース プラン情報のサポート
次の例のように、プラン情報を指定することもできます。
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage ソース

ソース イメージを、一般化された VHD または VM の既存のマネージド イメージとして設定します。

> [!NOTE]
> ソース マネージド イメージは、サポート対象の OS のものでなければならず、このイメージは Azure Image Builder テンプレートと同じリージョンに存在する必要があります。 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` は、マネージド イメージの ResourceId にする必要があります。 使用可能なイメージの一覧を表示するには、`az image list` を使います。


### <a name="sharedimageversion-source"></a>SharedImageVersion ソース
ソース イメージを、共有イメージ ギャラリー内の既存のイメージ バージョンに設定します。

> [!NOTE]
> ソース マネージド イメージは、サポート対象の OS のものでなければならず、このイメージは Azure Image Builder テンプレートと同じリージョンに存在する必要があります。それ以外の場合は、イメージ バージョンを Image Builder テンプレート リージョンにレプリケートしてください。


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` は、イメージ バージョンの ResourceId にする必要があります。 イメージ バージョンの一覧を表示するには、[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) を使います。


## <a name="properties-buildtimeoutinminutes"></a>プロパティ: buildTimeoutInMinutes

既定では、Image Builder は 240 分間実行されます。 その後、イメージのビルドが完了しているかどうかにかかわらず、タイムアウトして停止します。 タイムアウトに達した場合は、次のようなエラーが表示されます。

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

buildTimeoutInMinutes の値を指定しなかった場合、または 0 に設定した場合は、既定値が使用されます。 値は、最大で 960 分 (16 時間) まで増減できます。 Windows の場合、この値を 60 分未満に設定することはお勧めしません。 タイムアウトに達していることがわかった場合は、[ログ](image-builder-troubleshoot.md#customization-log)を確認し、カスタマイズの手順がユーザー入力などで待機しているかどうかを確認します。 

カスタマイズを完了するためにより多くの時間が必要な場合は、オーバーヘッドが小さい必要と思われるものに設定します。 ただし、エラーが表示される前にタイムアウトするまで待機する必要があるため、設定値を大きくしすぎないでください。 


## <a name="properties-customize"></a>プロパティ: customize

Image Builder では、複数の "カスタマイザー" がサポートされています。 カスタマイザーは、スクリプトの実行やサーバーの再起動など、イメージのカスタマイズに使われる機能です。 

`customize` を使うとき: 
- 複数のカスタマイザーを使用できますが、一意の `name` を持っている必要があります。
- カスタマイザーは、テンプレートで指定されている順序で実行されます。
- 1 つのカスタマイザーが失敗した場合、カスタマイズ コンポーネント全体が失敗し、エラーが報告されます。
- テンプレートで使う前に、スクリプトを十分にテストすることを強くお勧めします。 独自の VM でスクリプトをデバッグする方が簡単です。
- スクリプト内には機密データを記述しないでください。 
- [MSI](./image-builder-user-assigned-identity.md) を使っていない場合は、パブリックにアクセスできる場所にスクリプトを置く必要があります。

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
customize セクションは配列です。 Azure Image Builder では、カスタマイザーが順番に実行されます。 いずれかのカスタマイザーでエラーが発生すると、ビルド プロセスが失敗します。 

> [!NOTE]
> インライン コマンドは、イメージ テンプレート定義に表示され、サポート ケース支援時に Microsoft サポートによって表示されることがあります。 機密情報がある場合は、アクセスに認証が必要な Azure Storage 内のスクリプトに移動する必要があります。
 
### <a name="shell-customizer"></a>シェル カスタマイザー

シェル カスタマイザーではシェル スクリプトの実行がサポートされ、スクリプトは IB がアクセスできるようにパブリックにアクセス可能である必要があります。

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS のサポート: Linux 
 
カスタマイズのプロパティ:

- **type** - Shell 
- **name**- カスタマイズを追跡するための名前 
- **scriptUri** - ファイルの場所への URI 
- **inline** - コンマで区切られたシェル コマンドの配列。
- **sha256Checksum** - ファイルの sha256 チェックサムの値。これをローカルで生成すると、Image Builder によってチェックサムと検証が実行されます。
    * Mac/Linux のターミナルを使用して sha256Checksum を生成するには、`sha256sum <fileName>` を実行します。

> [!NOTE]
> インライン コマンドはイメージ テンプレート定義の一部として格納されます。イメージ定義をダンプ出力したときに、これらのコマンドを確認できます。また、トラブルシューティングのためにサポート ケースが発生した場合に、Microsoft サポートでこれらのコマンドを表示することもできます。 機密性の高いコマンドまたは値がある場合は、それらをスクリプトに移動し、ユーザー ID を使用して Azure Storage に対する認証を行うことを強くお勧めします。

#### <a name="super-user-privileges"></a>スーパー ユーザー特権
先頭に `sudo` がある、スーパーユーザー特権で実行するコマンドは、スクリプトに追加したり、次の例のように inline コマンドで使用したりすることができます。
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
sudo を使用したスクリプトの例。scriptUri を使用して参照できます。
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows 再起動カスタマイザー 
再起動カスタマイザーでは、Windows VM を再起動して、オンラインに戻るのを待機することができます。これにより、再起動が必要なソフトウェアをインストールできます。  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS のサポート: Windows
 
カスタマイズのプロパティ:
- **[種類]** :WindowsRestart
- **restartCommand** - 再起動を実行するコマンド (省略可能)。 既定では、 `'shutdown /r /f /t 0 /c \"packer restart\"'`です。
- **restartCheckCommand** - 再起動が成功したかどうかを確認するコマンド (省略可能)。 
- **restartTimeout** - 大きさと単位の文字列として指定された再起動のタイムアウト。 たとえば、`5m` (5 分) や `2h` (2 時間) などです。 既定値は "5m" です

### <a name="linux-restart"></a>Linux の再起動  
Linux 再起動カスタマイザーはありませんが、ドライバーまたは再起動が必要なコンポーネントをインストールする場合、それらをインストールし、シェル カスタマイザーを使用して再起動を呼び出すことができます。ビルド VM の SSH タイムアウトは 20 分です。

### <a name="powershell-customizer"></a>PowerShell カスタマイザー 
シェル カスタマイザーでは PowerShell スクリプトとインライン コマンドの実行がサポートされており、スクリプトは IB がアクセスできるようにパブリックにアクセス可能である必要があります。

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS のサポート: Windows と Linux

カスタマイズのプロパティ:

- **type** - PowerShell。
- **scriptUri** - PowerShell スクリプト ファイルの場所への URI。 
- **inline** - コンマで区切って指定された、実行するインライン コマンド。
- **validExitCodes** - 省略可能。スクリプト/インライン コマンドから返すことができる有効なコード。これにより、スクリプト/インライン コマンドの報告済みエラーが回避されます。
- **runElevated** - 省略可能。ブール値。昇格されたアクセス許可でコマンドとスクリプトを実行するためのサポート。
- **sha256Checksum** - ファイルの sha256 チェックサムの値。これをローカルで生成すると、Image Builder によってチェックサムと検証が実行されます。
    * Windows で PowerShell を使用して sha256Checksum を生成するには、[Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash) を実行します。


### <a name="file-customizer"></a>ファイル カスタマイザー

ファイル カスタマイザーを使うと、Image Builder で GitHub または Azure Storage からファイルをダウンロードできます。 ビルド成果物に依存するイメージ ビルド パイプラインがある場合、ファイル カスタマイザーを設定して、成果物をビルド共有からダウンロードし、イメージに移動することができます。  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS のサポート: Linux、Windows 

ファイル カスタマイザーのプロパティ:

- **sourceUri** - アクセス可能なストレージ エンドポイント。GitHub または Azure Storage を指定できます。 ダウンロードできるのは 1 つのファイルだけであり、ディレクトリ全体はできません。 ディレクトリをダウンロードする必要がある場合は、圧縮されたファイルを使用し、シェルまたは PowerShell カスタマイザーを使って圧縮を解除します。 

> [!NOTE]
> sourceUri が Azure Storage アカウントの場合、BLOB が public としてマークされているかどうかにかかわらず、BLOB での読み取りアクセス許可をマネージド ユーザー ID に付与します。 ストレージのアクセス許可を設定するには、この[例](./image-builder-user-assigned-identity.md#create-a-resource-group)を参照してください。

- **destination** - これは、ターゲットの完全なパスとファイル名です。 参照されているすべてのパスとサブディレクトリが存在する必要があり、事前にたこれらを設定するにはシェルまたは PowerShell カスタマイザーを使います。 スクリプト カスタマイザーを使ってパスを作成できます。 

これは Windows のディレクトリと Linux のパスでサポートされていますが、いくつか違いがあります。 
- Linux OS – Image builder で書き込むことができる唯一のパスは /tmp です。
- Windows – パスの制限はありませんが、パスが存在する必要があります。
 
 
ファイルのダウンロードまたは指定されたディレクトリへの格納を行おうとしてエラーが発生した場合、そのカスタマイズ ステップは失敗し、customization.log にそれが記録されます。

> [!NOTE]
> ファイル カスタマイザーは、小さいファイルのダウンロード (20 MB 未満) にのみ適しています。 大きいファイルをダウンロードする場合は、スクリプトまたはインライン コマンド (ファイルをダウンロードするための使用コード) を使用します。たとえば、Linux の場合は `wget` または `curl`、Windows の場合は `Invoke-WebRequest` などがあります。

### <a name="windows-update-customizer"></a>Windows Update カスタマイザー
このカスタマイザーは、Packer の[コミュニティ Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) に基づいて構築されたオープン ソース プロジェクトで、Packer コミュニティによって管理されています。 Microsoft では、Image Builder サービスを使ってプロビジョナーをテストおよび検証し、問題の調査を支援して、解決に取り組んでいきますが、正式にはこのオープン ソース プロジェクトをサポートしていません。 Windows Update Provisioner の詳細なドキュメントとヘルプについては、プロジェクトのリポジトリを参照してください。

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

カスタマイズのプロパティ:
- **type**  – WindowsUpdate。
- **searchCriteria** - 省略可能。インストールされている更新プログラムの種類を定義します (推奨、重要 など)。既定値は、BrowseOnly=0 and IsInstalled=0 (推奨) です。
- **filters** – 省略可能。更新プログラムを含めるか除外するフィルターを指定できます。
- **updateLimit** – 省略可能。インストールできる更新プログラムの数を定義します。既定値は 1000 です。
 
> [!NOTE]
> Windows Update カスタマイザーは、保留中の Windows の再起動や、まだ実行中のアプリケーションのインストールがある場合に失敗する可能性があります。このエラー `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` は、通常、customization.log で確認できます。 Windows Update を実行する前に、Windows の再起動を取り入れることや、インライン コマンドやスクリプトに [sleep](/powershell/module/microsoft.powershell.utility/start-sleep) や待機のコマンドを追加して、アプリケーションにインストールを完了するのに十分な時間を与えることを強くお勧めします。

### <a name="generalize"></a>Generalize 
既定の Azure Image Builder では、イメージを "一般化" するため、各イメージ カスタマイズ フェーズの最後に "プロビジョニング解除" コードも実行されます。 一般化とは、複数の VM を作成するために再利用できるようにイメージを設定するプロセスです。 Windows VM の Azure Image Builder では、Sysprep が使われます。 Linux の Azure Image Builder では、"waagent -deprovision" が使われます。 

状況によっては Image Builder で一般化に使われるコマンドが適していない可能性があるので、Azure Image Builder では、必要に応じてこのコマンドをカスタマイズできます。 

既存のカスタマイズを移行していて、異なる Sysprep/waagent コマンドを使っている場合、Image Builder の汎用コマンドを使うと VM の作成が失敗するときは、独自の Sysprep または waagent コマンドを使用できます。

Azure Image Builder で正常に作成された Windows のカスタム イメージから VM を作成した後、VM の作成が失敗したこと、または正常に完了しなかったことがわかった場合は、Windows Server Sysprep のドキュメントを確認するか、Windows Server Sysprep のカスタマー サービス サポート チームにサポート要求を送る必要があります。チームは、トラブルシューティングを行って、Sysprep の適切な使用方法をアドバイスできます。


#### <a name="default-sysprep-command"></a>既定の Sysprep コマンド
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>既定の Linux プロビジョニング解除コマンド

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>コマンドのオーバーライド
コマンドをオーバーライドするには、PowerShell またはシェル スクリプトのプロビジョナーを使って正確なファイル名のコマンド ファイルを作成し、適切なディレクトリに置きます。

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder では、これらのコマンドが読み取られて、AIB ログ "customization.log" に書き込まれます。 ログを収集する方法については、[troubleshooting (トラブルシューティング)](image-builder-troubleshoot.md#customization-log) に関する記事をご覧ください。
 
## <a name="properties-distribute"></a>プロパティ: distribute

Azure Image Builder では、次の 3 つの配布ターゲットがサポートされています。 

- **managedImage** - マネージド イメージ。
- **sharedImage** - 共有イメージ ギャラリー。
- **VHD** - ストレージ アカウント内の VHD。

すべてのターゲットの種類に同じ構成でイメージを配布できます。

> [!NOTE]
> 既定の AIB sysprep コマンドには、"/mode:vm" は含まれていませんが、HyperV ロールがインストールされるイメージを作成するときに必要になる場合があります。 このコマンド引数を追加する場合は、sysprep コマンドをオーバーライドする必要があります。

複数のターゲットに配布できるので、Image Builder ではすべての配布ターゲットの状態が維持されており、`runOutputName` のクエリを実行することによってアクセスできます。  配布の後で `runOutputName` オブジェクトのクエリを実行して、その配布に関する情報を取得できます。 たとえば、VHD の場所、イメージ バージョンがレプリケートされたリージョン、または作成された SIG イメージのバージョンのクエリを実行できます。 これは、すべての配布ターゲットのプロパティです。 `runOutputName` は配布ターゲットごとに一意である必要があります。 次に例を示します。これは、Shared Image Gallery の配布に対するクエリです。

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

出力:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>配布: managedImage

イメージの出力は、マネージド イメージ リソースになります。

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
配布のプロパティ:
- **type** – managedImage 
- **imageId** – 配布先イメージのリソース ID。必要な形式: /subscriptions/\<subscriptionId>/resourceGroups/\<destinationResourceGroupName>/providers/Microsoft.Compute/images/\<imageName>
- **location** - マネージド イメージの場所。  
- **runOutputName** – 配布を示す一意の名前。  
- **artifactTags** -省略可能なユーザー指定のキー値ペアのタグ。
 
 
> [!NOTE]
> 配布先のリソース グループは存在している必要があります。
> 別のリージョンにイメージを配布する場合は、デプロイ時間が長くなります。 

### <a name="distribute-sharedimage"></a>配布: sharedImage 
Azure 共有イメージ ギャラリーは新しいイメージ管理サービスであり、イメージのリージョン レプリケーションの管理、バージョン管理、カスタム イメージの共有を行うことができます。 Azure Image Builder ではこのサービスによる配布がサポートされているので、共有イメージ ギャラリーでサポートされているリージョンにイメージを配布できます。 
 
共有イメージ ギャラリーは次のもので構成されます。 
 
- ギャラリー - 複数の共有イメージ用のコンテナー。 ギャラリーは、1 つのリージョンにデプロイされます。
- イメージ定義 - イメージの概念的なグループ化。 
- イメージ バージョン - VM またはスケール セットのデプロイに使われるイメージの種類。 イメージ バージョンは、VM をデプロイする必要がある他のリージョンにレプリケートできます。
 
イメージ ギャラリーに配布するには、その前にギャラリーとイメージの定義を作成しておく必要があります。[共有イメージ](../shared-images-cli.md)に関する記事をご覧ください。 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

共有イメージ ギャラリーの配布プロパティ:

- **type** - sharedImage  
- **galleryImageId** – 共有イメージ ギャラリーの ID です。これは、次の 2 つの形式で指定できます。
    * 自動バージョン管理 - Image Builder によって、モノトニックなバージョン番号が自動的に生成されます。これは、同じテンプレートからイメージを再構築し続ける場合に便利です。形式は `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` です。
    * 明示的なバージョン管理 - Image Builder で使用するバージョン番号を渡すことができます。 形式は `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>` です。

- **runOutputName** – 配布を示す一意の名前。  
- **artifactTags** -省略可能なユーザー指定のキー値ペアのタグ。
- **replicationRegions** -レプリケーション用のリージョンの配列。 リージョンの 1 つは、ギャラリーがデプロイされているリージョンでなければなりません。 リージョンを追加すると、ビルド時間が長くなります。これは、レプリケーションが完了するまでビルドが完了しないためです。
- **excludeFromLatest** (省略可能) これにより、作成したイメージ バージョンを SIG 定義で最新バージョンとして使用しないように設定できます。既定値は "false" です。
- **storageAccountType** (省略可能) AIB では、作成されるイメージ バージョンに対して、次の種類のストレージを指定することがサポートされています。
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> イメージ テンプレートと参照されている `image definition` が同じ場所にない場合は、イメージを作成するための追加の時間が表示されます。 現在、イメージ バージョン リソースの `location` パラメーターは Image Builder にはなく、その親の `image definition` から取得されます。 たとえば、イメージ定義が westus にあり、イメージ バージョンを eastus にレプリケートする場合、BLOB が westus にコピーされます。この BLOB から westus のイメージ バージョン リソースが作成され、eastus にレプリケートされます。 追加のレプリケーション時間を回避するには、`image definition` とイメージ テンプレートが同じ場所にあるようにします。


### <a name="distribute-vhd"></a>配布: VHD  
VHD に出力することができます。 その後、VHD をコピーし、それを使って Azure MarketPlace に発行したり、Azure Stack で使ったりできます。  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS のサポート: Windows、Linux

VHD 配布のパラメーター:

- **type** - VHD。
- **runOutputName** – 配布を示す一意の名前。  
- **tags** -省略可能なユーザー指定のキー値ペアのタグ。
 
Azure Image Builder ではユーザーはストレージ アカウントの場所を指定できませんが、`runOutputs` の状態のクエリを実行して場所を取得できます。  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD が作成されたら、できるだけ早く別の場所にそれをコピーします。 VHD は、イメージ テンプレートが Azure Image Builder サービスに送信されるときに作成される一時的なリソース グループのストレージ アカウントに格納されます。 イメージ テンプレートを削除すると、VHD が失われます。 

## <a name="image-template-operations"></a>イメージ テンプレートの操作

### <a name="starting-an-image-build"></a>イメージ ビルドの開始
ビルドを開始するには、イメージ テンプレート リソースに対して "Run" を呼び出す必要があります。`run` コマンドの例は次のとおりです。

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>イメージ ビルドの取り消し
イメージのビルドを実行しているときに、正しくないと思われる場合、ユーザーの入力を待っている場合、または正常に完了しないと考えられる場合は、ビルドを取り消すことができます。

ビルドはいつでも取り消すことができます。 配布フェーズが始まっている場合でも取り消しはできますが、完了していない可能性があるイメージをクリーンアップする必要があります。 cancel コマンドで取り消しの完了は待機されません。取り消しの進行状況については、状態[コマンド](image-builder-troubleshoot.md#customization-log)を使用して `lastrunstatus.runstate` を監視してください。


`cancel` コマンドの例を次に示します。

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>次のステップ

さまざまなシナリオの .json ファイルのサンプルが、[Azure Image Builder の GitHub](https://github.com/azure/azvmimagebuilder) にあります。