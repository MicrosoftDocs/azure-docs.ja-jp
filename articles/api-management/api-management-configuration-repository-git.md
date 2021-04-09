---
title: Git を使用した API Management サービスの構成 - Azure | Microsoft Docs
description: Git を使用して API Management サービス構成を保存、構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: mattfarm
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/12/2019
ms.author: apimpm
ms.openlocfilehash: 18cc42c3447de733447c27db52a9a6d664539464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400359"
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Git を使用して API Management サービス構成を保存および構成する方法

各 API Management サービス インスタンスは、サービス インスタンスの構成とメタデータに関する情報が格納されている構成データベースを保持します。 サービス インスタンスへの変更は、Azure Portal の設定変更、PowerShell コマンドレットの使用、または REST API 呼び出しによって行うことができます。 これらの方法のほか、Git を使用してサービス インスタンスの構成を管理することもできます。Git を使用すると、次のようなサービス管理のシナリオが可能になります。

* 構成のバージョン管理 - サービス構成のさまざまなバージョンをダウンロードして保存する
* 構成の一括変更 - ローカル リポジトリ内のサービス構成の複数の部分を変更し、1 回の操作で変更をサーバーに返して統合する
* 慣れ親しんだ Git のツールチェーンとワークフロー - 既に使い慣れた Git ツールとワークフローを使用する

次の図は、API Management サービス インスタンスを構成するためのさまざまな方法の概要を示しています。

![Git configure][api-management-git-configure]

Azure Portal、PowerShell コマンドレット、または REST API を使用してサービスに変更を加える場合は、図の右側に示されているように、サービス構成データベースの管理に `https://{name}.management.azure-api.net` エンドポイントを使用します。 図の左側では、Git を使用してサービス構成を管理する方法を示しています。この場合、`https://{name}.scm.azure-api.net` にあるご利用のサービスの Git リポジトリを使用します。

Git を使用して API Management サービス インスタンスを管理する手順の概要は次のとおりです。

1. サービスの Git 構成にアクセスする
2. サービス構成データベースを Git リポジトリに保存する
3. Git リポジトリをローカル コンピューターに複製する
4. 最新のリポジトリをローカル コンピューターにプルし、変更をコミットしてリポジトリにプッシュする
5. リポジトリからサービス構成データベースに変更をデプロイする

この記事では、Git を有効にして使用し、サービス構成を管理する方法について説明します。また、Git リポジトリ内のファイルとフォルダーに関するリファレンス情報も提供します。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-git-configuration-in-your-service"></a>サービスの Git 構成にアクセスする

Git 構成設定を表示および構成するには、 **[デプロイとインフラストラクチャ]** メニューをクリックし、 **[リポジトリ]** タブに移動できます。

![Enable GIT][api-management-enable-git]

> [!IMPORTANT]
> 名前付きの値として定義されていないシークレットはすべて、リポジトリに格納され、Git アクセスを無効にしてから再度有効にするまで履歴に残ります。 名前付きの値は、すべての API 構成とポリシーの定数文字列値 (シークレットなど) を管理するための安全な場所を提供します。そのため、定数文字列値をポリシー ステートメントに直接格納する必要がありません。 詳細については、「[Azure API Management ポリシーでの名前付きの値の使用方法](api-management-howto-properties.md)」を参照してください。
>
>

REST API を使用して Git アクセスを有効または無効にする方法については、「 [Enable or disable Git access using the REST API (REST API を使用して Git アクセスを有効または無効にする)](/rest/api/apimanagement/2019-12-01/tenantaccess?EnableGit)」を参照してください。

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>サービス構成を Git リポジトリに保存するには

まず、リポジトリを複製する前に、サービス構成の現在の状態をリポジトリに保存します。 **[リポジトリに保存する]** をクリックします。

確認画面で必要な変更を行い、 **[保存]** をクリックして保存します。

しばらくすると構成が保存されます。また、最後に構成を変更した日時、サービス構成とリポジトリの間で最後に行われた同期の日時など、リポジトリの構成状態が表示されます。

構成がリポジトリに保存されたら、そのリポジトリを複製できます。

