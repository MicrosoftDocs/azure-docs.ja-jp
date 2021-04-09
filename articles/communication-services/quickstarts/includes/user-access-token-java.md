---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 6b75548d6fce7539c2eeb71523a5a045b0b6607b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495325"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- デプロイされた Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開きます。 Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" タスクによって、`artifactId` と同じ名前のディレクトリが作成されたことがわかります。 このディレクトリの下の src/main/java ディレクトリにはプロジェクトのソース コードが含まれており、 `src/test/java directory` にはテスト ソースが含まれており、`pom.xml` ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで **pom.xml** ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. */src/main/java/com/communication/quickstart* ディレクトリに移動します
1. 使用しているエディターで *App.java* ファイルを開きます
1. `System.out.println("Hello world!");` ステートメントを置き換えます
1. `import` ディレクティブを追加します

次のコードを使用して開始します。

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>クライアントを認証する

リソースのアクセス キーとエンドポイントを使用して、`CommunicationIdentityClient` をインスタンス化します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`main` メソッドに次のコードを追加します。

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .httpClient(httpClient)
        .buildClient();
```

`com.azure.core.http.HttpClient` インターフェイスを実装する任意のカスタム HTTP クライアントを使用して、クライアントを初期化できます。 上記のコードは、`azure-core` によって提供される [Azure Core Netty HTTP クライアント](/java/api/overview/azure/core-http-netty-readme)の使用方法を示しています。

エンドポイントとアクセス キーを指定する代わりに、`connectionString()` 関数を使用して接続文字列全体を指定することもできます。
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>ID の作成

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 受け取った ID を、アプリケーションのユーザーへのマッピングと共に格納します。 これらは、アプリケーション サーバーのデータベースなどに格納します。 ID は、後でアクセス トークンを発行するために必要になります。

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>アクセス トークンを発行する

既存の Communication Services ID のアクセス トークンを発行するには、`getToken` メソッドを使用します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。 パラメーター `user` の新しいインスタンスは、Azure Communication Services ID の文字列表現に基づいて作成できます。

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>1 回の呼び出しで ID を作成してトークンを発行する

または、"createUserAndToken" メソッドを使用して、ディレクトリに `Id` が一意である新しいエントリを作成し、アクセス トークンを発行します。

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

アクセス トークンは有効期間の短い資格情報であるため、再発行が必要になります。 そうしないと、アプリケーションのユーザー エクスペリエンスが中断される可能性があります。 `expiresAt` プロパティは、アクセス トークンの有効期間を示します。

## <a name="refresh-access-tokens"></a>アクセス トークンの更新

アクセス トークンを更新するには、`CommunicationUserIdentifier` オブジェクトを使用して再発行します。

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>アクセス トークンの取り消し

場合によっては、明示的にアクセス トークンを取り消すことがあります。 たとえば、アプリケーションのユーザーが、サービスに対する認証に使用するパスワードを変更するような場合です。 `revokeTokens` メソッドを使用すると、ID に対して発行されたすべてのアクティブなアクセス トークンを無効にできます。

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>ID の削除

ID を削除すると、すべてのアクティブなアクセス トークンが取り消され、その ID に対してアクセス トークンを発行できなくなります。 また、ID に関連付けられているすべての永続化されたコンテンツも削除されます。

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>コードの実行

*pom.xml* ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

```console
mvn compile
```

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを実行して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
