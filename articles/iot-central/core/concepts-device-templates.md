---
title: Azure IoT Central のテンプレートとは | Microsoft Docs
description: Azure IoT Central のデバイス テンプレートを使用すると、アプリケーションに接続されているデバイスの動作を指定できます。 デバイス テンプレートによって、デバイスで実装しなければならないテレメトリ、プロパティ、コマンドが指定されます。 また、デバイス テンプレートによって、オペレーターが使用するフォームやダッシュボードなど、IoT Central のデバイス用 UI が定義されます。
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97795430"
---
# <a name="what-are-device-templates"></a>デバイス テンプレートとは

"_この記事は、デバイス開発者およびソリューション ビルダーを対象としています。_ "

Azure IoT Central のデバイス テンプレートは、アプリケーションに接続する種類のデバイスの特性と動作を定義するブループリントです。 たとえば、デバイス テンプレートによって、IoT Central が、適切なユニットとデータの種類を使用して視覚化を作成できるように、デバイスが送信するテレメトリが定義されます。

ソリューション ビルダーは、デバイス テンプレートを IoT Central アプリケーションに追加します。 デバイス開発者は、デバイス テンプレートで定義された動作を実装するデバイス コードを作成します。

デバイス テンプレートは、次のセクションで構成されます。

- _デバイスのモデル_。 デバイス テンプレートのこの部分では、デバイスがアプリケーションと対話する方法を定義します。 デバイス開発者は、モデルで定義された動作を実装します。
    - _既定のコンポーネント_。 すべてのデバイス モデルには、既定のコンポーネントがあります。 既定のコンポーネントのインターフェイスは、デバイス モデルに固有の機能を記述します。
    - _コンポーネント_。 デバイス モデルには、デバイスの機能を記述するため、既定のコンポーネントのほかにも別のコンポーネントが含まれる場合があります。 各コンポーネントには、コンポーネントの機能を記述するインターフェイスがあります。 コンポーネントのインターフェイスは、他のデバイス モデルで再利用できます。 たとえば、複数の電話デバイス モデルで同じカメラ インターフェイスを使用できます。
    - _継承インターフェイス_。 デバイス モデルには、既定のコンポーネントの機能を拡張するインターフェイスが 1 つ以上含まれています。
- "_クラウド プロパティ_"。 デバイス テンプレートのこの部分では、ソリューション開発者が、格納するデバイス メタデータを指定します。 クラウド プロパティがデバイスと同期されることはなく、アプリケーション内にのみ存在します。 クラウド プロパティは、デバイス モデルを実装するためにデバイス開発者が作成するコードには影響しません。
- "_カスタマイズ_"。 デバイス テンプレートのこの部分では、ソリューション開発者が、デバイス モデル内の一部の定義をオーバーライドします。 カスタマイズは、ソリューション開発者が、アプリケーションにより値が処理される方法を設定し直す必要がある場合に役立ちます。たとえば、プロパティの表示名の変更、テレメトリ値を表示するために使用する色の変更などがあります。 カスタマイズは、デバイス モデルを実装するためにデバイス開発者が作成するコードには影響しません。
- "_ビュー_"。 デバイス テンプレートのこの部分では、ソリューション開発者が、デバイスからのデータを表示する視覚化と、デバイスの管理や制御を行うフォームを定義します。 ビューでは、デバイス モデル、クラウド プロパティ、カスタマイズが使用されます。 ビューは、デバイス モデルを実装するためにデバイス開発者が作成するコードには影響しません。

## <a name="device-models"></a>デバイス モデル

デバイス モデルでは、デバイスが IoT Central アプリケーションと対話する方法を定義します。 デバイス開発者は、IoT Central でデバイスを監視、管理できるように、デバイス モデルで定義された動作がデバイスに実装されていることを確認する必要があります。 デバイス モデルは、1 つ以上の "_インターフェイス_" で構成され、各インターフェイスでは、"_テレメトリ_" の種類、"_デバイス プロパティ_"、"_コマンド_" のコレクションを定義できます。 ソリューション開発者は、デバイス モデルを定義する JSON ファイルをデバイス テンプレートにインポートするか、IoT Central の Web UI を使用してデバイス モデルを作成または編集することができます。 Web UI を使用して作成されたデバイス モデルを変更するには、[デバイス テンプレートのバージョン管理する](./howto-version-device-template.md)必要があります。

ソリューション開発者は、デバイス モデルを含む JSON ファイルをエクスポートすることもできます。 デバイス開発者は、この JSON ドキュメントを使用して、デバイスが IoT Central アプリケーションと通信する方法を理解することができます。

