---
title: "Azure CLI での Azure アプリ ID の作成 | Microsoft Docs"
description: "Azure CLI を使用して、Azure Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。 パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する

リソースへのアクセスを必要とするアプリやスクリプトがある場合は、アプリの ID を設定し、アプリを独自の資格情報で認証できます。 この ID は、サービス プリンシパルと呼ばれます。 このアプローチを使用すると、以下のことを実行できます。

* ユーザー自身のアクセス許可とは異なるアクセス許可を、アプリケーション ID に割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* 無人インストール用スクリプトを実行するときに、証明書を使用して認証できます。

この記事では、[Azure CLI 1.0](../cli-install-nodejs.md) を使用してアプリケーションをその独自の資格情報と ID で実行する設定方法について説明します。 [Azure CLI 1.0](../cli-install-nodejs.md) の最新バージョンをインストールし、自分の環境がこの記事の例と一致するかどうかを確認します。

## <a name="required-permissions"></a>必要なアクセス許可
このトピックを完了するには、Azure Active Directory と Azure サブスクリプションの両方で適切なアクセス許可を持っている必要があります。 具体的には、Azure Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。 

自分のアカウントに適切なアクセス許可があるかどうかを確認する最も簡単な方法は、ポータルを使用することです。 [ポータルでの必要なアクセス許可のチェック](resource-group-create-service-principal-portal.md#required-permissions)に関するページをご覧ください。

ここで、[パスワード](#create-service-principal-with-password)認証または[証明書](#create-service-principal-with-certificate)認証に関するセクションに進みます。

## <a name="create-service-principal-with-password"></a>パスワードを使用したサービス プリンシパルの作成
このセクションでは、パスワードを使用して AD アプリケーションを作成し、閲覧者ロールをサービス プリンシパルに割り当てる手順を実行します。

1. ご使用のアカウントにサインインします。
   
   ```azurecli
   azure login
   ```
2. アプリ ID を作成するには、次のコマンドに示すように、アプリケーションの名前とパスワードを指定します。
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   新しいサービス プリンシパルが返されます。 アクセス許可を付与する場合は、オブジェクト ID が必要です。 ログイン時には、示されている GUID とサービス プリンシパル名が必要となります。 この GUID は、アプリケーション ID と同じ値です。 サンプル アプリケーションでは、この値は `Client ID` と呼ばれます。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. サブスクリプションに対する権限をサービス プリンシパルに付与します。 この例では、サブスクリプション内のすべてのリソースを読み取る権限である閲覧者ロールを、サービス プリンシパルに付与します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。 objectid パラメーターには、アプリケーションの作成時に使用した Object ID を指定します。 このコマンドを実行する前に、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設ける必要が必要があります。 これらのコマンドを手動で実行した場合、通常はタスクとタスクの間に十分な時間が経過しています。 スクリプトでは、コマンドとコマンドの間にスリープのための手順 (`sleep 15` など) を追加してください。 プリンシパルがディレクトリに存在しないことを示すエラーが表示された場合は、コマンドを再実行してください。
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。 次のセクションでは、Azure CLI から資格情報を使ってログインする方法を紹介します。 コード アプリケーションの資格情報を使用する場合は、このトピックを続行する必要はありません。 「 [サンプル アプリケーション](#sample-applications) 」に進んで、アプリケーション ID とパスワードでログインする例をご覧ください。 

### <a name="provide-credentials-through-azure-cli"></a>資格情報を Azure CLI で渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。 現在認証されているサブスクリプションのテナント ID を取得するには、次のコマンドを使用します。
   
   ```azurecli
   azure account show
   ```
   
   次のような結果が返されます。
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     別のサブスクリプションのテナント ID を取得する必要がある場合は、次のコマンドを使用します。
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. ログインに使用するクライアント ID を取得する場合は、次のコマンドを使用します。
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     ログインに使用する値は、サービス プリンシパル名に示されている GUID です。
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. サービス プリンシパルとしてログインします。
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    パスワードを入力するように求められます。 AD アプリケーションの作成時に指定したパスワードを使用します。
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

これで、作成したサービス プリンシパルのサービス プリンシパルとして認証されました。

また、コマンドラインからの REST 操作を呼び出して、ログインすることもできます。 認証の応答から、その他の操作で使用するためのアクセス トークンを取得できます。 REST 操作を呼び出してアクセス トークンを取得する例については、「[Generating an Access Token (アクセス トークンの生成)](resource-manager-rest-api.md#generating-an-access-token)」を参照してください。

## <a name="create-service-principal-with-certificate"></a>証明書を使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* 自己署名証明書を作成する
* 証明書を使用する AD アプリケーションとサービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順を完了するには、 [OpenSSL](http://www.openssl.org/) がインストールされている必要があります。

1. 自己署名証明書を作成します。
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. 前の手順では、privkey.pem と cert.pem の 2 つのファイルを作成しました。 パブリック キーとプライベート キーを 1 つのファイルに結合します。

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. **examplecert.pem** ファイルを開き、**-----BEGIN CERTIFICATE-----** と **-----END CERTIFICATE-----** の間の長い文字のシーケンスを探します。 証明書データをコピーします。 サービス プリンシパルを作成するときに、このデータをパラメーターとして渡します。

4. ご使用のアカウントにサインインします。

   ```azurecli
   azure login
   ```
5. サービス プリンシパルを作成するには、次のコマンドに示すように、アプリケーションの名前と証明書データを指定します。
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   新しいサービス プリンシパルが返されます。 アクセス許可を付与する場合は、オブジェクト ID が必要です。 ログイン時には、示されている GUID とサービス プリンシパル名が必要となります。 この GUID は、アプリケーション ID と同じ値です。 サンプル アプリケーションでは、この値はクライアント ID と呼ばれます。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. サブスクリプションに対する権限をサービス プリンシパルに付与します。 この例では、サブスクリプション内のすべてのリソースを読み取る権限である閲覧者ロールを、サービス プリンシパルに付与します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。 objectid パラメーターには、アプリケーションの作成時に使用した Object ID を指定します。 このコマンドを実行する前に、新しいサービス プリンシパルが Azure Active Directory 全体に反映されるまでの時間を設ける必要が必要があります。 これらのコマンドを手動で実行した場合、通常はタスクとタスクの間に十分な時間が経過しています。 スクリプトでは、コマンドとコマンドの間にスリープのための手順 (`sleep 15` など) を追加してください。 プリンシパルがディレクトリに存在しないことを示すエラーが表示された場合は、コマンドを再実行してください。
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>自動化された Azure CLI スクリプトから証明書を渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Azure Active Directory のインスタンスです。 現在認証されているサブスクリプションのテナント ID を取得するには、次のコマンドを使用します。
   
   ```azurecli
   azure account show
   ```
   
   次のような結果が返されます。
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   別のサブスクリプションのテナント ID を取得する必要がある場合は、次のコマンドを使用します。
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 証明書の拇印を取得し、不要な文字を削除するには、次のコマンドを使用します。
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   次のような拇印の値が返されます。
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. ログインに使用するクライアント ID を取得する場合は、次のコマンドを使用します。
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   ログインに使用する値は、サービス プリンシパル名に示されている GUID です。
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. サービス プリンシパルとしてログインします。
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

これで、作成した Azure Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## <a name="change-credentials"></a>資格情報の変更

セキュリティ侵害の発生または資格情報の期限切れのために AD アプリの資格情報を変更するには、`azure ad app set` を使用します。

パスワードを変更するには、次のコマンドを使用します。

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

証明書の値を変更するには、次のコマンドを使用します。

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>デバッグ

サービス プリンシパルの作成時に、以下のエラーが発生することがあります。

* **"Authentication_Unauthorized"** または **"コンテキストにサブスクリプトが見つかりません"** - アカウントが Azure Active Directory でアプリを登録するために[必要なアクセス許可](#required-permissions)を持っていない場合に、このエラーが表示されます。 通常は、Azure Active Directory の管理者ユーザーのみがアプリを登録できるときに、自分のアカウントが管理者でない場合に、このエラーが発生します。 管理者に連絡して、自分を管理者ロールに割り当ててもらうか、ユーザーがアプリケーションを登録できるようにしてもらいます。

* アカウントに**「'/subscriptions/{guid} ' をスコープとした 'Microsoft.Authorization/roleAssignments/write' のアクションを実行するためのアクセス権限がありません」:**このエラーは、自分のアカウントが ID にロールを割り当てるのに十分なアクセス許可を持っていない場合に表示されます。 サブスクリプション管理者に連絡して、自分をユーザー アクセス管理者ロールに追加してもらいます。

## <a name="sample-applications"></a>サンプル アプリケーション
さまざまなプラットフォームからアプリケーションとしてログインする方法については、以下を参照してください。

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.JS](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>次のステップ
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* 証明書と Azure CLI の使用方法の詳細については、「 [Certificate-based authentication with Azure Service Principals from Linux command line (Linux コマンド ラインからの Azure サービス プリンシパルの証明書での認証)](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)」を参照してください。 
* ユーザーに対して許可または拒否される場合がある使用可能なアクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../active-directory/role-based-access-control-resource-provider-operations.md)」を参照してください。

