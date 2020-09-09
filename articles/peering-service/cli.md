---
title: Azure CLI を使用して Peering Service (プレビュー) 接続を登録する
description: Azure CLI を使用して Peering Service 接続を登録する方法について説明します
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871462"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Azure CLI を使用して Peering Service 接続を登録する

Azure Peering Service は、Office 365、Dynamics 365、サービスとしてのソフトウェア (SaaS) サービス、Azure、パブリック インターネット経由でアクセス可能な Microsoft サービスなど、Microsoft クラウド サービスへのお客様の接続を強化するネットワーク サービスです。 この記事では、Azure CLI を使用して Peering Service 接続を登録する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、すぐに[アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件 

次のものが必要です。

### <a name="azure-account"></a>Azure アカウント

有効かつアクティブな Microsoft Azure アカウントが必要です。 Peering Service 接続を設定するには、このアカウントが必須です。 Peering Service は、Azure サブスクリプション内のリソースです。

### <a name="connectivity-provider"></a>接続プロバイダー

インターネット サービス プロバイダーまたはインターネット交換パートナーと協力して Peering Service を取得することで、お使いのネットワークを Microsoft ネットワークに接続することができます。

接続プロバイダーが Microsoft と提携していることを確認してください。

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1.Azure アカウントにサインインしてサブスクリプションを選択する

構成を始めるには、Azure アカウントにサインインします。 Cloud Shell の **[使ってみる]** オプションを使用している場合は、自動的にサインインします。 接続するには、次の例を参照してください。

```azurecli-interactive
az login
```

アカウントのサブスクリプションを確認します。

```azurecli-interactive
az account list
```

Peering Service 接続を登録するサブスクリプションを選択します。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

リソース グループがまだない場合は、Peering Service 接続を登録する前に、作成しておく必要があります。 リソース グループを作成するには、次のコマンドを実行します。

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2.サブスクリプションをリソースプロバイダーと機能フラグに登録する

Azure CLI を使用した Peering Service 接続の登録手順に進む前に、Azure CLI を使用して、サブスクリプションをリソース プロバイダーと機能フラグに登録します。 Azure CLI のコマンドは、次のように指定します。

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3.Peering Service 接続を登録する

Azure CLI で次のコマンド セットを使用して、Peering Service 接続を登録します。 この例では、myPeeringService という名前の Peering Service 接続を登録します。

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4.プレフィックスを登録する

Azure CLI を使用して次のコマンドを実行し、接続プロバイダーから提供されるプレフィックスを登録します。 この例では、myPrefix という名前のプレフィックスを登録します。

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
- Azure PowerShell を使用して接続を登録する方法については、[Azure PowerShell を使用した Peering Service 接続の登録](powershell.md)に関するチュートリアルを参照してください。
- Azure portal を使用して接続を登録する方法については、[Azure portal を使用した Peering Service 接続の登録](azure-portal.md)に関するページを参照してください。
