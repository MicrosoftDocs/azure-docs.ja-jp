---
title: CLI を使用した Azure Key Vault の管理 - Azure Key Vault | Microsoft Docs
description: この記事を活用し、Azure CLI を使用した Key Vault での一般的なタスクを自動化します
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb7e047d998342125a52af5ea3ae1e88fe88d313
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93289906"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Azure CLI を使用して Key Vault を管理します。 

この記事では、Azure CLI を使用した Azure Key Vault の操作の概要について説明します。  以下の情報をご覧いただけます。

- 強化されたコンテナー (資格情報コンテナー) を Azure に作成する方法
- キー コンテナーにキー、シークレット、または証明書を追加する
- Azure Active Directory にアプリケーションを登録する
- アプリケーションによるキーまたはシークレットの使用を承認する
- キー コンテナーの高度なアクセス ポリシーを設定する
- ハードウェア セキュリティ モジュール (HSM) を使用する
- キー コンテナーとそこに関連付けられているキーやシークレットを削除する
- Azure クロスプラットフォーム コマンドライン インターフェイスの各種コマンド


Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

> [!NOTE]
> この記事では、いずれかの手順に含まれる Azure アプリケーションの記述方法については説明していません。キー コンテナーでキーやシークレットを使用するためのアプリケーションの承認方法について説明しています。
>

Azure Key Vault の概要については、「[Azure Key Vault とは](overview.md)」をご覧ください。Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

この記事にある Azure CLI コマンドを使用するには、以下を用意する必要があります。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure コマンドライン インターフェイス バージョン 2.0 以降。 最新バージョンをインストールするには、 [Azure CLI をインストールする](/cli/azure/install-azure-cli)を参照してください。
* この記事で作成したキーやパスワードを使用して構成されるアプリケーション。 サンプル アプリケーションは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=45343)から入手できます。 手順については、付属の Readme ファイルをご覧ください。

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure クロスプラットフォーム コマンドライン インターフェイスでのヘルプの取得

この記事では、コマンドライン インターフェイス (Bash、Terminal、Command プロンプト) に慣れていることを前提としています。

--help または -h パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。 または、azure help [コマンド] [オプション] 形式も利用できます。 コマンドに必要なパラメーターがわからない場合は、ヘルプを参照してください。 たとえば、次のコマンドでは、すべて同じ情報が返されます。

```azurecli
az account set --help
az account set -h
```

また、次の記事を読めば、Azure クロスプラットフォーム コマンド ライン インターフェイスでの Azure Resource Manager について詳しく理解できます。

* [Azure CLI のインストール](/cli/azure/install-azure-cli)
* [Azure CLI の概要](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>強化されたコンテナー (資格情報コンテナー) を Azure に作成する方法

資格情報コンテナーは、ハードウェア セキュリティ モジュールに支えられた安全なコンテナーです。 コンテナーでは、アプリケーション シークレットを一元的に保管することで、セキュリティ情報が過って失われる可能性は低くなります。 また、Key Vault では、その中に格納されているすべての情報へのアクセスを制御し、記録します。 Azure Key Vault は、トランスポート層セキュリティ (TLS) 証明書の要求と更新を処理でき、堅牢な証明書ライフサイクル管理ソリューションに必要な機能を提供します。 次の手順で、資格情報コンテナーを作成します。

### <a name="connect-to-your-subscriptions"></a>サブスクリプションへの接続

対話でサインインするには、次のコマンドを使用します。

```azurecli
az login
```
組織アカウントを使用してサインインするために、ユーザー名とパスワードを渡すことができます。

```azurecli
az login -u username@domain.com -p password
```

サブスクリプションを複数保有していて、使用するサブスクリプションを指定する必要がある場合、次のように入力してお使いのアカウントのサブスクリプションを表示します。

```azurecli
az account list
```

サブスクリプション パラメーターを使用し、サブスクリプションを指定します。

```azurecli
az account set --subscription <subscription name or ID>
```

Azure クロスプラット フォーム コマンド ライン インターフェイスの構成の詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

### <a name="create-a-new-resource-group"></a>新しいリソース グループを作成する

Azure リソース マネージャーを使用すると、すべての関連するリソースが 1 つのリソース グループ内に作成されます。 既存のリソース グループでキー コンテナーを作成できます。 新しいリソース グループを使用する場合、リソース グループを新規作成できます。

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

最初のパラメーターはリソース グループ名で、2 番目のパラメーターは場所です。 利用可能なすべての場所の一覧を取得するには、次のように入力します。

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Key Vault リソース プロバイダーの登録

 新しいキー コンテナーを作成しようとすると、"サブスクリプションが名前空間 'Microsoft.KeyVault' を使用するように登録されていません" というエラーが表示される場合があります。 そのようなメッセージが表示される場合は、Key Vault リソースプロバイダーがサブスクリプションに登録されていることを確認します。 この操作は、サブスクリプションごとに 1 回だけ実行します。

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Key Vault を作成します

`az keyvault create` コマンドを使用して、Key Vault を作成します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、Key Vault 名、地理的な場所) が含まれています。

**東アジア** を拠点とするリソース グループ **ContosoResourceGroup** に **ContosoKeyVault** という名前の資格情報コンテナーを新規作成するには、次のように入力します。 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

