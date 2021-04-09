---
title: アプリケーションでマネージド ID を使用する
description: Azure Service Fabric アプリケーション コードでマネージド ID を使用して Azure サービスにアクセスする方法。
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: e26a29020f26583f7e4aa16434c7e8647ba9a5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98871063"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Service Fabric アプリケーションのマネージド ID を活用して Azure サービスにアクセスする方法

Service Fabric アプリケーションは、マネージド ID を利用して、Azure Active Directory ベースの認証をサポートしている他の Azure リソースにアクセスできます。 アプリケーションはその ID を表す[アクセス トークン](../active-directory/develop/developer-glossary.md#access-token) (システム割り当ての場合とユーザー割り当ての場合がある) を取得でき、それを "ベアラー" トークンとして使用して、別のサービス ([保護されたリソース サーバー](../active-directory/develop/developer-glossary.md#resource-server)とも呼ばれる) に対してそれ自体を認証することができます。 このトークンは Service Fabric アプリケーションに割り当てられた ID を表し、その ID を共有する Azure リソース (SF アプリケーションを含む) に対してのみ発行されます。 マネージド ID の詳細な説明、およびシステム割り当ての ID とユーザー割り当ての ID の違いについては、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するドキュメントを参照してください。 この記事の中では、マネージド ID が有効になった Service Fabric アプリケーションを[クライアント アプリケーション](../active-directory/develop/developer-glossary.md#client-application)と呼びます。

Reliable Services とコンテナーでシステム割り当ておよびユーザー割り当ての [Service Fabric アプリケーションのマネージド ID](https://github.com/Azure-Samples/service-fabric-managed-identity) を使用して示すコンパニオン サンプル アプリケーションをご確認ください。

> [!IMPORTANT]
> マネージド ID は、Azure リソースと、そのリソースを含むサブスクリプションに関連付けられた、対応する Azure AD テナント内のサービス プリンシパルの間の関連付けを表します。 そのため、Service Fabric のコンテキストでは、マネージド ID は、Azure リソースとしてデプロイされたアプリケーションでのみサポートされます。 

> [!IMPORTANT]
> Service Fabric アプリケーションのマネージド ID を使用する前に、保護されたリソースへのアクセス権をクライアント アプリケーションに付与する必要があります。 [Azure AD 認証をサポートする Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)の一覧を参照してサポートを確認してから、それぞれのサービスのドキュメントを参照して、関心のあるリソースへの ID アクセス権を付与する特定の手順を確認してください。 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Azure.Identity を使用してマネージド ID を活用する

Azure Identity SDK で Service Fabric がサポートされるようになりました。 Azure.Identity を使用すると、トークンのフェッチ、トークンのキャッシュ、およびサーバー認証が処理されるため、Service Fabric アプリのマネージド ID を使用するためのコードの記述が容易になります。 Azure リソースのほとんどは、アクセスされている間、トークンの概念が非表示になります。

Service Fabric のサポートは、これらの言語の次のバージョンで利用できます。 
- [C# (バージョン 1.3.0)](https://www.nuget.org/packages/Azure.Identity)。 [C# のサンプル](https://github.com/Azure-Samples/service-fabric-managed-identity)を参照してください。
- [Python (バージョン 1.5.0)](https://pypi.org/project/azure-identity/)。 [Python のサンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md)を参照してください。
- [Java (バージョン 1.2.0)](/java/api/overview/azure/identity-readme)。

資格情報の初期化、および資格情報を使用した Azure Key Vault からのシークレットのフェッチに関する C# のサンプルを次に示します。

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API を使用してアクセス トークンを取得する
マネージド ID が有効になっているクラスターでは、Service Fabric ランタイムによって、アプリケーションがアクセス トークンを取得するために使用できる localhost エンドポイントが公開されます。 このエンドポイントはクラスターのすべてのノードで使用でき、そのノード上のすべてのエンティティからアクセス可能です。 承認された呼び出し元は、このエンドポイントを呼び出して認証コードを提示することで、アクセス トークンを取得できます。このコードは、個別のサービス コード パッケージのアクティブ化ごとに Service Fabric ランタイムによって生成され、そのサービス コード パッケージをホストしているプロセスの有効期間にバインドされます。

具体的には、マネージド ID が有効になっている Service Fabric サービスの環境は、次の変数を使用してシード処理されます。
- 'IDENTITY_ENDPOINT': サービスのマネージド ID に対応する localhost エンドポイント
- 'IDENTITY_HEADER': 現在のノード上のサービスを表す一意の認証コード
- 'IDENTITY_SERVER_THUMBPRINT': Service Fabric のマネージド ID サーバーのサムプリント

> [!IMPORTANT]
> アプリケーション コードでは、'IDENTITY_HEADER' 環境変数の値を機密データと見なす必要があり、ログに記録したり、別の方法で配布したりすることはできません。 認証コードにはローカル ノードの外部の値がないか、サービスをホストしているプロセスが終了した後です。ただし、これは Service Fabric サービスの ID を表しているため、アクセス トークンそのものと同じぐらい注意して扱う必要があります。

クライアントは、トークンを取得するために次の手順を実行します。
- マネージド ID エンドポイント (IDENTITY_ENDPOINT 値) と、API バージョン、およびトークンに必須のリソース (audience) を連結して URI を形成する
- 指定した URI の GET HTTP 要求を作成する
- 適切なサーバー証明書の検証ロジックを追加する
- 認証コード (IDENTITY_HEADER 値) をヘッダーとして要求に追加する
- 要求を送信する

成功の応答には、結果のアクセス トークンを表す JSON ペイロードと、それを記述するメタデータが含まれます。 失敗の応答には、エラーの説明も含まれます。 エラー処理の詳細情報については、以下を参照してください。

アクセス トークンは、Service Fabric によって、さまざまなレベル (ノード、クラスター、リソース プロバイダー サービス) でキャッシュされます。したがって、成功の応答は、必ずしも、ユーザー アプリケーションの要求に応答してトークンが直接発行されたことを意味するわけではありません。 トークンがキャッシュされる期間はそれらの有効期間よりも短くなるため、アプリケーションは有効なトークンを受け取ることが保証されます。 アプリケーション コードでは、コード自体と取得したアクセス トークンをキャッシュすることをお勧めします。キャッシュするキーには、audience (の派生) を含める必要があります。 

要求のサンプル:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
各値の説明:

| 要素 | 説明 |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `https://localhost:2377/metadata/identity/oauth2/token` | Service Fabric アプリケーションのマネージド ID エンドポイント。IDENTITY_ENDPOINT 環境変数を介して提供されます。 |
| `api-version` | クエリ文字列パラメーター。マネージド ID トークン サービスの API バージョンを指定します。現在、許容される唯一の値は `2019-07-01-preview` であり、これは変更される可能性があります。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 これは、発行されたトークンの `aud` (audience) 要求として反映されます。 この例では、Azure Key Vault にアクセスするためのトークンを要求します。そのアプリ ID URI は https:\//vault.azure.net/ です。 |
| `Secret` | HTTP 要求ヘッダー フィールド。Service Fabric サービスが呼び出し元を認証するために、Service Fabric マネージド ID トークン サービスで必要です。 この値は、IDENTITY_HEADER 環境変数を介して SF ランタイムによって提供されます。 |


応答のサンプル:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
各値の説明:

| 要素 | 説明 |
| ------- | ----------- |
| `token_type` | トークンの種類。この場合は、"ベアラー" アクセス トークンです。これは、このトークンの提示側 ("ベアラー") がこのトークンの対象であることを意味します。 |
| `access_token` | 要求されたアクセス トークン。 セキュリティで保護された REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 | 
| `expires_on` | アクセス トークンの有効期限のタイムスタンプ。"1970-01-01T0:0:0Z UTC" からの秒数で表され、トークンの `exp` 要求に対応しています。 この場合、トークンは 2019-08-08T06:10:11+00:00 (RFC 3339) に有効期限が切れます。|
| `resource` | アクセス トークンの発行対象リソース。要求の `resource` クエリ文字列パラメーターを使用して指定され、トークンの "aud" 要求に対応しています。 |


## <a name="acquiring-an-access-token-using-c"></a>C# を使用してアクセス トークンを取得する
上記は C# では次のようになります。

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>マネージド ID を使用して Service Fabric アプリケーションから Key Vault にアクセスする
このサンプルは上記に基づいて構築されており、マネージド ID を使用して Key Vault に格納されているシークレットにアクセスする方法を示しています。

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>エラー処理
HTTP 応答ヘッダーの "状態コード" フィールドは、要求の成功状態を示します。"200 OK" 状態は成功を示し、応答には前述のアクセス トークンが含まれます。 考えられるエラー応答の短い列挙値を次に示します。

| 状態コード | エラーの理由 | 処理方法 |
| ----------- | ------------ | ------------- |
| 404 見つかりません。 | 認証コードが不明であるか、アプリケーションにマネージド ID が割り当てられていません。 | アプリケーションのセットアップまたはトークン取得コードを修正します。 |
| 429 要求が多すぎます。 |  AAD または SF によって設定されているスロットル制限に達しました。 | 指数バックオフを使用して再試行してください。 以下のガイダンスを参照してください。 |
| 要求の 4xx エラーです。 | 1 つ以上の要求パラメーターが正しくありませんでした。 | 再試行はしないでください。  詳しくは、エラーの詳細を確認します。  4xx エラーは、デザイン時のエラーです。|
| サービスからの 5xx エラーです。 | マネージド ID のサブシステムまたは Azure Active Directory から、一時的なエラーが返されました。 | しばらく待ってから安全に再試行できます。 再試行時にスロットル条件 (429) が発生する可能性があります。|

エラーが発生すると、対応する HTTP 応答本文に、JSON オブジェクトとエラーの詳細が含まれます。

| 要素 | 説明 |
| ------- | ----------- |
| code | エラー コード。 |
| correlationId | デバッグに使用できる相関 ID。 |
| message | エラーの詳細な説明。 **エラーの説明は、予告なく変更になる場合があります。エラー メッセージ自体に依存しないでください。**|

サンプル エラー:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

マネージド ID に固有の一般的な Service Fabric エラーの一覧を次に示します。

| コード | Message | 説明 | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | Secret is not found in the request headers. (要求ヘッダー内にシークレットが見つかりません。) | 認証コードが要求に指定されていません。 | 
| ManagedIdentityNotFound | Managed identity not found for the specified application host. (指定されたアプリケーション ホストのマネージド ID が見つかりません。) | アプリケーションに ID がないか、認証コードが不明です。 |
| ArgumentNullOrEmpty | The parameter 'resource' should not be null or empty string. (パラメーター 'resource' を Null または空の文字列にすることはできません。) | リソース (audience) が要求に指定されていません。 |
| InvalidApiVersion | The api-version '' is not supported. Supported version is '2019-07-01-preview'. (API バージョン '' はサポートされていません。サポートされているバージョンは '2019-07-01-preview' です。) | 要求 URI に指定されている API バージョンが見つからないか、サポートされていません。 |
| InternalServerError | エラーが発生しました。 | マネージド ID のサブシステムでエラーが発生しました。Service Fabric スタックの外部の可能性があります。 原因として、リソースに対して間違った値が指定されたことが考えられます (末尾の '/' を確認してください)。 | 

## <a name="retry-guidance"></a>再試行のガイダンス 

通常、唯一の再試行可能なエラー コードは 429 (要求が多すぎます) です。内部サーバー エラー/5xx エラー コードは再試行可能な場合がありますが、原因は永続的である可能性があります。 

スロットリング制限は、マネージド ID のサブシステム、具体的には、"アップストリーム" の依存関係 (マネージド ID Azure サービス、または Secure Store Service) に対して行われた呼び出しの数に適用されます。 Service Fabric は、パイプラインのさまざまなレベルでトークンをキャッシュしますが、関連するコンポーネントの分散型の性質を考えると、呼び出し元は一貫性のないスロットル応答を経験する可能性があります (つまり、アプリケーションのあるノード/インスタンスではスロットルされ、別のノードでは、同じ ID のトークンを要求しているときにスロットルされない)。スロットル条件が設定されている場合、同じアプリケーションからの後続の要求は、条件がクリアされるまで、HTTP 状態コード 429 (要求が多すぎます) によって失敗する可能性があります。  

スロットリングが原因で失敗した要求は、次のようにエクスポネンシャル バックオフを使用して再試行することをお勧めします。 

| 呼び出しインデックス | 429 を受け取ったときのアクション | 
| --- | --- | 
| 1 | 1 秒待ってから再試行する |
| 2 | 2 秒待ってから再試行する |
| 3 | 4 秒待ってから再試行する |
| 4 | 8 秒待ってから再試行する |
| 4 | 8 秒待ってから再試行する |
| 5 | 16 秒待ってから再試行する |

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID
Azure AD をサポートするサービスの一覧と、それぞれのリソース ID については、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [システム割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つ Azure Service Fabric アプリケーションをデプロイする](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセス権を付与する](./how-to-grant-access-other-resources.md)
* [Service Fabric マネージド ID を使用してサンプル アプリケーションを探索する](https://github.com/Azure-Samples/service-fabric-managed-identity)