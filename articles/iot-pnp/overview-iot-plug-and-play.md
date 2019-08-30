---
title: IoT プラグ アンド プレイ プレビューの概要 | Microsoft Docs
description: IoT プラグ アンド プレイ プレビューについて説明します IoT プラグ アンド プレイは、IoT デバイスがその機能を宣言できるようにするオープン モデリング言語に基づいています。 IoT デバイスが Azure IoT Central やパートナー アプリケーションなどのクラウド ソリューションに接続するとき、IoT デバイスからはデバイス機能モデルと呼ばれる宣言が提示されます。 これで、クラウド ソリューションでは、デバイスを自動的に認識して、デバイスとのやりとりを開始できるようになります。すべてコードを記述することなく行うことができます。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: fd1be6e2354a87bfb0de87b85fe46a1feb42100c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881601"
---
# <a name="what-is-azure-iot-plug-and-play-preview"></a>Azure IoT プラグ アンド プレイ プレビューとは?

IoT プラグ アンド プレイ プレビューを使用することで、ソリューション開発者は埋め込みコードを記述することなく、ご自身のソリューションにデバイスを統合できます。 IoT プラグ アンド プレイの中核となるのは、デバイスの機能を記述する_デバイス機能モデル_ スキーマです。 このスキーマは、次の定義を含む一連のインターフェイスとして構成された JSON ドキュメントです。

- デバイスまたは他のエンティティの読み取り専用および読み取り/書き込みの状態を表す "_プロパティ_"。 たとえば、デバイスのシリアル番号は読み取り専用プロパティである可能性があり、サーモスタットでの目標温度は読み取り/書き込みプロパティである可能性があります。
- デバイスによって出力されるデータである "_テレメトリ_"。このデータはセンサー読み取り値の通常のストリーム、偶発的なエラー、または情報メッセージのいずれかです。
- デバイス上で実行できる関数または操作を記述した "_コマンド_"。 たとえば、コマンドでは、ゲートウェイを再起動したり、リモート カメラを使用して写真を撮影したりすることが可能です。

デバイス機能モデル間でインターフェイスを再利用して、コラボレーションを容易にし、開発を高速化することができます。

