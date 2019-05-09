---
title: デバイス シミュレーションの概要 - Azure | Microsoft Docs
description: デバイス シミュレーション ソリューション アクセラレータとその機能の説明。
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452226"
---
# <a name="device-simulation-solution-accelerator-overview"></a>デバイス シミュレーション ソリューション アクセラレータの概要

クラウドベースの IoT ソリューションでは、温度、場所、状態などのテレメトリを送信するために、ご自分のデバイスをクラウド エンドポイントに接続します。 このテレメトリをご自分のソリューションで使用して、アクションを実行したり、分析情報を引き出したりできます。

IoT ソリューションを開発するプロセスで不可欠なものは、実験とテストです。 シミュレーションは、このプロセス中、常に重要なツールです。 デバイス シミュレーションを使用して、以下を実行できます。

* プロトタイプを短時間で作動させた後、シミュレートされたデバイスの動作をすぐに調整して反復処理を行うことができます。 このプロセスによって、高価なハードウェアに投資する前に、アイデアの有効性を実証できます。 Web UI を使用してカスタム デバイスを作成し、プロトタイプ デバイスを数秒で生成できます。
* 実際のデバイスの動作をシミュレートすることで、デバイスからソリューションまで期待どおりにソリューションが機能することを検証します。 JavaScript を使用して複雑なデバイスの動作をスクリプト化して、現実的なシミュレートされたテレメトリを生成できます。
* 通常時、ピーク時、およびピーク時を超える負荷条件をシミュレートすることによって、ソリューションのスケール テストを行います。 スケール テストは、ソリューションを実行するために必要な Azure リソースの適切なサイズを判断するためにも有用です。

![ドローンのシミュレーションの例](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

デバイス シミュレーションを使用して、実際のデバイスをシミュレートするデバイス モデルを定義できます。 このモデルには、メッセージ形式、ツインのプロパティ、およびメソッドが含まれます。 JavaScript を使用して複雑なデバイスの動作をシミュレートすることもできます。

任意の IoT ハブに接続されている 1 台から数千台のデバイスに対するシミュレーションを実行できます。 テストに役立つように、必要に応じて IoT ハブとデバイス シミュレーションをスタンドアロン環境にデプロイできます。

デバイス シミュレーションは無料です。 ただし、デバイス シミュレーションはクラウド上の Azure サブスクリプション内にデプロイされ、Azure リソースを消費します。 デバイス シミュレーションが要件に合わない場合は、コピーして修正するための[ソース コードを GitHub から入手する](https://github.com/Azure/device-simulation-dotnet)こともできます。

## <a name="sample-simulations"></a>サンプル シミュレーション

デバイス シミュレーションをデプロイすると、いくつかのサンプル シミュレーションとサンプル デバイスが取得されます。 これらのサンプルを使用して、デバイス シミュレーションの使用方法を確認できます。 最初に、[10 台のトラックをシミュレートするサンプル シミュレーション](quickstart-device-simulation-deploy.md)を実行します。 [用意されている多数のサンプル デバイスのいずれかを使用して独自のシミュレーションを作成する](iot-accelerators-device-simulation-create-simulation.md)こともできます。

![シミュレーションの構成](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>カスタムのシミュレートされたデバイス

デバイス シミュレーションを使用して、シミュレーションで使用する[カスタム デバイス モデルを作成](iot-accelerators-device-simulation-create-custom-device.md)できます。 たとえば、温度と湿度のテレメトリを送信する新しい冷蔵庫デバイス モデルを定義できます。 カスタムのシミュレートされたデバイスは、テレメトリの値がランダムに増減する単純なデバイスの動作に最適です。

![デバイス モデルを作成する](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>高度なシミュレートされたデバイス

デバイスが送信するテレメトリ値を細かく制御する必要がある場合は、高度なデバイス モデルを使用できます。 高度なデバイス モデルでは、送信されるテレメトリ値を操作するための JavaScript のサポートが可能です。 たとえば、炎天下に駐車している車の車内温度をシミュレートできます。この場合、外気温が上昇すると、車内温度が指数関数的に上昇します。

高度なデバイス モデルを使用して、JSON デバイス定義ファイルと対応する JavaScript ファイルで構成される[独自のデバイス モデルを作成してアップロードする](iot-accelerators-device-simulation-advanced-device.md)ことができます。

高度なデバイス モデルを使用して、以下を実行できます。

* テレメトリの種類と共に、デバイスから送信されるメッセージの形式を指定します。
* カスタム スクリプトを使用して、一定時間デバイスの状態を保持するテレメトリ値を生成します。
* カスタム スクリプトを使用して、シミュレートされたデバイスがメソッドにどのように応答するかを指定します。

## <a name="next-steps"></a>次の手順

この記事では、デバイス シミュレーション ソリューション アクセラレータとその機能について説明しました。 このソリューション アクセラレータの使用を開始するには、「クイック スタート: 

> [!div class="nextstepaction"]
> [Azure で IoT デバイス シミュレーションをデプロイして実行する](quickstart-device-simulation-deploy.md)」に進みます。
