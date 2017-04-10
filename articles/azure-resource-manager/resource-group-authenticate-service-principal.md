---
title: "PowerShell での Azure アプリ ID の作成 | Microsoft Docs"
description: "Azure PowerShell を使用して、Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。 パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 31495f402b810c524bd7b906498774302500b732
ms.lasthandoff: 01/24/2017


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [ポータル](resource-group-create-service-principal-portal.md)
> 
> 

リソースへのアクセスを必要とするアプリケーションやスクリプトがある場合は、そのアプリの ID を設定し、それをお客様自身の資格情報で認証できます。 この方法は、お客様自身の資格情報でアプリを実行するよりも推奨されます。

* お客様自身のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可は、アプリが行う必要があることに制限されます。
* お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。 
* 無人インストール用スクリプトを実行するときに、証明書を使用して認証を自動化できます。

このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定をすべて [Azure PowerShell](/powershell/azureps-cmdlets-docs) で行う方法を紹介しています。

PowerShell では、AD アプリケーションの認証に次の&2; つの選択肢を利用できます。

* パスワード
* 証明書

このトピックでは、PowerShell でのこれらの選択肢の使用方法について説明します。 プログラミング フレームワーク (Python、Ruby、Node.js など) から Azure にログインする場合は、パスワード認証が最適な選択肢と考えられます。 パスワードと証明書のどちらを使用するか決める前に、「 [サンプル アプリケーション](#sample-applications) 」セクションで、各種フレームワークにおける認証の例を参照してください。

## <a name="active-directory-concepts"></a>Active Directory の概念
この記事では、Active Directory (AD) アプリケーションとサービス プリンシパルという&2; つのオブジェクトを作成します。 AD アプリケーションは、アプリケーションをグローバルに表現したものです。 資格情報としてアプリケーション ID と、パスワードまたは証明書のいずれかを保持します。 サービス プリンシパルは、Active Directory のアプリケーションをローカルに表現したものです。 こちらは、ロールの割り当てを保持します。 このトピックでは、シングル テナント アプリケーション (単一組織内でのみ実行するように意図されたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。 シングルテナント アプリケーションでは、1 つの AD アプリケーションと&1; つのサービス プリンシパルを設定します。

両方のオブジェクトが必要になるわけを疑問に思う方もいるでしょう。 このような方法の利点は、マルチテナント アプリケーションについて考えるとはっきりします。 通常、マルチテナント アプリケーションは、さまざまなサブスクリプションでアプリケーションが実行されるサービスとしてのソフトウェア (SaaS) アプリケーションで使用します。 マルチテナント アプリケーションでは、1 つの AD アプリケーションと複数のサービス プリンシパル (1 つは Active Directory でアプリケーションにアクセスを付与するためのもの) を設定できます。 マルチテナント アプリケーションのセットアップについては、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## <a name="required-permissions"></a>必要なアクセス許可
このトピックを完了するには、Azure Active Directory と Azure サブスクリプションの両方で適切なアクセス許可を持っている必要があります。 具体的には、Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。 

自分のアカウントに適切なアクセス許可があるかどうかを確認する最も簡単な方法は、ポータルを使用することです。 [必要なアクセス許可のチェック](resource-group-create-service-principal-portal.md#required-permissions)に関するページを参照してください。

ここで、[パスワード](#create-service-principal-with-password)認証または[証明書](#create-service-principal-with-certificate)認証に関するセクションに進みます。

## <a name="create-service-principal-with-password"></a>パスワードを使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* パスワードを指定して AD アプリケーションを作成する
* サービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順をすぐに実行するには、次のコマンドレットを参照してください。

```powershell
$app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

スクリプトは、新しいサービス プリンシパルが Active Directory 全体に反映されるまでの時間を設けるために、15 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {id} がディレクトリにありません" というエラーが表示されます。 このエラーが表示された場合は、ロールに割り当てるコマンドレットを再実行できます。

それでは、各プロセスについて理解できるように、これらの手順についてより細かく見ていきましょう。

1. ご使用のアカウントにサインインします。
   
   ```powershell
   Login-AzureRmAccount
   ```

2. 表示名、アプリケーションを説明する URI、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定して、新しい Active Directory アプリケーションを作成します。

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "{Your_Password}"
   ```

     シングルテナント アプリケーションでは、URI の検証は行われません。
   
     Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
3. 新しいアプリケーション オブジェクトを調べます。 
   
   ```powershell
   $app
   ```
   
     特に、サービス プリンシパルの作成、ロールの割り当て、アクセス トークンの取得に必要な `ApplicationId` プロパティがあることを確認します。
   
   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```

5. サブスクリプションに対する権限をサービス プリンシパルに付与します。 この例では、サブスクリプション内のすべてのリソースを読み取る権限である閲覧者ロールを、サービス プリンシパルに付与します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。 `ServicePrincipalName` パラメーターには、アプリケーションの作成時に使用した `ApplicationId` を指定します。 このコマンドレットを実行する前に、新しいサービス プリンシパルが Active Directory 全体に反映されるまでの時間を設ける必要が必要があります。 これらのコマンドレットを手動で実行した場合、通常はコマンドレットとコマンドレットの間に十分な時間が経過しています。 スクリプトでは、コマンドレットとコマンドレットの間にスリープのための手順 (`Start-Sleep 15` など) を追加してください。 "PrincipalNotFound: プリンシパル {id} がディレクトリにありません" というエラーが表示された場合は、コマンドレットを再実行します。

   ```powershell   
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```

    ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。 このメッセージは、アカウントで、スコープ "/subscriptions/{guid} に対するアクション Microsoft.Authorization/roleAssignments/write の実行が承認されていない" ことを示しています。 

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。 次のセクションでは、PowerShell から資格情報を使ってログインする方法を紹介します。 コード アプリケーションの資格情報を使用する場合は、「 [サンプル アプリケーション](#sample-applications)」に進んでください。 

### <a name="provide-credentials-through-powershell"></a>PowerShell を使用して資格情報を渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. `Get-Credential` コマンドを実行して、ユーザーの資格情報を含む `PSCredential` オブジェクトを作成します。 このコマンドを実行するには `ApplicationId` が必要ですので、この ID を貼り付けできるようにしておいてください。

   ```powershell   
   $creds = Get-Credential
   ```

2. 資格情報を入力するためのプロンプトが表示されます。 ユーザー名には、アプリケーションの作成時に使用した `ApplicationId` を使用します。 パスワードには、アカウントの作成時に指定したものを使用します。
   
     ![資格情報を入力](./media/resource-group-authenticate-service-principal/arm-get-credential.png)
3. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Active Directory のインスタンスです。 所有するサブスクリプションが&1; つのみである場合は、次のコマンドを使用できます。

   ```powershell   
   $tenant = (Get-AzureRmSubscription).TenantId
   ```
   
     サブスクリプションが複数ある場合は、Active Directory が関連付けられているサブスクリプションを指定します。 詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

   ```powershell
   $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
   ```

4. このアカウントがサービス プリンシパルであることを指定し、資格情報オブジェクトを提供することにより、サービス プリンシパルとしてログインします。 
   
   ```powershell
   Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
   ```
   
     これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

### <a name="save-access-token-to-simplify-log-in"></a>アクセス トークンを保存してログインを簡略化する
ログインの必要があるたびにサービス プリンシパルを指定しなくても済むように、アクセス トークンを保存することができます。

1. 後のセッションで現在のアクセス トークンを使用するために、プロファイルを保存します。
   
   ```powershell
   Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```
   
     プロファイルを開いて中身を確かめます。 アクセス トークンが含まれていることを確認します。 
2. 手動でもう一度ログインするのではなく、プロファイルをロードするだけで済みます。
   
   ```powershell
   Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
   ```

  > [!NOTE]
  > アクセス トークンには有効期限があるため、保存したプロファイルを使用できるのはトークンが有効である間のみです。
  >  

また、PowerShell から REST 操作を呼び出して、ログインすることもできます。 認証の応答から、その他の操作で使用するためのアクセス トークンを取得できます。 REST 操作を呼び出してアクセス トークンを取得する例については、「[Generating an Access Token (アクセス トークンの生成)](resource-manager-rest-api.md#generating-an-access-token)」を参照してください。

## <a name="create-service-principal-with-certificate"></a>証明書を使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* 自己署名証明書を作成する
* 証明書を指定して AD アプリケーションを作成する
* サービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順をすぐに Windows 10 または Windows Server 2016 Technical Preview 上の Azure PowerShell 2.0 で実行するには、次のコマンドレットを参照してください。

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
$app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
Start-Sleep 15
New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
```

スクリプトは、新しいサービス プリンシパルが Active Directory 全体に反映されるまでの時間を設けるために、15 秒間スリープします。 スクリプトの待機時間が不足している場合、"PrincipalNotFound: プリンシパル {id} がディレクトリにありません" というエラーが表示されます。 このエラーが表示された場合は、ロールに割り当てるコマンドレットを再実行できます。

それでは、各プロセスについて理解できるように、これらの手順についてより細かく見ていきましょう。 以前のバージョンの Azure PowerShell またはオペレーティング システムを使用してタスクを完了する方法についても説明します。

### <a name="create-the-self-signed-certificate"></a>自己署名証明書を作成する
Windows 10 および Windows Server 2016 Technical Preview に搭載されているバージョンの PowerShell では、`New-SelfSignedCertificate` コマンドレットが自己署名証明書を生成できるように更新されています。 New-SelfSignedCertificate コマンドレットは古いオペレーティング システムにも備わっていますが、このトピックに必要なパラメーターは使用できません。 この場合は、証明書を生成するモジュールをインポートする必要があります。 このトピックでは、お使いのオペレーティング システムに応じて証明書を生成する方法を紹介します。 

* **Windows 10 または Windows Server 2016 Technical Preview**をお使いの場合は、次のコマンドを実行して自己署名証明書を作成します。 
   
  ```powershell
  $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
  ```
* **Windows 10 または Windows Server 2016 Technical Preview をお使いでない**場合は、Microsoft スクリプト センターから [Self-signed certificate generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) スクリプトをダウンロードします。 ダウンロードしたファイルを展開し、必要なコマンドレットをインポートします。

  ```powershell  
  # Only run if you could not use New-SelfSignedCertificate
  Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  ```
  
     その後、証明書を生成します。
  
  ```powershell
  New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
  $cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
  ```

これで証明書を用意し、AD アプリケーションを作成できるようになりました。

### <a name="create-the-active-directory-app-and-service-principal"></a>Active Directory アプリケーションとサービス プリンシパルを作成する
1. 証明書からキーの値を取得します。
   
   ```powershell
   $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
   ```
2. Azure アカウントにサインインします。
   
   ```powershell
   Login-AzureRmAccount
   ```
3. 表示名、アプリケーションを説明する URI、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定して、新しい Active Directory アプリケーションを作成します。
   
     Azure PowerShell 2.0 (2016 年 8 月以降) をお使いの場合は、次のコマンドレットを実行します。

   ```powershell   
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
   ```
   
    Azure PowerShell 1.0 をお使いの場合は、次のコマンドレットを実行します。

   ```powershell
   $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore
   ```
   
    シングルテナント アプリケーションでは、URI の検証は行われません。
   
    Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
   
    新しいアプリケーション オブジェクトを調べます。 
   
   ```powershell
   $app
   ```
   
    サービス プリンシパルの作成、ロールの割り当て、アクセス トークンの取得に必要な **ApplicationId** プロパティがあることを確認します。

   ```powershell
   DisplayName             : exampleapp
   ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
   IdentifierUris          : {https://www.contoso.org/example}
   HomePage                : https://www.contoso.org
   Type                    : Application
   ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
   AvailableToOtherTenants : False
   AppPermissions          : 
   ReplyUrls               : {}
   ```
4. Active Directory アプリケーションのアプリケーション ID を渡して、アプリケーションのサービス プリンシパルを作成します。
   
   ```powershell
   New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
   ```
5. サブスクリプションに対する権限をサービス プリンシパルに付与します。 この例では、サブスクリプション内のすべてのリソースを読み取る権限である閲覧者ロールを、サービス プリンシパルに付与します。 その他のロールについては、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。 `ServicePrincipalName` パラメーターには、アプリケーションの作成時に使用した `ApplicationId` を指定します。 このコマンドレットを実行する前に、新しいサービス プリンシパルが Active Directory 全体に反映されるまでの時間を設ける必要が必要があります。 これらのコマンドレットを手動で実行した場合、通常はコマンドレットとコマンドレットの間に十分な時間が経過しています。 スクリプトでは、コマンドレットとコマンドレットの間にスリープのための手順 (`Start-Sleep 15` など) を追加してください。 "PrincipalNotFound: プリンシパル {id} がディレクトリにありません" というエラーが表示された場合は、コマンドレットを再実行します。
   
   ```powershell
   New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId
   ```
   
    ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。 このメッセージは、アカウントで、スコープ "/subscriptions/{guid} に対するアクション Microsoft.Authorization/roleAssignments/write の実行が承認されていない" ことを示しています。

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。 次のセクションでは、PowerShell から証明書を使ってログインする方法を紹介します。

### <a name="provide-certificate-through-automated-powershell-script"></a>自動化された PowerShell スクリプトから証明書を渡す
サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。 テナントは、Active Directory のインスタンスです。 所有するサブスクリプションが&1; つのみである場合は、次のコマンドを使用できます。

```powershell
$tenant = (Get-AzureRmSubscription).TenantId
```

サブスクリプションが複数ある場合は、Active Directory が関連付けられているサブスクリプションを指定します。 詳細については、[Azure AD ディレクトリの管理](../active-directory/active-directory-administer.md)に関するページを参照してください。

```powershell
$tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

スクリプトで認証するためには、アカウントがサービス プリンシパルであることを指定し、証明書の拇印、アプリケーション ID、およびテナント ID を提供します。 これらの値を環境変数に格納しておいて実行時に取得するか、スクリプトに記述することで、スクリプトを自動化することができます。

```powershell
Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant
```

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## <a name="change-credentials"></a>資格情報の変更

セキュリティ侵害の発生または資格情報の期限切れのために AD アプリの資格情報を変更するには、[Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) コマンドレットと [New-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermadappcredential) コマンドレットを使用します。

アプリケーションのすべての資格情報を削除するには、次のコマンドレットを使用します。

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

パスワードを追加するには、次のコマンドレットを使用します。

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

証明書の値を追加するには、このトピックの説明に従って、自己署名証明書を作成します。 その後、次のコマンドレットを使用します。

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="sample-applications"></a>サンプル アプリケーション
サービス プリンシパルとしてログインする方法については、以下のサンプル アプリケーションで紹介されています。

**.NET**

* [.NET からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Azure のリソースとリソース グループを .NET で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [リソースの概要 - Java で Azure Resource Manager テンプレートをデプロイする](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [リソースの概要 - Java でリソース グループを管理する](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Python からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Azure のリソースとリソース グループを Python で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Node.js からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Azure のリソースとリソース グループを Node.js で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Azure のリソースとリソース グループを Ruby で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>次のステップ
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「 [Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)」を参照してください。 
* Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](../active-directory/active-directory-authentication-scenarios.md)」をご覧ください。


