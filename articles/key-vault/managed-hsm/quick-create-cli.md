---
title: クイックスタート - Azure Managed HSM をプロビジョニングしてアクティブにする
description: Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする方法を紹介するクイックスタート。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91756820"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする

Azure Key Vault Managed HSM は、フル マネージド、高可用性、シングル テナント、標準準拠を特徴とするクラウド サービスで、**FIPS 140-2 レベル 3** 適合の HSM を使用してクラウド アプリケーションの暗号化キーを保護することができます。 Managed HSM の詳細については、[概要](overview.md)に関するページを参照してください。 

このクイックスタートでは、Azure CLI を使用してマネージド HSM を作成し、アクティブにします。 この作業を完了したら、シークレットを格納します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure CLI バージョン 2.12.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* お使いのサブスクリプション内のマネージド HSM。 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM をプロビジョニングしてアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*ContosoResourceGroup* という名前のリソース グループを *eastus2* の場所に作成します。

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Managed HSM を作成する

マネージド HSM は、次の 2 段階のプロセスで作成します。
1. Managed HSM リソースをプロビジョニングします。
1. セキュリティ ドメインをダウンロードして Managed HSM をアクティブにします。

### <a name="provision-a-managed-hsm"></a>マネージド HSM をプロビジョニングする

Managed HSM を作成するには、`az keyvault create` コマンドを使用します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、HSM 名、地理的な場所) が含まれています。

Managed HSM リソースを作成するには、次の入力を指定する必要があります。
- サブスクリプション内での配置先となるリソース グループ。
- Azure の場所。
- 初期管理者のリスト。

次の例では、**ContosoMHSM** という名前の HSM を、リソース グループ **ContosoResourceGroup** に作成して、**米国東部 2** に配置し、**現在サインインしているユーザー** を唯一の管理者としています。

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Create コマンドには数分かかることがあります。 正常に制御が戻ったら、HSM をアクティブにする準備が整います。

このコマンドの出力は、作成した Managed HSM のプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **name**:この例では、名前は ContosoMHSM です。 この名前を他の Key Vault コマンドに使用できます。
* **hsmUri**: この例では、URI は "https://contosohsm.managedhsm.azure.net" です。 REST API から HSM を使用するアプリケーションでは、この URI を使用する必要があります。

これで、お使いの Azure アカウントは、このマネージド HSM に対して任意の操作を実行できるようになりました。 現在のところ、誰も承認されていません。

### <a name="activate-your-managed-hsm"></a>マネージド HSM をアクティブにする

HSM をアクティブにするまでは、データ プレーンのコマンドはすべて無効です。 キーを作成することはできず、ロールを割り当てることもできません。 HSM をアクティブにできるのは、create コマンドの実行時に割り当てられた指定された管理者だけです。 HSM をアクティブにするには、[セキュリティ ドメイン](security-domain.md)をダウンロードする必要があります。

HSM をアクティブにするための要件は次のとおりです。
- 最低 3 つの RSA キーペア (最大 10 個)
- セキュリティ ドメインの暗号化を解除するために必要なキーの最小数 (クォーラム) の指定

HSM をアクティブにするには、少なくとも 3 つ (最大 10 個) の RSA 公開キーを HSM に送信する必要があります。 HSM は、それらのキーでセキュリティ ドメインを暗号化して返します。 このセキュリティ ドメインのダウンロードが正常に完了したら、HSM を使用する準備は完了です。 加えて、クォーラムの指定も必要となります。クォーラムは、セキュリティ ドメインの暗号化を解除するために必要な秘密キーの最小数です。

次の例は、`openssl` を使用して 3 つの自己署名証明書を生成する方法を示しています。

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> この手順で生成した RSA キー ペアとセキュリティ ドメイン ファイルは、安全に作成、保存してください。

セキュリティ ドメインをダウンロードして、マネージド HSM をアクティブにするには、`az keyvault security-domain download` コマンドを使用します。 以下の例では、3 つの RSA キー ペア (このコマンドで必要なのは公開キーのみ) を使用し、クォーラムは 2 に設定しています。

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

セキュリティ ドメイン ファイルと RSA キー ペアは安全に保存してください。 これらは、ディザスター リカバリーに必要となります。また、同じセキュリティ ドメインを共有するマネージド HSM を新たに作成し、キーを共有できるようにする際にも必要となります。

セキュリティ ドメインを正常にダウンロードすると、HSM がアクティブな状態となり、いつでも使用することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループおよびすべての関連リソースを削除できます。 次のように、リソースを削除できます。

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Managed HSM の概要](overview.md)に関するページを読む
- [マネージド HSM におけるキーの管理](key-management.md)について学習する
- [Managed HSM のベスト プラクティス](best-practices.md)を確認する
