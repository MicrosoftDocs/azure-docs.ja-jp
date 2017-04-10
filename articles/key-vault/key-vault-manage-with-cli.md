---
title: "CLI を使用した Key Vault の管理 | Microsoft Docs"
description: "このチュートリアルを活用し、CLI を使用した Key Vault での一般的なタスクを自動化します"
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: eec68b1cbfbddf0b72b55376ee11451b25898b68
ms.lasthandoff: 03/21/2017


---
# <a name="manage-key-vault-using-cli"></a>CLI を使用した Key Vault の管理
Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

## <a name="introduction"></a>はじめに
このチュートリアルを使用すると、Azure Key Vault で、強化されたコンテナー (資格情報コンテナー) を Azure に作成し、暗号化キーやシークレットを Azure に格納して管理できるようになります。 ここでは、Azure クロスプラットフォーム コマンドライン インターフェイスを使用して、Azure アプリケーションで使用できるキーまたはパスワードを含む資格情報コンテナーを作成するプロセスについて説明します。 アプリケーションがそのキーやパスワードを使用する方法についても説明します。

**推定所要時間:** 20 分

> [!NOTE]
> このチュートリアルでは、いずれかの手順に含まれる Azure アプリケーションの記述方法については説明していません。Key Vault でキーやシークレットを使用するためのアプリケーションの承認方法について説明しています。
> 
> 現時点では、Azure ポータルで Azure Key Vault を構成できません。 代わりに、クロスプラットフォーム コマンドライン インターフェイスの手順を使用します。 また、Azure PowerShell の手順については、 [対応するチュートリアル](key-vault-get-started.md)をご覧ください。
> 
> 

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、 [無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* コマンドライン インターフェイス バージョン 0.9.1 以降。 最新バージョンをインストールして、Azure サブスクリプションに接続するには、「 [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](../cli-install-nodejs.md)」を参照してください。
* このチュートリアルで作成したキーやパスワードを使用して構成されるアプリケーション。 サンプル アプリケーションは、[Microsoft ダウンロード センター](http://www.microsoft.com/download/details.aspx?id=45343)から入手できます。 手順については、付属の Readme ファイルをご覧ください。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure クロスプラットフォーム コマンドライン インターフェイスでのヘルプの取得
このチュートリアルでは、コマンドライン インターフェイス (Bash、Terminal、Command プロンプト) に慣れていることを前提としています。

--help または -h パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。 または、azure help [コマンド][オプション] の形式で指定しても、同じ情報が返されます。 たとえば、次のコマンドでは、すべて同じ情報が返されます。

    azure account set --help

    azure account set -h

    azure help account set

コマンドに必要なパラメーターがわからない場合は、--help、-h、または azure help [コマンド] を使用してヘルプを参照してください。

また、次のチュートリアルを読めば、Azure クロスプラットフォーム コマンド ライン インターフェイスでの Azure リソース マネージャーについて詳しく理解できます。

* [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成方法](../cli-install-nodejs.md)
* [Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>サブスクリプションへの接続
組織のアカウントを使用してログインするには、次のコマンドを使用します。

    azure login -u username -p password

また、対話形式で入力してログインする場合には、次のコマンドを使用します。

    azure login

> [!NOTE]
> ログインによる方法は組織のアカウントでのみ機能します。 組織のアカウントは、組織で管理されるユーザーであり、組織の Azure Active Directory テナントで定義されています。
> 
> 

組織のアカウントが現在なく、Azure サブスクリプションへのログインに Microsoft アカウントを使用している場合は、次に示している手順を使用して組織のアカウントを簡単に作成できます。

1. [Microsoft Azure 管理ポータル](https://manage.windowsazure.com/)にログインし、[Active Directory] をクリックします。
2. ディレクトリがない場合は、[ディレクトリの作成] を選択し、求められる情報を入力します。
3. ディレクトリを選択し、新しいユーザーを追加します。 この新しいユーザーが組織のアカウントです。 ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。 この情報は別の手順で使用するため保管しておいてください。
4. ポータルで [設定]、[Administrators] の順に選択します。 [追加] を選択し、共同管理者として新しいユーザーを追加します。 これにより、組織のアカウントで Azure サブスクリプションを管理できるようになります。
5. 最後に、Azure ポータルからログアウトし、新しい組織のアカウントを使用してログインし直します。 初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。

Microsoft Azure での組織アカウントの使用の詳細については、「 [Microsoft Azure への組織としてのサインアップ](../active-directory/sign-up-organization.md)」をご覧ください。

複数のサブスクリプションがあり、特定の&1; つのサブスクリプションを指定して Azure Key Vault を使用する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

    azure account list

使用するサブスクリプションを指定するには、次のように入力します。

    azure account set <subscription name>

Azure クロスプラットフォーム コマンドライン インターフェイスの構成方法については、「 [Azure クロスプラットォーム コマンドライン インターフェイスのインストールと構成方法](../cli-install-nodejs.md)」をご覧ください。

## <a name="switch-to-using-azure-resource-manager"></a>Azure リソース マネージャーの使用に切り替える
Key Vault では Azure リソース マネージャーが必要なため、次のように入力して Azure リソース マネージャー モードに切り替えます。

    azure config mode arm

## <a name="create-a-new-resource-group"></a>新しいリソース グループを作成する
Azure リソース マネージャーを使用すると、すべての関連するリソースが&1; つのリソース グループ内に作成されます。 このチュートリアルでは、'ContosoResourceGroup' という新しいリソース グループを作成します。

    azure group create 'ContosoResourceGroup' 'East Asia'

最初のパラメーターはリソース グループ名で、2 番目のパラメーターは場所です。 場所の場合には、 `azure location list` コマンドを使用して別の場所をこの例の場所に指定する方法を識別します。 詳細情報が必要な場合は、 `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Key Vault リソース プロバイダーの登録
Key Vault リソース プロバイダーがサブスクリプションに登録されていることを確認します。

`azure provider register Microsoft.KeyVault`

これは、サブスクリプションごとに&1; 回だけ実行する必要があります。

## <a name="create-a-key-vault"></a>Key Vault を作成します
`azure keyvault create` コマンドを使用して、Key Vault を作成します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、Key Vault 名、地理的な場所) が含まれています。

たとえば、Key Vault 名に ContosoKeyVault、リソース グループ名に ContosoResourceGroup、場所に東アジアを使用する場合は、次のように入力します。

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

このコマンドの出力は、作成されたばかりの Key Vault のプロパティを示します。 最も重要な&2; つのプロパティは、次のとおりです。

* **Name**: この例では、これは ContosoKeyVault です。 この名前を他の Key Vault コマンドレットに使用できます。
* **vaultUri**: この例では、これは https://contosokeyvault.vault.azure.net です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 まだ、どのユーザーも許可されていません。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>キーやシークレットを Key Vault に追加します
Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、`azure key create` コマンドを使用して次のように入力します。

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

ただし、Azure Key Vault にアップロードする softkey.pem という名前の、ローカル ファイルとして保存された .pem ファイル内に既存のキーがある場合には、キーを .PEM ファイルからインポートするために次のコマンドを入力します。これにより Key Vault サービスでソフトウェアによりキーが保護されます。

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** を使用すると、その特定のバージョンを取得できます。

資格情報コンテナーにシークレットを追加するには (SQLPassword という名前のパスワードで、Azure Key Vault に Pa$$w0rd 値を設定)、次のように入力します。

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** を使用すると、その特定のバージョンを取得できます。

作成したキーやシークレットを表示してみましょう。

* キーを表示するには、次のように入力します。`azure keyvault key list --vault-name 'ContosoKeyVault'`
* シークレットを表示するには、次のように入力します。`azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録します
この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有のものではありませんが、完全を期すために説明します。

> [!IMPORTANT]
> チュートリアルを完了するには、この手順で登録するアカウント、資格情報コンテナー、アプリケーションがすべて同じ Azure ディレクトリに格納されている必要があります。
> 
> 

Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。 これを行うには、アプリケーションの所有者は、まず Azure Active Directory でアプリケーションを登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

* **アプリケーション ID** (クライアント ID とも呼ばれます) と**認証キー** (共有シークレットとも呼ばれます)。 アプリケーションは、トークンを取得するために、この&2; つの値を Azure Active Directory に示す必要があります。 これを行うようにアプリケーションを構成する方法は、アプリケーションによって異なります。 Key Vault のサンプル アプリケーション用に、アプリケーション所有者は app.config ファイルでこれらの値を設定します。

Azure Active Directory にアプリケーションを登録するには:

1. Azure ポータルにサインインします。
2. 左側で、 **[Active Directory]**をクリックし、アプリケーションを登録するディレクトリを選択します。 <br> <br> 注: Key Vault を作成した Azure サブスクリプションが含まれている、同じディレクトリを選択する必要があります。 ディレクトリが不明な場合は、 **[設定]**をクリックし、Key Vault を作成したサブスクリプションを見つけて、最後の列に表示されているディレクトリ名をご確認ください。
3. **[アプリケーション]**をクリックします。 アプリがディレクトリに追加されていない場合は、このページには **[アプリケーションの追加]** リンクのみが表示されます。 リンクをクリックするか、コマンド バーの **[追加]** をクリックします。
4. **アプリケーションの追加**ウィザードの **[実行する作業を選択してください。]** ページで、**[組織で開発中のアプリケーションを追加]** をクリックします。
5. **[アプリケーション情報の指定]** ページで、アプリケーションの名前を指定し、**[Web アプリケーションや Web API]** (既定値) を選択します。 [次へ] アイコンをクリックします。
6. **[アプリのプロパティ]** ページで、**[サインオン URL]** と **[アプリケーション ID/URI]** を Web アプリケーションに指定します。 アプリケーションにこれらの値を設定していない場合は、この手順のための値を作成します (両方のボックスに http://test1.contoso.com を指定するなど)。 これらのサイトが存在するかどうかは関係ありません。各アプリケーションのアプリケーション ID URI がディレクトリ内のすべてのアプリケーションで異なっていることが重要です。 ディレクトリは、この文字列を使用してアプリを識別します。
7. [完了] アイコンをクリックし、ウィザードで変更内容を保存します。
8. [クイック スタート] ページで、 **[構成]**をクリックします。
9. **[キー]** セクションまでスクロールし、期間を選択して **[保存]** をクリックします。 ページが更新され、キーの値が表示されます。 このキーと**クライアント ID** の値を使用してアプリケーションを構成する必要があります  (この構成の手順はアプリケーション固有です)。
10. このページからクライアント ID 値をコピーします。この値は、資格情報コンテナーに権限を設定するために次の手順で使用します

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>キーまたはシークレットを使用してアプリケーションを承認します
資格情報コンテナーのキーやシークレットにアクセスするアプリケーションを承認するには、 `azure keyvault set-policy` コマンドを使用します。

たとえば、資格情報コンテナー名が ContosoKeyVault で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のように実行します。

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

> [!NOTE]
> Windows コマンド プロンプトで実行する場合は、単一引用符を二重引用符に置き換え、内側の二重引用符をエスケープします。 たとえば、"[\"decrypt\",\"sign\"]" のようになります。
> 
> 

その同じアプリケーションを認証し、資格情報コンテナーのシークレットの読み取りを許可する場合、次を実行します。

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>ハードウェア セキュリティ モジュール (HSM) を使用する場合
さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、 [Key Vault と関連するキーとシークレットを削除する](#delete-the-key-vault-and-associated-keys-and-secrets)に進んでください。

これらの HSM で保護されたキーを作成するには、HSM で保護されたキーをサポートする資格情報コンテナーのサブスクリプションが必要です。

資格情報コンテナーを作成するときに、'sku' パラメーターを追加します。

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

この資格情報コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、Destination パラメーターを 'HSM' に設定します。

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

次のコマンドを使用して、自分のコンピューターの .pem ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

次のコマンドは、“bring your own key" (BYOK) パッケージをインポートします。 これを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)」をご覧ください。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Key Vault と関連するキーとシークレットを削除する
Key Vault と、これに含まれるキーやシークレットが不要になった場合は、Azure Key Vault の削除コマンドを使用して Key Vault を削除できます。

    azure keyvault delete --vault-name 'ContosoKeyVault'

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>その他の Azure クロスプラットフォーム コマンドライン インターフェイスのコマンド
Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

このコマンドは、すべてのキーと選択したプロパティを表形式で一覧表示します。

    azure keyvault key list --vault-name 'ContosoKeyVault'

このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧表示します。

    azure keyvault secret list --vault-name 'ContosoKeyVault'

特定のキーを削除する方法の例です。

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

特定のシークレットを削除する方法の例です。

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>次のステップ
プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。


