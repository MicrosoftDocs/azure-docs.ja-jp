---
title: Terratest を使用して Azure で Terraform モジュールをテストする
description: Terratest を使用して Terraform モジュールをテストする方法を学習します。
services: terraform
ms.service: azure
keywords: Terraform, DevOps, ストレージ アカウント, Azure, Terratest, 単体テスト, 統合テスト
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 637bb01bff625989e392d5d711ebd5cdef5c0e09
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169636"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Terratest を使用して Azure で Terraform モジュールをテストする

> [!NOTE]
> この記事のサンプル コードは、バージョン 0.12 (以降) では動作しません。

Azure Terraform モジュールを使用して、再利用可能、構成可能、かつテスト可能なコンポーネントを作成できます。 Terraform モジュールには、コード プロセスとしてインフラストラクチャを実装するのに役立つカプセル化が組み込まれています。

Terraform モジュールを作成するときは、品質保証を実装することが重要です。 残念ながら、Terraform モジュールで単体テストと統合テストを作成する方法が説明されているドキュメントは限られています。 このチュートリアルでは、Microsoft が [Azure Terraform モジュール](https://registry.terraform.io/browse?provider=azurerm)を構築するときに採用したテスト インフラストラクチャとベスト プラクティスを紹介します。

最もよく利用されているすべてのテスト インフラストラクチャを調べて、Terraform モジュールのテストには [Terratest](https://github.com/gruntwork-io/terratest) を使用することにしました。 Terratest は Go ライブラリとして実装されています。 Terratest では、特定の仮想マシンにアクセスするために HTTP 要求を行ったり SSH を使用するなど、一般的なインフラストラクチャ テスト タスク用のヘルパー関数とパターンのコレクションが提供されています。 次の一覧では、Terratest を使用する主な利点の一部について説明します。

- **インフラストラクチャを確認するのに便利なヘルパーが提供されます**。 この機能は、実際の環境で実際のインフラストラクチャを確認したい場合に便利です。
- **フォルダー構造が明確に構成されています**。 テスト ケースは明確に編成され、[Terraform モジュールの標準のフォルダー構造](https://www.terraform.io/docs/modules/create.html#standard-module-structure)に従います。
- **すべてのテスト ケースが Go で記述されます**。 Terraform を使用するほとんどの開発者は、Go 開発者です。 Go 開発者の場合、Terratest を使用するために別のプログラミング言語を習得する必要はありません。 また、Terratest でテスト ケースを実行するために必要な依存関係は、Go と Terraform のみです。
- **インフラストラクチャが高い拡張性を備えています**。 Azure 固有の機能などの追加機能で Terratest を拡張できます。

## <a name="prerequisites"></a>前提条件

この実践的な記事は、プラットフォームに依存しません。 この記事で使用するコード例は、Windows、Linux、または MacOS で実行できます。 

始める前に、次のソフトウェアをインストールします。

- **Go プログラミング言語**: Terraform テスト ケースは [Go](https://golang.org/dl/) で記述します。
- **dep**: [dep](https://github.com/golang/dep#installation) は、Go 向けの依存関係管理ツールです。
- **Azure CLI**:[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) は、Azure リソースを管理するために使用できるコマンドライン ツールです。 (Terraform では、サービス プリンシパル経由または [Azure CLI を介した](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html) Azure への認証がサポートされます。)
- **mage**: Terratest ケースの実行を簡単にする方法を示すため、[mage 実行可能ファイル](https://github.com/magefile/mage/releases)を使用します。 

## <a name="create-a-static-webpage-module"></a>静的 Web ページ モジュールを作成する

このチュートリアルでは、単一の HTML ファイルを Azure Storage Blob にアップロードすることで静的 Web ページをプロビジョニングする Terraform モジュールを作成します。 このモジュールでは、モジュールが返す URL により世界中のユーザーが Web ページにアクセスできます。

> [!NOTE]
> このセクションで説明されているすべてのファイルを、[GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) の場所の下に作成します。

まず、GoPath の `src` フォルダーの下に `staticwebpage` という名前の新しいフォルダーを作成します。 このチュートリアルの全体的なフォルダー構造を次の例に示します。 アスタリスク `(*)` が付いたファイルは、このセクションの主要なファイルです。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

静的 Web ページ モジュールは、3 つの入力を受け取ります。 入力は `./variables.tf` で宣言されています。

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

前に説明したように、このモジュールでは `./outputs.tf` で宣言されている URL も出力されます。

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

モジュールのメイン ロジックでは、4 つのリソースがプロビジョニングされます。
- **リソース グループ**: リソース グループの名前は、`website_name` の入力に `-staging-rg` が追加されたものです。
- **ストレージ アカウント**: ストレージ アカウントの名前は、`website_name` の入力に `data001` が追加されたものです。 ストレージ アカウントの名前の制限に従うため、モジュールではすべての特殊文字が削除され、ストレージ アカウント名全体で小文字が使用されます。
- **固定名コンテナー**: コンテナーは `wwwroot` という名前で、ストレージ アカウントに作成されます。
- **単一の HTML ファイル**: HTML ファイルは `html_path` 入力から読み取られて、`wwwroot/index.html` にアップロードされます。

静的 Web ページ モジュールのロジックは `./main.tf` で実装されています。

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>単体テスト

Terratest は統合テスト用に設計されています。 そのため、Terratest では実際の環境に実際のリソースがプロビジョニングされます。 統合テストのジョブは、非常に大きくなることがあります (特に、プロビジョニングするリソースの量が多い場合)。 前のセクションで参照しているストレージ アカウント名を変換するロジックがよい例です。 

しかし、すべてのリソースを実際にプロビジョニングする必要はありません。 名前付けの変換ロジックが正しいことを確認するだけでいいのです。 Terratest は柔軟なので、単体テストを使用できます。 単体テストはローカルに実行するテスト ケースです (ただし、インターネットへのアクセスが必要です)。 単体テスト ケースでは、`terraform init` コマンドと `terraform plan` コマンドが実行されて `terraform plan` の出力が解析され、比較する属性値が検索されます。

このセクションの残りの部分では、ストレージ アカウント名の変換に使用されるロジックが正しいことを確認するために、Terratest を使用して単体テストを実装する方法について説明します。 注目するのは、アスタリスク `(*)` の付いたファイルのみです。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

最初に、`./test/fixtures/storage-account-name/empty.html` という名前の空の HTML ファイルをプレースホルダーとして使用します。

ファイル `./test/fixtures/storage-account-name/main.tf` は、テスト ケースのフレームです。 それは 1 つの入力 `website_name` を受け取ります。これは単体テストの入力でもあります。 ロジックは次のとおりです。

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

主要なコンポーネントは、`./test/storage_account_name_unit_test.go` 内の単体テストの実装です。

Go 開発者であれば、おそらく、単体テストは `*testing.T` 型の引数を受け取ることで従来の Go テスト関数のシグネチャと一致することに気付くでしょう。

単体テストの本体では、全部で 5 つのケースが変数 `testCases` に定義されています (`key` は入力、`value` は期待される出力)。 それぞれの単体テスト ケースについて、最初に `terraform init` を実行し、テスト フィクスチャ フォルダー (`./test/fixtures/storage-account-name/`) をターゲットにします。 

次に、特定のテスト ケース入力 (`tfOptions` の `website_name` の定義に注目してください) を使用する `terraform plan` コマンドで、結果を `./test/fixtures/storage-account-name/terraform.tfplan` (全体のフォルダー構造には示されていません) に保存します。

この結果ファイルは、公式の Terraform プラン パーサーを使用して、コードが判読できる構造に解析されます。

ここで、対象となる属性 (この場合は `azurerm_storage_account` の `name`) を探し、期待される出力と比較します。

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

単体テストを実行するには、コマンド ラインで次の手順を完了します。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

従来の Go テストの結果は、1 分程度で返ります。

### <a name="integration-test"></a>統合テスト

単体テストとは対照的に、統合テストでは、エンドツーエンドの観点から実際の環境にリソースをプロビジョニングする必要があります。 Terratest では、この種のタスクが適切に行われます。 

Terraform モジュールのベスト プラクティスには、`examples` フォルダーのインストールが含まれます。 `examples` フォルダーには、いくつかのエンド ツー エンド サンプルが含まれます。 実際のデータの使用を避けるためにそれらのサンプルを統合テストとしてテストしないのはなぜでしょうか。 このセクションでは、次のフォルダー構造でアスタリスク `(*)` のマークが付いている 3 つのファイルに注目します。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

まずサンプルから見ていきましょう。 `hello-world/` という名前の新しいサンプル フォルダーが `./examples/` フォルダーに作成されています。 ここで、アップロードする単純な HTML ページ `./examples/hello-world/index.html` を提供します。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Terraform のサンプル `./examples/hello-world/main.tf` は、単体テストで示したものと似ています。 重要な違いが 1 つあります。サンプルでは、`homepage` という名前の Web ページとしてアップロードされた HTML の URL も出力されます。

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

統合テスト ファイル `./test/hello_world_example_test.go` では、Terratest と従来の Go テスト関数を再び使用しています。

単体テストとは異なり、統合テストでは Azure に実際のリソースを作成します。 そのため、名前付けが競合しないように注意する必要があります。 (ストレージ アカウント名のようないくつかのグローバルに一意な名前には特に注意してください。)したがって、テスト ロジックの最初のステップは、TerraTest によって提供される `UniqueId()` 関数を使用してランダムな `websiteName` を生成することです。 この関数では、小文字、大文字、数字が含まれるランダムな名前が生成されます。 `tfOptions` では、`./examples/hello-world/` フォルダーを対象とするすべての Terraform コマンドが作成されます。 また、`website_name` がランダム化された `websiteName` に設定されます。

次に、`terraform init`、`terraform apply`、および `terraform output` が 1 つずつ実行されます。 Terratest によって提供されているもう 1 つのヘルパー関数 `HttpGetWithCustomValidation()` を使用します。 ヘルパー関数を使用して、`terraform output` によって返される出力 `homepage` の URL に HTML がアップロードされることを確認します。 HTTP GET の状態コードを `200` と比較し、HTML コンテンツでいくつかのキーワードを探します。 最後に、Go の `defer` 機能を利用して、`terraform destroy` が実行されることが "約束" されています。

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

統合テストを実行するには、コマンド ラインで次の手順を完了します。

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

従来の Go テストの結果は、2 分程度で返ります。 以下のコマンドを実行して、単体テストと統合テストの両方を実行することもできます。

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

統合テストは単体テストよりはるかに時間がかかります (1 つの統合ケースに 2 分に対し、5 つの単体ケースに 1 分)。 ただし、シナリオで単体テストまたは統合テストを使用するかどうかは、開発者が決定します。 Microsoft では通常、複雑なロジックには Terraform HCL 関数を使用する単体テストを使用するのが好まれます。 ユーザーのエンド ツー エンドの観点には、通常、統合テストを使用します。

## <a name="use-mage-to-simplify-running-terratest-cases"></a>mage を使用して Terratest ケースの実行を簡略化する 

Azure Cloud Shell でテスト ケースを実行するのは、簡単な作業はありません。 異なるディレクトリに移動し、異なるコマンドを実行する必要があります。 Cloud Shell を使用しなくて済むように、プロジェクトにビルド システムが導入されています。 このセクションでは、Go のビルド システムである mage をジョブに対して使用します。

mage で必要になるのは、プロジェクトのルート ディレクトリ内の `magefile.go` のみです (下の例では `(+)` とマークされています)。

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

以下に `./magefile.go` の例を示します。 Go で記述されたこのビルド スクリプトには、5 つのビルド ステップが実装されています。
- `Clean`:このステップでは、テストの実行中に生成されるすべての一時ファイルを削除します。
- `Format`:このステップでは、`terraform fmt` と `go fmt` を実行してコード ベースを書式設定します。
- `Unit`:このステップでは、`./test/` フォルダーにあるすべての単体テストを (関数の名前規則 `TestUT_*` を使用して) 実行します。
- `Integration`:このステップは `Unit` と似ていますが、単体テストではなく統合テスト (`TestIT_*`) を実行します。
- `Full`:このステップでは、`Clean`、`Format`、`Unit`、`Integration` を順番に実行します。

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

次のコマンドを使用して、完全なテスト スイートを実行できます。 そのコードは、前のセクションで使用した実行手順に似ています。 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

最後のコマンド ラインは、追加の mage ステップで置き換えることができます。 たとえば、`mage unit` や `mage clean` を使用できます。 `dep` コマンドと`az login` を magefile に埋め込むのはよい考えです。 ここではコードは示しません。 

mage では、Go パッケージ システムを使用して、ステップを共有することもできます。 その場合、共通の実装を参照して依存関係 (`mg.Deps()`) を宣言するだけで、すべてのモジュールで magefile を簡略化できます。

**省略可能: 受け入れテストを実行するようにサービス プリンシパル環境変数を設定する**
 
テストの前に `az login` を実行する代わりに、サービス プリンシパル環境変数を設定して Azure 認証を完了できます。 Terraform から、[環境変数名の一覧](https://www.terraform.io/docs/providers/azurerm/index.html#testing)が公開されています。 (必要になるのは、これらの環境変数のうち最初の 4 つのみです)。[これらの環境変数の値を取得する](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)方法が説明された詳しい手順も Terraform から公開されています。

## <a name="next-steps"></a>次の手順

* Terratest について詳しくは、[Terratest の GitHub ページ](https://github.com/gruntwork-io/terratest)をご覧ください。
* mage について詳しくは、[mage の GitHub ページ](https://github.com/magefile/mage)と [mage の Web サイト](https://magefile.org/)をご覧ください。
