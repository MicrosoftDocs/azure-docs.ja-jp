---
title: .NET を使用した Azure Key Vault に対するサービス間認証
description: .NET を使用して Azure Key Vault の認証を受けるために Microsoft.Azure.Services.AppAuthentication ライブラリを使用します。
keywords: Azure Key Vault 認証 ローカル資格情報
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 201f35e7b3ccf7c113ae30a6d007ad3a1f9adb98
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087679"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET を使用した Azure Key Vault に対するサービス間認証

Azure Key Vault に対する認証を行うには、Azure Active Directory (Azure AD) の資格情報として、共有シークレットまたは証明書のいずれかが必要です。

このような資格情報を管理することは困難な場合があります。 資格情報をソース ファイルまたは構成ファイルに含めて、アプリにバンドルしたくなります。 .NET 用の `Microsoft.Azure.Services.AppAuthentication` ライブラリを使うと、この問題が簡略化されます。 これにより、ローカルでの開発中は認証に開発者の資格情報が使用されます。 その後、ソリューションを Azure にデプロイすると、このライブラリは、自動的にアプリケーションの資格情報に切り替わります。 ローカル開発時に開発者の資格情報を使用すると、Azure AD 資格情報を作成したり、開発者間で資格情報を共有したりする必要がないため、より安全です。

