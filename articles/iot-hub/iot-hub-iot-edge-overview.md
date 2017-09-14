---
title: "Azure IoT Edge の概要 | Microsoft Docs"
description: "ゲートウェイ、モジュール、ブローカーなど、Azure IoT Edge のアーキテクチャ上の主要な概念について説明します。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: ecdd56c91a8fc2011b3d7abe93b9d27c1e1e0bef
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---
# <a name="azure-iot-edge-architectural-concepts"></a>Azure IoT Edge アーキテクチャの概念

サンプルのコードを調べたり、IoT Edge を使用して独自のフィールド ゲートウェイを作成したりする前に、IoT Edge のアーキテクチャの基礎となる主要な概念を理解する必要があります。

## <a name="iot-edge-modules"></a>IoT Edge のモジュール

Azure IoT Edge でゲートウェイを構築するには、*IoT Edge モジュール*を作成してアセンブルします。 モジュールは、 *メッセージ* を使用して互いにデータを交換します。 モジュールがメッセージを受信すると、そのメッセージに対して何らかのアクションを実行し、必要に応じて新しいメッセージに変換したうえで、他のモジュールが処理できるように発行します。 モジュールの中には、新しいメッセージを生成するだけで、受け取ったメッセージを処理しないものもあります。 モジュールのチェーンによってデータ処理のパイプラインが作られ、このパイプライン上のそれぞれの時点で、各モジュールがデータの変換を行います。

![Azure IoT Edge で構築されたゲートウェイのモジュールのチェーン][1]

IoT Edge には次の構成要素があります。

* 一般的なゲートウェイ機能を実行する記述済みのモジュール。
* 開発者がカスタム モジュールを記述する際に使用できるインターフェイス。
* 一連のモジュールをデプロイして実行するために必要なインフラストラクチャ。

Gateway SDK が提供する抽象化レイヤーによって、さまざまなオペレーティング システムとプラットフォームで実行するゲートウェイの作成が可能になります。

![Azure IoT Edge の抽象化レイヤー][2]

## <a name="messages"></a>メッセージ

ゲートウェイの機能を概念化する上で、モジュール同士がメッセージを交換すると考えると簡単ですが、正確にはそうではありません。 IoT Edge モジュールはブローカーを利用して互いに通信します。 モジュールはブローカーにメッセージを発行し (バスや発行/サブスクライブなど、メッセージング パターンを利用)、ブローカーに接続しているモジュールにメッセージをルーティングさせます。

モジュールは、**Broker_Publish** 関数を使用してブローカーにメッセージを発行します。 ブローカーは、コールバック関数を呼び出すことでモジュールにメッセージを配信します。 メッセージは、一連のキー/値のプロパティと、メモリのブロックとして渡されるコンテンツで構成されます。

![Azure IoT Edge でのブローカーの役割][3]

## <a name="message-routing-and-filtering"></a>メッセージのルーティングとフィルター処理

メッセージを適切な IoT Edge モジュールにルーティングする方法は、2 つあります。

* 一連のリンクをブローカーに渡せば、ブローカーはモジュールごとにソースとシンクを把握できます。
* モジュールでは、メッセージのプロパティでフィルター処理できます。

モジュールは、そのモジュール向けのメッセージに基づいて動作する必要があります。 リンクとメッセージのフィルター処理により、メッセージのパイプラインを効果的に作成できます。

## <a name="next-steps"></a>次のステップ

ユーザーが実行できる、サンプルに適用された概念は、「[Azure IoT Edge アーキテクチャについて][lnk-hello-world]」で確認できます。

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
