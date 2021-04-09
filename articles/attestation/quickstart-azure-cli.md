---
title: Azure CLI を使用して Azure Attestation を設定する
description: Azure CLI を使用して構成証明プロバイダーを設定、構成する方法。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178713"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure Attestation を設定する

[Azure CLI を使用して Azure Attestation](/cli/azure/ext/attestation/attestation) を導入します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="get-started"></a>はじめに

1. 次の CLI コマンドを使用して、この拡張機能をインストールします。

   ```azurecli
   az extension add --name attestation
   ```
   
1. バージョンをチェックする

   ```azurecli
   az extension show --name attestation --query version
   ```

1. 次のコマンドを使用して、Azure にサインインします。

   ```azurecli
   az login
   ```

1. 必要に応じて、Azure Attestation のサブスクリプションに切り替えます。

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. [az provider register](/cli/azure/provider#az_provider_register) コマンドを使用して、サブスクリプションに Microsoft.Attestation リソース プロバイダーを登録します。

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Azure リソース プロバイダーについて、またリソース プロバイダーを構成、管理する方法について詳しくは、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

   > [!NOTE]
   > リソース プロバイダーはサブスクリプションに 1 回だけ登録する必要があります。

1. 構成証明プロバイダー用のリソース グループを作成します。 クライアント アプリケーション インスタンスがインストールされた仮想マシンなど、他の Azure リソースを同じリソース グループに配置することができます。 [az group create](/cli/azure/group#az_group_create) コマンドを実行してリソース グループを作成するか、既存のリソース グループを使用します。

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>構成証明プロバイダーを作成して管理する

構成証明プロバイダーの作成と管理に使用できるコマンドは以下のとおりです。

1. [az attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) コマンドを実行して、ポリシー署名要件なしで構成証明プロバイダーを作成します。

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) コマンドを実行して、構成証明プロバイダーのプロパティ (状態や AttestURI など) を取得します。

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   このコマンドを実行すると、次の出力に示したような値が表示されます。

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

構成証明プロバイダーは、[az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) コマンドを使用して削除できます。

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>ポリシー管理

構成証明プロバイダー用のポリシーを管理するには、ここで説明するコマンドを使用します (一度に 1 つの構成証明の種類)。

[az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) コマンドからは、指定の TEE の現行ポリシーが返されます。

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> このコマンドを実行すると、テキストと JWT 形式の両方でポリシーが表示されます。

サポートされる TEE タイプは次のとおりです。

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

指定した構成証明の種類の新しいポリシーを設定するには、[az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) コマンドを使用します。

ファイル パスを使用して、特定の構成証明の種類のポリシーをテキスト形式で設定するには、次のように指定します。

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

ファイル パスを使用して、特定の構成証明の種類のポリシーを JWT 形式で設定するには、次のように指定します。

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>次のステップ

- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [コード サンプルを使用して SGX エンクレーブの構成証明を実装する](/samples/browse/?expanded=azure&terms=attestation)