デバイス モデルを定義する JSON ファイルでは、[デジタル ツイン定義言語 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)を使用します。 IoT Central では、JSON ファイルに、個別のファイルではなく、インラインで定義されたインターフェイスがあるデバイス モデルが含まれていると想定しています。

一般的な IoT デバイスは次のように構成されています。

- カスタム パーツ。デバイスを一意にするパーツです。
- 標準パーツ。すべてのデバイスに共通するパーツです。

デバイス モデルでは、これらのパーツは "_インターフェイス_" と呼ばれます。 インターフェイスは、デバイスが実装する各パーツの詳細を定義します。 インターフェイスは、デバイス モデル間で再利用できます。 DTDL では、コンポーネントは別の DTDL ファイルで定義されているインターフェイスを参照します。

次の例は、温度コントローラー デバイスのデバイスモデルの概要を示しています。 既定のコンポーネントには、`workingSet`、`serialNumber`、`reboot` の定義が含まれています。 デバイス モデルには、`thermostat` と `deviceInformation` のインターフェイスも含まれます。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

インターフェイスには、いくつかの必須フィールドがあります。

- `@id`: 単純な Uniform Resource Name 形式の一意の ID。
- `@type`: このオブジェクトがインターフェイスであることを宣言します。
- `@context`: インターフェイスに使用される DTDL バージョンを指定します。
- `contents`: デバイスを構成するプロパティ、テレメトリ、およびコマンドが一覧表示されます。 機能は、複数のインターフェイスで定義される場合があります。

他にも表示名や説明など、機能モデルに詳細を追加するために使用できるオプションのフィールドがいくつかあります。

implements セクションのインターフェイス一覧の各エントリには、次のフィールドがあります。

- `name`: インターフェイスのプログラミング名。
- `schema`: 機能モデルが実装するインターフェイス。

## <a name="interfaces"></a>インターフェイス

DTDL を使用すると、デバイスの機能を記述することができます。 関連する機能は、インターフェイスにグループ化されます。 インターフェイスでは、プロパティ、テレメトリ、コマンドをデバイスの実装の一部として記述します。

- `Properties`. プロパティは、デバイスの状態を表すデータ フィールドです。 プロパティは、冷却ポンプのオンオフ状態など、デバイスの持続的な状態を表すために使用します。 また、プロパティはデバイスのファームウェア バージョンなど、デバイスの基本的なプロパティも表すことができます。 プロパティは、読み取り専用または書き込み可能として宣言できます。 デバイスでのみ、読み取り専用プロパティの値を更新できます。 オペレーターは、書き込み可能なプロパティの値を設定し、デバイスに送信できます。
- `Telemetry`. テレメトリ フィールドは、センサーの測定値を表します。 デバイスがセンサーの測定値を取得するたびに、センサー データを含むテレメトリ イベントを送信する必要があります。
- `Commands`. コマンドは、デバイスのユーザーがデバイスで実行できるメソッドを表します。 たとえば、リセット コマンドや、ファンのオンとオフを切り替えるコマンドなどです。

次の例は、サーモスタット インターフェイスの定義を示しています。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name" : "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

この例では、2 つのプロパティ (1 つの読み取り専用と 1 つの書き込み可能)、テレメトリの種類、1 つのコマンドを確認できます。 最小限記述するフィールドは、次の通りです。

- 機能の種類 (`Telemetry`、`Property`、または`Command`) を指定する `@type`。  種類に、IoT Central で値の処理方法を想定できるセマンティックの種類が含まれる場合があります。
- テレメトリ値の `name`。
- テレメトリまたはプロパティのデータ型を指定する `schema`。 この値は、double、integer、boolean、string などのプリミティブ型にすることができます。 複合オブジェクト型およびマップもサポートされています。

表示名や説明などのオプション フィールドを使用すると、インターフェイスや機能に詳細を追加できます。

## <a name="properties"></a>Properties

既定では、プロパティは読み取り専用です。 読み取り専用のプロパティは、デバイスがプロパティ値の更新を IoT Central アプリケーションに報告することを意味します。 IoT Central アプリケーションでは、読み取り専用のプロパティの値を設定することはできません。

インターフェイスでは、プロパティを書き込み可能としてマークすることもできます。 デバイスは、IoT Central アプリケーションから書き込み可能なプロパティに対する更新を受信できるだけでなく、アプリケーションに対するプロパティ値の更新を報告することもできます。

プロパティ値を設定するためにデバイスを接続する必要はありません。 更新された値は、デバイスが次にアプリケーションに接続したときに転送されます。 この動作は、読み取り専用プロパティと書き込み可能なプロパティの両方に適用されます。

デバイスからテレメトリを送信するために、プロパティを使用しないでください。 たとえば、`temperatureSetting=80` などの読み取り専用プロパティは、デバイスの温度が 80 度に設定されており、デバイスがこの温度に達しようとしている、またはこの温度を維持しようとしていることを意味します。

