---
title: Azure クイックスタート - Azure Resource Manager テンプレートを使用して Managed HSM を作成する
description: Resource Manager テンプレートを使用して Azure Key Vault Managed HSM を作成する方法について説明するクイックスタート。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d9fb9e0221ad6a5749899c89bbd9dc5631e7a91c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213268"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用して Key Vault Managed HSM を作成する

Managed HSM は、フル マネージド、高可用性、シングル テナント、標準準拠を特徴とするクラウド サービスで、**FIPS 140-2 レベル 3** 適合の HSM を使用してクラウド アプリケーションの暗号化キーを保護することができます。  

このクイックスタートでは、Resource Manager テンプレートをデプロイして Managed HSM を作成する過程を中心に取り上げます。  [Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)は JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、デプロイしようとしているものを、それを作成する一連のプログラミング コマンドを記述しなくても記述できる、宣言型の構文を使用しています。 Resource Manager テンプレートの開発に関する詳細を学ぶには、[Resource Manager ドキュメント](../../azure-resource-manager/index.yml)と[テンプレート リファレンス](/azure/templates/microsoft.keyvault/allversions)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
- Azure CLI バージョン 2.12.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="create-a-manage-hsm"></a>マネージド HSM を作成する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/)からのものです。

このテンプレートで定義されている Azure リソースは次のとおりです。

* **Microsoft.KeyVault/managedHSMs**: Azure Key Vault マネージド HSM を作成します。

その他の Azure Key Vault テンプレートのサンプルは[こちら](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)から入手できます。

このテンプレートには、アカウントに関連付けられたオブジェクト ID が必要です。 これを見つけるには、Azure CLI の [az ad user show](/cli/azure/ad/user#az_ad_user_show) コマンドを使用します。お使いのメール アドレスを `--id` パラメーターに渡してください。 `--query` パラメーターを使用すると、オブジェクト ID のみに出力を制限することができます。

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

さらに、テナント ID も必要になることがあります。 これを見つけるには、Azure CLI の [az ad user show](/cli/azure/account#az_account_show) コマンドを使用します。 `--query` パラメーターを使用すると、テナント ID のみに出力を制限することができます。

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートを使用すると、キー コンテナーとシークレットが作成されます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. 次の値を選択または入力します。

    指定がない場合、既定値を使用してキー コンテナーとシークレットを作成してください。

    - **サブスクリプション**:Azure サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** をクリックします。
    - **[場所]** :場所を選択します。 たとえば、**米国中南部** とします。
    - **managedHSMName**: マネージド HSM の名前を入力します。
    - **テナント ID**:テンプレート関数によって自動的にテナント ID が取得されます。既定値を変更しないでください。  値が存在しない場合は、「[前提条件](#prerequisites)」で取得したテナント ID を入力します。
    * **initialAdminObjectIds**: 「[前提条件](#prerequisites)」で取得したオブジェクト ID を入力します。

3. **[購入]** を選択します。 キー コンテナーが正常にデプロイされると、次の通知が表示されます。

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、マネージド HSM を作成しました。 このマネージド HSM は、アクティブにして初めて完全に機能します。 HSM をアクティブにする方法については、「[マネージド HSM をアクティブにする](quick-create-cli.md#activate-your-managed-hsm)」を参照してください。

- [Managed HSM の概要](overview.md)に関するページを読む
- [マネージド HSM におけるキーの管理](key-management.md)について学習する
- [Managed HSM のベスト プラクティス](best-practices.md)を確認する
