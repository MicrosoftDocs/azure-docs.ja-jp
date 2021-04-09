---
title: Azure Relay Node API の概要 | Microsoft Docs
description: この記事では、Azure Relay サービスの Node.js API の概要について説明します。 また、hyco-ws Node パッケージの使用方法についても説明します。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 558f49c09203192ff4cbb1af392eaeef8d705c94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263489"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Relay ハイブリッド接続 Node API の概要

## <a name="overview"></a>概要

Azure Relay ハイブリッド接続用の [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) Node パッケージは、["ws"](https://www.npmjs.com/package/ws) NPM パッケージをベースに拡張を加えたものです。 このパッケージを使用すると、その基本パッケージのすべてのエクスポートをもう一度エクスポートし、Azure Relay サービスのハイブリッド接続機能との統合を可能にする新しいエクスポートを追加できます。 

既存のアプリケーションは、`require('ws')` の代わりに `require('hyco-ws')` を使用して、このパッケージを使用できます。これにより、アプリケーションが "ファイアウォールの内側" からローカルに WebSocket 接続をリッスンすると同時にハイブリッド接続を介して WebSocket 接続をリッスンできるハイブリッド シナリオが可能になります。
  
## <a name="documentation"></a>ドキュメント

この API については、[メインの "ws" パッケージのドキュメント](https://github.com/websockets/ws/blob/master/doc/ws.md)を参照してください。 この記事では、基本パッケージとこのパッケージの違いについて説明します。 

基本パッケージとこの "hyco-ws" の主な違いは、`require('hyco-ws').RelayedServer` 経由でエクスポートされる新しいサーバー クラスといくつかのヘルパー メソッドが追加されている点にあります。

### <a name="package-helper-methods"></a>パッケージ ヘルパー メソッド

パッケージ エクスポートに関してはいくつかのユーティリティ メソッドがあり、次のように参照することができます。

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

これらのヘルパー メソッドはこのパッケージで使用するためのものですが、Web サーバーまたはデバイス クライアントでリスナーまたはセンダーを作成できるようにするために Node サーバーで使用することもできます。 サーバーは、有効期間の短いトークンが埋め込まれた URI を渡すことによって、これらのメソッドを使用します。 これらの URI は、WebSocket ハンドシェイクの HTTP ヘッダーの設定をサポートしない一般的な WebSocket スタックでも使用できます。 承認トークンを URI に埋め込むことは、主にこれらのライブラリ外の使用シナリオでサポートされています。 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

指定された名前空間とパスに対して有効な Azure Relay ハイブリッド接続リスナー URI を作成します。 この URI は、WebSocketServer クラスのリレー バージョンで使用できます。

- `namespaceName` (必須) - 使用する Azure Relay 名前空間のドメイン修飾名。
- `path` (必須) - その名前空間内の既存の Azure Relay ハイブリッド接続の名前。
- `token` (省略可能) - リスナー URI に埋め込まれた、以前に発行された Relay アクセス トークン (次の例を参照)。
- `id` (省略可能) - 要求のエンド ツー エンドの診断追跡を可能にする追跡識別子。

`token` の値は省略可能です。W3C WebSocket スタックの場合と同様に、WebSocket ハンドシェイクと共に HTTP ヘッダーを送信できない場合にのみ使用する必要があります。                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

指定された名前空間とパスに対して有効な Azure Relay ハイブリッド接続送信 URI を作成します。 この URI は、任意の WebSocket クライアントで使用できます。

- `namespaceName` (必須) - 使用する Azure Relay 名前空間のドメイン修飾名。
- `path` (必須) - その名前空間内の既存の Azure Relay ハイブリッド接続の名前。
- `token` (省略可能) - 送信 URI に埋め込まれた、以前に発行された Relay アクセス トークン (次の例を参照)。
- `id` (省略可能) - 要求のエンド ツー エンドの診断追跡を可能にする追跡識別子。

`token` の値は省略可能です。W3C WebSocket スタックの場合と同様に、WebSocket ハンドシェイクと共に HTTP ヘッダーを送信できない場合にのみ使用する必要があります。                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

現在のインスタントから指定された秒数、または 1 時間 (有効期限の引数を省略した場合) 有効な、指定されたターゲット URI、SAS 規則、SAS 規則キーに対する Azure Relay Shared Access Signature (SAS) トークンを作成します。

- `uri` (必須) - トークンを発行する対象の URI。 URI は HTTP スキームを使用するために正規化され、クエリ文字列情報が削除されます。
- `ruleName` (必須) - 指定された URI によって表されるエンティティまたは URI ホスト部分によって表される名前空間の SAS 規則名。
- `key` (必須) - SAS 規則の有効なキー。 
- `expirationSeconds` (省略可能) - 生成されたトークンが期限切れになるまでの秒数。 指定しない場合、既定値は 1 時間 (3600) です。

発行されたトークンにより、指定された期間、指定の SAS 規則に関連付けられた権限が付与されます。

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

このメソッドは、前述の `createRelayToken` メソッドと機能的には同等ですが、入力 URI に正しく追加されたトークンを返します。

### <a name="class-wsrelayedserver"></a>ws.RelayedServer クラス

`hycows.RelayedServer` クラスは `ws.Server` クラスの代替クラスであり、ローカル ネットワーク上でリッスンする代わりに、リッスンを Azure Relay サービスに委任します。

これらの 2 つのクラスは、ほとんどの場合、コントラクトに互換性があります。つまり、`ws.Server` クラスを使用している既存のアプリケーションを、リレー バージョンを使用するように簡単に変更できます。 主な相違点は、コンストラクターと使用可能なオプションにあります。

#### <a name="constructor"></a>コンストラクター  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

`RelayedServer` コンストラクターは、スタンドアロン リスナーではなく、既存の HTTP リスナー フレームワークに埋め込むこともできないため、`Server` とは異なる引数のセットをサポートします。 WebSocket の管理は主に Relay サービスに委任されるため、使用可能なオプションも少なくなります。

コンストラクターの引数:

- `server` (必須) - リッスンに使用するハイブリッド接続名の完全修飾 URI。通常、WebSocket.createRelayListenUri() ヘルパー メソッドで構築します。
- `token` (必須) - この引数には、以前に発行されたトークン文字列またはそのようなトークン文字列を取得するために呼び出すことができるコールバック関数が保持されます。 トークンを更新できるコールバック オプションをお勧めします。

#### <a name="events"></a>events

`RelayedServer` インスタンスは、受信要求の処理、接続の確立、およびエラー状態の検出を可能にする 3 つのイベントを出力します。 メッセージを処理するには、`connect` イベントをサブスクライブする必要があります。 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

`headers` イベントは、受信接続が受け入れられる直前に発生します。これにより、クライアントに送信するヘッダーを変更できます。 

##### <a name="connection"></a>connection

```JavaScript
function(socket)
```

新しい WebSocket 接続が受け入れられたときに出力されます。 オブジェクトの型は、基本パッケージと同じ `ws.WebSocket` です。


##### <a name="error"></a>error

```JavaScript
function(error)
```

基本となるサーバーがエラーを出力すると、ここで転送されます。  

#### <a name="helpers"></a>ヘルパー

リレーされたサーバーを開始して受信接続を即座にサブスクライブする操作を簡単にするために、このパッケージにはサンプルにも使用されている単純なヘルパー関数が用意されています。次に例を示します。

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

このメソッドを使用すると、コンストラクターを呼び出して RelayedServer の新しいインスタンスを作成し、指定されたコールバックを "connection" イベントに対してサブスクライブさせることができます。
 
##### <a name="relayedconnect"></a>relayedConnect

`relayedConnect` を使用して、単純に `createRelayedServer` ヘルパーを関数でミラーリングすることにより、クライアント接続を作成し、結果のソケットでの "open" イベントをサブスクライブできます。

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [使用可能なリレー API](relay-api-overview.md)
