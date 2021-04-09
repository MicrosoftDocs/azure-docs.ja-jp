---
title: terraform を使用して Azure Spring Cloud インスタンスをプロビジョニングする
description: Terraform を使用して Azure Spring Cloud インスタンスをプロビジョニングします。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877541"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Terraform を使用して Azure Spring Cloud インスタンスをプロビジョニングする

**この記事の適用対象:** ✔️ Java ✔️ C#

この例では、Terraform を使用して Azure Spring Cloud インスタンスを作成します。 一連の手順を通じて次のリソースを作成します。

> [!div class="checklist"]
> * リソース グループ
> * Azure Spring Cloud インスタンス
> * Log Analytics 用の Azure Storage

> [!NOTE]
> Terraform 固有のサポートについては、HashiCorp の Terraform へのコミュニティ サポート チャネルのいずれかをご利用ください。
>
> * 質問、ユースケース、および便利なパターン:[HashiCorp コミュニティ ポータルの Terraform セクション](https://discuss.hashicorp.com/c/terraform-core)
> * プロバイダーに関連する質問:[HashiCorp コミュニティ ポータルの Terraform Providers セクション](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

## <a name="create-configuration-file"></a>構成ファイルの作成

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell) を開きます。

1. Cloud Shell エディターを開始します。

    ```bash
    code main.tf
    ```

1. この手順の構成では、Azure リソース (Azure リソース グループと Azure Spring Cloud インスタンスを含む) をモデル化します。

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="apply-the-configuration"></a>構成を適用する

このセクションでは、いくつかの Terraform コマンドを使用して構成を実行します。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) コマンドを実行すると、作業ディレクトリが初期化されます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) コマンドを使用して、構成構文を検証します。 `-out` パラメーターは、結果をファイルに送信します。 出力ファイルは、後で構成を適用するために使用できます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform plan --out plan.out
    ```

1. 構成を適用するには、[terraform apply](https://www.terraform.io/docs/commands/apply.html) コマンドを使用します。 このコマンドでは、前の手順の出力ファイルを指定します。 このコマンドによって Azure リソースが作成されます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform apply plan.out
    ```

1. Azure portal 内の結果を確認するには、新しいリソース グループを参照します。 新しいリソース グループに新しい **Azure Spring Cloud** インスタンスが表示されます。

## <a name="update-configuration-to-config-logs-and-metrics"></a>構成を更新してログとメトリックを設定する

このセクションでは、構成を更新して、Azure Storage アカウントと共に Azure Spring Cloud のログとメトリックを有効にする方法について説明します。

1. Cloud Shell エディターを開始します。

    ```bash
    code main.tf
    ```

1. ファイルの最後に次の構成を追加します。

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

1. 前のセクションと同様に、次のコマンドを実行して変更を加えます。

    ```bash
    terraform plan --out plan.out
    ```

1. `terraform apply` コマンドを実行して構成を適用します。

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要になったら、削除してください。

この演習で作成した Azure リソースを削除するには、[terraform destroy](https://www.terraform.io/docs/commands/destroy.html) コマンドを実行します。

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure リソースを作成するために Terraform をインストールして構成する](/azure/developer/terraform/getting-started-cloud-shell)。