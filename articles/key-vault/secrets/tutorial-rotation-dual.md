---
title: 2 セットの資格情報を使用したリソースのローテーションのチュートリアル
description: このチュートリアルでは、2 セットの認証資格情報を使用するリソースを対象に、シークレットのローテーションを自動化する方法について学習します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: b9478d3b171189decb4e2cca7fc93ba2fa75e32e
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482784"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>2 セットの認証資格情報を使用したリソースを対象にシークレットのローテーションを自動化する

Azure サービスに対する認証を行う最善の方法は[マネージド ID](../general/authentication.md) を使用することですが、この方法を選択できないシナリオもあります。 このような場合は、アクセス キーまたはパスワードが使用されます。 アクセス キーとパスワードはたびたびローテーションする必要があります。

このチュートリアルでは、2 セットの認証資格情報を使用するデータベースとサービスを対象に、シークレットの定期的なローテーションを自動化する方法について説明します。 具体的には、Azure Event Grid の通知によってトリガーされる関数を使用して、Azure Key Vault にシークレットとして格納されている Azure ストレージ アカウント キーをローテーションします。 :

> [!NOTE]
> Key Vault は、ストレージ アカウントに対する委任アクセス用の共有アクセス署名トークンを指定することで、ストレージ アカウント キーを自動的に管理することができます。 アクセス キーと共にストレージ アカウントの接続文字列が必要とされるサービスもあり、そのようなシナリオでは、このソリューションが推奨されます。

![ローテーション ソリューションの図](../media/secrets/rotation-dual/rotation-diagram.png)

上のソリューションでは、Azure Key Vault がストレージ アカウントの個々のアクセス キーを、同じシークレットの異なるバージョンとして格納し、後続のバージョンでは、プライマリ キーとセカンダリ キーが交互に入れ替わることになります。 最新バージョンのシークレットに格納されるアクセス キーは 1 つなので、代替キーが再生成されて、新たな最新バージョンのシークレットとして Key Vault に追加されます。 このソリューションによって、再生成された最新のキーに更新するための完全なローテーション サイクルがアプリケーションに提供されます。 

1. シークレットの有効期限が切れる 30 日前に、Key Vault から "有効期限が近づいている" ことを示すイベントが Event Grid に発行されます。
1. Event Grid がイベント サブスクリプションを確認し、HTTP POST を使用して、このイベントをサブスクライブしている関数アプリ エンドポイントを呼び出します。
1. 関数アプリが代替キー (最新ではないキー) を特定し、ストレージ アカウントを呼び出してキーを再生成します。
1. 関数アプリが、再生成された新しいキーを新しいバージョンのシークレットとして Azure Key Vault に追加します。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Key Vault
* 2 つの Azure ストレージ アカウント

まだ Key Vault とストレージ アカウントをお持ちでない場合は、以下のデプロイ リンクを使用してください。