このコマンドの出力は、作成したキー コンテナーのプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **name**:この例では、名前は ContosoKeyVault です。 この名前を他の Key Vault コマンドに使用できます。
* **vaultUri**: この例では、URI は https://contosokeyvault.vault.azure.net です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 現在のところ、誰も承認されていません。

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>キー コンテナーにキー、シークレット、または証明書を追加する

Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、`az key create` コマンドを使用します。

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

.pem ファイルに既存のキーがある場合は、Azure Key Vault にアップロードできます。 ソフトウェアまたは HSM でキーを保護することを選択できます。 この例では、.pem ファイルからキーをインポートし、パスワード "hVFkk965BuUv" を使用してソフトウェアでそれを保護します。

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用し、常に現在のバージョンを取得します。 https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] を使用し、この特定のバージョンを取得します。 たとえば、 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** です。 

資格情報コンテナーにシークレットを追加します (SQLPassword という名前のパスワードで、Azure Key Vault に "hVFkk965BuUv" の値を設定)。 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

その URI を使用し、このパスワードを参照します。 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用して常に最新バージョンを取得し、 https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] を使用してこの特定のバージョンを取得します。 たとえば、 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** です。

.pem または .pfx を使用して資格情報コンテナーに証明書をインポートします。

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

それでは、作成したキー、シークレット、または証明書を見てみましょう。

* キーを表示するには、次のように入力します。 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* シークレットを表示するには、次のように入力します。 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* 証明書を表示するには、次のように入力します。 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録する

この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有のものではありませんが、知識として知っていただくためにここで説明します。 アプリの登録を完了するには、アカウント、資格情報コンテナー、アプリケーションを同じ Azure ディレクトリに置く必要があります。

Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。  アプリケーションの所有者が先にそれを Azure Active Directory で登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

- **アプリケーション ID** (AAD クライアント ID や appID とも呼ばれています)
- **認証キー** (共有シークレットとも呼ばれます) 

アプリケーションは、トークンを取得するために、この 2 つの値を Azure Active Directory に示す必要があります。 トークンを取得するようにアプリケーションを構成する方法は、アプリケーションによって変わります。 [Key Vault のサンプル アプリケーション](https://www.microsoft.com/download/details.aspx?id=45343)の場合は、アプリケーション所有者がこれらの値を app.config ファイルに設定します。

Azure Active Directory にアプリケーションを登録する手順の詳細については、「[Azure Active Directory とアプリケーションの統合](../../active-directory/develop/quickstart-register-app.md)」、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」の各記事をご確認ください。

Azure Active Directory にアプリケーションを登録するには:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>アプリケーションによるキーまたはシークレットの使用を承認する

資格情報コンテナーのキーやシークレットにアクセスするアプリケーションを承認するには、 `az keyvault set-policy` コマンドを使用します。

たとえば、資格情報コンテナー名が ContosoKeyVault で、アプリケーションの appID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のコマンドを実行します。

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

同じアプリケーションを認証し、資格情報コンテナーのシークレットを読み取るには、次のコマンドを実行します。

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> キー コンテナーの高度なアクセス ポリシーを設定する

[az keyvault update](/cli/azure/keyvault#az-keyvault-update) を使用し、キー コンテナーの高度なポリシーを有効にします。

 Key Vault のデプロイを有効にする: 資格情報コンテナーからシークレットとして保存されている証明書を取得することを仮想マシンに許可します。

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault のディスク暗号化を有効にする: Azure Disk Encryption に資格情報コンテナーを使用するときに必須です。

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault のテンプレートのデプロイを有効にする: Resource Manager がコンテナーからシークレットを取得することを許可します。

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>ハードウェア セキュリティ モジュール (HSM) を使用する

さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、「Key Vault と関連するキーとシークレットを削除する」に進んでください。

これらの HSM で保護されたキーを作成するには、HSM で保護されたキーをサポートする資格情報コンテナーのサブスクリプションが必要です。

資格情報コンテナーを作成するときに、'sku' パラメーターを追加します。

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

この資格情報コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、Destination パラメーターを 'HSM' に設定します。

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

次のコマンドを使用して、自分のコンピューターの .pem ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

次のコマンドは、"bring your own key" (BYOK) パッケージをインポートします。 これを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](../keys/hsm-protected-keys.md)」をご覧ください。

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>キー コンテナーとそこに関連付けられているキーやシークレットを削除する

キー コンテナーと、そのキーまたはシークレットが不要になった場合は、次の `az keyvault delete` コマンドを使用してキー コンテナーを削除できます。

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Azure クロスプラットフォーム コマンドライン インターフェイスの各種コマンド

Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

このコマンドは、すべてのキーと選択したプロパティを表形式で一覧表示します。

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧表示します。

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

特定のキーを削除する方法の例です。

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

特定のシークレットを削除する方法の例です。

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>次のステップ

- Key Vault コマンドの完全な Azure CLI リファレンスについては、「[Key Vault CLI リファレンス](/cli/azure/keyvault)」をご覧ください。

- プログラミング リファレンスについては、「[Azure Key Vault 開発者ガイド](developers-guide.md)」を参照してください。

- Azure Key Vault と HSM の詳細については、[Azure Key Vault での HSM 保護キーの使用方法](../keys/hsm-protected-keys.md)に関するページをご覧ください。