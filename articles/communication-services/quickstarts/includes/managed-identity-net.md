---
ms.openlocfilehash: c1b74b43c6ef884c68282dcaaae8dfc9a5541453
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622309"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Communication Services ソリューションにマネージド ID を追加する (.NET)

### <a name="install-the-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>クライアント ライブラリ パッケージを使用する

Azure ID と Azure Storage クライアント ライブラリを使用するために、次の `using` ディレクティブをコードに追加します。

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

下の例では [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) が使用されています。 この資格情報は、運用と開発の各環境に適しています。

`AZURE_CLIENT_SECRET`、`AZURE_CLIENT_ID`、`AZURE_TENANT_ID` の環境変数は、`DefaultAzureCredential` オブジェクトを作成するために必要です。 開発環境で登録済みアプリケーションを作成し、環境変数を設定するには、[マネージド ID を使用したアクセスの承認](../managed-identity-from-cli.md)に関するページを参照してください。

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>マネージド ID を使用して ID を作成しトークンを発行する

次のコード例は、Azure Active Directory トークンを使用してサービス クライアント オブジェクトを作成する方法を示しています。

次に、クライアントを使用して、新しいユーザーに対してトークンを発行します。

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>マネージド ID を使用して SMS を送信する

次のコード例は、マネージド ID を使用して SMS サービス クライアント オブジェクトを作成し、このクライアントを使用して SMS メッセージを送信する方法を示しています。

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

