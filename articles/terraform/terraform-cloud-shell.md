---
title: Terraform 用に Azure Cloud Shell を構成する
description: Terraform と Azure Cloud Shell を使用すると、認証とテンプレートの構成が簡単になります。
services: terraform
ms.service: azure
keywords: terraform, 開発, スケール セット, 仮想マシン, ネットワーク, ストレージ, モジュール
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e0a59697a3e4da97cf082c4c771fe93ad33b6035
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173544"
---
# <a name="terraform-cloud-shell-development"></a>Terraform Cloud Shell の開発 

Terraform は、macOS ターミナルや、Windows Linux 上の Bash などの Bash コマンド ラインから利用することができます。 [Azure Cloud Shell](/azure/cloud-shell/overview) の Bash 機能で Terraform の構成を実行すると、開発サイクルを高速にすることができる独自の利点がいくつかあります。

概念に関するこの記事では、Azure にデプロイする Terraform スクリプトを作成することができる Cloud Shell 機能について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>資格情報の自動構成

Terraform をインストールすると、Cloud Shell ですぐに使用できます。 Cloud Shell にログインしてインフラストラクチャを管理すると、Terraform スクリプトは Azure で認証されます。追加の構成は必要ありません。 自動認証によって、手動で Active Directory サービス プリンシパルを作成し、Azure Terraform プロバイダー変数を構成する必要はなくなります。


## <a name="using-modules-and-providers"></a>モジュールとプロバイダーの使用

Azure サブスクリプションのリソースにアクセスし、変更を加える場合、Azure Terraform モジュールは資格情報を要求します。 Cloud Shell で作業するときに、Cloud Shell で Azure Terraform モジュールを使用するには、次のコードをスクリプトに追加します。

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell は、`terraform` CLI コマンドのいずれかを使用するときに、環境変数を介して `azurerm` プロバイダーに必要な値を渡します。

## <a name="other-cloud-shell-developer-tools"></a>その他の Cloud Shell 開発者用ツール

ファイルとシェルの状態は、複数の Cloud Shell セッションにまたがって Azure Storage に維持されます。 ローカル コンピューターのファイルをコピーし、Cloud Shell にアップロードするには、[Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) を使用します。

Azure CLI は Cloud Shell で使用可能で、`terraform apply` または `terraform destroy` が完了した後の構成のテストと作業のチェックに適したツールです。


## <a name="next-steps"></a>次の手順

[モジュール レジストリを使用した Terraform での VM クラスターの作成](terraform-create-vm-cluster-module.md)
[カスタム HCL を使用した小規模な VM クラスターの作成](terraform-create-vm-cluster-with-infrastructure.md)
