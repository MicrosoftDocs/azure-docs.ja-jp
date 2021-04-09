---
title: ARM テンプレート テスト ツールキット
description: テンプレートで Azure Resource Manager テンプレート (ARM テンプレート) テスト ツールキットを実行する方法について説明します。 ツールキットを使用すると、推奨される方法を実装しているかどうかを確認できます。
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e5ad0b6dca7718166517b52148fbc6dd49f38869
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674014"
---
# <a name="use-arm-template-test-toolkit"></a>ARM テンプレート テスト ツールキットを使用する

[Azure Resource Manager テンプレート (ARM テンプレート) テスト ツールキット](https://aka.ms/arm-ttk)では、推奨される方法がテンプレートで使用されているかどうかが確認されます。 テンプレートが推奨されるプラクティスに準拠していない場合は、推奨される変更を含む警告の一覧が返されます。 テスト ツールキットを使用すると、テンプレートの開発での一般的な問題を回避する方法がわかります。

テスト ツールキットでは、[既定のテストのセット](test-cases.md)が提供されます。 これらのテストは推奨されるものですが、必須ではありません。 自分の目標に関連するテストを決定し、実行するテストをカスタマイズできます。

この記事では、テスト ツールキットを実行する方法と、テストを追加または削除する方法について説明します。 既定のテストの説明については、[ツールキットのテスト ケース](test-cases.md)に関する記事を参照してください。

ツールキットは、PowerShell または CLI のコマンドから実行できる一連の PowerShell スクリプトです。

## <a name="install-on-windows"></a>Windows にインストールする

1. PowerShell をまだ持っていない場合は、[Windows に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-windows)します。

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で **arm-ttk** フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 **arm-ttk** フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Linux にインストールする

1. PowerShell をまだ持っていない場合は、[Linux に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-linux)します。

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

   ```bash
   pwsh
   ```

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で **arm-ttk** フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 **arm-ttk** フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>macOS へのインストール

1. PowerShell をまだ持っていない場合は、[macOS に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-macos)します。

1. `coreutils` をインストールします。

   ```bash
   brew install coreutils
   ```

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

   ```bash
   pwsh
   ```

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で **arm-ttk** フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 **arm-ttk** フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>結果の形式

合格になったテストは **緑色** で表示され、前に **[+]** が付きます。

不合格になったテストは **赤色** で表示され、前に **[-]** が付きます。

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="テスト結果を表示する":::

テキストの結果は次のようになります。

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>テスト パラメーター

**-TemplatePath** パラメーターを指定すると、ツールキットでは、そのフォルダーで azuredeploy.json または maintemplate.json という名前のテンプレートが検索されます。 最初にこのテンプレートがテストされた後、そのフォルダーとサブフォルダー内の他のすべてのテンプレートがテストされます。 他のテンプレートは、リンクされたテンプレートとしてテストされます。 パスに [CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md) という名前のファイルが含まれている場合、UI 定義に関連するテストが実行されます。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

そのフォルダー内の 1 つのファイルをテストするには、 **-File** パラメーターを追加します。 ただし、その場合でもフォルダーには、azuredeploy.json または maintemplate.json という名前のメイン テンプレートが存在する必要があります。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

既定では、すべてのテストが実行されます。 個別のテストを指定して実行するには、 **-Test** パラメーターを使用します。 テストの名前を指定します。 名前については、[ツールキットのテスト ケース](test-cases.md)に関する記事を参照してください。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>テストをカスタマイズする

ARM テンプレートの場合、ツールキットでは **\arm-ttk\testcases\deploymentTemplate** フォルダー内のすべてのテストが実行されます。 テストを完全に削除する場合は、そのファイルをフォルダーから削除します。

[CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) ファイルの場合は、 **\arm-ttk\testcases\CreateUiDefinition** フォルダー内のすべてのテストが実行されます。

独自のテストを追加するには、次の名前付け規則に従ってファイルを作成します: **<カスタム テスト名>.test.ps1**。

テストでは、オブジェクト パラメーターまたは文字列パラメーターとしてテンプレートを取得できます。 通常はどちらか一方を使用しますが、両方を使用することもできます。

テンプレートのセクションを取得し、そのプロパティを反復処理する必要がある場合は、オブジェクト パラメーターを使用します。 オブジェクト パラメーターを使用するテストの形式は次のとおりです。

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

テンプレート オブジェクトのプロパティは次のとおりです。

* $schema
* contentVersion
* parameters
* variables
* resources
* outputs

たとえば、`$TemplateObject.parameters` を使用してパラメーターのコレクションを取得できます。

テンプレート全体で文字列操作を行う必要がある場合は、文字列パラメーターを使用します。 文字列パラメーターを使用するテストの形式は次のとおりです。

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

たとえば、文字列パラメーターの正規表現を実行して、特定の構文が使用されているかどうかを確認できます。

テストの実装の詳細については、そのフォルダー内の他のテストを参照してください。

## <a name="integrate-with-azure-pipelines"></a>Azure Pipelines と統合する

テスト ツールキットを Azure パイプラインに追加できます。 パイプラインを使用すると、テンプレートが更新されるたびにテストを実行したり、デプロイ プロセスの一部として実行したりできます。

テスト ツールキットをパイプラインに追加する最も簡単な方法は、サードパーティ製の拡張機能を使用することです。 次の 2 つの拡張機能を使用できます。

* [Run ARM TTK Tests](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM Template Tester](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

または、独自のタスクを実装することもできます。 次の例では、テスト ツールキットをダウンロードする方法を示します。

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

次の例では、テストを実行する方法を示します。

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>次のステップ

- 既定のテストの詳細については、「[ARM テンプレート テスト ツールキットの既定のテスト ケース](test-cases.md)」を参照してください。
- テスト ツールキットの使用に関して説明している Microsoft Learn モジュールについては、「[what-if と ARM テンプレート テスト ツールキットを使用して変更をプレビューし、Azure リソースを検証する](/learn/modules/arm-template-test/)」を参照してください。
