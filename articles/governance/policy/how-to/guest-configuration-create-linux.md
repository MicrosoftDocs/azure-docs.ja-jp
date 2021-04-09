---
title: Linux 用のゲスト構成ポリシーを作成する方法
description: Linux VM に対する Azure Policy のゲスト構成ポリシーを作成する方法について説明します。
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 352c8b1936c38c9b5f706ac88bd4fd06e008b892
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525349"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Linux 用のゲスト構成ポリシーを作成する方法

カスタム ポリシーを作成する前に、[Azure Policy のゲスト構成](../concepts/guest-configuration.md)に関する概要情報を参照してください。
 
Windows のゲスト構成ポリシーを作成する方法の詳細については、[Windows 用のゲスト構成ポリシーを作成する方法](./guest-configuration-create.md)に関するページを参照してください。

Linux を監査する場合、ゲスト構成では [Chef InSpec](https://www.inspec.io/) を使用します。 InSpec プロファイルでは、マシンが満たす必要のある条件を定義します。 構成の評価が失敗した場合、ポリシー効果の **auditIfNotExists** がトリガーされて、マシンは **非準拠** と見なされます。

[Azure Policy のゲスト構成](../concepts/guest-configuration.md)は、マシン内の設定を監査するためにのみ使用できます。 マシン内の設定の修復はまだ利用できません。

Azure または非 Azure マシンの状態を検証するための独自の構成を作成するには、次のアクションを使用します。

> [!IMPORTANT]
> Azure Government 環境と Azure China 環境でのゲスト構成を使用したカスタム ポリシー定義は、プレビュー機能です。
>
> Azure の仮想マシンで監査を実行するには、ゲスト構成拡張機能が必要です。 すべての Linux マシンに拡張機能を大規模にデプロイするには、ポリシー定義 `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs` を割り当てます。
> 
> カスタム コンテンツ パッケージでは、シークレットや機密情報を使用しないでください。

## <a name="install-the-powershell-module"></a>PowerShell モジュールをインストールする

ゲスト構成モジュールは、次のようなカスタム コンテンツの作成プロセスを自動化します。

- ゲスト構成コンテンツ アーティファクト (.zip) の作成
- アーティファクトの自動テスト
- ポリシー定義の作成
- ポリシーの発行

モジュールは、ローカルで実行している PowerShell 6.2 以降、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Core Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell-core)を使用して、Windows、macOS、または Linux を実行しているマシンにインストールできます。

> [!NOTE]
> Linux では、構成のコンパイルはサポートされていません。

### <a name="base-requirements"></a>基本要件

モジュールをインストールできるオペレーティング システム:

- Linux
- macOS
- Windows

> [!NOTE]
> OMI に対する依存関係があるため、コマンドレット `Test-GuestConfigurationPackage` には OpenSSL バージョン 1.0 が必要です。 これにより、OpenSSL 1.1 以降を使用するすべての環境でエラーが発生します。
>
> コマンドレット `Test-GuestConfigurationPackage` の実行は、ゲスト構成モジュール バージョン2.1.0 についてのみ Windows でサポートされています。

ゲスト構成のリソース モジュールには、次のソフトウェアが必要です。

- PowerShell 6.2 以降。 インストールされていない場合は、こちらの[手順](/powershell/scripting/install/installing-powershell)に従ってください。
- Azure PowerShell 1.5.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。
  - Az モジュール "Az.Accounts" および "Az.Resources" のみが必要です。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell で **GuestConfiguration** モジュールをインストールするには:

1. PowerShell プロンプトから、次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. モジュールがインポートされていることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux 用のゲスト構成アーティファクトとポリシー

Linux 環境でも、ゲスト構成では、言語の抽象化として Desired State Configuration が使用されます。 実装はネイティブ コード (C++) に基づいているため、PowerShell の読み込みは必要ありません。 ただし、環境の詳細を記述する構成 MOF が必要です。
DSC は InSpec のラッパーとして機能し、実行方法、パラメーターの提供方法、およびサービスに出力が返される方法を標準化します。 カスタム InSpec コンテンツを使用する場合は、DSC に関する知識はほとんど必要ありません。

#### <a name="configuration-requirements"></a>構成要件

カスタム構成の名前は、すべての場所で一貫している必要があります。 コンテンツ パッケージの .zip ファイルの名前、MOF ファイル内の構成名、および Azure Resource Manager テンプレート (ARM テンプレート) 内のゲスト割り当て名は同じである必要があります。

