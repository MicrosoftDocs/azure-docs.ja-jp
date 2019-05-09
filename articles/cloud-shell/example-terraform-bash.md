---
title: Azure Cloud Shell で Bash から Terraform を使ってデプロイする | Microsoft Docs
description: Azure Cloud Shell で Bash から Terraform を使ってデプロイする
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159266"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Azure Cloud Shell で Bash から Terraform を使ってデプロイする
この記事では、[Terraform AzureRM プロバイダー](https://www.terraform.io/docs/providers/azurerm/index.html)を使ってリソース グループを作成する手順について説明します。 

[Hashicorp Terraform](https://www.terraform.io/) は、チーム メンバー間で共有して編集、レビュー、バージョン管理できる宣言型の構成ファイルに API を体系化するオープン ソース ツールです。 Microsoft AzureRM プロバイダーは、Azure Resource Manager によってサポートされるリソースと AzureRM API を介してやり取りするために使われます。 

## <a name="automatic-authentication"></a>自動認証
Terraform は、Cloud Shell の Bash に既定でインストールされます。 さらに、Cloud Shell は既定の Azure CLI サブスクリプションを自動的に認証し、Terraform Azure モジュールを介してリソースをデプロイします。

Terraform は、設定されている既定の Azure CLI サブスクリプションを使います。 既定のサブスクリプションを更新するには、次のコマンドを実行します。

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>チュートリアル
### <a name="launch-bash-in-cloud-shell"></a>Cloud Shell で Bash を起動する
1. 好みの場所から Cloud Shell を起動します
2. 優先するサブスクリプションが設定されていることを確認します

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform テンプレートを作成する
任意のテキスト エディターで、main.tf という名前の新しい Terraform テンプレートを作成します。

```
vim main.tf
```

次のコードをコピーして Cloud Shell に貼り付けます。

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

ファイルを保存してテキスト エディターを終了します。

### <a name="terraform-init"></a>terraform init
最初に `terraform init` を実行します。

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Terraform 構成ファイルを含む作業ディレクトリを初期化するには、[terraform init コマンド](https://www.terraform.io/docs/commands/init.html)を使います。 新しい Terraform 構成を作成した後、またはバージョン管理から既存の Terraform 構成を複製した後は、最初に `terraform init` コマンドを実行する必要があります。 このコマンドは複数回実行しても安全です。

### <a name="terraform-plan"></a>Terraform プラン
Terraform テンプレートによって作成されるリソースを、`terraform plan` でプレビューします。

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

実行プランを作成するには、[terraform plan コマンド](https://www.terraform.io/docs/commands/plan.html)を使います。 Terraform は、明示的に無効にされていない限り更新を実行した後、構成ファイルで指定されている目的の状態を実現するために必要アクションを決定します。 プランは、-out を使って保存でき、その後は、terraform apply に渡して事前に計画されているアクションだけを確実に実行できます。

### <a name="terraform-apply"></a>terraform apply
`terraform apply` で Azure リソースをプロビジョニングします。

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

目的の構成状態にするために必要な変更を適用するには、[terraform apply コマンド](https://www.terraform.io/docs/commands/apply.html)を使います。

### <a name="verify-deployment-with-azure-cli"></a>Azure CLI でデプロイを検証する
`az group show -n myRgName` を実行して、リソースが正しくプロビジョニングされたことを確認します。

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>terraform destroy でクリーンアップする
作成されたリソース グループを [terraform destroy コマンド](https://www.terraform.io/docs/commands/destroy.html)でクリーンアップし、Terraform で作成されたインフラストラクチャをクリーンアップします。

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Terraform で Azure リソースを正常に作成できました。 次のステップに進んで Cloud Shell の学習を続けてください。

## <a name="next-steps"></a>次の手順
[Terraform Azure プロバイダーについて学習する](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Cloud Shell の Bash のクイックスタート](quickstart.md)
