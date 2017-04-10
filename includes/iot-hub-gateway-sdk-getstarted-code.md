## <a name="typical-output"></a>典型的な出力
Hello World サンプルによってログ ファイルに書き込まれた出力の例を以下に示します。 読みやすいように改行やタブ文字が追加されています。

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>コード スニペット
このセクションでは、Hello World サンプルの主なコード部分について説明します。

### <a name="gateway-creation"></a>ゲートウェイの作成
開発者は *ゲートウェイ プロセス*を作成する必要があります。 このプログラムは、内部インフラストラクチャ (ブローカー) を作成し、モジュールを読み込んで、すべてが正常に動作するように設定します。 SDK には、JSON ファイルからゲートウェイのブートストラップを行うための **Gateway_Create_From_JSON** 関数が含まれています。 **Gateway_Create_From_JSON** 関数を使用するには、読み込むモジュールを指定する JSON ファイルへのパスを渡す必要があります。 

ゲートウェイ プロセスのコードは、[main.c][lnk-main-c] ファイルの Hello World サンプル内にあります。 次のスニペットは、ゲートウェイ プロセス コードを読みやすく省略したものです。 このプログラムは、ゲートウェイを作成し、ユーザーが **Enter** キーを押すまで待機してから、ゲートウェイを破棄します。 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
} 
```

JSON 設定ファイルには、読み込むモジュールとモジュール間のリンクの一覧が含まれています。
各モジュールについて、以下を指定する必要があります。

* **name**: モジュールの一意の名前。
* **loader**: 必要なモジュールを読み込む方法を認識しているローダー。  ローダーは、さまざまな種類のモジュールを読み込むための拡張ポイントです。 ネイティブ C、Node.js、Java、.NET で記述されたモジュールで使用するローダーが提供されます。 Hello World サンプルでは、このサンプル内のすべてのモジュールが C で記述されたダイナミック ライブラリであるため、"ネイティブ" ローダーのみが使用されます。各種言語で記述されたモジュールの使用の詳細については、[Node.js](https://github.com/Azure/azure-iot-gateway-sdk/blob/develop/samples/nodejs_simple_sample/)、[Java](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/java_sample)、または [.NET](https://github.com/Azure/azure-iot-gateway-sdk/tree/develop/samples/dotnet_binding_sample) のサンプルを参照してください。
    * **name**: モジュールの読み込みに使用されるローダーの名前。  
    * **entrypoint**: モジュールを含むライブラリへのパス。 Linux の場合は .so ファイル、Windows の場合は .dll ファイルです。 このエントリ ポイントは、使用されているローダーの種類に固有であることに注意してください。 たとえば、Node.js ローダーのエントリ ポイントは.js ファイル、Java ローダーのエントリ ポイントは classpath とクラス名の組み合わせ、.NET ローダーのエントリ ポイントはアセンブリ名とクラス名の組み合わせです。

* **args**: モジュールに必要な構成情報。

次のコードは、Linux 上の Hello World サンプルのすべてのモジュールを宣言するための JSON を示しています。 引数が必要かどうかは、モジュールの設計によって変わります。 この例では、logger モジュールは出力ファイルへのパスを引数として受け取り、Hello World モジュールは引数を受け取りません。

```
"modules" :
[
    {
        "name" : "logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/logger/liblogger.so"
        }
        },
        "args" : {"filename":"log.txt"}
    },
    {
        "name" : "hello_world",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "./modules/hello_world/libhello_world.so"
        }
        },
        "args" : null
    }
]
```

JSON ファイルには、ブローカーに渡されるモジュール間のリンクも含まれています。 リンクには、2 つのプロパティがあります。

* **source**: `modules` セクションからのモジュール名か、"\*"。
* **sink**: `modules` セクションからのモジュール名。

各リンクにより、メッセージのルートと方向が定義されます。 モジュール `source` からのメッセージはモジュール `sink` に配信されます。 `source` は "\*" に設定することもできます。これは、モジュールからのメッセージが `sink` によって受信されることを示します。

次のコードは、Linux 上の Hello World サンプルで使用されているモジュール間のリンクを構成するための JSON を示しています。 モジュール `hello_world` から生成されたメッセージは、いずれもモジュール `logger` によって使用されます。

```
"links": 
[
    {
        "source": "hello_world",
        "sink": "logger"
    }
]
```

### <a name="hello-world-module-message-publishing"></a>Hello World モジュールでのメッセージの発行
"hello world" モジュールでメッセージを発行するコードは、[hello_world.c][lnk-helloworld-c] ファイル内にあります。 次のスニペットは、コメントを追加してエラー処理コードを取り除き、読みやすく修正したコードです。

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);

    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;

    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Hello World モジュールでのメッセージの処理
Hello World モジュールでは、他のモジュールからブローカーに発行されたメッセージを処理する必要はありません。 このため、Hello World モジュールでのメッセージ コールバックの実装は no-op 関数になります。

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Logger モジュールでのメッセージの発行と処理
Logger モジュールはブローカーからメッセージを受信し、ファイルに書き込みます。 メッセージを発行することはありません。 このため、logger モジュールのコードで **Broker_Publish** 関数を呼び出すことはありません。

[logger.c][lnk-logger-c] ファイル内の **Logger_Recieve** 関数は、logger モジュールにメッセージを配信するためにブローカーが呼び出すコールバックです。 次のスニペットは、コメントを追加してエラー処理コードを取り除き、読みやすく修正したコードです。

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>次のステップ
IoT Gateway SDK の使用方法については、以下を参照してください。

* [IoT ゲートウェイ SDK – Linux を使用してシミュレートされたデバイスから D2C メッセージを送信する][lnk-gateway-simulated]
* GitHub の [Azure IoT Gateway SDK][lnk-gateway-sdk]

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md

<!--HONumber=Dec16_HO1-->


