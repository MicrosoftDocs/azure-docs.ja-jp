### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

`sender.js` という新しい JavaScript ファイルを作成します。

### <a name="add-the-relay-npm-package"></a>Relay NPM パッケージを追加する

プロジェクト フォルダーの Node のコマンド プロンプトから `npm install hyco-ws` を実行します。

### <a name="write-some-code-to-send-messages"></a>メッセージを送信するコードを記述する

1. `sender.js` ファイルの先頭に次の `constants` を追加します。
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
    ```
2. ハイブリッド接続の詳細に関する次の定数を `sender.js` ファイルに追加します。 中かっこ内のプレースホルダーを、ハイブリッド接続の作成時に取得した値に置き換えます。
   
   1. `const ns` - Relay 名前空間。 必ず完全修飾名前空間名を使用してください (たとえば、`{namespace}.servicebus.windows.net`)。
   2. `const path` - ハイブリッド接続の名前。
   3. `const keyrule` - SAS キーの名前。
   4. `const key` - SAS キーの値。

3. 次のコードを `sender.js` ファイルに追加します。
   
    ```js
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```
    sender.js ファイルは次のようになります。
   
    ```js
    const WebSocket = require('hyco-ws');
    const readline = require('readline')
        .createInterface({
            input: process.stdin,
            output: process.stdout
        });;
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    WebSocket.relayedConnect(
        WebSocket.createRelaySendUri(ns, path),
        WebSocket.createRelayToken('http://'+ns, keyrule, key),
        function (wss) {
            readline.on('line', (input) => {
                wss.send(input, null);
            });
   
            console.log('Started client interval.');
            wss.on('close', function () {
                console.log('stopping client interval');
                process.exit();
            });
        }
    );
    ```

