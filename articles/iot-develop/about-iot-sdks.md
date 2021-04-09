---
title: Azure IoT device SDK の選択肢の概要
description: 開発上の役割とタスクに応じて、どの Azure IoT device SDK を使用すべきかについて説明します。
author: elhorton
ms.author: elhorton
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c9624e9a23d005185429c82199324ac570cbd63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607732"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT device SDK の概要

Azure IoT device SDK には、デバイスのクライアント ライブラリ、開発者ガイド、サンプル、ドキュメントが集約されています。 プログラムから device SDK を使用すると、Azure IoT サービスにデバイスを接続するのに役立ちます。

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="各種 Azure IoT SDK を示す図":::

図を見るとわかるように、ご使用のデバイスやプログラミング言語のニーズに合ったさまざまな device SDK が用意されています。 適切な device SDK を選ぶうえでのガイダンスについては、「[使用する SDK](#which-sdk-should-i-use)」を参照してください。 クラウドベースのアプリケーションをバックエンドの Azure IoT サービスに接続するための Azure IoT service SDK も用意されています。 この記事では、device SDK に重点を置いていますが、[こちら](#service-sdks)で Azure service SDK についても詳しく説明しています。

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Azure IoT device SDK を使用する理由

Azure IoT にデバイスを接続するために、カスタム接続レイヤーを作成するか、Azure IoT device SDK を使用することができます。 Azure IoT device SDK の使用には、いくつかの利点があります。

| 開発コスト &nbsp; &nbsp; &nbsp; &nbsp; | カスタム接続レイヤー | Azure IoT device SDK |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| サポート | 構築したものをサポートし、文書化する必要がある | Microsoft サポート (GitHub、Microsoft Q&A、Microsoft Docs、カスタマー サポートチーム) にアクセスできる |
| 新機能 | カスタム ミドルウェアに新しい Azure 機能を追加する必要がある | Microsoft によって絶えず IoT SDK に追加される新機能をすぐに利用できる |
| 投資金 | カスタム バージョンを設計、構築、テスト、保守するための組み込み開発に何百時間も費やす必要がある | 無料のオープンソース ツールを利用できる。 SDK に関連したコストは学習曲線のみです。 |

## <a name="which-sdk-should-i-use"></a>使用する SDK

Azure IoT device SDK は、C、C#、Java、Node.js、Python など広く使われているプログラミング言語で利用できます。 SDK を選択する際に考慮すべき最も重要な事柄が 2 つあります。デバイスの機能とプログラミング言語に対するチームの習熟です。

### <a name="device-capabilities"></a>デバイスの機能

SDK を選ぶ際は、使用するデバイスの制限を考慮する必要があります。 制約のあるデバイスとは、マイクロコントローラー (MCU) が 1 つしかなく、メモリに限りがあるようなものです。 制約のあるデバイスを使用する場合は、[Embedded C SDK](#embedded-c-sdk) の使用をお勧めします。 この SDK は、Azure IoT に接続するための必要最小限の機能セットを提供するように設計されています。 組み込みデバイス向けに最適化されたコンポーネント (MQTT クライアント、TLS、ソケット ライブラリ) を選択することもできます。 制約のあるデバイスで Azure RTOS も実行する場合は、Azure RTOS ミドルウェアを使用して Azure IoT に接続することができます。 Azure RTOS ミドルウェアには、追加機能と共に Embedded C SDK がラップされているため、簡単な手間で Azure RTOS デバイスをクラウドに接続することができます。

制約のないデバイスとは、オペレーティング システムを実行でき、.NET や Python といった言語ランタイムをサポートする、より堅牢な CPU を備えているものです。 制約のないデバイスをご使用なら、主な考慮事項は言語への習熟ということになります。

### <a name="your-teams-familiarity-with-the-programming-language"></a>プログラミング言語に対するチームの習熟

Azure IoT device SDK は、任意の言語をユーザーが選べるよう複数の言語で実装されています。 また、device SDK には、その他の、使い慣れている言語固有のツールとの親和性もあります。 チームは使い慣れた開発言語やツールを利用できるため、リサーチ、プロトタイプ作成、製品開発、継続的なメンテナンスの開発サイクルを最適化することができます。

可能な限り、開発チームにとってなじみがある SDK を選ぶようにしてください。 Azure IoT SDK はすべてオープン ソースであり、いくつかのサンプルが用意されています。チームはそれらを評価、テストしたうえで、特定の SDK に本格的に取り組むことができます。

## <a name="how-can-i-get-started"></a>利用を始めるには?

まず、Azure device SDK の GitHub リポジトリを参照してください。 SDK を使って簡単にテレメトリを Azure IoT に送信する方法を紹介した[クイックスタート](quickstart-send-telemetry-python.md)に取り組むのもよいでしょう。

最初に何をすべきかは、所有するデバイスの種類によって異なります。
- 制約のあるデバイスをご使用の場合は、[Embedded C SDK](#embedded-c-sdk) を使用してください。 
- Azure RTOS 上で動作するデバイスの場合は、[Azure RTOS ミドルウェア](#azure-rtos-middleware)を使用して開発できます。 
- 制約のないデバイスの場合は、任意の言語の [SDK を選択](#unconstrained-device-sdks)できます。 

### <a name="constrained-device-sdks"></a>制約のあるデバイスの SDK
これらの SDK は、コンピューティング リソースやメモリ リソースに限りがあるデバイスでの実行に特化されています。 一般的なデバイスの種類について詳しくは、「[Azure IoT のデバイスの種類の概要](concepts-iot-device-types.md)」を参照してください。

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [サンプル](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [リファレンス ドキュメント](https://azure.github.io/azure-sdk-for-c/)
* [Embedded C SDK をビルドする方法](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [制約のあるデバイスのサイズの表](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS ミドルウェア

* [GitHub リポジトリ](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [ファースト ステップ ガイド](https://github.com/azure-rtos/getting-started)と[その他のサンプル](https://github.com/azure-rtos/samples)
* [リファレンス ドキュメント](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>制約のないデバイスの SDK
これらの SDK は、より上位の言語ランタイムをサポートするあらゆるデバイスで実行できます。 たとえば PC や Raspberry Pi、スマートフォンなどのデバイスが該当します。 これらは、実際のチームやシナリオに最もふさわしいライブラリを選べるよう主に言語で区別されます。

#### <a name="c-device-sdk"></a>C デバイス SDK
* [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c)
* [サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [パッケージ](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [リファレンス ドキュメント](/azure/iot-hub/iot-c-sdk-ref/)
* [エッジ モジュールのリファレンス ドキュメント](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [C デバイス SDK のコンパイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [C SDK から他のプラットフォームへの移植](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* クロスコンパイル、さまざまなプラットフォームでの基本など、[開発者向けドキュメント](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)
* [Azure IoT Hub C SDK のリソース消費情報](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# デバイス SDK

* [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-csharp)
* [サンプル](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Package](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [リファレンス ドキュメント](/dotnet/api/microsoft.azure.devices)
* [エッジ モジュールのリファレンス ドキュメント](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java デバイス SDK

* [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-java)
* [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Package](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [リファレンス ドキュメント](/java/api/com.microsoft.azure.sdk.iot.device)
* [エッジ モジュールのリファレンス ドキュメント](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js デバイス SDK

* [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-node)
* [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Package](https://www.npmjs.com/package/azure-iot-device)
* [リファレンス ドキュメント](/javascript/api/azure-iot-device/)
* [エッジ モジュールのリファレンス ドキュメント](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python デバイス SDK

* [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-python)
* [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Package](https://pypi.org/project/azure-iot-device/)
* [リファレンス ドキュメント](/python/api/azure-iot-device)
* [エッジ モジュールのリファレンス ドキュメント](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>サービス SDK
Azure IoT にはサービス SDK も用意されており、デバイスの管理、分析情報の入手、データの視覚化などを行う、ソリューション側のアプリケーションを構築することができます。 これらの SDK は個々の Azure IoT サービスに固有であり、C#、Java、JavaScript、Python で利用できるため開発環境が簡素になります。 

#### <a name="iot-hub"></a>IoT Hub

IoT Hub サービス SDK を使用すれば、IoT ハブと容易にやり取りしてデバイスやセキュリティを管理するアプリケーションを構築できます。 これらの SDK を使用して、たとえば cloud-to-device メッセージを送信したり、デバイス上でダイレクト メソッドを呼び出したり、デバイスのプロパティを更新したりすることができます。

[**IoT Hub の詳細情報**](https://azure.microsoft.com/services/iot-hub/) | [**デバイスの制御を試す**](../iot-hub/quickstart-control-device-python.md)

**C# IoT Hub サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) | [パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [サンプル](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples) | [リファレンス ドキュメント](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-java/tree/master/service) | [パッケージ](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples) | [リファレンス ドキュメント](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [パッケージ](https://www.npmjs.com/package/azure-iothub) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [リファレンス ドキュメント](/javascript/api/azure-iothub/)

**Python IoT Hub サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [パッケージ](https://pypi.python.org/pypi/azure-iot-hub/) | [サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [リファレンス ドキュメント](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins は、環境全体のデジタル モデルに基づいたナレッジ グラフの作成を可能にする、サービスとしてのプラットフォーム (PaaS) オファリングです。 これらの環境には、ビル、工場、農場、エネルギー ネットワーク、鉄道、スタジアムなどがあり、さらには都市全体が含まれます。 これらのデジタル モデルを使用して、より優れた製品、最適化された操作、コストの削減、および画期的なカスタマー エクスペリエンスを実現する分析情報を得ることができます。 Azure IoT には、Azure Digital Twins の強みを活かしたアプリケーションを簡単に構築できるサービス SDK が用意されています。

[**Azure Digital Twins の詳細情報**](https://azure.microsoft.com/services/digital-twins/) | [**ADT アプリケーションのコーディング**](../digital-twins/tutorial-code.md)

**C# ADT サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [パッケージ](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [リファレンス ドキュメント](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [パッケージ](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [リファレンス ドキュメント](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [パッケージ](https://www.npmjs.com/package/@azure/digital-twins-core) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [リファレンス ドキュメント](/javascript/api/@azure/digital-twins-core/)

**Python ADT サービス SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [パッケージ](https://pypi.org/project/azure-digitaltwins-core/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [リファレンス ドキュメント](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>デバイス プロビジョニング サービス

IoT Hub Device Provisioning Service (DPS) は、IoT Hub のヘルパー サービスです。適切な IoT Hub へのゼロタッチの Just-In-Time プロビジョニングを人間の介入を必要とせずに行うことができます。 DPS を使用すると、膨大な数のデバイスを、安全かつスケーラブルな方法でプロビジョニングすることができます。 DPS サービス SDK を使用すれば、登録グループを作成して一括操作を実行することによりデバイスを安全に管理できるアプリケーションを構築できます。

[**Device Provisioning Service の詳細情報**](../iot-dps/index.yml) | [**X.509 デバイスのグループ登録を作成してみる**](../iot-dps/quick-enroll-device-x509-csharp.md)

**C# Device Provisioning Service SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service) | [パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [サンプル](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples) | [リファレンス ドキュメント](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning Service SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src) | [パッケージ](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [リファレンス ドキュメント](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning Service SDK**: [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [パッケージ](https://www.npmjs.com/package/azure-iot-provisioning-service) | [サンプル](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [リファレンス ドキュメント](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>次の手順

* [クイックスタート: デバイスを IoT Central に接続する (Python)](quickstart-send-telemetry-python.md)
* [クイックスタート: デバイスを IoT Hub に接続する (Python)](quickstart-send-telemetry-cli-python.md)
* [埋め込み開発の概要](quickstart-device-development.md)
* [Azure IoT SDK を使用した開発の利点](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)について詳しい情報を入手する