PowerShell コマンドレットは、パッケージの作成に役立ちます。
ルート レベル フォルダーまたはバージョン フォルダーは必要ありません。
パッケージ形式は .zip ファイルである必要があります。 圧縮されていない場合、合計サイズは 100 MB を超えることはできません。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux でのカスタム ゲスト構成の構成

Linux でのゲスト構成では、`ChefInSpecResource` リソースを使って、エンジンに [InSpec プロファイル](https://www.inspec.io/docs/reference/profiles/)の名前を設定します。 **Name** は、唯一必要なリソース プロパティです。 次に説明するように、YaML ファイルと Ruby スクリプト ファイルを作成します。

最初に、InSpec によって使用される YaML ファイルを作成します。 ファイルは、環境に関する基本的な情報を提供します。 次に例を示します。

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

このファイルを `inspec.yml` という名前で、プロジェクト ディレクトリ内の `linux-path` という名前のフォルダーに保存します。

次に、マシンの監査に使用される InSpec 言語抽象化で Ruby ファイルを作成します。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

このファイルを `linux-path.rb` という名前で、`linux-path` ディレクトリ内の `controls` という名前の新しいフォルダーに保存します。

最後に、構成を作成し、**PSDesiredStateConfiguration** リソース モジュールをインポートした後、構成をコンパイルします。

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

このファイルを `config.ps1` という名前でプロジェクト フォルダーに保存します。 ターミナルで `./config.ps1` を実行して、これを PowerShell で実行します。 新しい mof ファイルが作成されます。

`Node AuditFilePathExists` コマンドは技術的には必須ではありませんが、既定の `localhost.mof` ではなく `AuditFilePathExists.mof` という名前のファイルを生成します。 .mof ファイル名が構成に従うことにより、大規模な運用時に多くのファイルを簡単に整理できます。

これで、プロジェクトの構造は次のようになります。

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

サポート ファイルはまとめてパッケージ化する必要があります。 完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。

`New-GuestConfigurationPackage` コマンドレットでパッケージを作成します。 Linux コンテンツを作成するときの `New-GuestConfigurationPackage` コマンドレットのパラメーター:

- **Name**:ゲスト構成のパッケージ名。
- **構成**:コンパイル済み構成ドキュメントの完全なパス。
- **パス**:出力フォルダーのパス。 このパラメーターは省略可能です。 指定しないと、パッケージは現在のディレクトリに作成されます。
- **ChefInspecProfilePath**: InSpec プロファイルへの完全なパス。 このパラメーターは、Linux を監査するコンテンツを作成する場合にのみサポートされます。

次のコマンドを実行して、前の手順で指定した構成を使用してパッケージを作成します。

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

構成パッケージを作成したら、Azure に発行する前に、ワークステーションまたは継続的インテグレーションおよび継続的デプロイ (CI/CD) 環境からパッケージをテストできます。 GuestConfiguration `Test-GuestConfigurationPackage` コマンドレットには、Azure マシンで使われるのと同じエージェントが開発環境に含まれます。 このソリューションを使って、有料のクラウド環境にリリースする前に、ローカル環境で統合テストを実行できます。

エージェントは実際にローカル環境を評価しているため、ほとんどの場合、監査を計画しているのと同じ OS プラットフォームで Test- コマンドレットを実行する必要があります。

`Test-GuestConfigurationPackage` コマンドレットのパラメーター:

- **Name**:ゲスト構成ポリシーの名前。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **パス**:ゲスト構成パッケージの完全なパス。

次のコマンドを実行して、前の手順で作成したパッケージをテストします。

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

コマンドレットでは、PowerShell パイプラインからの入力もサポートされています。 `New-GuestConfigurationPackage` コマンドレットの出力を `Test-GuestConfigurationPackage` コマンドレットにパイプします。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

次の手順はファイルの Azure Blob Storage への発行です。 コマンド `Publish-GuestConfigurationPackage` には `Az.Storage` モジュールが必要です。

`Publish-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**:発行するパッケージの場所
- **ResourceGroupName**:ストレージ アカウントが配置されているリソース グループの名前
- **StorageAccountName**:パッケージを発行する必要があるストレージ アカウントの名前
- **StorageContainerName**: (既定: *guestconfiguration*) ストレージ アカウントのストレージ コンテナーの名前
- **Force**:同じ名前のストレージ アカウント内の既存のパッケージを上書きする

次の例では、パッケージをストレージ コンテナー名 'guestconfiguration' に発行します。

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

ゲスト構成のカスタム ポリシー パッケージを作成してアップロードした後、ゲスト構成ポリシー定義を作成します。 `New-GuestConfigurationPolicy` コマンドレットは、カスタム ポリシー パッケージを受け取り、ポリシー定義を作成します。

`New-GuestConfigurationPolicy` コマンドレットのパラメーター:

- **ContentUri**: ゲスト構成コンテンツ パッケージのパブリック HTTP(S) URI。
- **DisplayName**: ポリシーの表示名。
- **説明**:ポリシーの説明。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **バージョン**:ポリシーのバージョン。
- **パス**:ポリシー定義が作成されるターゲット パス。
- **Platform**: ゲスト構成ポリシーとコンテンツ パッケージのターゲット プラットフォーム (Windows/Linux)。
- **Tag** は、ポリシー定義に 1 つ以上のタグ フィルターを追加します
- **カテゴリ** は、ポリシー定義のカテゴリ メタデータ フィールドを設定します

次の例では、カスタム ポリシー パッケージから指定されたパスにポリシー定義を作成します。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy` により、次のファイルが作成されます。

- **auditIfNotExists.json**

コマンドレットの出力では、イニシアティブの表示名とポリシー ファイルのパスが含まれるオブジェクトが返されます。

最後に、`Publish-GuestConfigurationPolicy` コマンドレットを使用してポリシー定義を発行します。 コマンドレットのパラメーターは、`New-GuestConfigurationPolicy` によって作成される JSON ファイルの場所を指し示す **Path** だけです。

Publish コマンドを実行するには、Azure でポリシーを作成するためのアクセス権が必要です。 特定の承認要件については、[Azure Policy の概要](../overview.md)に関するページに記載されています。 最適な組み込みロールは、**リソース ポリシーの共同作成者** です。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 `Publish-GuestConfigurationPolicy` コマンドレットは、PowerShell パイプラインからパスを受け取ります。 この機能では、パイプされたコマンドの 1 つのセットで、ポリシー ファイルを作成して発行できます。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Azure で作成されるポリシーに関する最後のステップでは、定義を割り当てます。 [ポータル](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md)、および [Azure PowerShell](../assign-policy-powershell.md) で定義を割り当てる方法を確認してください。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>カスタム ゲスト構成ポリシーでのパラメーターの使用

ゲスト構成では、実行時に構成のプロパティをオーバーライドすることができます。 この機能は、パッケージの MOF ファイル内の値を静的と見なす必要がないことを意味します。 オーバーライドする値は Azure Policy を通じて提供され、構成の作成方法またはコンパイル方法には影響しません。

InSpec では、通常、パラメーターは、実行時に入力として、または属性を使用したコードとして処理されます。 ゲスト構成はこのプロセスを難読化するため、ポリシーが割り当てられたときに入力を提供できます。 属性ファイルは、マシン内に自動的に作成されます。 プロジェクトにファイルを作成して追加する必要はありません。 Linux 監査プロジェクトにパラメーターを追加するには、2 つの手順を実行します。

マシン上の監査対象をスクリプト化する Ruby ファイルの入力を定義します。 次に例を示します。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

プロパティ **AttributesYmlContent** を構成に追加します。値として任意の文字列を含めます。
ゲスト構成エージェントでは、属性を格納するために InSpec によって使われる AML ファイルが自動的に作成されます。 次の例を見てください。

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

このドキュメントに含まれる例を使用し、MOF ファイルを再コンパイルします。

`New-GuestConfigurationPolicy` と `Test-GuestConfigurationPolicyPackage` のコマンドレットには、**Parameters** という名前のパラメーターが含まれています。 このパラメーターは、各パラメーターの詳細をすべて含むハッシュテーブルを受け取り、各 Azure Policy 定義の作成に使用されるファイルのすべての必要なセクションが自動的に作成されます。

次の例では、ファイル パスを監査するポリシー定義を作成します。このパスは、ポリシーの割り当て時にユーザーが指定します。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
```


## <a name="policy-lifecycle"></a>ポリシーのライフサイクル

ポリシーの更新をリリースする場合は、ゲスト構成パッケージと Azure Policy 定義の詳細の両方に対して変更を行います。

> [!NOTE]
> ゲスト構成割り当ての `version` プロパティは、Microsoft によってホストされているパッケージにのみ影響します。 カスタム コンテンツのバージョン管理のベスト プラクティスは、ファイル名にバージョンを含めることです。

まず、`New-GuestConfigurationPackage` を実行するときに、以前のバージョンと異なる一意のパッケージの名前を指定します。 名前には、`PackageName_1.0.0` などのバージョン番号を含めることができます。
この例の番号は、パッケージを一意にするためにのみ使用されており、パッケージを他のパッケージよりも新しいまたは古いものとして見なすように指定するものではありません。

次に、以下の各説明に従って、`New-GuestConfigurationPolicy` コマンドレットで使用するパラメーターを更新します。

- **バージョン**:`New-GuestConfigurationPolicy` コマンドレットを実行するときは、現在発行されているバージョンより大きいバージョン番号を指定する必要があります。
- **contentUri**: `New-GuestConfigurationPolicy` コマンドレットを実行するときは、パッケージの場所の URI を指定する必要があります。 ファイル名にパッケージのバージョンを含めると、各リリースでこのプロパティの値が変更されます。
- **contentHash**: このプロパティは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に更新されます。 `New-GuestConfigurationPackage` によって作成されるパッケージのハッシュ値です。 このプロパティは、発行する `.zip` ファイルに対して適切なものである必要があります。 **contentUri** プロパティのみが更新された場合、拡張機能ではコンテンツ パッケージが受け入れられません。

更新されたパッケージをリリースする最も簡単な方法は、この記事で説明されているプロセスを繰り返し、更新されたバージョン番号を指定することです。 このプロセスにより、すべてのプロパティが正しく更新されることが保証されます。

### <a name="filtering-guest-configuration-policies-using-tags"></a>タグを使用したゲスト構成ポリシーのフィルター処理

ゲスト構成モジュールのコマンドレットによって作成されたポリシーには、必要に応じてタグのフィルターを含めることができます。 `New-GuestConfigurationPolicy` の **-Tag** パラメーターは、個々のタグ エントリを含むハッシュテーブルの配列をサポートしています。 タグは、ポリシー定義の `If` セクションに追加され、ポリシーの割り当てで変更することはできません。

タグをフィルター処理するポリシー定義のスニペットの例を次に示します。

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>省略可能:ゲスト構成パッケージに署名する

ゲスト構成カスタム ポリシーでは、SHA256 ハッシュを使用して、ポリシー パッケージが変更されていないことが検証されます。
必要に応じて、お客様は証明書を使ってパッケージに署名し、署名されたコンテンツのみを許可するようにゲスト構成拡張機能を強制することもできます。

このシナリオを有効にするには、2 つのステップを実行する必要があります。 コマンドレットを実行してコンテンツ パッケージに署名し、コードが署名されていることを要求する必要があるようにマシンにタグを追加します。

署名検証機能を使用するには、`Protect-GuestConfigurationPackage` コマンドレットを実行して、発行前にパッケージに署名します。 このコマンドレットには "コード署名" 証明書が必要です。

`Protect-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**:ゲスト構成パッケージの完全なパス。
- **PublicGpgKeyPath**: パブリック GPG キーのパス。 このパラメーターは、Linux 用のコンテンツに署名する場合にのみサポートされます。

Linux マシンで使用する GPG キーの作成については、GitHub の[新しい GPG キーの生成](https://help.github.com/en/articles/generating-a-new-gpg-key)に関する記事に優れたリファレンスが提供されています。

GuestConfiguration エージェントにより、Linux マシンの場合はパス `/usr/local/share/ca-certificates/extra` に、証明書の公開キーが存在していることが求められます。 署名されたコンテンツをノードで検証するには、カスタム ポリシーを適用する前に、マシンに証明書の公開キーをインストールします。 このプロセスは、VM 内で任意の方法を使うか、Azure Policy を使って、行うことができます。 テンプレートの例は、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)で提供されています。
Key Vault のアクセス ポリシーでは、デプロイ中にコンピューティング リソース プロバイダーが証明書にアクセスできるようにする必要があります。 詳しい手順については、[Azure Resource Manager の仮想マシンの Key Vault を設定する](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)に関する記事をご覧ください。

コンテンツを発行した後、コード署名が必要なすべての仮想マシンに、名前が `GuestConfigPolicyCertificateValidation` で値が `enabled` のタグを追加します。 Azure Policy を使用して大規模にタグを配信する方法については、[タグのサンプル](../samples/built-in-policies.md#tags)に関する記事を参照してください。 このタグを配置すると、`New-GuestConfigurationPolicy` コマンドレットを使って生成されるポリシー定義では、ゲスト構成拡張による要件が有効になります。

## <a name="next-steps"></a>次のステップ

- [ゲスト構成](../concepts/guest-configuration.md)による VM の監査について学習します。
- [プログラムによってポリシーを作成する](./programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](./get-compliance-data.md)方法を学習します。