IoT プラグ アンド プレイが [Azure Digital Twins](../digital-twins/about-digital-twins.md) とシームレスに連携するように、IoT プラグ アンド プレイ スキーマは、[Digital Twin Definition Language (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) を使用して定義されます。 IoT プラグ アンド プレイと DTDL はコミュニティにも開かれており、Microsoft はお客様、パートナー、業界とのコラボレーションを歓迎しています。 どちらも、サービスおよびツールをまたいで簡単に導入できるオープンな W3C 標準 (JSON-LD や RDF など) に基づいています。 また、IoT プラグ アンド プレイと DTDL を使用する場合、追加料金は発生しません。 [Azure IoT Hub](../iot-hub/about-iot-hub.md)、[Azure IoT Central](../iot-central/overview-iot-central.md)、およびその他の Azure サービスの標準料金は変わりません。

IoT Hub または IoT Central で構築されたソリューションは、IoT プラグ アンド プレイのメリットを活用することができます。

この記事では以下の内容について概説します。

- IoT プラグ アンド プレイを使用するプロジェクトに関連付けられる一般的なロール。
- ご利用のアプリケーション内で IoT プラグ アンド プレイ デバイスを使用する方法。
- IoT プラグ アンド プレイをサポートする IoT デバイス アプリケーションを開発する方法。
- IoT プラグ アンド プレイ デバイスを認定し、[Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)に発行する方法。

## <a name="user-roles"></a>ユーザー ロール

IoT プラグ アンド プレイは、次の 2 種類の開発者にとって有用です。

- "_ソリューション開発者_": Azure IoT およびその他の Azure リソースを使用した IoT ソリューションの開発、および統合する IoT デバイスの特定を担当します。
- "_デバイス開発者_": ご利用のソリューションに接続されたデバイス上で実行するコードを作成します。

## <a name="use-iot-plug-and-play-devices"></a>IoT プラグ アンド プレイ デバイスを使用する

ソリューション開発者は、IoT プラグ アンド プレイ デバイスを使用する、クラウドでホストされた IoT ソリューションを開発できます。 次のいずれかの Azure サービスを使用できます。

- [IoT Central](../iot-central/overview-iot-central.md) - 物理的な世界とデジタルの世界とを結ぶ製品が簡単に作成できるフル マネージドの IoT SaaS (サービスとしてのソフトウェア) ソリューションです。
- [IoT Hub](../iot-hub/about-iot-hub.md) - マネージド クラウド サービスであり、ご利用の IoT アプリケーションとデバイスとの間で、セキュリティで保護された双方向通信を行うためのメッセージ ハブとして機能します。

IoT プラグ アンド プレイ デバイスは、Azure Certified for IoT デバイス カタログの中で見つけることができます。 カタログ内の各 IoT プラグ アンド プレイ デバイスは検証済みであり、デバイス機能モデルを備えています。 デバイス機能モデルを表示してデバイスの機能を理解したり、それを使用して Azure IoT Central 内でデバイスをシミュレートしたりできます。

IoT プラグ アンド プレイ デバイスに接続すると、そのデバイスの機能モデル、モデルに含まれているインターフェイス、それらのインターフェイス内で定義されているテレメトリ、プロパティ、コマンドを表示できます。

## <a name="develop-an-iot-device-application"></a>IoT デバイス アプリケーションを開発する

デバイス開発者は、IoT プラグ アンド プレイをサポートする IoT ハードウェア製品を開発できます。 このプロセスには、次の 2 つの主要な手順が含まれます。

1. デバイス機能モデルとインターフェイスを定義します。 [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) を使用してデバイスの機能を宣言する一連の JSON ファイルを作成します。 デバイス機能モデルでは、物理的な製品などの完全なエンティティが記述され、さらにそのエンティティによって実装される一連のインターフェイスが定義されます。 インターフェイスは、デバイスでサポートされているテレメトリ、プロパティ、コマンドを一意に識別する共有コントラクトです。 インターフェイスは、さまざまなデバイス機能モデルで再利用できます。

1. デバイスの機能モデルとインターフェイスで宣言された機能を実装するデバイス ソフトウェアまたはファームウェアを作成します。 Azure IoT SDK には、デバイス機能モデルを実装するための API が含まれています。

[VS Code 用 IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) の拡張機能には、お客様の役に立つ多くの機能が含まれています。 たとえば、デバイス開発者は、この拡張機能を使用して、機能モデルからスケルトン C プロジェクトを作成できます。 ただし、デバイス機能モデルは、任意の IDE を使用して作成し実装することができます。

## <a name="certify-an-iot-plug-and-play-device"></a>IoT プラグ アンド プレイ デバイスを認定する

デバイス開発者は、認定を受けるために IoT ハードウェア製品を送ることができます。 認定されたデバイスは、Certified for IoT デバイス カタログに発行できます。 認定プロセスの手順は次のとおりです。

- [Microsoft Partner Network](https://partner.microsoft.com) に参加します。
- Certified for Azure IoT ポータルにオンボードします。
- IoT プラグ アンド プレイ デバイス機能モデルとマーケティング情報を送信して、新しいデバイス レコードを作成します。
- 自動化された一連のデバイス検証テストに合格します。
- Certified for IoT デバイス カタログに発行します。

## <a name="regional-availability"></a>リージョン別の提供状況
パブリック プレビューの期間中、IoT プラグ アンド プレイは、北ヨーロッパ、米国中部、東日本の各リージョンでご利用いただけます。 これらのリージョンのいずれかでハブを作成するようにしてください。

## <a name="message-quotas-in-iot-hub"></a>IoT Hub でのメッセージ クォータ
パブリック プレビュー中、IoT プラグ アンド プレイ デバイスでは、インターフェイスごとに個別のメッセージが送信されます。これにより、ご利用の[メッセージ クォータ](../iot-hub/iot-hub-devguide-quotas-throttling.md)に対してカウントされるメッセージ数が増加する可能性があります。

## <a name="next-steps"></a>次の手順

IoT プラグ アンド プレイの概要を説明したので、次の手順では、クイックスタートのいずれかを試してみましょう。

- [デバイス機能モデルを使用して IoT プラグ アンド プレイ デバイスを作成する](./quickstart-create-pnp-device.md)
- [デバイスを IoT Hub に接続する](./quickstart-connect-pnp-device.md)
- [ソリューション内のデバイスに接続する](./quickstart-connect-pnp-device-solution.md)
