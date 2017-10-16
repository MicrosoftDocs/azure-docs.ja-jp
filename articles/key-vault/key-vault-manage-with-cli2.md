---
title: "CLI を使用した Azure Key Vault の管理 | Microsoft Docs"
description: "このチュートリアルでは、CLI 2.0 を使用して Key Vault で一般的なタスクを自動化します。"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>CLI 2.0 を使用した Key Vault の管理
Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

## <a name="introduction"></a>はじめに
このチュートリアルを使用すると、Azure Key Vault で、強化されたコンテナー (資格情報コンテナー) を Azure に作成し、暗号化キーやシークレットを Azure に格納して管理できるようになります。 ここでは、Azure クロスプラットフォーム コマンドライン インターフェイスを使用して、Azure アプリケーションで使用できるキーまたはパスワードを含む資格情報コンテナーを作成するプロセスについて説明します。 アプリケーションがそのキーやパスワードを使用する方法についても説明します。

**推定所要時間:** 20 分

> [!NOTE]
> このチュートリアルでは、いずれかの手順に含まれる Azure アプリケーションの記述方法については説明していません。Key Vault でキーやシークレットを使用するためのアプリケーションの承認方法について説明しています。
>
> このチュートリアルでは、最新の Azure CLI 2.0 を使用します。
>
>

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、 [無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* コマンドライン インターフェイス バージョン 2.0 以降。 最新バージョンをインストールして Azure サブスクリプションに接続するには、「[Azure クロスプラットフォーム コマンド ライン インターフェイス 2.0 のインストールと構成](/cli/azure/install-azure-cli)」を参照してください。
* このチュートリアルで作成したキーやパスワードを使用して構成されるアプリケーション。 サンプル アプリケーションは、[Microsoft ダウンロード センター](http://www.microsoft.com/download/details.aspx?id=45343)から入手できます。 手順については、付属の Readme ファイルをご覧ください。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure クロスプラットフォーム コマンドライン インターフェイスでのヘルプの取得
このチュートリアルでは、コマンドライン インターフェイス (Bash、Terminal、Command プロンプト) に慣れていることを前提としています。

--help または -h パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。 または、azure help [コマンド][オプション] の形式で指定しても、同じ情報が返されます。 たとえば、次のコマンドでは、すべて同じ情報が返されます。

```
az account set --help
az account set -h
```

コマンドに必要なパラメーターがわからない場合は、--help、-h、または azure help [コマンド] を使用してヘルプを参照してください。

また、次のチュートリアルを読めば、Azure クロスプラットフォーム コマンド ライン インターフェイスでの Azure リソース マネージャーについて詳しく理解できます。

* [Azure CLI のインストール](/cli/azure/install-azure-cli)
* [Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>サブスクリプションへの接続
組織のアカウントを使用してログインするには、次のコマンドを使用します。

```
az login -u username@domain.com -p password
```

また、対話形式で入力してログインする場合には、次のコマンドを使用します。

```
az login
```

複数のサブスクリプションがあり、特定の 1 つのサブスクリプションを指定して Azure Key Vault を使用する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

```
az account list
```

使用するサブスクリプションを指定するには、次のように入力します。

```
az account set --subscription <subscription name or ID>
```

Azure クロスプラット フォーム コマンド ライン インターフェイスの構成の詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-new-resource-group"></a>新しいリソース グループを作成する
Azure リソース マネージャーを使用すると、すべての関連するリソースが 1 つのリソース グループ内に作成されます。 このチュートリアルでは、'ContosoResourceGroup' という新しいリソース グループを作成します。

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

最初のパラメーターはリソース グループ名で、2 番目のパラメーターは場所です。 場所の場合には、 `az account list-locations` コマンドを使用して別の場所をこの例の場所に指定する方法を識別します。 詳細情報が必要な場合は、`az account list-locations -h` と入力します。

## <a name="register-the-key-vault-resource-provider"></a>Key Vault リソース プロバイダーの登録
Key Vault リソース プロバイダーがサブスクリプションに登録されていることを確認します。

```
az provider register -n Microsoft.KeyVault
```

これは、サブスクリプションごとに 1 回だけ実行する必要があります。

## <a name="create-a-key-vault"></a>Key Vault を作成します
`az keyvault create` コマンドを使用して、Key Vault を作成します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、Key Vault 名、地理的な場所) が含まれています。

たとえば、Key Vault 名に ContosoKeyVault、リソース グループ名に ContosoResourceGroup、場所に東アジアを使用する場合は、次のように入力します。
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

このコマンドの出力は、作成されたばかりの Key Vault のプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **name**: この例では、ContosoKeyVault です。 この名前を他の Key Vault コマンドに使用できます。
* **vaultUri**: この例では、これは https://contosokeyvault.vault.azure.net です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 まだ、どのユーザーも許可されていません。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>キーやシークレットを Key Vault に追加します
Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、`az key create` コマンドを使用して次のように入力します。
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
ただし、Azure Key Vault にアップロードする softkey.pem という名前の、ローカル ファイルとして保存された .pem ファイル内に既存のキーがある場合には、キーを .PEM ファイルからインポートするために次のコマンドを入力します。これにより Key Vault サービスでソフトウェアによりキーが保護されます。
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** を使用すると、その特定のバージョンを取得できます。

資格情報コンテナーにシークレットを追加するには (SQLPassword という名前のパスワードで、Azure Key Vault に Pa$$w0rd 値を設定)、次のように入力します。
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** を使用すると、その特定のバージョンを取得できます。

作成したキーやシークレットを表示してみましょう。

* キーを表示するには、次のように入力します。`az keyvault key list --vault-name 'ContosoKeyVault'`
* シークレットを表示するには、次のように入力します。`az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録します
この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有のものではありませんが、完全を期すために説明します。

> [!IMPORTANT]
> チュートリアルを完了するには、この手順で登録するアカウント、資格情報コンテナー、アプリケーションがすべて同じ Azure ディレクトリに格納されている必要があります。
>
>

Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。 これを行うには、アプリケーションの所有者は、まず Azure Active Directory でアプリケーションを登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

* **アプリケーション ID** (クライアント ID とも呼ばれます) と**認証キー** (共有シークレットとも呼ばれます)。 アプリケーションは、トークンを取得するために、この 2 つの値を Azure Active Directory に示す必要があります。 これを行うようにアプリケーションを構成する方法は、アプリケーションによって異なります。 Key Vault のサンプル アプリケーション用に、アプリケーション所有者は app.config ファイルでこれらの値を設定します。

Azure Active Directory にアプリケーションを登録するには:

1. Azure ポータルにサインインします。
2. 左側で **[Azure Active Directory]** をクリックし、アプリケーションを登録するディレクトリを選択します。 <br> <br> 

> [!Note] 
> キー コンテナーを作成した Azure サブスクリプションが含まれている、同じディレクトリを選択する必要があります。 ディレクトリが不明な場合は、 **[設定]**をクリックし、Key Vault を作成したサブスクリプションを見つけて、最後の列に表示されているディレクトリ名をご確認ください。

3. **[アプリケーション]**をクリックします。 アプリがディレクトリに追加されていない場合は、このページには **[アプリケーションの追加]** リンクのみが表示されます。 リンクをクリックするか、コマンド バーの **[追加]** をクリックします。
4. **アプリケーションの追加**ウィザードの **[実行する作業を選択してください。]** ページで、**[組織で開発中のアプリケーションを追加]** をクリックします。
5. **[アプリケーション情報の指定]** ページで、アプリケーションの名前を指定し、**[Web アプリケーションや Web API]** (既定値) を選択します。 [次へ] アイコンをクリックします。
6. **[アプリのプロパティ]** ページで、**[サインオン URL]** と **[アプリケーション ID/URI]** を Web アプリケーションに指定します。 アプリケーションにこれらの値を設定していない場合は、この手順のための値を作成します (両方のボックスに http://test1.contoso.com を指定するなど)。 これらのサイトが存在するかどうかは関係ありません。各アプリケーションのアプリケーション ID URI がディレクトリ内のすべてのアプリケーションで異なっていることが重要です。 ディレクトリは、この文字列を使用してアプリを識別します。
7. [完了] アイコンをクリックし、ウィザードで変更内容を保存します。
8. [クイック スタート] ページで、 **[構成]**をクリックします。
9. **[キー]** セクションまでスクロールし、期間を選択して **[保存]** をクリックします。 ページが更新され、キーの値が表示されます。 このキーと**クライアント ID** の値を使用してアプリケーションを構成する必要があります  (この構成の手順はアプリケーション固有です)。
10. このページからクライアント ID 値をコピーします。この値は、資格情報コンテナーに権限を設定するために次の手順で使用します

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>キーまたはシークレットを使用してアプリケーションを承認します
資格情報コンテナーのキーやシークレットにアクセスするアプリケーションを承認するには、 `az keyvault set-policy` コマンドを使用します。

たとえば、資格情報コンテナー名が ContosoKeyVault で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のように実行します。
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

その同じアプリケーションを認証し、資格情報コンテナーのシークレットの読み取りを許可する場合、次を実行します。
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>ハードウェア セキュリティ モジュール (HSM) を使用する場合
さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、 [Key Vault と関連するキーとシークレットを削除する](#delete-the-key-vault-and-associated-keys-and-secrets)に進んでください。

これらの HSM で保護されたキーを作成するには、HSM で保護されたキーをサポートする資格情報コンテナーのサブスクリプションが必要です。

資格情報コンテナーを作成するときに、'sku' パラメーターを追加します。

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
この資格情報コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、Destination パラメーターを 'HSM' に設定します。

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

次のコマンドを使用して、自分のコンピューターの .pem ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
次のコマンドは、“bring your own key" (BYOK) パッケージをインポートします。 これを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)」をご覧ください。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Key Vault と関連するキーとシークレットを削除する
Key Vault と、これに含まれるキーやシークレットが不要になった場合は、次の `az keyvault delete` コマンドを使用して Key Vault を削除できます。

```
az keyvault delete --name 'ContosoKeyVault'
```

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>その他の Azure クロスプラットフォーム コマンドライン インターフェイスのコマンド
Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

このコマンドは、すべてのキーと選択したプロパティを表形式で一覧表示します。

az keyvault key list --vault-name 'ContosoKeyVault'

このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。

az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧表示します。

az keyvault secret list --vault-name 'ContosoKeyVault'

特定のキーを削除する方法の例です。

az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

特定のシークレットを削除する方法の例です。

az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'


## <a name="next-steps"></a>次のステップ
Key Vault コマンドの完全な Azure CLI リファレンスについては、[Key Vault CLI リファレンス](/cli/azure/keyvault)を参照してください。

プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。