書き込み可能なプロパティの場合、デバイス アプリケーションは、必要な状態の状態コード、バージョン、および説明を返して、プロパティ値を受け取って適用したかどうかを示します。

## <a name="telemetry"></a>テレメトリ

IoT Central を使用すると、テレメトリをダッシュボードおよびグラフに表示し、ルールを使用して、しきい値に達したときにアクションをトリガーできます。 IoT Central では、データ型、ユニット、表示名などのデバイス モデル内の情報を使用して、テレメトリ値の表示方法が決定されます。

IoT Central のデータ エクスポート機能を使用して、ストレージや Event Hubs などの他の保存先にテレメトリをストリーミングすることができます。

## <a name="commands"></a>コマンド

コマンドは、既定で 30 秒以内に実行する必要があり、コマンドが到着したときにデバイスを接続する必要があります。 デバイスが時間内に応答しない場合、またはデバイスが接続されていない場合、コマンドは失敗します。

コマンドは、要求パラメーターを使用して応答を返すことができます。

### <a name="offline-commands"></a>オフライン コマンド

デバイス テンプレートでコマンドの **[Queue if offline]\(オフラインの場合にキュー\)** オプションを有効にすることで、デバイスが現在オフラインになっている場合にキュー コマンドを選択できます。

オフライン コマンドは、ご利用のソリューションからデバイスへの一方向通知です。 オフライン コマンドは、要求パラメーターを使用できますが、応答を返すことはできません。

> [!NOTE]
> このオプションは、IoT Central Web UI でのみ使用できます。 この設定は、デバイス テンプレートからモデルまたはインターフェイスをエクスポートする場合は含まれません。

## <a name="cloud-properties"></a>クラウド プロパティ

クラウド プロパティはデバイス テンプレートの一部ですが、デバイス モデルの一部ではありません。 クラウド プロパティでは、ソリューション開発者が、IoT Central アプリケーションに格納するデバイス メタデータを指定します。 クラウド プロパティは、デバイス モデルを実装するためにデバイス開発者が作成するコードには影響しません。

ソリューション開発者は、デバイス プロパティと共にクラウド プロパティをダッシュボードおよびビューに追加して、オペレーターがアプリケーションに接続されているデバイスを管理できるようにすることができます。 さらに、ソリューション開発者は、クラウド プロパティをルール定義の一部として使用して、オペレーターがしきい値を編集できるようにすることもできます。

## <a name="customizations"></a>カスタマイズ

カスタマイズはデバイス テンプレートの一部ですが、デバイス モデルの一部ではありません。 カスタマイズでは、ソリューション開発者が、デバイス モデル内の一部の定義を強化またはオーバーライドします。 たとえば、ソリューション開発者は、テレメトリの種類またはプロパティの表示名を変更できます。 ソリューション開発者は、カスタマイズを使用して、文字列のデバイス プロパティの最小または最大の長さなどの視覚化を追加できます。

カスタマイズは、デバイス モデルを実装するためにデバイス開発者が作成するコードに影響する場合があります。 たとえば、カスタマイズでは、文字列の最大および最小の長さや、テレメトリの数値の最大および最小値を設定できます。

## <a name="views"></a>ビュー

ソリューション開発者は、オペレーターが接続されたデバイスを監視および管理できるビューを作成します。 ビューはデバイス テンプレートの一部であるため、特定のデバイスの種類に関連付けられます。 ビューには、次のものを含めることができます。

- テレメトリをプロットするグラフ。
- 読み取り専用デバイスのプロパティを表示するタイル。
- オペレーターが書き込み可能なデバイス プロパティを編集できるようにするタイル。
- オペレーターがクラウド プロパティを編集できるようにするタイル。
- オペレーターが、ペイロードを想定するコマンドなどのコマンドを呼び出すために使用するタイル。
- ラベル、画像、またはマークダウン テキストを表示するタイル。

ビューに追加できるテレメトリ、プロパティ、コマンドは、デバイス モデルと、デバイス テンプレート内のクラウド プロパティおよびカスタマイズによって決定されます。

## <a name="next-steps"></a>次のステップ

デバイス開発者としてデバイス テンプレートについて学習したので、推奨される次の手順は、[テレメトリ、プロパティ、およびコマンド ペイロード](./concepts-telemetry-properties-commands.md)を読み取り、デバイスが IoT Central とやり取りするデータの詳細について学ぶことです。

ソリューション開発者として、次のステップでは、「[Azure IoT Central アプリケーションで新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)」を読み、デバイス テンプレートを作成する方法の詳細を把握することをお勧めします。
