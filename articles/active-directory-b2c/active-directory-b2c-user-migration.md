---
title: Azure Active Directory B2C でのユーザー移行の方法 | Microsoft Docs
description: Graph API を使用した、および任意で Azure AD B2C のカスタム ポリシーを使用した、ユーザー移行の主要かつ高度なコンセプトについて説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 1e913b02f99095afb7ee1a3f2122e3c1fe1a60b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507657"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C:ユーザー移行
ご利用の ID プロバイダーを Azure Active Directory B2C (Azure AD B2C) に移行する場合は、ユーザー アカウントも移行する必要がある場合があります。 この記事では、既存のユーザー アカウントを ID プロバイダーから Azure AD B2C に移行する方法を説明します。 この記事の内容はこうしなければならないというものではなく、いくつかのシナリオを紹介しています。 どちらの方法が適しているかは、開発者が判断してください。

## <a name="user-migration-flows"></a>ユーザー移行のフロー
Azure AD B2C を使用すると、[Azure AD Graph API][B2C-GraphQuickStart] を通じてユーザーを移行できます。 ユーザー移行プロセスは、次の 2 つのフローに分類されます。

- **移行前**: このフローは、ユーザーの資格情報 (ユーザー名とパスワード) に明確なアクセス権を持っている場合、または資格情報は暗号化されていて、それを復号することができる場合に適用されます。 この移行前プロセスには、旧 ID プロバイダーからユーザーを読み込み、Azure AD B2C ディレクトリで新しいアカウントを作成する作業も含まれます。

- **移行前とパスワードのリセット**: このフローは、ユーザーのパスワードにアクセスできない場合に適用されます。 例:
   - パスワードは、ハッシュ形式で格納されている。
   - パスワードは、アクセスできない ID プロバイダーに格納されている。 利用していた旧 ID プロバイダーが、Web サービスを呼び出してユーザー資格情報を検証している。

どちらのフローでも、最初に移行前プロセスを実行し、ご利用の旧 ID プロバイダーからユーザーを読み込み、Azure AD B2C ディレクトリで新しいアカウントを作成します。 パスワードがない場合は、ランダム に生成されたパスワードを使用してアカウントを作成します。 パスワードを変更するようにユーザーに依頼するか、ユーザーが初めてサインインするときに、Azure AD B2C でパスワードをリセットするようにユーザーに求めます。

## <a name="password-policy"></a>パスワード ポリシー
Azure AD B2C のパスワード ポリシー (ローカル アカウント用) は、Azure AD のポリシーに基づいています。 Azure AD B2C のサインアップまたはサインインとパスワード リセットの各ポリシーでは、"強力な" パスワード強度を使用しており、いずれのパスワードにも有効期限がありません。 詳細については、「[Azure AD でのパスワード ポリシー][AD-PasswordPolicies]」を参照してください。

移行するアカウントのパスワード強度が [Azure AD B2C によって適用された強力なパスワード強度][AD-PasswordPolicies]より弱い場合は、強力なパスワードという要件を無効にすることができます。 既定のパスワード ポリシーを変更するには、`passwordPolicies` プロパティを `DisableStrongPassword` に設定します。 たとえば、ユーザー作成要求を次のように変更できます。

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>手順 1:Azure AD Graph API を使用してユーザーを移行する
Graph API を使用して Azure AD B2C ユーザー アカウントを (パスワードまたはランダム パスワードで) 作成します。 このセクションでは、Graph API を使用して Azure AD B2C ディレクトリでユーザー アカウントを作成する方法について説明します。

### <a name="step-11-register-your-application-in-your-tenant"></a>手順 1.1: アプリケーションをテナントに登録する
Graph API と通信するには、まず管理特権を持つサービス アカウントを持っている必要があります。 Azure AD 内で、アプリケーションと認証を Azure AD に登録します。 アプリケーションの資格情報は、**アプリケーション ID** と**アプリケーション シークレット**です。 アプリケーションはユーザーとしてではなくアプリケーションそれ自体として Graph API を呼び出します。

