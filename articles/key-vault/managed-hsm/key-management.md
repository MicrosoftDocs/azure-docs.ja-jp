---
title: マネージド HSM のキーを管理する - Azure Key Vault | Microsoft Docs
description: この記事では、マネージド HSM のキーを管理します
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212044"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Azure CLI を使用してマネージド HSM を管理する

> [!NOTE]
> Key Vault では、コンテナーとマネージド HSM という 2 種類のリソースがサポートされています。 この記事では、**Managed HSM** について説明します。 コンテナーの管理方法については、「[Azure CLI を使用して Key Vault を管理する](../general/manage-with-cli2.md)」を参照してください。

Managed HSM の概要については、[Managed HSM の概要](overview.md)に関するページを参照してください

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

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

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください

> [!NOTE]
> 以下のすべてのコマンドは、2 つの使用方法を示しています。 1 つは **--hsm-name** と **--name** (キー名用) パラメーターを使用し、もう 1 つは **--id** パラメーターを使用して、必要に応じてキー名を含む URL 全体を指定できます。 後者の方法は、呼び出し元 (ユーザーまたはアプリケーション) にコントロール プレーンに対する読み取りアクセス権がなく、データ プレーンに対する制限付きアクセスだけがある場合に便利です。

## <a name="create-an-hsm-key"></a>HSM キーを作成する

キーを作成するには、`az keyvault key create` コマンドを使用します。

### <a name="create-an-rsa-key"></a>RSA キーを作成する

次の例は、**wrapKey、unwrapKey** 操作 (--ops) にのみ使用される 3072 ビット **RSA** キーを作成する方法を示しています。 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

`get` 操作では、公開キーとキーの属性のみが返されることに注意してください。 秘密キー (非対称キーの場合) またはキー マテリアル (対称キーの場合) は返されません。

### <a name="create-an-ec-key"></a>EC キーを作成する

次の例は、P-256 曲線を含む **EC** キーを作成する方法を示しています。これは **sign および verify** 操作 (--ops) にのみ使用され、2 つのタグとして **usage** と **appname** があります。 タグは、追跡と管理のためにキーにメタデータを追加する場合に役立ちます。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>256 ビット対称キーを作成する

次の例は、**encrypt と decrypt** 操作 (--ops) にのみ使用される 256 ビット **対称** キーを作成する方法を示しています。

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>キー属性とタグを表示する

キーの属性、バージョン、およびタグを表示するには、`az keyvault key show` コマンドを使用します。

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>キーのリスト

マネージド HSM 内のすべてのキーを一覧表示するには、`az keyvault key list` コマンドを使用します。

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>キーの削除

マネージド HSM からキーを削除するには、`az keyvault key delete` コマンドを使用します。 論理的な削除が常に有効にされていることに注意してください。 そのため、削除されたキーは削除された状態のまま保持され、保持日数が経過してキーが消去 (完全に削除) されて復旧できなくなるまでは、復旧できます。

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>削除したキーを一覧表示する

マネージド HSM の削除状態のすべてのキーを一覧表示するには、`az keyvault key list-deleted` コマンドを使用します。

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>削除したキーを復旧する (削除を取り消す)

マネージド HSM の削除状態のすべてのキーを一覧表示するには、`az keyvault key list-deleted` コマンドを使用します。 削除したキーを復旧するときに、--id パラメーターを使用してキーを復旧する (削除を取り消す) 必要がある場合は、`az keyvault key list-deleted` コマンドから取得した削除済みキーの `recoveryId` 値を書き留めておく必要があります。

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>キーを消去 (完全に削除) する

キーを消去 (完全に削除) するには、`az keyvault key purge` コマンドを使用します。

> [!NOTE]
> マネージド HSM で消去保護が有効になっている場合、消去操作は許可されません。 保持期間を過ぎると、キーは自動的に消去されます。

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>単一キーのバックアップを作成する

キーのバックアップを作成するには、`az keyvault key backup` を使用します。 バックアップ ファイルは、ソース HSM のセキュリティ ドメインに暗号的に結び付けられている、暗号化された BLOB です。 同じセキュリティ ドメインを共有する HSM でのみ復元できます。 詳細については、[セキュリティ ドメイン](security-domain.md)に関するページを参照してください。

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>バックアップから単一キーを復元する

単一キーを復元するには、`az keyvault key restore` を使用します。 バックアップが作成されたソース HSM は、キーが復元されるターゲット HSM と同じセキュリティ ドメインを共有している必要があります。

> [!NOTE]
> 同じ名前のキーがアクティブまたは削除済みの状態である場合、復元は成功しません。

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>ファイルからキーをインポートする

ファイルからキー (RSA と EC のみ) をインポートするには、`az keyvault key import` コマンドを使用します。 証明書ファイルには秘密キーが必要であり、PEM エンコードを使用する必要があります (RFC [1421](https://tools.ietf.org/html/rfc1421)、[1422](https://tools.ietf.org/html/rfc1422)、[1423](https://tools.ietf.org/html/rfc1423)、[1424](https://tools.ietf.org/html/rfc1424) で定義されているように)。

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

オンプレミスの HSM からマネージド HSM にキーをインポートする方法については、「[HSM で保護されたキーをマネージド HSM にインポートする (BYOK)](hsm-protected-keys-byok.md)」を参照してください

## <a name="next-steps"></a>次のステップ

- Key Vault コマンドの完全な Azure CLI リファレンスについては、「[Key Vault CLI リファレンス](/cli/azure/keyvault)」をご覧ください。
- プログラミング リファレンスについては、「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照してください。
- [Managed HSM のロール管理](role-management.md)について、さらに学習します
- [Managed HSM のベスト プラクティス](best-practices.md)について、さらに学習します
