---
title: Bicep の開発環境とデプロイ環境のセットアップ
description: Bicep の開発環境とデプロイ環境の構成方法
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: d665a863affdec2009fc208f76b85a7f25de451d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594395"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Bicep の開発環境とデプロイ環境のセットアップ

Bicep の開発環境とデプロイ環境のセットアップ方法を説明します。

## <a name="development-environment"></a>開発環境

Bicep の作成エクスペリエンスを最大限に活用するには、次の 2 つのコンポーネントが必要になります。

- **Visual Studio Code 用の Bicep 拡張機能**。 Bicep ファイルを作成するには、適切な Bicep エディターが必要です。 [Visual Studio Code](https://code.visualstudio.com/) と [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)の組み合わせをお勧めします。 これらのツールでは、言語サポートとリソース オートコンプリートが提供されます。 これらは、Bicep ファイルの作成と検証を行うときに役立ちます。 Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
- **BICEP CLI**。 Bicep CLI を使用して Bicep ファイルを ARM JSON テンプレートにコンパイルし、ARM JSON テンプレートを Bicep ファイルに逆コンパイルします。 詳細については、「[Bicep CLI のインストール](#install-bicep-cli)」を参照してください。

## <a name="deployment-environment"></a>デプロイ環境

Azure CLI または Azure PowerShell を使用して、Bicep ファイルをデプロイできます。 Azure CLI には、2.20.0 以降のバージョンが必要です。Azure PowerShell には、5.6.0 以降のバージョンが必要です。 インストール手順については、以下を参照してください。

- [Azure PowerShell をインストールするには](/powershell/azure/install-az-ps)
- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> 現時点では、Azure CLI と Azure PowerShell はどちらもローカルの Bicep ファイルのデプロイのみを行えます。 Azure CLI を使用した Bicep ファイルのデプロイの詳細については、「[デプロイ - CLI](/deploy-cli.md#deploy-remote-template)」を参照してください。 Azure PowerShell を使用した Bicep ファイルのデプロイの詳細については、「[デプロイ - PowerShell](/deploy-powershell.md#deploy-remote-template)」を参照してください。

サポートされているバージョンの Azure PowerShell または Azure CLI をインストールしたら、次のものを使用して Bicep ファイルをデプロイできます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Bicep CLI のインストール

Azure CLI または Azure PowerShell を使用するか、あるいは手動で Bicep CLI をインストールできます。

### <a name="use-azure-cli"></a>Azure CLI の使用

Az CLI バージョン 2.20.0 以降がインストールされている場合、Bicep CLI に依存するコマンドが実行されると、Bicep CLI は自動的にインストールされます。 たとえば、`az deployment ... -f *.bicep` または `az bicep ...` です。

組み込みコマンドを使用して、CLI を手動でインストールすることもできます。

```bash
az bicep install
```

最新バージョンにアップグレードするには、以下を実行します。

```bash
az bicep upgrade
```

特定のバージョンをインストールするには、以下を実行します。

```bash
az bicep install --version v0.2.212
```

> [!NOTE]
> Az CLI は、既にインストールされている他の Bicep があれば、それらのインストールと競合しない別のバージョンの Bicep CLI をインストールします。また、PATH には Bicep を追加しません。

インストールされているバージョンを表示するには、以下を実行します。

```bash
az bicep version
```

Bicep CLI の使用可能なすべてのバージョンを一覧表示するには、以下を実行します。

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

Azure PowerShell には、Bicep CLI をインストールする機能がまだありません。 Azure PowerShell (v 5.6.0 以降) では、Bicep CLI が既にインストールされていて、PATH で使用できることが前提となっています。 [手動インストール](#install-manually)のいずれかの手順に従います。 Bicep CLI がインストールされると、デプロイ コマンドレットで必要とされるときに、Bicep CLI が常に呼び出されます。 たとえば、「 `New-AzResourceGroupDeployment ... -TemplateFile main.bicep` 」のように入力します。

### <a name="install-manually"></a>手動でインストールする

次のメソッドでは、Bicep CLI をインストールし、PATH に追加します。

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>homebrew を使用

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>macOS での手動インストール

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows インストーラー

[最新の Windows インストーラー](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)をダウンロードして実行します。 インストーラーには管理者特権は必要ありません。 インストールが完了すると、Bicep CLI がユーザーの PATH に追加されます。 PATH の変更を有効にするために、開いているコマンド シェル ウィンドウを閉じてから再度開きます。

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>PowerShell での手動

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>夜間ビルドのインストール

リリース前に Bicep の最新プレリリース ビットを試す場合は、[夜間ビルドのインストール](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)に関する記事を参照してください。

> [!WARNING]
> これらのプレリリース ビルドには、既知または不明なバグが含まれている可能性が非常に高くなります。

## <a name="next-steps"></a>次のステップ

[Bicep クイックスタート](./quickstart-create-bicep-use-visual-studio-code.md)を開始します。