最初に、移行するアプリケーションを Azure AD に登録します。 次に、アプリケーション キー (アプリケーション シークレット) を作成し、適切な権限を使用してアプリケーションを設定します。

1. [Azure Portal][Portal] にサインインします。
   
1. ウィンドウの右上のアカウント名を選択して、Azure AD **B2C** テナントを選択します。
   
1. 左側のウィンドウで、([Azure AD B2C] ではなく) **[Azure Active Directory]** を選択します。 表示されていない場合は、 **[その他のサービス]** を選択します。
   
1. **[アプリの登録]** を選択します。
   
1. **[新しいアプリケーションの登録]** を選択します。
   
   ![[新しいアプリケーションの登録]](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. 次の手順で、新しいアプリケーションを作成します。
   - **[名前]** には、「**B2CUserMigratioin**」または他の任意の名前を入力します。
   - **[アプリケーションの種類]** には **[Web アプリ/API]** を使用します。
   - **[サインオン URL]** には、(このアプリケーションには関係がないので)「`https://localhost`」と入力します。
   - **作成** を選択します。
   
1. アプリケーションを作成したら、 **[アプリケーション]** 一覧から、新しく作成された **B2CUserMigratioin** アプリケーションを選択します。
   
1. **[プロパティ]** を選択し、**アプリケーション ID** をコピーして、後で使用するために保存します。

### <a name="step-12-create-the-application-secret"></a>手順 1.2: アプリケーション シークレットを作成する
1. Azure Portal の **[登録済みのアプリ]** ウィンドウで、 **[キー]** を選択します。
   
1. 新しいキー (クライアント シークレットとも呼ばれます) を追加し、後で使うためにそのキーをコピーします。
   
   ![[アプリケーション ID] と [キー]](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>手順 1.3: アプリケーションに管理アクセス許可を付与する
1. Azure Portal の **[登録済みのアプリ]** ウィンドウで、 **[必要なアクセス許可]** を選択します。

1. **[Windows Azure Active Directory]** を選択します。
   
1. **[アクセスの有効化]** ウィンドウの **[アプリケーションのアクセス許可]** で、 **[ディレクトリ データの読み取りと書き込み]** を選択し、 **[保存]** を選択します。
   
1. **[必要なアクセス許可]** ウィンドウで、 **[アクセス許可の付与]** を選択します。
   
   ![アプリケーションのアクセス許可](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
これで Azure AD B2C テナントのユーザーの作成、読み取り、更新を実行するアクセス許可をアプリケーションに付与できました。

### <a name="step-14-optional-environment-cleanup"></a>手順 1.4: (省略可能) 環境のクリーンアップ
ディレクトリ データの読み取りと書き込みのアクセス許可には、ユーザーを削除する権限は*含まれていません*。 (環境をクリーンアップするために) ユーザーを削除する権限をアプリケーションに付与するには、PowerShell を実行してユーザー アカウント管理者のアクセス許可を設定する操作など、追加の手順を実行する必要があります。 それ以外の場合は、次のセクションにスキップできます。

> [!IMPORTANT]
> B2C テナントの*ローカル*である B2C テナント管理者アカウントを使用する必要があります。 アカウント名の構文は *admin\@contosob2c.onmicrosoft.com* です。

>[!NOTE]
> 以下の PowerShell スクリプトを実行するには、[Azure Active Directory PowerShell バージョン 2][AD-Powershell] が必要になります。

この PowerShell スクリプトで、次の手順を実行します。
1. ご利用のオンライン サービスに接続します。 そのためには、Windows PowerShell コマンド プロンプトで `Connect-AzureAD` コマンドレットを実行して、資格情報を入力します。
   
1. **アプリケーション ID** を使用して、アプリケーションにユーザー アカウント管理者ロールを割り当てます。 このロールにはよく使用される識別子が付いているため、必要な作業はスクリプトに**アプリケーション ID** を入力することだけです。
   
```powershell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

`$AppId` の値を Azure AD の **アプリケーション ID** に変更します。

## <a name="step-2-pre-migration-application-sample"></a>手順 2:移行前のアプリケーション サンプル
[サンプル コードをダウンロードして実行する][UserMigrationSample] .zip ファイルとしてダウンロードできます。

### <a name="step-21-edit-the-migration-data-file"></a>手順 2.1:移行データ ファイルを編集する
このサンプル アプリでは、ダミーのユーザー データを含む JSON ファイルを使用します。 サンプルが正常に実行されたら、自分のデータベースのデータを使用するようにコードを変更できます。 または、ユーザー プロファイルを JSON ファイルにエクスポートして、そのファイルを使用するようにアプリを設定します。

JSON ファイルを編集するには、`AADB2C.UserMigration.sln` Visual Studio ソリューションを開きます。 `AADB2C.UserMigration` プロジェクトで、`UsersData.json` ファイルを開きます。

![ユーザー データ ファイル](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

ご覧のように、このファイルにはユーザー エンティティの一覧が含まれています。 各ユーザー エンティティには、次のプロパティがあります。
- email
- displayName
- firstName
- lastName
- パスワード (空にできます)

> [!NOTE]
> コンパイル時に Visual Studio はファイルを `bin` ディレクトリにコピーします。

### <a name="step-22-configure-the-application-settings"></a>手順 2.2:アプリケーション設定を構成する
`AADB2C.UserMigration` プロジェクトで、*App.config* ファイルを開きます。 以下のアプリ設定を独自の値に置き換えます。

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Azure テーブル接続文字列の使用については、後半のセクションで説明します
> - B2C テナントの名前はテナントの作成時に入力したドメインであり、Azure Portal に表示されます。 通常、テナント名の末尾には *.onmicrosoft.com* が付きます (たとえば、*contosob2c.onmicrosoft.com*)。

### <a name="step-23-run-the-pre-migration-process"></a>手順 2.3:移行前プロセスを実行する
`AADB2C.UserMigration` ソリューションを右クリックして、サンプルをリビルドします。 リビルドできると、`AADB2C.UserMigration\bin\Debug\net461` に `UserMigration.exe` 実行可能ファイルが表示されます。 移行プロセスを実行するには、次のコマンド ライン パラメーターのいずれかを使用します。

- **パスワードを使用してユーザーを移行する**には、`UserMigration.exe 1` コマンドを使用します。

- **ランダムなパスワードを使用してユーザーを移行する**には、`UserMigration.exe 2` コマンドを使用します。 この操作により、Azure テーブル エンティティも作成されます。 後でポリシーを構成して REST API サービスを呼び出すようにします。 このサービスは Azure テーブルを使用して、移行プロセスを追跡し管理します。

![移行プロセスのデモ](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>手順 2.4:移行前プロセスを確認する
移行を検証するには、次の 2 つの方法のいずれかを使用します。

- 表示名でユーザーを検索するには、Azure Portal を使用します。
   
   1. **Azure AD B2C** を開き、 **[ユーザーとグループ]** を選択します。
   
   1. 検索ボックスにユーザーの表示名を入力して、ユーザーのプロファイルを表示します。
   
- サインインのメール アドレスを使用してユーザーを取得するには、このサンプル アプリケーションを使用します。
   
   1. 次のコマンドを実行します。
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > 次のコマンドを使用して、表示名を指定してユーザーを取得することもできます。`UserMigration.exe 4 "<Display name>"`
      
   1. JSON エディターで UserProfile.json ファイルを開き、ユーザーの情報を表示します。
   
      ![UserProfile.json ファイル](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>手順 2.5:(省略可能) 環境のクリーンアップ
Azure AD テナントをクリーンアップして Azure AD ディレクトリからユーザーを削除する場合は、`UserMigration.exe 5` コマンドを実行します

> [!NOTE]
> * テナントをクリーンアップするには、ご利用のアプリケーション用に [ユーザー アカウント管理者] アクセス許可を構成します
> * サンプルの移行アプリを実行すると、JSON ファイルに記述されているすべてのユーザーがクリーンアップされます

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>手順 2.6: 移行したユーザー (とパスワード) でサインインする
ユーザーのパスワードを使用して移行前プロセスを実行すると、アカウントを使用する準備が完了し、Azure AD B2C を使用してご利用のアプリケーションにユーザーがサインインできるようになります。 ユーザーのパスワードにアクセスできない場合は、次のセクションをご覧ください。

## <a name="step-3-help-users-reset-their-password"></a>手順 3:ユーザーがパスワードをリセットできるようにする
ランダムなパスワードを使用してユーザーを移行する場合は、ユーザー自身がパスワードをリセットする必要があります。 ユーザーがパスワードをリセットできるように、パスワードをリセットするリンクが記載された "ようこそ" メールを送信します。

パスワード リセット ポリシーのリンクを取得するには、次の手順を実行します。

1. **[Azure AD B2C の設定]** を選択し、 **[パスワードのリセット]** ポリシーのプロパティを選択します。

1. アプリケーションを選択します。

    > [!NOTE]
    > [今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、[Azure AD B2C の概要][B2C-GetStarted]に関する記事または[アプリケーションの登録][B2C-AppRegister]に関する記事を参照してください。

1. **[今すぐ実行]** を選択して、ポリシーを確認します

1. **[今すぐ実行のエンドポイント]** ボックスの URL をコピーし、その URL をユーザーに送信します。

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>手順 4:(省略可能) ユーザー移行の状態を確認して設定するようポリシーを変更する

> [!NOTE]
> ユーザー移行の状態を確認して変更するには、カスタム ポリシーを使用する必要があります。 「[カスタム ポリシーの概要][B2C-GetStartedCustom]」のセットアップ手順を完了する必要があります。
>

ユーザーが最初にパスワードをリセットせずにサインインしようとした場合に、分かりやすいエラー メッセージをポリシーが返すようにする必要があります。 例:
>*パスワードの有効期限が切れました。パスワードをリセットするには、[パスワードのリセット] リンクを選択してください。*

この任意の手順では、[カスタム ポリシーの概要][B2C-GetStartedCustom]に関する記事で説明したように、カスタム ポリシーを使用して Azure AD B2C を使用する必要があります。

このセクションでは、サインイン時にユーザー移行の状態を確認するようポリシーを変更します。 ユーザーがパスワードを変更しなかった場合は、 **[パスワードを忘れた場合]** リンクを選択するようにユーザーに求める HTTP 409 のエラー メッセージを返します。

パスワードの変更を追跡するには、Azure テーブルを使用します。 コマンド ライン パラメーターの `2` を使用して移行前プロセスを実行するときに、Azure テーブルにユーザー エンティティを作成します。 このサービスで次の処理を実行します。

- サインイン時に、Azure AD B2C ポリシーは移行の RESTful サービスを呼び出して、入力要求のメール メッセージを送信します。 このサービスは Azure テーブルのメール アドレスを検索します。 アドレスが存在する場合、サービスからは次のエラー メッセージがスローされます: *"パスワードを変更する必要があります"* 。

- ユーザーがパスワードの変更に成功したあと、Azure テーブルからそのエンティティを削除します。

>[!NOTE]
>サンプルを単純にするために、ここでは Azure テーブルを使用します。 移行の状態は任意のデータベースに格納できます。または、Azure AD B2C アカウントにカスタム プロパティとして格納できます。

### <a name="41-update-your-application-setting"></a>4.1: アプリケーション設定を更新する
1. RESTful API デモをテストするには、`AADB2C.UserMigration.sln` を Visual Studio で開きます。

1. `AADB2C.UserMigration.API` プロジェクトで、*Web.config* ファイルを開きます。 [手順 2.2](#step-22-configure-the-application-settings) で構成されている設定で設定を置き換えます。

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>手順 4.2: Azure App Service に Web アプリケーションをデプロイする
ソリューション エクスプローラーで、`AADB2C.UserMigration.API` を右クリックし、[発行] を選択します。 Azure App Service に発行する手順に従います。 詳細については、[Azure App Service へのアプリのデプロイ][AppService-Deploy]に関する記事を参照してください。

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>手順 4.3: ポリシーに技術プロファイルと技術プロファイル検証を追加する
1. ソリューション エクスプローラーで、"ソリューション アイテム" を展開し、*TrustFrameworkExtensions.xml* ポリシー ファイルを開きます。
1. `TenantId`、`PublicPolicyUri`、および `<TenantId>` フィールドを `yourtenant.onmicrosoft.com` からテナントの名前に変更します。
1. `<TechnicalProfile Id="login-NonInteractive">` 要素の下で、`ProxyIdentityExperienceFrameworkAppId` と `IdentityExperienceFrameworkAppId` のすべてのインスタンスを「[カスタム ポリシーの概要][B2C-GetStartedCustom]」で構成されているアプリケーション ID に置き換えます。
1. `<ClaimsProviders>` ノードの下で、次の XML スニペットを見つけます。 Azure App Service URL を指すよう、`ServiceUrl` の値を変更します。

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

前述の技術プロファイルは 1 つの入力要求 `signInName` を定義します (メールとして送信)。 サインイン時に、この要求はご利用の RESTful エンドポイントに送信されます。

ご利用の RESTful API 用の技術プロファイルを定義したあと、Azure AD B2C ポリシーがその技術プロファイルを呼び出すようにします。 XML スニペットは、基本ポリシーで定義されている `SelfAsserted-LocalAccountSignin-Email` をオーバーライドします。 また、XML スニペットは、技術プロファイル `LocalAccountUserMigration` を指す ReferenceId を使用して、`ValidationTechnicalProfile` を追加します。

### <a name="step-44-upload-the-policy-to-your-tenant"></a>手順 4.4: ポリシーをテナントにアップロードする
1. [Azure Portal][Portal] で、[Azure AD B2C テナントのコンテキスト][B2C-NavContext]に切り替えてから、 **[Azure AD B2C]** を選択します。

1. **[Identity Experience Framework]** を選択します。

1. **[すべてのポリシー]** を選択します。

1. **[ポリシーのアップロード]** を選択します。

1. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

1. *TrustFrameworkExtensions.xml* ファイルをアップロードし、検証に合格することを確認します。

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>手順 4.5: [今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C の設定]** を選択してから、 **[Identity Experience Framework]** に移動します。

1. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、 **[今すぐ実行]** を選択します。

1. 移行したユーザーの資格情報のいずれかでサインインを試み、 **[サインイン]** を選択します。 REST API から、次のようなエラー メッセージがスローされます。

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>手順 4.6: (省略可能) REST API をトラブルシューティングする
ほぼリアルタイムでログ情報を確認および監視できます。

1. RESTful アプリケーションの設定メニューの **[監視]** で、 **[診断ログ]** を選択します。

1. **[アプリケーション ログ (ファイル システム)]** を**オン**に設定します。

1. **[レベル]** を **[詳細]** に設定します。

1. **[保存]** を選びます。

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. **[設定]** メニューで **[ログ ストリーム]** を選択します。

1. RESTful API の出力を確認します。

> [!IMPORTANT]
> 診断ログは、開発段階とテスト段階にのみ使用してください。 RESTful API の出力には、運用環境で公開すべきではない機密情報が含まれている場合があります。
>

## <a name="optional-download-the-complete-policy-files"></a>(省略可能) 完全なポリシー ファイルをダウンロードする
「[カスタム ポリシーの概要][B2C-GetStartedCustom]」チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル][UserMigrationSample]が提供されています。

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/yoelhor/Azure-AD-B2C-UserMigration
