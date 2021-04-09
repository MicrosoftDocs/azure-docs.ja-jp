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
ms.openlocfilehash: 68114893ed3db858d00ca7811b1a83395ff02507
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495324"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python](https://www.python.org/downloads/) 2.7、3.5 以降
- アクティブな Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. ターミナルまたはコマンド ウィンドウを開き、アプリ用の新しいディレクトリを作成し、そこに移動します。

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. テキスト エディターを使用して、**issue-access-tokens.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理を含むプログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加します。

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>パッケージをインストールする

引き続きアプリケーション ディレクトリで、`pip install` コマンドを使用して、Python 用の Azure Communication Services ID クライアント ライブラリ パッケージをインストールします。

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>クライアントを認証する

接続文字列を使用して `CommunicationIdentityClient` をインスタンス化します。 次のコードは、 `COMMUNICATION_SERVICES_CONNECTION_STRING` という名前の環境変数からリソースの接続文字列を取得します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

このコードを `try` ブロック内に追加します。

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

または、マネージド ID をセットアップしている場合は、[マネージド ID の使用](../managed-identity.md)に関する記事を参考に、マネージド ID で認証することもできます。
```python
const endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"];
var client = new CommunicationIdentityClient(endpoint, DefaultAzureCredential());
```

## <a name="create-an-identity"></a>ID の作成

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `create_user` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 受け取った ID を、アプリケーションのユーザーへのマッピングと共に格納します。 これらは、アプリケーション サーバーのデータベースなどに格納します。 ID は、後でアクセス トークンを発行するために必要になります。

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>アクセス トークンを発行する

既存の Communication Services ID のアクセス トークンを発行するには、`get_token` メソッドを使用します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。 パラメーター `CommunicationUserIdentifier` の新しいインスタンスは、Azure Communication Services ID の文字列表現に基づいて作成できます。

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

アクセス トークンは有効期間の短い資格情報であるため、再発行が必要になります。 そうしないと、アプリケーションのユーザー エクスペリエンスが中断される可能性があります。 `expires_on` 応答プロパティは、アクセス トークンの有効期間を示します。

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>ID を作成し、同じ要求内でアクセス トークンを発行する

`create_user_with_token` メソッドを使用して、Communication Services ID を作成し、そのアクセス トークンを発行します。 パラメーター `scopes` によって、このアクセス トークンを承認するプリミティブのセットが定義されます。 [サポートされているアクションの一覧](../../concepts/authentication.md)を参照してください。

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_with_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>アクセス トークンの更新

アクセス トークンを更新するには、`CommunicationUserIdentifier` オブジェクトを使用して再発行します。

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>アクセス トークンの取り消し

場合によっては、明示的にアクセス トークンを取り消すことがあります。 たとえば、アプリケーションのユーザーが、サービスに対する認証に使用するパスワードを変更するような場合です。 `revoke_tokens` メソッドを使用すると、ID に対して発行されたすべてのアクティブなアクセス トークンを無効にできます。

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>ID の削除

ID を削除すると、すべてのアクティブなアクセス トークンが取り消され、その ID に対してアクセス トークンを発行できなくなります。 また、ID に関連付けられているすべての永続化されたコンテンツも削除されます。

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>コードの実行

コンソール プロンプトから、*issue-access-tokens.py* ファイルが格納されているディレクトリに移動し、次の `python` コマンドを実行してアプリを実行します。

```console
python ./issue-access-tokens.py
```