REST API を使用してこの操作を実行する方法については、「 [Commit configuration snapshot using the REST API (REST API を使用して構成スナップショットをコミットする)](/rest/api/apimanagement/2019-12-01/tenantaccess?CommitSnapshot)」を参照してください。

## <a name="to-clone-the-repository-to-your-local-machine"></a>ローカル コンピューターにリポジトリを複製するには

リポジトリを複製するには、リポジトリの URL、ユーザー名、パスワードが必要です。 ユーザー名と他の資格情報を取得するには、ページの上部にある **[アクセス資格情報]** をクリックします。

パスワードを生成するには、希望する有効期限の日時を **[有効期限]** に設定してから、 **[生成]** をクリックします。

> [!IMPORTANT]
> このパスワードを書き留めておいてください。 このページから移動すると、パスワードが再度表示されることはありません。
>

次の例では [Git for Windows](https://www.git-scm.com/downloads) の Git Bash ツールを使用していますが、使い慣れた Git ツールも使用できます。

Git ツールを目的のフォルダーで開き、次のコマンドを実行して、Git リポジトリをローカル マシンに複製します (コマンドは Azure Portal で提供されます)。

```
git clone https://{name}.scm.azure-api.net/
```

入力を求められたら、ユーザー名とパスワードを入力します。

エラーが発生する場合は、次の例のように、 `git clone` コマンドを変更してユーザー名とパスワードを含めてみてください。

```
git clone https://username:password@{name}.scm.azure-api.net/
```

それでもエラーが発生する場合は、コマンドのパスワード部分をエンコードする URL を試してください。 これを簡単に行う 1 つの方法では、Visual Studio を開き、 **[イミディエイト ウィンドウ]** で次のコマンドを発行します。 **[イミディエイト ウィンドウ]** を開くには、Visual Studio で任意のソリューションまたはプロジェクトを開き (または新しく空のコンソール アプリケーションを作成し)、 **[デバッグ]** メニューから **[ウィンドウ]** 、 **[イミディエイト]** の順に選択します。

```
?System.Net.WebUtility.UrlEncode("password from the Azure portal")
```

エンコードされたパスワードをユーザー名とリポジトリの場所と共に使用して Git コマンドを作成します。

```
git clone https://username:url encoded password@{name}.scm.azure-api.net/
```

リポジトリの複製が完了したら、ローカル ファイル システムのリポジトリを表示して操作できます。 詳細については、「 [ローカル Git リポジトリのファイルとフォルダーの構造のリファレンス](#file-and-folder-structure-reference-of-local-git-repository)」を参照してください。

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>最新のサービス インスタンス構成を使用してローカル リポジトリを更新するには

Azure Portal または REST API で API Management サービス インスタンスに変更を加える場合、これらの変更をリポジトリに保存しておく必要があります。その後、最新の変更を使用してローカル リポジトリを更新できます。 これを行うには、Azure portal の **[リポジトリ]** タブで **[リポジトリに保存する]** をクリックし、ローカル リポジトリで次のコマンドを発行します。

```
git pull
```

`git pull` を実行する前に、ローカル リポジトリのフォルダーに移動してください。 `git clone` コマンドを完了した直後に、次のようなコマンドを使用して、ディレクトリをリポジトリに変更する必要があります。

```
cd {name}.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>ローカル リポジトリからサーバー リポジトリに変更をプッシュするには
ローカル リポジトリからサーバー リポジトリに変更をプッシュするには、変更をコミットした後、サーバー リポジトリにプッシュする必要があります。 変更をコミットするには、Git コマンド ツールを開き、ローカル リポジトリのディレクトリに切り替えて、次のコマンドを発行します。

```
git add --all
git commit -m "Description of your changes"
```

コミットをすべてサーバーにプッシュするには、次のコマンドを実行します。

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>API Management サービス インスタンスにサービス構成の変更をデプロイするには

ローカルの変更をコミットし、サーバー リポジトリにプッシュしたら、これらの変更を API Management サービス インスタンスにデプロイできます。

REST API を使用してこの操作を実行する方法については、「 [Deploy Git changes to configuration database using the REST API (REST API を使用して構成データベースに Git の変更をデプロイする)](/rest/api/apimanagement/2019-12-01/tenantconfiguration)」を参照してください。

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>ローカル Git リポジトリのファイルとフォルダーの構造のリファレンス

ローカル Git リポジトリのファイルとフォルダーには、サービス インスタンスに関する構成情報が含まれています。

| Item | 説明 |
| --- | --- |
| api-management ルート フォルダー |サービス インスタンスの最上位の構成が含まれています |
| apis フォルダー |サービス インスタンス内の API の構成が含まれています |
| groups フォルダー |サービス インスタンス内のグループの構成が含まれています |
| policies フォルダー |サービス インスタンス内のポリシーが含まれています |
| portalStyles フォルダー |サービス インスタンス内の開発者ポータルのカスタマイズに関する構成が含まれています |
| products フォルダー |サービス インスタンス内の製品の構成が含まれています |
| templates フォルダー |サービス インスタンス内の電子メール テンプレートの構成が含まれています |

各フォルダーには 1 つ以上のファイルを含めることができ、場合によっては 1 つ以上のフォルダーも含めることができます。たとえば、各 API、製品、またはグループに 1 つのフォルダーを含めることができます。 各フォルダー内のファイルは、フォルダー名で示されるエンティティの種類に固有です。

| ファイルの種類 | 目的 |
| --- | --- |
| json |各エンティティの構成情報 |
| html |エンティティについての説明 (多くの場合、開発者ポータルに表示されます) |
| xml |ポリシー ステートメント |
| css |開発者ポータルのカスタマイズのスタイル シート |

これらのファイルは、ローカル ファイル システム上で作成、削除、編集、管理できます。また、変更は API Management サービス インスタンスにデプロイして戻すことができます。

> [!NOTE]
> 次のエンティティは、Git リポジトリに含まれないため、Git を使用して構成することはできません。
>
> * [ユーザー](/rest/api/apimanagement/2019-12-01/user)
> * [サブスクリプション](/rest/api/apimanagement/2019-12-01/subscription)
> * 名前付きの値
> * スタイル以外の開発者ポータルのエンティティ
>

### <a name="root-api-management-folder"></a>api-management ルート フォルダー
`api-management` ルート フォルダーには、`configuration.json` ファイルがあります。このファイルには、サービス インスタンスに関する最上位の情報が次の形式で含まれています。

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": "",
    "RequireUserSigninEnabled": "false"
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

最初の 4 つの設定 (`RegistrationEnabled`、`UserRegistrationTerms`、`UserRegistrationTermsEnabled`、`UserRegistrationTermsConsentRequired`) は、 **[開発者ポータル]** セクションの **[ID]** タブにある次の設定に対応します。

| ID の設定 | 対応する設定 |
| --- | --- |
| RegistrationEnabled |**ユーザー名とパスワード** ID プロバイダーの存在 |
| UserRegistrationTerms |**[ユーザー サインアップの使用条件]** ボックス |
| UserRegistrationTermsEnabled |**[サインアップ ページに使用条件を表示する]** チェック ボックス |
| UserRegistrationTermsConsentRequired |**[同意を要求する]** チェック ボックス |
| RequireUserSigninEnabled |**[匿名ユーザーをサインイン ページにリダイレクトする]** チェック ボックス |

次の 4 つの設定 (`DelegationEnabled`、`DelegationUrl`、`DelegatedSubscriptionEnabled`、`DelegationValidationKey`) は、 **[開発者ポータル]** セクションの **[委任]** タブにある次の設定に対応します。

| 委任の設定 | 対応する設定 |
| --- | --- |
| DelegationEnabled |**[サインインとサインアップを委任する]** チェック ボックス |
| DelegationUrl |**[委任エンドポイント URL]** ボックス |
| DelegatedSubscriptionEnabled |**[製品のサブスクリプションを委任する]** チェック ボックス |
| DelegationValidationKey |**[検証キーを委任する]** ボックス |

最後の設定 `$ref-policy`は、サービス インスタンスのグローバル ポリシー ステートメントのファイルに対応します。

### <a name="apis-folder"></a>apis フォルダー
`apis` フォルダーには、サービス インスタンス内の各 API のフォルダーがあります。API のフォルダーには次の項目が含まれます。

* `apis\<api name>\configuration.json` - これは API の構成で、バックエンド サービス URL と操作に関する情報が含まれています。 この情報は、[特定の API の取得](/rest/api/apimanagement/2019-12-01/apis/get)を `application/json` 形式で `export=true` を指定して呼び出した場合に返される情報と同じです。
* `apis\<api name>\api.description.html` - これは API の説明で、[API エンティティ](/java/api/com.microsoft.azure.storage.table.entityproperty)の `description` プロパティに対応します。
* `apis\<api name>\operations\` - このフォルダーには、API での操作に対応する `<operation name>.description.html` ファイルが含まれています。 各ファイルには、API での 1 つの操作の説明が含まれています。この操作は、REST API の[操作エンティティ](/rest/api/visualstudio/operations/list#operationproperties)の `description` プロパティに対応します。

### <a name="groups-folder"></a>groups フォルダー
`groups` フォルダーには、サービス インスタンスで定義された各グループのフォルダーが含まれています。

* `groups\<group name>\configuration.json` - これはグループの構成です。 [特定のグループの取得](/rest/api/apimanagement/2019-12-01/group/get) 操作を呼び出した場合に返される情報と同じです。
* `groups\<group name>\description.html` - これはグループの説明で、[グループ エンティティ](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)の `description` プロパティに対応します。

### <a name="policies-folder"></a>policies フォルダー
`policies` フォルダーには、サービス インスタンスのポリシー ステートメントが含まれています。

* `policies\global.xml` - サービス インスタンスのグローバル スコープで定義されたポリシーが含まれています。
* `policies\apis\<api name>\` - API スコープで定義されたポリシーがある場合は、このフォルダーに含まれます。
* `policies\apis\<api name>\<operation name>\` フォルダー - 操作スコープで定義されたポリシーがある場合は、このフォルダー内の `<operation name>.xml` ファイルに含まれます。これらのファイルは、各操作のポリシー ステートメントに対応します。
* `policies\products\` - 製品スコープで定義されたポリシーがある場合は、このフォルダーに含まれます。このフォルダーには、各製品のポリシー ステートメントに対応する `<product name>.xml` ファイルが含まれています。

### <a name="portalstyles-folder"></a>portalStyles フォルダー
`portalStyles` フォルダーには、開発者ポータルでのサービス インスタンスのカスタマイズに関する構成とスタイル シートが含まれています。

* `portalStyles\configuration.json` - 開発者ポータルで使用されるスタイル シートの名前が含まれています。
* `portalStyles\<style name>.css` - 各 `<style name>.css` ファイルには、開発者ポータル用のスタイルが含まれています (既定では `Preview.css` と `Production.css`)。

### <a name="products-folder"></a>products フォルダー
`products` フォルダーには、サービス インスタンスで定義された各製品のフォルダーが含まれています。

* `products\<product name>\configuration.json` - これは製品の構成です。 [特定の製品の取得](/rest/api/apimanagement/2019-12-01/product/get) 操作を呼び出した場合に返される情報と同じです。
* `products\<product name>\product.description.html` - これは製品の説明で、REST API の[製品エンティティ](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-product-entity)の `description` プロパティに対応します。

### <a name="templates"></a>テンプレート
`templates` フォルダーには、サービス インスタンスの [電子メール テンプレート](api-management-howto-configure-notifications.md) の構成が含まれています。

* `<template name>\configuration.json` - これは電子メール テンプレートの構成です。
* `<template name>\body.html` - これは電子メール テンプレートの本文です。

## <a name="next-steps"></a>次のステップ
サービス インスタンスの他の管理方法については、以下を参照してください。

* 次の PowerShell コマンドレットを使用したサービス インスタンスの管理
  * [Azure API Management Deployment Cmdlets (Azure API Management のデプロイ コマンドレット)](/powershell/module/wds)
  * [Azure API Management Service Management Cmdlets (Azure API Management のサービス管理コマンドレット)](/powershell/azure/servicemanagement/overview)
* REST API を使用したサービス インスタンスの管理
  * [API Management REST (API Management REST)](/rest/api/apimanagement/)


[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png
