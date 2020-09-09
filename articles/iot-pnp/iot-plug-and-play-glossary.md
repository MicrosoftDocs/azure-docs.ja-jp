---
title: 用語集 - IoT プラグ アンド プレイ プレビュー | Microsoft Docs
description: 概念 - IoT プラグ アンド プレイ プレビューに関連する一般用語の用語集。
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2320bed07f574c096be1883a9d82da7311e92fa7
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854211"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT プラグ アンド プレイ プレビューの用語集

IoT プラグ アンド プレイに関する記事で使用される一般的な用語の定義です。

## <a name="azure-iot-explorer-tool"></a>Azure IoT エクスプローラー ツール

Azure IoT エクスプローラーとは、お使いの [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)と対話してテストするために使用できるグラフィカル ツールです。 このツールをローカル コンピューターにインストールすると、次のことに使用できます。

- お使いの [IoT ハブ](#azure-iot-hub)に接続されているデバイスを表示する。
- IoT プラグ アンド プレイ デバイスに接続する。
- デバイス [コンポーネント](#component)を表示します。
- デバイスが送信する[テレメトリ](#telemetry)を表示する。
- デバイスの[プロパティ](#properties)を操作する。
- デバイスの[コマンド](#commands)を呼び出す。

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub は、クラウド内でホストされているマネージド サービスであり、IoT アプリケーションとそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。 [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は IoT ハブに接続できます。 An IoT ソリューションで IoT ハブを使用すると、次のことが可能になります。

- デバイスがクラウドベースのソリューションにテレメトリを送信する。
- クラウドベースのソリューションが、接続されているデバイスを管理する。

## <a name="azure-iot-device-sdk"></a>Azure IoT device SDK

IoT プラグ アンド プレイ デバイスのクライアント アプリケーションの構築に使用できる、多言語に対応したデバイス SDK が用意されています。

## <a name="commands"></a>コマンド

[インターフェイス](#interface)で定義されたコマンドは、[デジタル ツイン](#digital-twin)で実行できるメソッドを表します。 たとえば、デバイスを再起動するコマンドがあります。

## <a name="component"></a>コンポーネント

コンポーネントを使用すると、他のインターフェイスのアセンブリとしてモデル [インターフェイス](#interface)を作成できます。 [デバイス モデル](#device-model)では、複数のインターフェイスをコンポーネントとして組み合わせることができます。 たとえば、モデルにスイッチ コンポーネントとサーモスタット コンポーネントを含めることができます。 モデル内の複数のコンポーネントで、同じインターフェイスの種類を使用することもできます。 たとえば、モデルに 2 つのサーモスタット コンポーネントを含めることができます。

## <a name="connection-string"></a>接続文字列

接続文字列は、エンドポイントに接続するために必要な情報をカプセル化します。 通常、接続文字列には、エンドポイントのアドレスとセキュリティ情報が含まれますが、接続文字列の形式はサービス間で異なります。 IoT Hub サービスに関連付けられた接続文字列には次の 2 種類があります。

- デバイス接続文字列を使用すると、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は、IoT ハブ上のデバイス向けエンドポイントに接続できます。 デバイス上のクライアント コードでは、この接続文字列を使用して、IoT ハブとのセキュリティで保護された接続を確立します。
- IoT Hub 接続文字列を使用すると、バックエンドのソリューションやツールは IoT ハブ上のサービス向けエンドポイントに安全に接続できます。 これらのソリューションやツールは、その IoT ハブと、それに接続されているデバイスを管理します。

## <a name="device-certification"></a>デバイス認定

IoT プラグ アンド プレイ デバイス認定プログラムは、IoT プラグ アンド プレイの認定要件をデバイスが満たしていることを確認するものです。 認定されたデバイスは、公開されている [Azure IoT 認定デバイス カタログ](https://aka.ms/devicecatalog)に登録できます。

## <a name="device-model"></a>デバイスのモデル

デバイス モデルでは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が記述され、デバイスを構成する[コンポーネント](#component)が定義されます。 単純なデバイス モデルには個別のコンポーネントがなく、1 つのルートレベルのインターフェイスの定義が含まれています。 より複雑なデバイス モデルには、複数のコンポーネントが含まれています。 一般的に、デバイス モデルは物理デバイス、製品、または SKU に対応します。 デバイス モデルを定義するには、[Digital Twins 定義言語バージョン 2](#digital-twins-definition-language) を使用します。

## <a name="device-builder"></a>デバイス ビルダー

デバイス ビルダーは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)で実行するコードを実装するときに、[デバイス モデル](#device-model)と[インターフェイス](#interface)を使用します。 デバイス ビルダーは、通常、デバイス クライアントを実装するために [Azure IoT device SDK](#azure-iot-device-sdk) の 1 つを使用しますが、これは必須ではありません。

## <a name="device-modeling"></a>デバイス モデリング

[デバイス ビルダー](#device-builder)は、[Digital Twins 定義言語](#digital-twins-definition-language)を使用して、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)の機能をモデル化します。 [ソリューション ビルダー](#solution-builder)は、このモデルから IoT ソリューションを構成できます。

## <a name="digital-twin"></a>デジタル ツイン

デジタル ツインは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)のモデルです。 デジタル ツインは、[Digital Twins 定義言語](#digital-twins-definition-language)を使用してモデル化されています。 [Azure IoT device SDK](#azure-iot-device-sdk) を使用すると、実行時にデジタル ツインと対話できます。 たとえば、デバイス上でデジタル ツインのプロパティ値を設定すると、この SDK によりこの変更がクラウド内の IoT ソリューションに通知されます。

## <a name="digital-twin-change-events"></a>デジタル ツインの変更イベント

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)が [IoT ハブ](#azure-iot-hub)に接続されると、このハブはそのルーティング機能を使用して、デジタル ツインの変更に関する通知を送信できます。 たとえば、デバイス上で[プロパティ](#properties)値が変更されるたびに、IoT Hub では通知をイベント ハブなどのエンドポイントに送信できます。

## <a name="digital-twins-definition-language"></a>Digital Twins Definition Language

[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)用のモデルとインターフェイスを記述する言語です。 [Digital Twins](#digital-twin) の機能を記述したり、IoT プラットフォームと IoT ソリューションでエンティティのセマンティクスを活用できるようにしたりするには、[Digital Twins 定義言語バージョン 2](https://github.com/Azure/opendigitaltwins-dtdl) を使用します。

## <a name="digital-twin-route"></a>デジタル ツイン ルート

[デジタル ツインの変更イベント](#digital-twin-change-events)をイベント ハブなどのエンドポイントに配信するために [IoT ハブ](#azure-iot-hub)で設定されたルート。

## <a name="interface"></a>インターフェイス

インターフェイスは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)または[デジタル ツイン](#digital-twin)によって実装される関連機能を記述します。 インターフェイスは、さまざまな[デバイス モデル](#device-model)で再利用できます。 インターフェイスがデバイス モデルで使用されるときは、デバイスの[コンポーネント](#component)が定義されます。

## <a name="iot-hub-query-language"></a>IoT Hub クエリ言語

IoT Hub クエリ言語は、複数の目的で使用されます。 たとえば、この言語を使用すると、お使いの IoT ハブに登録されているデバイスを検索したり、[デジタル ツインのルーティング](#digital-twin-route)の動作を調整したりできます。

## <a name="iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイス

IoT プラグ アンド プレイ デバイスは、データの収集や他のデバイスの制御を行い、[デバイス モデル](#device-model)を実装するソフトウェアまたはファームウェアを実行する、一般的には小規模なスタンドアロンのコンピューティング デバイスです。  環境監視デバイス (スマート農業灌漑システムのコントローラー) は、IoT プラグ アンド プレイ デバイスの一例です。 IoT プラグ アンド プレイ デバイスに対する命令と制御を行い、そのデバイスからのデータを受信する、クラウドでホストされる IoT ソリューションを作成できます。

## <a name="iot-plug-and-play-conventions"></a>IoT プラグ アンド プレイ規則

IoT プラグ アンド プレイ [デバイス](#iot-plug-and-play-device)は、データをソリューションとやり取りするときに一連の[規則](concepts-convention.md)に従うことが想定されています。

## <a name="model-id"></a>モデル ID

IoT プラグ アンド プレイ デバイスが IoT Hub に接続されると、そのデバイスによって実装される [DTDL](#digital-twins-definition-language) モデルの**モデル ID** が送信されます。 これにより、ソリューションではデバイス モデルを見つけることができます。

## <a name="model-repository"></a>モデル リポジトリ

[モデル リポジトリ](concepts-model-repository.md)には、[デバイス モデル](#device-model)と[インターフェイス](#interface)が格納されます。

## <a name="model-repository-rest-api"></a>モデル リポジトリの REST API

モデル リポジトリの管理と対話のための API です。 たとえば、API を使用して[デバイス モデル](#device-model)を追加および検索できます。

## <a name="properties"></a>Properties

プロパティは、デジタル ツインの何らかの状態を表す[インターフェイス](#interface)で定義されたデータ フィールドです。 プロパティは、読み取り専用または書き込み可能として宣言できます。 シリアル番号などの読み取り専用プロパティは、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)自体で実行されるコードによって設定されます。  アラームのしきい値などの書き込み可能なプロパティは、通常、クラウドベースの IoT ソリューションから設定されます。

## <a name="shared-access-signature"></a>共有アクセス署名

Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Shared Access Signature 認証には、共有アクセス ポリシーと Shared Access Signature (多くの場合トークンと呼ばれます) という 2 つのコンポーネントがあります。 [IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)は、[IoT ハブ](#azure-iot-hub)による認証に Shared Access Signature を使用します。

## <a name="solution-builder"></a>ソリューション ビルダー

ソリューション ビルダーは、ソリューション バックエンドを作成します。 一般的に、ソリューション ビルダーは、[IoT Hub](#azure-iot-hub) や[モデル リポジトリ](#model-repository)などの Azure リソースを操作します。

## <a name="telemetry"></a>テレメトリ

[インターフェイス](#interface)で定義されているテレメトリ フィールドは、測定値を表します。 これらの測定値は、通常、[IoT プラグ アンド プレイ デバイス](#iot-plug-and-play-device)からデータ ストリームとして送信される、センサーの読み取り値などの値です。