`Microsoft.Azure.Services.AppAuthentication` ライブラリで認証が自動的に管理されます。これにより、資格情報ではなく、ソリューションに重点を置くことができます。 これは、Microsoft Visual Studio、Azure CLI、Azure AD の統合認証を使用したローカル開発をサポートしています。 マネージド ID をサポートする Azure リソースにデプロイすると、ライブラリでは [Azure リソースのマネージド ID](../active-directory/msi-overview.md) が自動的に使用されます。 コードまたは構成を変更する必要はありません。 マネージド ID を利用できない場合や、ローカル開発中に開発者のセキュリティ コンテキストを特定できない場合、ライブラリでは、Azure AD の[クライアントの資格情報](../azure-resource-manager/resource-group-authenticate-service-principal.md)を直接使用することもサポートされます。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) または [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Visual Studio 用のアプリ認証拡張機能。Visual Studio 2017 Update 5 用の個別の拡張機能として入手可能であり、Update 6 以降の製品にはバンドルされています。 Update 6 以降では、Visual Studio インストーラー内から [Azure Development tools]\(Azure 開発ツール\) を選択して、アプリ認証拡張機能のインストールを確認できます。

## <a name="using-the-library"></a>ライブラリの使用

.NET アプリケーションの場合、マネージド ID を利用する最も簡単な方法は、`Microsoft.Azure.Services.AppAuthentication` パッケージを経由する方法です。 次のようにして使い始めることができます。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** を選択して、[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) および [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet パッケージに対する参照をプロジェクトに追加します。

1. 次のコードを追加します。

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` クラスは、トークンをメモリ内にキャッシュし、有効期限の直前に Azure AD から取得します。 そのため、`GetAccessTokenAsync` メソッドを呼び出す前に有効期限を確認する必要がなくなります。 トークンが必要になった時点で、いつでもメソッドを呼び出すことができます。

`GetAccessTokenAsync` メソッドには、リソース識別子が必要です。 Microsoft Azure サービスの詳細については、「[Azure リソースのマネージド ID とは](../active-directory/msi-overview.md)」を参照してください。

## <a name="local-development-authentication"></a>ローカル開発用における認証

ローカル開発向けの主な認証シナリオには、[Azure サービスに対する認証](#authenticating-to-azure-services)と[カスタム サービスに対する認証](#authenticating-to-custom-services)の 2 つがあります。

### <a name="authenticating-to-azure-services"></a>Azure サービスに対する認証

ローカル コンピューターでは、Azure リソースのマネージド ID はサポートされません。 その結果、`Microsoft.Azure.Services.AppAuthentication` ライブラリは、ローカル開発環境で実行するために開発者の資格情報を使用します。 ソリューションを Azure にデプロイすると、このライブラリは、マネージド ID を使用して OAuth 2.0 クライアント資格情報の付与フローに切り替えます。 この方法では、同じコードをローカルでもリモートでも、心配せずにテストできます。

ローカル開発では、`AzureServiceTokenProvider` は、**Visual Studio**、**Azure コマンド ライン インターフェイス** (CLI)、**Azure AD 統合認証**を使用してトークンをフェッチします。 このライブラリは、各オプションを順番に試行し、最初に成功したオプションを使用します。 どのオプションも機能しない場合、詳しい情報と共に `AzureServiceTokenProviderException` 例外がスローされます。

#### <a name="authenticating-with-visual-studio"></a>Visual Studio での認証

Visual Studio を使用して認証するには:

1. Visual Studio にサインインし、 **[ツール]** &nbsp;>&nbsp; **[オプション]** を使用して **[オプション]** を開きます。

1. **[Azure サービス認証]** を選択し、ローカル開発用のアカウントを選んでから **[OK]** を選択します。

トークン プロバイダー ファイルに関連するエラーなど、Visual Studio の使用に関して問題が発生した場合は、前述の手順をよくご確認ください。

開発者トークンの再認証が必要になる場合があります。 これを行うには、 **[ツール]** &nbsp;>&nbsp; **[オプション]** の順に選択し、 **[Azure&nbsp;サービス認証]** &nbsp;を選びます。 選択したアカウントの下にある **[再認証]** リンクを探します。 それを選択して認証してください。

#### <a name="authenticating-with-azure-cli"></a>Azure CLI での認証

ローカル開発で Azure CLI を使用する場合は、バージョンが [Azure CLI v 2.0.12](/cli/azure/install-azure-cli) 以降であることを確認してください。

Azure CLI を使用するには:

1. Windows タスクバーで Azure CLI を検索して、**Microsoft Azure コマンド プロンプト**を開きます。

1. Azure portal にサインインします。*az login* で Azure にサインインします。

1. 「*az account get-access-token*」と入力して、アクセスを確認します。 エラーが発生した場合は、適切なバージョンの Azure CLI が正しくインストールされていることを確認してください。

   Azure CLI が既定のディレクトリにインストールされていない場合は、`AzureServiceTokenProvider` で Azure CLI のパスが見つけられないことを報告するエラーが発生することがあります。 **AzureCLIPath** 環境変数を使用して、Azure CLI のインストール フォルダーを定義してください。 `AzureServiceTokenProvider` は、必要な場合に、**AzureCLIPath** 環境変数に指定されたディレクトリを **Path** 環境変数に追加します。

1. 複数のアカウントを使用して Azure CLI にサインインしている場合、または使用しているアカウントで複数のサブスクリプションにアクセスできる場合は、使用するサブスクリプションを指定する必要があります。 コマンド「*az account set --subscription <subscription-id>* 」を入力します。

このコマンドは、エラーが発生した場合にのみ出力を生成します。 現在のアカウント設定を確認するには、コマンド `az account list` を入力します。

#### <a name="authenticating-with-azure-ad-authentication"></a>Azure AD 認証を使用した認証

Azure AD の認証を使用するには、次の点を確認します。

- ご利用のオンプレミスの Active Directory が Azure AD と同期していること。 詳細については、「[Azure Active Directory でのハイブリッド ID とは](../active-directory/connect/active-directory-aadconnect.md)」を参照してください。

- ご自分のコードが、ドメインに参加しているコンピューターで実行されていること。

### <a name="authenticating-to-custom-services"></a>カスタム サービスに対する認証

サービスから Azure サービスを呼び出すとき、Azure サービスがユーザーとアプリケーションの両方にアクセスすることを許可するので、前の手順が動作します。

カスタム サービスを呼び出すサービスを作成するときには、ローカル開発における認証に Azure AD のクライアント資格情報を使用します。 2 つのオプションがあります。

- 次のようにサービス プリンシパルを使用して Azure にサインインします。

    1. サービス プリンシパルを作成する。 詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」をご覧ください。

    1. Azure CLI を使用して、次のコマンドでサインインします。

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        サービス プリンシパルには、サブスクリプションへのアクセス権がない可能性があるため、`--allow-no-subscriptions` 引数を使用します。

- 環境変数を使用して、サービス プリンシパルの詳細を指定します。 詳細については、「[サービス プリンシパルを使用してアプリケーションを実行する](#running-the-application-using-a-service-principal)」を参照してください。

Azure にサインインした後、`AzureServiceTokenProvider` ではサービス プリンシパルを使用して、ローカル開発用のトークンを取得します。

この方法は、ローカル開発に対してのみ適用されます。 ソリューションを Azure にデプロイすると、ライブラリは管理 ID 認証に切り替わります。

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>マネージド ID またはユーザー割り当て ID を使用してアプリケーションを実行する

Azure App Service 上またはマネージド ID が有効な Azure VM 上でコードを実行すると、ライブラリは自動的にマネージド ID を使用します。 コードの変更は必要ありませんが、マネージド ID にはキー コンテナーに対する *get* アクセス許可が必要です。 キー コンテナーの*アクセス ポリシー*を通して、マネージド ID に *get* アクセス許可を付与できます。

また、ユーザー割り当て ID を使用して認証することもできます。 ユーザー割り当て ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)」を参照してください。 ユーザー割り当て ID を使用して認証するには、接続文字列内でユーザー割り当て ID のクライアント ID を指定する必要があります。 接続文字列は、「[接続文字列のサポート](#connection-string-support)」で指定されています。

## <a name="running-the-application-using-a-service-principal"></a>サービス プリンシパルを使用してアプリケーションを実行する

認証のために、Azure AD のクライアント資格情報を作成する必要がある場合があります。 この状況は、次の例で発生する可能性があります。

- コードはローカル開発環境で実行されるが、開発者の ID の下ではない。 たとえば、Service Fabric では、ローカル開発用に [NetworkService アカウント](../service-fabric/service-fabric-application-secret-management.md)を使用します。

- コードはローカル開発環境で実行されるが、開発者自身はカスタム サービスに対して認証を行っているため、自分の開発者 ID は使用できない。

- コードが、Azure Batch など、Azure リソースのマネージド ID がまだサポートされていない Azure コンピューティング リソース上で実行されている。

サービス プリンシパルを使用してアプリケーションを実行する方法は、主に 3 つあります。 そのいずれかを使用するには、最初にサービス プリンシパルを作成する必要があります。 詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」をご覧ください。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>ローカル キーストア内の証明書を使用して Azure AD にサインインする

1. Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパル証明書を作成します。

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    このコマンドでは、ホーム ディレクトリに格納される .pem ファイル (秘密キー) が作成されます。 *LocalMachine* または *CurrentUser* ストアのいずれかに、この証明書をデプロイします。

    > [!Important]
    > この CLI コマンドによって .pem ファイルが生成されますが、Windows で提供されるネイティブ サポートの対象は PFX 証明書のみです。 代わりに PFX 証明書を生成するには、こちらに示されている PowerShell コマンドを使用してください: 「[自己署名証明書を使用したサービス プリンシパルの作成](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)」。 これらのコマンドを実行すると、証明書も自動的にデプロイされます。

1. **AzureServicesAuthConnectionString** という名前の環境変数を次の値に設定します。

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    *{AppId}* 、 *{TenantId}* 、 *{Thumbprint}* は、手順 1 で生成された値に置き換えます。 デプロイ計画に基づき、 *{CertificateStore}* を *LocalMachine* または *CurrentUser* のいずれかで置き換えます。

1. アプリケーションを実行します。

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>共有シークレット資格情報を使用して Azure AD にサインインする

1. [az ad sp create-for-rbac --password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) を使用してパスワード付きのサービス プリンシパル証明書を作成します。

1. **AzureServicesAuthConnectionString** という名前の環境変数を次の値に設定します。

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    _{AppId}_ 、 _{TenantId}_ 、 _{ClientSecret}_ は、手順 1 で生成された値に置き換えます。

1. アプリケーションを実行します。

すべてを適切に設定した後は、それ以上のコード変更は必要ありません。 `AzureServiceTokenProvider` では、環境変数と証明書を使用して Azure AD に対する認証が行われます。

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Key Vault 内の証明書を使用して Azure AD にサインインする

このオプションでは、サービス プリンシパルのクライアント証明書を Key Vault に格納し、それをサービス プリンシパルの認証に使用できます。 このオプションは、次のシナリオに利用できます。

- 明示的なサービス プリンシパルを使用して認証すると共に、サービス プリンシパル資格情報を安全な状態でキー コンテナー内に保管しておくことが必要なローカル認証。 開発者アカウントからキー コンテナーにアクセスできる必要があります。

- 明示的な資格情報を使用すると共に、サービス プリンシパル資格情報を安全な状態でキー コンテナー内に保管しておくことが必要な Azure からの認証。 クロステナント シナリオでこのオプションを使用することができます。 マネージド ID でキー コンテナーにアクセスできる必要があります。

マネージド ID または開発者 ID には、Key Vault からクライアント証明書を取得するためのアクセス許可が必要です。 取得した証明書は、AppAuthentication ライブラリによってサービス プリンシパルのクライアント証明書として使用されます。

サービス プリンシパル認証にクライアント証明書を使用するには:

1. サービス プリンシパル証明書を作成し、キー コンテナーに自動的に格納します。 Azure CLI の [az ad sp create-for-rbac --keyvault \<keyvaultname> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用します。

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    証明書の識別子は、`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>` 形式の URL になります。

1. 次の接続文字列内の `{KeyVaultCertificateSecretIdentifier}` を証明書の識別子に置き換えます。

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    たとえば、キー コンテナーの名前が *myKeyVault* で、*myCert* という名前の証明書を作成した場合、証明書の識別子は次のようになります。

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>接続文字列のサポート

既定では、`AzureServiceTokenProvider` は複数の方法を使用してトークンを取得します。

プロセスを制御するには、接続文字列を `AzureServiceTokenProvider` のコンストラクターに渡すか、*AzureServicesAuthConnectionString* 環境変数に指定して、使用します。

次のオプションがサポートされています。

| 接続文字列のオプション | シナリオ | 説明|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | ローカル開発 | `AzureServiceTokenProvider` では、AzureCli を使用してトークンを取得します。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | ローカル開発 | `AzureServiceTokenProvider` では、Visual Studio を使用してトークンを取得します。 |
| `RunAs=CurrentUser` | ローカル開発 | `AzureServiceTokenProvider` では、Azure AD 統合認証を使用してトークンを取得します。 |
| `RunAs=App` | [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` では、マネージド ID を使用してトークンを取得します。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure リソースのユーザー割り当て ID](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider` では、ユーザー割り当て ID を使用してトークンを取得します。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | カスタム サービスの認証 | `KeyVaultCertificateSecretIdentifier` は証明書のシークレット識別子です。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| サービス プリンシパル | `AzureServiceTokenProvider` は証明書を使用して Azure AD からトークンを取得します。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | サービス プリンシパル | `AzureServiceTokenProvider` は証明書を使用して Azure AD からトークンを取得します|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | サービス プリンシパル |`AzureServiceTokenProvider` はシークレットを使用して Azure AD からトークンを取得します。 |

## <a name="samples"></a>サンプル

`Microsoft.Azure.Services.AppAuthentication` ライブラリの動作を確認する場合は、次のコード サンプルを参照してください。

- [実行時にマネージド ID を使用して Azure Key Vault からシークレットを取得する](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [マネージド ID を使用して Azure VM から Azure Resource Manager テンプレートをプログラムでデプロイする](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

- [.NET Core サンプルとマネージド ID を使用して、Azure Linux VM から Azure サービスを呼び出す](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。

## <a name="appauthentication-troubleshooting"></a>AppAuthentication のトラブルシューティング

### <a name="common-issues-during-local-development"></a>ローカル開発時の一般的な問題

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI がインストールされていないか、ログインしていないか、または最新バージョンがない

*az account get-access-token* を実行して、Azure CLI にトークンが表示されるかどうかを確認します。 **そのようなプログラムが見つからない**場合は、[最新バージョンの Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールします。 サインインを要求される場合があります。

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider で Azure CLI のパスを見つけることができない

AzureServiceTokenProvider では、既定のインストール場所で Azure CLI が検索されます。 Azure CLI が見つからない場合は、環境変数 **AzureCLIPath** を Azure CLI インストール フォルダーに設定します。 AzureServiceTokenProvider によって、環境変数が Path 環境変数に追加されます。

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>複数のアカウントを使用して Azure CLI にログインしているか、同じアカウントに複数のテナント内のサブスクリプションへのアクセス権があるか、ローカル開発中に呼び出そうとしたときにアクセス拒否エラーが発生する

Azure CLI を使って、使用するアカウントがあるものに既定のサブスクリプションを設定します。 サブスクリプションは、アクセスするリソースと同じテナントに存在する必要があります: **az account set --subscription [subscription-id]** 。 出力が表示されない場合は、成功しています。 **az account list** を使用して、適切なアカウントが既定値になっていることを確認します。

### <a name="common-issues-across-environments"></a>環境間の一般的な問題

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>未承認のアクセス、アクセスの拒否、禁止、または同様のエラー

使用されているプリンシパルに、アクセスしようとしているリソースへのアクセス権がありません。 ユーザー アカウントまたは App Service の MSI に、リソースへの "共同作成者" アクセス権を付与します。 これは、サンプルをローカル コンピューターで実行しているか、Azure の App Service にデプロイしたかによって異なります。 キー コンテナーなどの一部のリソースには、ユーザー、アプリ、グループなどのプリンシパルへのアクセス権の付与に使用する独自の[アクセス ポリシー](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies)もあります。

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Azure App Service にデプロイするときの一般的な問題

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>App Service に対してマネージド ID が設定されていない

[Kudu デバッグ コンソール](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)を使用して、環境変数 MSI_ENDPOINT および MSI_SECRET が存在することを確認します。 これらの環境変数が存在しない場合は、App Service に対してマネージド ID が有効になりません。

### <a name="common-issues-when-deployed-locally-with-iis"></a>IIS を使用してローカルにデプロイするときの一般的な問題

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>IIS 内でアプリをデバッグするときにトークンを取得できない

既定では、AppAuth は IIS の別のユーザー コンテキストで実行されます。 そのため、開発者 ID を使用してアクセス トークンを取得するためのアクセス権はありません。 次の 2 つの手順に従って、ユーザー コンテキストで実行するように IIS を構成できます。
- 現在のユーザー アカウントとして実行するように Web アプリのアプリケーション プールを構成します。 詳細については、[こちら](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)をご覧ください。
- "SetProfileEnvironment" を "True" に構成します。 詳細については、[こちら](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)をご覧ください。 

    - %windir%\System32\inetsrv\config\applicationHost.config にアクセスします
    - "setProfileEnvironment" を検索します。 "False" に設定されている場合は、"True" に変更します。 これが存在しない場合は、processModel 要素 (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) に属性として追加し、それを "True" に設定します。

- 詳細については、「[Azure リソースの管理 ID について](../active-directory/managed-identities-azure-resources/index.yml)」を参照してください。
- [Azure AD の認証シナリオ](../active-directory/develop/active-directory-authentication-scenarios.md)について詳細を参照する。
