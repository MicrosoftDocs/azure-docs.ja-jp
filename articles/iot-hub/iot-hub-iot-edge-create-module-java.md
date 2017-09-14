---
title: "Azure IoT Edge モジュールを Javaで作成する | Microsoft Docs"
description: "このチュートリアルでは、BLE データ コンバーター モジュールを最新の Azure IoT Edge Maven パッケージを使用して記述する方法を紹介します。"
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 682feb4d889ecd881abe1a70d36e0a5a4df3d910
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Azure IoT Edge モジュールを Java で作成する

このチュートリアルでは、Azure IoT Edge 用のモジュールを Java でビルドする方法を紹介します。

このチュートリアルでは、環境をセットアップし、最新の Azure IoT Edge Maven パッケージを使用して [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) データ コンバーター モジュールを記述する方法を示します。

## <a name="prerequisites"></a>前提条件

このセクションでは、IoT Edge モジュールを開発するための環境をセットアップします。 *64 ビット 版の Windows* オペレーティング システムと *64 ビット版の Linux (Ubuntu/Debian 8)* オペレーティング システムの両方に適用されます。

次のソフトウェアが必要です。

* [Git クライアント](https://git-scm.com/downloads)
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html)。

コマンド ライン ターミナル ウィンドウを開き、次のリポジトリを複製します。

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>全体的なアーキテクチャ

Azure IoT Edge プラットフォームは、[ノイマン型アーキテクチャ](https://en.wikipedia.org/wiki/Von_Neumann_architecture)を採用しています。 これは、Azure IoT Edge アーキテクチャ全体が入力を処理して出力を生成する 1 つのシステムであり、個別のモジュールもそれぞれが小さな入出力サブシステムであることを意味します。 このチュートリアルでは、次の 2 つのモジュールについて説明します。

1. シミュレートされた [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) シグナルを受信し、それをフォーマットされた [JSON](https://en.wikipedia.org/wiki/JSON) メッセージに変換するモジュール。
2. 受信した [JSON](https://en.wikipedia.org/wiki/JSON) メッセージを出力するモジュール。

次の図は、このプロジェクトの典型的なエンド ツー エンドのデータ フローを示しています。

![3 つのモジュール間のデータフロー (](media/iot-hub-iot-edge-create-module/dataflow.png "入力: シミュレートされた BLE モジュール; プロセッサー: コンバーター モジュール; 出力: 出力モジュール"))

## <a name="understanding-the-code"></a>コードについて

### <a name="maven-project-structure"></a>Maven プロジェクト構造

Azure IoT Edge パッケージは Maven に基づいているため、`pom.xml` ファイルを含む典型的な Maven プロジェクト構造を作成する必要があります。

POM は `com.microsoft.azure.gateway.gateway-module-base` パッケージから継承され、モジュール プロジェクトで必要なすべての依存関係 (ランタイム バイナリ、ゲートウェイ構成ファイルのパス、実行動作など) を宣言します。 これにより、時間が大幅に短縮され、数百行のコードの記述と再記述を何度も繰り返す必要がなくなります。

必要な依存関係/プラグインとモジュールで使用される構成ファイルの名前を宣言することで、次のコード スニペットに示すように pom.xml ファイルを更新する必要があります。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Azure IoT Edge モジュールの基礎知識

Azure IoT Edge モジュールは、そのジョブが入力の受信、処理の実行、出力の生成であるデータ プロセッサとして扱うことができます。

入力には、ハードウェア (モーション ディテクターなど) からのデータ、他のモジュールからのメッセージ、その他 (タイマーによって定期的に生成されるランダムな数値など) があります。

出力も入力と同じように、ハードウェアの動作 (LED の点滅など) のトリガー、他のモジュールへのメッセージ、その他 (コンソールへの出力など) があります。

モジュールは、`com.microsoft.azure.gateway.messaging.Message` クラスを使用して相互に通信します。 `Message` の **Content** は、あらゆる種類のデータを表現することができるバイト配列です。 **Properties** も `Message` で使用することができます。これは単純な文字列と文字列のマッピングです。 **Properties** は、HTTP 要求のヘッダーまたはファイルのメタデータと考えることができます。

Azure IoT Edge を Java で開発するためには、`com.microsoft.azure.gateway.core.GatewayModule` から継承される新しいモジュール クラスを作成し、必要な抽象メソッド (`receive()` と `destroy()`) を実装する必要があります。 この時点で、省略可能なメソッド (`start()` または `create()`) の実装を選択することもできます。 次のコード スニペットは、Azure IoT Edge モジュールの作成を開始する方法を示しています。

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>コンバーター モジュール

| 入力                    | プロセッサ                              | 出力                 | ソース ファイル            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 温度データ メッセージ | 解析を行って新しい JSON メッセージを構築する | JSON メッセージを構築する | `ConverterModule.java` |

このモジュールは、典型的な Azure IoT Edge モジュールです。 これは、その他のモジュール (ハードウェア モジュール。ここではシミュレートされた BLE モジュール) から温度メッセージを受け取った後、温度メッセージを構造化された JSON メッセージに正規化します (メッセージ ID の追加、温度アラートをトリガーする必要があるかどうかを示すプロパティの設定などの操作があります)。

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>出力モジュール

| 入力                          | プロセッサ | 出力                     | ソース ファイル          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 他のモジュールからのメッセージ | 該当なし       | メッセージをコンソールに記録する | `PrinterModule.java` |

これは、受信したメッセージをターミナル ウィンドウに出力する、単純な自明のモジュールです。

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT Edge の構成

モジュールを実行する前の最後の手順は、Azure IoT Edge を構成し、モジュール間の接続を確立することです。

最初に、後のセクションで `name` によって参照できる Java ローダーを宣言する必要があります (これは Azure IoT Edge が異なる言語のローダーをサポートするためです)。

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

ローダーを宣言した後、モジュールも同様に宣言する必要があります。 ローダーの宣言と同じように、それらも `name` 属性によって参照することができます。 モジュールを宣言するときは、使用するローダー (先ほど定義したものにする必要があります) と各モジュールのエントリ ポイント (モジュールの正規化されたクラス名) を指定する必要があります。 `simulated_device` モジュールは、Azure IoT Edge コア ランタイム パッケージに含まれているネイティブ モジュールです。 `args` は、`null` の場合でも、常に JSON ファイルに含める必要があります。

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
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
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
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>モジュールの実行

`mvn package` を使用して、すべてを `target/` フォルダー内に構築します。 クリーン ビルド用の `mvn clean package` もお勧めします。

`mvn exec:exec` を使用して Azure IoT Edge を実行し、温度データとすべてのプロパティが一定のレートでコンソールに出力されることを観察する必要があります。

アプリケーションを終了する場合は、`<Enter>` キーを押します。

> [!IMPORTANT]
> Ctrl + C キーを使用して IoT Edge ゲートウェイを終了することはお勧めしません。 これを行うと、プロセスが異常終了する可能性があるためです。


