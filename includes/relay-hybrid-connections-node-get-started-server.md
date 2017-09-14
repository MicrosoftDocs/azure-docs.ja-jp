### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

`listener.js` という新しい JavaScript ファイルを作成します。

### <a name="add-the-relay-npm-package"></a>Relay NPM パッケージを追加する

プロジェクト フォルダーの Node のコマンド プロンプトから `npm install hyco-ws` を実行します。

### <a name="write-some-code-to-receive-messages"></a>メッセージを受信するコードを記述する

1. `listener.js` ファイルの先頭に次の定数を追加します。
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. ハイブリッド接続の詳細に関する次の定数を `listener.js` ファイルに追加します。 中かっこ内のプレースホルダーを、ハイブリッド接続の作成時に取得した値に置き換えます。
   
   1. `const ns` - Relay 名前空間。 必ず完全修飾名前空間名を使用してください (たとえば、`{namespace}.servicebus.windows.net`)。
   2. `const path` - ハイブリッド接続の名前。
   3. `const keyrule` - SAS キーの名前。
   4. `const key` - SAS キーの値。

3. 次のコードを `listener.js` ファイルに追加します。
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    listener.js ファイルは次のようになります。
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

