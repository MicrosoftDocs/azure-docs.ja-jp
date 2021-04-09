---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495456"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Python](https://www.python.org/downloads/) のインストール
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイント** を記録する必要があります
- [ユーザー アクセス トークン](../../access-tokens.md)。 スコープは必ず "chat" に設定し、トークン文字列と userId 文字列をメモしてください。

## <a name="setting-up"></a>設定

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir chat-quickstart && cd chat-quickstart
```

テキスト エディターを使用して、**start-chat.py** という名前のファイルをプロジェクトのルート ディレクトリに作成し、基本的な例外処理など、プログラムの構造を追加します。 このクイックスタートのソース コードはすべて、以降のセクションでこのファイルに追加することになります。

```python
import os
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>クライアント ライブラリをインストールする

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認を行います。 |

## <a name="create-a-chat-client"></a>チャット クライアントを作成する

チャット クライアントを作成するには、Communications Service エンドポイントと、前提条件の手順で生成された `Access Token` を使用します。 詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、サービス レベルの使用をお勧めします。 詳細については、[チャットのアーキテクチャ](../../../concepts/chat/concepts.md)に関するドキュメントを参照してください

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`create_chat_thread` メソッドを使用して作成します。

- スレッドにトピックを指定するには、`topic` を使用します。チャット スレッドの作成後に、`update_thread` 関数を使用してトピックを更新することができます。
- チャット スレッドに追加する `ChatThreadParticipant` をリストアップするには、`thread_participants` を使用します。`ChatThreadParticipant` は、[ユーザーを作成](../../access-tokens.md#create-an-identity)することによって得られる `CommunicationUserIdentifier` 型を `user` として受け取ります。
- 要求が反復可能であることを指示するには、`repeatability_request_id` を使用します。 クライアントは、同じ Repeatability-Request-ID を使用して要求を複数回行い、サーバーで要求が複数回実行されなくても、適切な応答を取得できます。

`CreateChatThreadResult` は、スレッドの作成から返された結果であり、作成されたチャット スレッドの `id` を取得するために使用できます。 その後、この `id` は、`get_chat_thread_client` メソッドを使用して `ChatThreadClient` オブジェクトをフェッチするために使用できます。 `ChatThreadClient` は、このチャット スレッドに対して他のチャット操作を実行するために使用できます。

#### <a name="without-repeatability-request-id"></a>Repeatability-Request-ID を使用しない
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>Repeatability-Request-ID を使用する
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する
`get_chat_thread_client` メソッドは、既に存在するスレッドのスレッド クライアントを返します。 これは、作成したスレッドに対して操作 (参加者の追加、メッセージの送信など) を実行するために使用できます。thread_id は、既存のチャット スレッドの一意の ID です。

`ChatThreadClient` は、このチャット スレッドに対して他のチャット操作を実行するために使用できます。

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>チャット スレッドを取得する

`get_chat_thread` メソッドを使用して、サービスから `ChatThread` を取得します。`thread_id` はスレッドの一意の ID です。
- スレッドの一意の ID を指定するには、`thread_id` (必須) を使用します。 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>すべてのチャット スレッドを一覧表示する
`list_chat_threads` メソッドは、`ChatThreadInfo` タイプの反復子を返します。 これを使用すると、すべてのチャット スレッドを一覧表示できます。

- チャット スレッドを取得する最も早い時点を指定するには、`start_time` を使用します。
- ページごとに返されるチャット スレッドの最大数を指定するには、`results_per_page` を使用します。

`[ChatThreadInfo]` の反復子は、スレッドの一覧表示から返される応答です

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>チャット スレッドを削除する
`delete_chat_thread` は、チャット スレッドを削除するために使用されます。

- 削除する必要がある既存のチャット スレッドの thread_id を指定するには、`thread_id` を使用します

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

先ほど作成したチャット スレッド (thread_id で識別されます) にメッセージを送信するには、`send_message` メソッドを使用します。

- チャット メッセージの内容は、`content` を使用して設定します。
- メッセージのコンテンツの種類を指定するには、`chat_message_type` を使用します。 指定できる値は、"text" と "html" です。指定しない場合は、既定値の "text" が割り当てられます。
- 送信者の表示名を指定するには、`sender_display_name` を使用します。

応答は、`str` 型の "id" (そのメッセージの一意の ID) です。

#### <a name="message-type-not-specified"></a>メッセージの種類が指定されていない
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>メッセージの種類が指定されている
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>チャット スレッドから特定のチャット メッセージを取得する
`get_message` 関数を使用すると、message_id で識別される特定のメッセージを取得できます

- メッセージ ID を指定するには、`message_id` を使用します。

`ChatMessage` タイプの応答には、1 つのメッセージに関連するすべての情報が含まれています。

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

チャット メッセージは、特定の間隔で `list_messages` メソッドをポーリングすることによって取得できます。

- ページごとに返されるメッセージの最大数を指定するには、`results_per_page` を使用します。
- メッセージを取得する最も早い時点を指定するには、`start_time` を使用します。

`[ChatMessage]` の反復子は、メッセージの一覧表示から返される応答です

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` は、メッセージに対して `update_message` や `delete_message` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。 削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `ChatMessage.deleted_on` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `ChatMessage.edited_on` から返されます。 メッセージの最初の作成日時には、`ChatMessage.created_on` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`list_messages` は、`ChatMessage.type` で識別できるさまざまな種類のメッセージを返します。 次の種類があります。

- `ChatMessageType.TEXT`:スレッド参加者によって送信された通常のチャット メッセージ。

- `ChatMessageType.HTML`: スレッド参加者によって送信された HTML チャット メッセージ。

- `ChatMessageType.TOPIC_UPDATED`:トピックが更新されたことを示すシステム メッセージ。

- `ChatMessageType.PARTICIPANT_ADDED`:1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。

- `ChatMessageType.PARTICIPANT_REMOVED`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="update-topic-of-a-chat-thread"></a>チャット スレッドのトピックを更新する
`update_topic` メソッドを使用してチャット スレッドのトピックを更新できます

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>メッセージを更新する
`update_message` メソッドを使用して、message_id で識別される既存のメッセージのコンテンツを更新できます

- `message_id` (必須) を使用します。これは、メッセージの一意の ID です。
- `content` (省略可能) は、更新されるメッセージの内容です。指定しなかった場合は、空になります

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>メッセージの開封確認メッセージを送信する
`send_read_receipt` メソッドを使用すると、ユーザーに代わって開封確認イベントをスレッドに送信できます。

- 現在のユーザーに読まれた最新のメッセージの ID を指定するには、`message_id` を使用します。

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>チャット スレッドの開封確認メッセージを一覧表示する
`list_read_receipts` メソッドを使用すると、スレッドの開封確認メッセージを取得できます。

- ページごとに返されるチャット メッセージの開封確認メッセージの最大数を指定するには、`results_per_page` を使用します。
- 応答内の指定された位置までチャット メッセージの開封確認メッセージをスキップするように指定するには、`skip` を使用します。

`[ChatMessageReadReceipt]` の反復子は、開封確認メッセージの一覧表示から返される応答です

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>入力通知を送信する
`send_typing_notification` メソッドを使用すると、ユーザーに代わって入力イベントをスレッドに送信できます。

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>メッセージを削除する
`delete_message` メソッドを使用すると、message_id で識別されるメッセージを削除できます

- message_id を指定するには、`message_id` を使用します

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

チャット スレッドの作成後、ユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他の参加者を追加または削除したりできるアクセス権が与えられます。 `add_participant` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

thread_id で識別されるスレッドにスレッド参加者を追加するには、`add_participant` メソッドを使用します。

- チャット スレッドに追加する参加者を指定するには、`thread_participant` を使用します。
- `user` (必須) は、[ユーザーの作成](../../access-tokens.md#create-an-identity)時に `CommunicationIdentityClient` で作成した `CommunicationUserIdentifier` です。
- `display_name` (省略可) は、スレッド参加者の表示名です。
- `share_history_time` (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 参加者が追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 履歴を部分的に共有するには、中間の日付に設定します。

参加者が正常に追加された場合、エラーはスローされません。 参加者の追加中にエラーが発生した場合は、`RuntimeError` がスローされます

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

`add_participants` メソッドを使用してチャット スレッドに複数のユーザーを追加することもでき ます (すべてのユーザーが新しいアクセス トークンと ID を使用できる場合)。

`list(tuple(ChatThreadParticipant, CommunicationError))` が返される。 参加者が正常に追加されると、空の一覧が予期されます。 参加者の追加中にエラーが発生した場合は、失敗した参加者と、発生したエラーが一覧に設定されます。

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

参加者の追加と同様、スレッドから参加者を削除することもできます。 削除するには、追加した参加者の ID を追跡する必要があります。

threadId で識別されるスレッドからスレッド参加者を削除するには、`remove_participant` メソッドを使用します。
- `user` は、スレッドから削除される `CommunicationUserIdentifier` です。

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>コードの実行

アプリケーション ディレクトリから `python` コマンドを使用してアプリケーションを実行します。

```console
python start-chat.py
```