[![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **[リソース グループ]** で、 **[新規作成]** を選択します。 このグループに **akvrotation** という名前を付けて **[OK]** をクリックします。
1. **[確認および作成]** を選択します。
1. **[作成]**

    ![リソース グループを作成する](../media/secrets/rotation-dual/dual-rotation-1.png)

これで 1 つの Key Vault と 2 つのストレージ アカウントが作成されます。 このセットアップは、Azure CLI から次のコマンドを実行して検証できます。

```azurecli
az resource list -o table -g akvrotation
```

次のような出力結果が得られます。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>ストレージ アカウント キーのローテーション関数を作成してデプロイする

次に、システムマネージド ID を使用して関数アプリとその他の必須コンポーネントを作成し、ストレージ アカウント キーのローテーション関数をデプロイします。

関数アプリのローテーション関数には、次のコンポーネントおよび構成が必要となります。
- Azure App Service プラン
- 関数アプリのトリガーの管理に必要なストレージ アカウント
- Key Vault 内のシークレットにアクセスするためのアクセス ポリシー
- ストレージ アカウントのアクセス キーにアクセスする関数アプリにストレージ アカウント キー オペレーターのサービス ロールを割り当てる
- ストレージ アカウント キーのローテーション関数とイベント トリガーおよび HTTP トリガー (オンデマンド ローテーション)
- **SecretNearExpiry** イベントの EventGrid イベント サブスクリプション

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **[リソース グループ]** 一覧から **[akvrotation]** を選択します。
1. ローテーションするアクセス キーがあるストレージ アカウントの名前を **[ストレージ アカウント名]** に入力します。
1. **[Key Vault 名]** に、Key Vault の名前を入力します。
1. **[関数アプリ名]** に関数アプリの名前を入力します。
1. **[シークレット名]** に、アクセス キーが格納されるシークレットの名前を入力します。
1. **[Repo Url]\(リポジトリの URL\)** に、関数コードがある GitHub の場所 ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ) を入力します。
1. **[確認および作成]** を選択します。
1. **[作成]**

   ![最初のストレージ アカウントを確認して作成します](../media/secrets/rotation-dual/dual-rotation-2.png)

上記の手順を完了すると、ストレージ アカウント、サーバー ファーム、関数アプリ、Application Insights を使用できるようになります。 デプロイが完了すると、以下の画面が表示されます。![デプロイ完了](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> エラーが発生した場合は、 **[再デプロイ]** をクリックすれば、残りのコンポーネントのデプロイを完了できます。


デプロイ テンプレートとローテーション関数のコードは、[GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell) にあります。

## <a name="add-storage-account-access-key-to-key-vault"></a>ストレージ アカウントのアクセス キーを Key Vault に追加する

まず、"*シークレットの管理*" 権限をユーザーに付与するアクセス ポリシーを設定します。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

これで、ストレージ アカウントのアクセス キーを値として新しいシークレットを作成することができます。 さらに、ローテーション関数でストレージ アカウントにキーを再生成できるよう、ストレージ アカウントのリソース ID、シークレットの有効期間、シークレットに追加するキーの ID が必要となります。

ストレージ アカウントのリソース ID を取得します。 値は `id` プロパティで確認できます。
```azurecli
az storage account show -n akvrotationstorage
```

ストレージ アカウントのアクセス キーを一覧表示して、キーの値を取得します。

```azurecli
az storage account keys list -n akvrotationstorage 
```

取得した値を **key1Value** と **storageAccountResourceId** に反映します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

有効期限が短いシークレットを作成すると、数分以内に `SecretNearExpiry` イベントが発行されます。これにより、関数がトリガーされてシークレットがローテーションされます。

アクセス キーが再生成されたことは、ストレージ アカウント キーと Key Vault のシークレットを取得して比較することで確認できます。

シークレット情報は、次のコマンドを使用して表示できます。
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
`CredentialId` が別の `keyName` に更新され、`value` が再生成されたことに注意してください ![最初のストレージ アカウントに対する az keyvault secret show の出力](../media/secrets/rotation-dual/dual-rotation-4.png)

アクセス キーを取得して値を確認します。
```azurecli
az storage account keys list -n akvrotationstorage 
```
![最初のストレージ アカウントに対する az storage account keys list の出力](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>ローテーションに使用するストレージ アカウントをもう 1 つ追加する

同じ関数アプリを再利用して、複数のストレージ アカウントをローテーションすることができます。 

ローテーションに使用するもう 1 つのストレージ アカウント キーを既存の関数に追加するための要件は次のとおりです。
- ストレージ アカウントのアクセス キーにアクセスする関数アプリにストレージ アカウント キー オペレーターのサービス ロールを割り当てる
- **SecretNearExpiry** イベントの EventGrid イベント サブスクリプション

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. **[リソース グループ]** 一覧から **[akvrotation]** を選択します。
1. ローテーションするアクセス キーがあるストレージ アカウントの名前を **[ストレージ アカウント名]** に入力します。
1. **[Key Vault 名]** に、Key Vault の名前を入力します。
1. **[関数アプリ名]** に関数アプリの名前を入力します。
1. **[シークレット名]** に、アクセス キーが格納されるシークレットの名前を入力します。
1. **[確認および作成]** を選択します。
1. **[作成]**

   ![2 つ目のストレージ アカウントを確認して作成します](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>もう 1 つのストレージ アカウントのアクセス キーを Key Vault に追加する

ストレージ アカウントのリソース ID を取得します。 値は `id` プロパティで確認できます。
```azurecli
az storage account show -n akvrotationstorage2
```

ストレージ アカウントのアクセス キーを一覧表示して、キー 2 の値を取得します。

```azurecli
az storage account keys list -n akvrotationstorage2 
```

取得した値を **key2Value** と **storageAccountResourceId** に反映します。

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

次のコマンドを使用してシークレット情報を表示します。
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
`CredentialId` が別の `keyName` に更新され、`value` が再生成されたことに注意してください ![2 つ目のストレージ アカウントに対する az keyvault secret show の出力](../media/secrets/rotation-dual/dual-rotation-8.png)

アクセス キーを取得して値を確認します。
```azurecli
az storage account keys list -n akvrotationstorage 
```
![2 つ目のストレージ アカウントに対する az storage account keys list の出力](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>公開されている Key Vault デュアル資格情報ローテーション関数

- [ストレージ アカウント](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>詳細情報
- 概要:[Azure Event Grid での Key Vault の監視 (プレビュー)](../general/event-grid-overview.md)
- 方法:[Azure portal で初めての関数を作成する](../../azure-functions/functions-create-first-azure-function.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](../general/event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)](../../event-grid/event-schema-key-vault.md)
