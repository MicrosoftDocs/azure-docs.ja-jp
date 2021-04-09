---
title: 2 セットの資格情報を使用したリソースのローテーションのチュートリアル
description: このチュートリアルでは、2 セットの認証資格情報を使用するリソースを対象に、シークレットのローテーションを自動化する方法について学習します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: e7e63ea56edc2b76383ee4c034fd39dd8b8259c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786007"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>2 セットの認証資格情報があるリソースを対象にシークレットのローテーションを自動化する

Azure サービスに対する認証を行う最善の方法は[マネージド ID](../general/authentication.md) を使用することですが、この方法を選択できないシナリオもあります。 このような場合は、アクセス キーまたはパスワードが使用されます。 アクセス キーとパスワードは頻繁にローテーションする必要があります。

このチュートリアルでは、2 セットの認証資格情報を使用するデータベースとサービスを対象に、シークレットの定期的なローテーションを自動化する方法について説明します。 具体的には、Azure Key Vault にシークレットとして格納されている Azure ストレージ アカウント キーをローテーションする方法について説明します。 Azure Event Grid の通知によってトリガーされる関数を使用します。 

> [!NOTE]
> ストレージ アカウントに対する委任アクセス用の共有アクセス署名トークンを指定すると、Key Vault でストレージ アカウント キーを自動的に管理できます。 アクセス キーと共にストレージ アカウントの接続文字列を必要とするサービスがあります。 そのようなシナリオで、このソリューションは推奨されます。

このチュートリアルで説明するローテーション ソリューションを次に示します。 

![ローテーション ソリューションを示す図。](../media/secrets/rotation-dual/rotation-diagram.png)

このソリューションでは、ストレージ アカウントの個々のアクセス キーが、同じシークレットの別バージョンとして Azure Key Vault に格納され、プライマリとセカンダリのキーが後続のバージョンで交互に入れ替わります。 最新バージョンのシークレットにアクセス キーが格納されるときは、代替キーが再生成されて、新たな最新バージョンのシークレットとして Key Vault に追加されます。 このソリューションによって、再生成された最新のキーに更新するための完全なローテーション サイクルがアプリケーションで実現します。 

1. シークレットの有効期限が切れる 30 日前に、Key Vault から "有効期限が近づいている" ことを示すイベントが Event Grid に発行されます。
1. Event Grid でイベント サブスクリプションが確認され、HTTP POST を使用して、このイベントをサブスクライブしている関数アプリ エンドポイントが呼び出されます。
1. 関数アプリによって代替キー (最新ではない方) が特定され、ストレージ アカウントが呼び出されてキーが再生成されます。
1. 関数アプリによって、再生成された新しいキーが新しいバージョンのシークレットとして Azure Key Vault に追加されます。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 [無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/)。 このチュートリアルでは、PowerShell 環境でポータル Cloud Shell を使用しています
* Azure Key Vault。
* 2 つの Azure ストレージ アカウント。

まだキー コンテナーとストレージ アカウントをお持ちでない場合は、このデプロイ リンクを使用してください。

