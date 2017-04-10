---
title: "Azure CLI 1.0 を使用して Linux VM のディスクを暗号化する | Microsoft Docs"
description: "Azure CLI 1.0 と Resource Manager デプロイメント モデルを使って、Linux VM のディスクを暗号化する方法について説明します"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 825963ec7452130904204c0a32236ee297dfff11
ms.lasthandoff: 03/24/2017


---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Azure CLI 1.0 を使って Linux VM のディスクを暗号化する
仮想マシン (VM) のセキュリティと法令遵守を強化するため、Azure の仮想ディスクに保存されるデータを暗号化できます。 ディスクは、Azure Key Vault で保護されている暗号化キーを使って暗号化されます。 これらの暗号化キーを制御し、その使用を監査することができます。 この記事では、Azure CLI 1.0 と Resource Manager デプロイメント モデルを使って、Linux VM の仮想ディスクを暗号化する方法を詳しく説明します。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM の仮想ディスクを暗号化するための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#overview-of-disk-encryption)。

[最新の Azure CLI 1.0](../xplat-cli-install.md) をインストールし、次のように Resource Manager モードを使ってログインする必要があります。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myKeyVault`、`myVM` などは、例として使われているパラメーター名です。

最初に、Azure サブスクリプションで Azure Key Vault プロバイダーを有効にして、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure Key Vault を作成します。 次の例では、`myKeyVault` という名前の Key Vault を作成します。

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Key Vault に暗号化キーを作成し、ディスクの暗号化に使用できるようにします。 次の例では、`myKey` という名前のキーを作成します。

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

認証を処理し、Key Vault からの暗号化キーを交換するために、Azure Active Directory を使ってエンドポイントを作成します。 `--home-page` と `--identifier-uris` は、実際にルーティング可能なアドレスである必要はありません。 最高レベルのセキュリティのためには、パスワードではなくクライアント シークレットを使う必要があります。 現在、Azure CLI ではクライアント シークレットを生成できません。 クライアント シークレットは、Azure Portal でのみ生成できます。 次の例では、`myAADApp` という名前の Azure Active Directory エンドポイントを作成し、`myPassword` というパスワードを使います。 次のように、独自のパスワードを指定してください。

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

前のコマンドからの出力で示されている `applicationId` に注意してください。 以下の手順では、このアプリケーション ID を使います。

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

既存の VM にデータ ディスクを追加します。 次の例では、`myVM` という名前の VM にデータ ディスクを追加します。

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Key Vault および作成したキーの詳細を確認します。 最後の手順では、Key Vault ID、URI、キーの URL が必要になります。 次の例では、`myKeyVault` という名前の Key Vault と、`myKey` という名前のキーの詳細を確認します。

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

次のようにしてディスクを暗号化します。すべての箇所で、独自のパラメーター名を入力します。

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI では、暗号化処理中には詳細なエラーは提供されません。 追加のトラブルシューティング情報は、`/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` を確認してください。 上記のコマンドには多数の変数があり、処理が失敗した理由がよくわからないことがあります。完全なコマンドの例は次のとおりです。

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

最後に、暗号化の状態を再度調べて、仮想ディスクが暗号化されていることを確認します。 次の例では、`myResourceGroup` リソース グループ内にある `myVM` という名前の VM の状態を確認します。

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>ディスク暗号化の概要
Linux VM の仮想ディスクは、[dm-crypt](https://wikipedia.org/wiki/Dm-crypt) を使って暗号化します。 Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 Azure Active Directory エンドポイントは、VM の電源がオン/オフされるときにこれらの暗号化キーを発行するためのセキュリティで保護されたメカニズムを提供します。

VM 暗号化のプロセスは次のとおりです。

1. Azure Key Vault で暗号化キーを作成します。
2. ディスクの暗号化に使うことができるように暗号化キーを構成します。
3. Azure Key Vault から暗号化キーを読み取るには、Azure Active Directory を使って適切なアクセス許可を持つエンドポイントを作成します。
4. コマンドを発行して仮想ディスクを暗号化します。このとき、Azure Active Directory エンドポイントと、使用する適切な暗号化キーを指定します。
5. Azure Active Directory エンドポイントは、Azure Key Vault に必要な暗号化キーを要求します。
6. 提供された暗号化キーを使って仮想ディスクが暗号化されます。

## <a name="supporting-services-and-encryption-process"></a>サポート サービスと暗号化プロセス
ディスクの暗号化は、以下の他のコンポーネントに依存します。

* **Azure Key Vault** - ディスクの暗号化/暗号化解除プロセスに使われる暗号化キーとシークレットの保護に使われます。 
  * Azure Key Vault が既に存在する場合は、それを使うことができます。 Key Vault をディスク暗号化専用にする必要はありません。
  * 管理境界とキーの可視性を分離するため、専用の Key Vault を作成してもかまいません。
* **Azure Active Directory** - 必要な暗号化キーの安全な交換と、要求されたアクションの認証を処理します。 
  * 通常は、既存の Azure Active Directory インスタンスを使ってアプリケーションを保持できます。 
  * アプリケーションは、Key Vault と仮想マシン サービスに対するエンドポイントのようなものであり、適切な暗号化キーの発行を要求して取得します。 Azure Active Directory と統合する実際のアプリケーションを開発しているのではありません。

## <a name="requirements-and-limitations"></a>要件と制限
ディスク暗号化のサポートされるシナリオと要件:

* 次の Linux サーバー SKU - Ubuntu、CentOS、SUSE と SUSE Linux Enterprise Server (SLES)、Red Hat Enterprise Linux。
* すべてのリソース (Key Vault、ストレージ アカウント、VM など) は、同じ Azure リージョンとサブスクリプションに存在している必要があります。
* Standard A、D、DS、G、GS シリーズの VM。

現在、ディスクの暗号化は次のシナリオではサポートされていません。

* Basic レベルの VM。
* クラシック デプロイメント モデルで作成された VM。
* Linux VM での OS ディスク暗号化の無効化。
* 既に暗号化されている Linux VM での暗号化キーの更新。

## <a name="create-the-azure-key-vault-and-keys"></a>Azure Key Vault とキーを作成する
このガイドの残りの部分を行うには、[最新の Azure CLI 1.0](../xplat-cli-install.md) をインストールし、次のように Resource Manager モードを使ってログインする必要があります。

```azurecli
azure config mode arm
```

コマンドの例全体を通して、パラメーターの例を実際の名前、場所、およびキーの値に置き換えます。 以下の例では、`myResourceGroup`、`myKeyVault`、`myAADApp` などの表記を使います。

最初に、暗号化キーを格納する Azure Key Vault を作成します。 Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 仮想ディスクの暗号化では、仮想ディスクの暗号化または暗号化解除に使われる暗号化キーを格納するために Key Vault を使います。 

Azure サブスクリプションで Azure Key Vault プロバイダーを有効にして、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

暗号化キーおよびストレージや VM 自体などの関連するコンピューティング リソースを格納する Azure Key Vault は、同じリージョンに存在する必要があります。 次の例では、`myKeyVault` という名前の Azure Key Vault を作成します。

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

ソフトウェアまたはハードウェア セキュリティ モデル (HSM) の保護を使って、暗号化キーを格納できます。 HSM を使うには、Premium Key Vault が必要です。 ソフトウェアで保護されたキーを格納する Standard Key Vault ではなく Premium Key Vault を作成するには、追加コストがかかります。 Premium Key Vault を作成するには、前の手順で `--sku Premium` をコマンドに追加します。 ここでは Standard Key Vault を作成したので、次の例ではソフトウェアで保護されたキーを使います。 

どちらの保護モデルでも、VM が起動して仮想ディスクを復号化するときに、Azure プラットフォームは暗号化キーを要求するためのアクセスを許可される必要があります。 Key Vault に暗号化キーを作成した後、仮想ディスクの暗号化で使えるようにします。 次の例では、`myKey` という名前のキーを作成し、ディスク暗号化に対して有効にします。

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する
仮想ディスクが暗号化または暗号化解除されるときは、エンドポイントを使って、認証と Key Vault からの暗号化キーの交換を処理します。 このエンドポイントは Azure Active Directory アプリケーションであり、Azure プラットフォームが VM の代わりの適切な暗号化キーを要求できるようにします。 サブスクリプションでは既定の Azure Active Directory インスタンスを使うことができますが、多くの場合、専用の Azure Active Directory ディレクトリが使われます。

完全な Azure Active Directory アプリケーションを作成しない場合、次の例の `--home-page` および `--identifier-uris` パラメーターは、実際のルーティング可能なアドレスではなくてもかまいません。 次の例では、Azure Portal 内からキーを生成するのではなく、パスワードに基づくシークレットも指定します。 現時点では、Azure CLI からキーを生成することはできません。 

Azure Active Directory アプリケーションを作成します。 次の例では、`myAADApp` という名前のアプリケーションを作成し、`myPassword` というパスワードを使います。 次のように、独自のパスワードを指定してください。

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

前のコマンドの出力で返される `applicationId` を記録しておきます。 このアプリケーション ID を後の手順で使います。 次に、環境内でアプリケーションにアクセスできるように、サービス プリンシパル名 (SPN) を作成します。 仮想ディスクを正常に暗号化または暗号化解除するには、Key Vault に格納されている暗号化キーに対するアクセス許可を設定し、Azure Active Directory アプリケーションにキーの読み取りを許可します。 

次のように、SPN を作成して適切なアクセス許可を設定します。

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>仮想ディスクを追加して暗号化の状態を確認する
実際に仮想ディスクを暗号化するため、既存の VM にディスクを追加します。 次のように、既存の VM に 5 GB のデータ ディスクを追加します。

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

仮想ディスクは現在は暗号化されていません。 VM の現在の暗号化の状態を次のようにして確認します。

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>仮想ディスクを暗号化する
仮想ディスクを暗号化するには、これまでのコンポーネントをすべてまとめます。

1. Azure Active Directory のアプリケーションとパスワードを指定します。
2. 暗号化されたディスクのメタデータを格納する Key Vault を指定します。
3. 実際の暗号化と暗号化解除に使う暗号化キーを指定します。
4. OS ディスク、データ ディスク、またはすべてのディスクのいずれを暗号化するかを指定します。

Azure Key Vault の詳細と、作成したキーを確認します。最後の手順で Key Vault ID、URI、キーの URL が必要になります。

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

次のように、`azure keyvault show` および `azure keyvault key show` コマンドからの出力を使って仮想ディスクを暗号化します。

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

前のコマンドには多くの変数があります。参考のために完全なコマンドの例を次に示します。

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI では、暗号化処理中には詳細なエラーは提供されません。 追加のトラブルシューティング情報は、暗号化している VM で `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` を確認してください。

最後に、暗号化の状態を再び調べて、仮想ディスクが暗号化されていることを確認します。

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>新しいデータ ディスクを追加する
データ ディスクを暗号化した後で、別の仮想ディスクを VM に追加して暗号化することもできます。 `azure vm enable-disk-encryption` コマンドを実行するときに、`--sequence-version` パラメーターを使ってシーケンスのバージョンを増やします。 このシーケンス バージョン パラメーターを使うと、同じ VM に対して操作を繰り返し実行できます。

たとえば、次のようにして 2 番目の仮想ディスクを VM に追加できます。

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

コマンドを再度実行して仮想ディスクを暗号化します。今度は、`--sequence-version` パラメーターを追加し、次のように最初の実行から値を増分します。

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>次のステップ
* 暗号化キーや Vault の削除など、Azure Key Vault の管理について詳しくは、「[CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md)」をご覧ください。
* 暗号化されたカスタム VM を Azure にアップロードするための準備など、ディスクの暗号化について詳しくは、「[Azure Disk Encryption](../security/azure-security-disk-encryption.md)」をご覧ください。


