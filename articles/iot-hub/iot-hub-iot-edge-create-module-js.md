---
title: "Azure IoT Edge モジュールを Node.jsで作成する | Microsoft Docs"
description: "このチュートリアルでは、BLE データ コンバーター モジュールを最新の Azure IoT Edge NPM パッケージと Yeoman ジェネレーターを使用して記述する方法を紹介します。"
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 12e0702c31f36368fd95127abeede1a0d4ff2c87
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Azure IoT Edge モジュールを Node.js で作成する

このチュートリアルでは、Azure IoT Edge 用のモジュールを JS で作成する方法を紹介します。

このチュートリアルでは、環境をセットアップし、最新の Azure IoT Edge NPM パッケージを使用して [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) データ コンバーター モジュールを記述する方法を示します。

## <a name="prerequisites"></a>前提条件

このセクションでは、IoT Edge モジュールを開発するための環境をセットアップします。 *64 ビット 版の Windows* オペレーティング システムと *64 ビット版の Linux (Ubuntu 14 以降)* オペレーティング システムの両方に適用されます。

次のソフトウェアが必要です。
* [Git クライアント](https://git-scm.com/downloads)
* [Node LTS](https://nodejs.org)
* `npm install -g yo`
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>アーキテクチャ

Azure IoT Edge プラットフォームは、[ノイマン型アーキテクチャ](https://en.wikipedia.org/wiki/Von_Neumann_architecture)を採用しています。 これは、Azure IoT Edge アーキテクチャ全体が入力を処理して出力を生成する 1 つのシステムであり、個別のモジュールもそれぞれが小さな入出力サブシステムであることを意味します。 このチュートリアルでは、次の 2 つのモジュールについて説明します。

1. シミュレートされた [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) シグナルを受信し、それをフォーマットされた [JSON](https://en.wikipedia.org/wiki/JSON) メッセージに変換するモジュール。
2. 受信した [JSON](https://en.wikipedia.org/wiki/JSON) メッセージを出力するモジュール。

次の図は、このプロジェクトの典型的なエンド ツー エンドのデータ フローを示しています。

![3 つのモジュール間のデータフロー (](media/iot-hub-iot-edge-create-module/dataflow.png "入力: シミュレートされた BLE モジュール; プロセッサー: コンバーター モジュール; 出力: 出力モジュール"))

## <a name="set-up-the-environment"></a>環境をセットアップする
JS で最初の BLE コンバーター モジュールの記述を開始するための環境をすばやくセットアップする方法を次に示します。

### <a name="create-module-project"></a>モジュール プロジェクトを作成する
1. コマンド ライン ウィンドウを開き、`yo az-iot-gw-module` を実行します。
2. 画面の手順に従って、モジュール プロジェクトの初期化を完了します。

### <a name="project-structure"></a>プロジェクト構造
JS モジュール プロジェクトは、次のコンポーネントで構成されます。

`modules` - カスタマイズされる JS モジュールのソース ファイル。 既定の `sensor.js` と `printer.js` を、独自のモジュール ファイルに置き換えます。

`app.js` - Edge インスタンスを起動するためのエントリ ファイル。

`gw.config.json` - モジュールをカスタマイズするために Edge によって読み込まれる構成ファイル。

`package.json` - モジュール プロジェクトのメタデータ情報。

`README.md` - モジュール プロジェクトの基本ドキュメント。


### <a name="package-file"></a>パッケージ ファイル

この `package.json` は、モジュール プロジェクトで必要なすべてのメタデータ情報 (名前、バージョン、エントリ、スクリプト、ランタイム、開発の依存関係など) を宣言します。

次のコード スニペットは、BLE コンバーター サンプル プロジェクトを構成する方法を示しています。
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>エントリ ファイル
`app.js` は、Edge インスタンスを初期化する方法を定義します。 ここでは、変更を加える必要はありません。

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>モジュールのインターフェイス
Azure IoT Edge モジュールは、そのジョブが入力の受信、処理の実行、出力の生成であるデータ プロセッサとして扱うことができます。

入力には、ハードウェア (モーション ディテクターなど) からのデータ、他のモジュールからのメッセージ、その他 (タイマーによって定期的に生成されるランダムな数値など) があります。

出力も入力と同じように、ハードウェアの動作 (LED の点滅など) のトリガー、他のモジュールへのメッセージ、その他 (コンソールへの出力など) があります。

モジュールは、`message` オブジェクトを使用して相互に通信します。 `message` の **Content** は、あらゆる種類のデータを表現することができるバイト配列です。 **Properties** も `message` で使用することができます。これは単純な文字列と文字列のマッピングです。 **Properties** は、HTTP 要求のヘッダーまたはファイルのメタデータと考えることができます。

Azure IoT Edge モジュールを JS で開発するためには、必要なメソッドである `receive()` を実装する新しいモジュール オブジェクトを作成する必要があります。 この時点で、省略可能なメソッド (`create()`、`start()`、または`destroy()`) の実装を選択することもできます。 次のコード スニペットは、JS モジュール オブジェクトのスキャフォールディングを示しています。

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>コンバーター モジュール
| 入力                    | プロセッサ                              | 出力                 | ソース ファイル            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 温度データ メッセージ | 解析を行って新しい JSON メッセージを構築する | JSON メッセージを構築する | `converter.js` |

このモジュールは、典型的な Azure IoT Edge モジュールです。 これは、その他のモジュール (ハードウェア モジュール。ここではシミュレートされた BLE モジュール) から温度メッセージを受け取った後、温度メッセージを構造化された JSON メッセージに正規化します (メッセージ ID の追加、温度アラートをトリガーする必要があるかどうかを示すプロパティの設定などの操作があります)。

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>出力モジュール
| 入力                          | プロセッサ | 出力                     | ソース ファイル          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 他のモジュールからのメッセージ | 該当なし       | メッセージをコンソールに記録する | `printer.js` |

このモジュールは、受信したメッセージ (プロパティ、コンテンツ) をターミナル ウィンドウに出力する、単純な自明のモジュールです。

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>構成
モジュールを実行する前の最後の手順は、Azure IoT Edge を構成し、モジュール間の接続を確立することです。

最初に、後のセクションで `name` によって参照できる `node` ローダーを宣言する必要があります (これは Azure IoT Edge が異なる言語のローダーをサポートするためです)。

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

ローダーを宣言した後、モジュールも同様に宣言する必要があります。 ローダーの宣言と同じように、それらも `name` 属性によって参照することができます。 モジュールを宣言するときは、使用するローダー (先ほど定義したものにする必要があります) と各モジュールのエントリ ポイント (モジュールの正規化されたクラス名) を指定する必要があります。 `simulated_device` モジュールは、Azure IoT Edge コア ランタイム パッケージに含まれているネイティブ モジュールです。 `args` は、`null` の場合でも、JSON ファイルに含めます。

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

構成の最後に、接続を確立します。 各接続は、`source` と `sink` によって表されます。 どちらも、定義済みのモジュールを参照する必要があります。 `source` モジュールの出力メッセージは、`sink` モジュールの入力に転送されます。

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>モジュールの実行
1. `npm install`
2. `npm start`

アプリケーションを終了する場合は、`<Enter>` キーを押します。

> [!IMPORTANT]
> Ctrl + C キーを使用して IoT Edge アプリケーションを終了することはお勧めしません。 その方法では、プロセスが異常終了する可能性があるためです。