[!["Azure へのデプロイ" と表示されたリンク。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. **[リソース グループ]** で、 **[新規作成]** を選択します。 このグループに **vaultrotation** という名前を付けて **[OK]** を選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

    ![リソース グループの作成方法を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-1.png)

これで 1 つのキー コンテナーと 2 つのストレージ アカウントが作成されます。 この設定は、Azure CLI でこのコマンドを実行して検証できます。

```azurecli
az resource list -o table -g vaultrotation
```

結果として得られる出力は次のようになります。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>キー ローテーション関数を作成してデプロイする

次に、システム マネージド ID を使用する関数アプリと、その他の必須コンポーネントを作成します。 また、ストレージ アカウント キーのローテーション関数もデプロイします。

関数アプリのローテーション関数には、次のコンポーネントおよび構成が必要です。
- Azure App Service プラン
- 関数アプリのトリガーを管理するためのストレージ アカウント
- Key Vault 内のシークレットにアクセスするためのアクセス ポリシー
- ストレージ アカウントのアクセス キーにアクセスできるよう関数アプリに割り当てられるストレージ アカウント キー オペレーターのサービス ロール
- キー ローテーション関数とイベント トリガーおよび HTTP トリガー (オンデマンド ローテーション)
- **SecretNearExpiry** イベントの Event Grid イベント サブスクリプション

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![Azure テンプレートのデプロイのリンク。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. **[リソース グループ]** 一覧から **[vaultrotation]** を選択します。
1. **[Storage Account RG]\(ストレージ アカウント RG\)** ボックスに、自分のストレージ アカウントがあるリソース グループの名前を入力します。 キー ローテーション関数のデプロイ先と同じリソース グループにストレージ アカウントが既にある場合は、既定値の **[resourceGroup().name]** のままにしてください。
1. **[ストレージ アカウント名]** ボックスに、ローテーションするアクセス キーを含んだストレージ アカウントの名前を入力します。 「[前提条件](#prerequisites)」で作成したストレージ アカウントを使用する場合は、既定値 **[concat(resourceGroup().name, 'storage')]** をそのまま使用します。
1. **[Key Vault RG]\(キー コンテナー RG\)** ボックスに、自分のキー コンテナーがあるリソース グループの名前を入力します。 キー ローテーション関数のデプロイ先と同じリソース グループに、キー コンテナーが既にある場合は、既定値の **[resourceGroup().name]** のままにしてください。
1. **[Key Vault 名]** ボックスに、キー コンテナーの名前を入力します。 「[前提条件](#prerequisites)」で作成したキー コンテナーを使用する場合は、既定値 **[concat(resourceGroup().name, '-kv')]** をそのまま使用します。
1. **[App Service Plan Type]\(App Service プランの種類\)** ボックスで、ホスティング プランを選択します。 **Premium プラン** は、キー コンテナーがファイアウォールの背後にある場合にのみ必要です。
1. **[関数アプリ名]** ボックスに、関数アプリの名前を入力します。
1. **[シークレット名]** ボックスに、アクセス キーの保存先となるシークレットの名前を入力します。
1. **[Repo Url]\(リポジトリの URL\)** ボックスに、関数コードがある GitHub の場所を入力します。 このチュートリアルでは、 **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** を使用できます。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

   ![関数を作成してデプロイする方法を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-2.png)

上記の手順を完了すると、ストレージ アカウント、サーバー ファーム、関数アプリ、Application Insights を使用できるようになります。 デプロイが完了すると、このページが表示されます。

   !["Your deployment is complete (デプロイが完了しました)" ページのスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> エラーが発生した場合、 **[再デプロイ]** を選択して、コンポーネントのデプロイを完了できます。


デプロイ テンプレートとローテーション関数のコードは、[Azure のサンプル](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell)に関するページにあります。

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>ストレージ アカウントのアクセス キーを Key Vault に追加する

まず、**シークレットの管理** 権限をユーザー プリンシパルに付与するようにアクセス ポリシーを設定します。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```

これで、ストレージ アカウントのアクセス キーを値とする新しいシークレットを作成できます。 さらに、ローテーション関数でストレージ アカウントにキーを再生成できるよう、ストレージ アカウントのリソース ID、シークレットの有効期間、シークレットに追加するキーの ID も必要となります。

ストレージ アカウントのリソース ID を調べます。 この値は、`id` プロパティで確認できます。

```azurecli
az storage account show -n vaultrotationstorage
```

キーの値を取得できるよう、ストレージ アカウントのアクセス キーを一覧表示します。

```azurecli
az storage account keys list -n vaultrotationstorage 
```

有効期限を明日、有効期間を 60 日間に設定し、ストレージ アカウントのリソース ID を指定して、キー コンテナーにシークレットを追加します。取得した `key1Value` と `storageAccountResourceId` の値を使用して、このコマンドを実行します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

上記のシークレットでは、数分以内に `SecretNearExpiry` イベントがトリガーされます。 このイベントによって関数がトリガーされ、有効期限が 60 日に設定されたシークレットがローテーションされます。 この構成では、"SecretNearExpiry" イベントが 30 日ごと (有効期限の 30 日前) にトリガーされ、ローテーション関数によって key1 と key2 が交互にローテーションされます。

アクセス キーが再生成されたことは、ストレージ アカウント キーと Key Vault のシークレットを取得して比較することで確認できます。

シークレットの情報を得るには、このコマンドを使用します。
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```

`CredentialId` が代替の `keyName` に更新され、`value` が再生成されたことがわかります。

![1 つ目のストレージ アカウントに対する a z keyvault secret show コマンドの出力を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-4.png)

アクセス キーを取得して値を比較します。
```azurecli
az storage account keys list -n vaultrotationstorage 
```
キーの `value` がキー コンテナー内のシークレットと同じであることに注意してください。

![1 つ目のストレージ アカウントに対する a z storage account keys list コマンドの出力を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>ローテーションの対象となるストレージ アカウントを追加する

複数のストレージ アカウントを対象に、同じ関数アプリを再利用してキーのローテーションを行うことができます。 

ローテーションするストレージ アカウント キーを既存の関数に追加するには、以下が必要です。
- ストレージ アカウントのアクセス キーにアクセスできるよう関数アプリに割り当てられるストレージ アカウント キー オペレーターのサービス ロール。
- **SecretNearExpiry** イベントの Event Grid イベント サブスクリプション。

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![Azure テンプレートのデプロイのリンク。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. **[リソース グループ]** 一覧から **[vaultrotation]** を選択します。
1. **[Storage Account RG]\(ストレージ アカウント RG\)** ボックスに、自分のストレージ アカウントがあるリソース グループの名前を入力します。 キー ローテーション関数のデプロイ先と同じリソース グループにストレージ アカウントが既にある場合は、既定値の **[resourceGroup().name]** のままにしてください。
1. **[ストレージ アカウント名]** ボックスに、ローテーションするアクセス キーを含んだストレージ アカウントの名前を入力します。
1. **[Key Vault RG]\(キー コンテナー RG\)** ボックスに、自分のキー コンテナーがあるリソース グループの名前を入力します。 キー ローテーション関数のデプロイ先と同じリソース グループに、キー コンテナーが既にある場合は、既定値の **[resourceGroup().name]** のままにしてください。
1. **[Key Vault 名]** ボックスに、キー コンテナーの名前を入力します。
1. **[関数アプリ名]** ボックスに、関数アプリの名前を入力します。
1. **[シークレット名]** ボックスに、アクセス キーの保存先となるシークレットの名前を入力します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します

   ![追加のストレージ アカウントを作成する方法を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>別のストレージ アカウントのアクセス キーを Key Vault に追加する

ストレージ アカウントのリソース ID を調べます。 この値は、`id` プロパティで確認できます。
```azurecli
az storage account show -n vaultrotationstorage2
```

キー 2 の値を取得できるよう、ストレージ アカウントのアクセス キーを一覧表示します。

```azurecli
az storage account keys list -n vaultrotationstorage2 
```

有効期限を明日、有効期間を 60 日間に設定し、ストレージ アカウントのリソース ID を指定して、キー コンテナーにシークレットを追加します。取得した `key2Value` と `storageAccountResourceId` の値を使用して、このコマンドを実行します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

シークレットの情報を得るには、このコマンドを使用します。
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```

`CredentialId` が代替の `keyName` に更新され、`value` が再生成されたことがわかります。

![2 つ目のストレージ アカウントに対する a z keyvault secret show コマンドの出力を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-8.png)

アクセス キーを取得して値を比較します。
```azurecli
az storage account keys list -n vaultrotationstorage 
```

キーの `value` がキー コンテナー内のシークレットと同じであることに注意してください。

![2 つ目のストレージ アカウントに対する a z storage account keys list コマンドの出力を示すスクリーンショット。](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>2 組の資格情報を使用する Key Vault ローテーション関数

2 組の資格情報用のローテーション関数と、すぐに使用できるいくつかの関数:

- [プロジェクト テンプレート](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [ストレージ アカウント](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> 上記のローテーション関数は、Microsoft ではなく、コミュニティのメンバーによって作成されています。 コミュニティの Azure 関数は、Microsoft サポート プログラムまたはサービスのサポート対象ではなく、手を加えずに提供され、いかなる保証もありません。

## <a name="next-steps"></a>次のステップ

- チュートリアル:[1 組の資格情報を使用するシークレットのローテーション](./tutorial-rotation.md)
- 概要:[Azure Event Grid での Key Vault の監視](../general/event-grid-overview.md)
- 方法:[Azure portal で初めての関数を作成する](../../azure-functions/functions-get-started.md)
- 方法: [Key Vault シークレットが変更されたときにメールを受信する](../general/event-grid-logicapps.md)
- リファレンス: [Azure Key Vault 用の Azure Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)