---
title: 新しい IoT シミュレーションを作成する - Azure | Microsoft Docs
description: このチュートリアルでは、デバイス シミュレーションを使用して、新しいシミュレーションを作成し、実行します。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: e0d7310c4863c8dd431b991a2c249f2d8e257aeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852355"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>チュートリアル: IoT デバイス シミュレーションを作成して実行する

このチュートリアルでは、デバイス シミュレーションを使用して、1 台または複数台のデバイスで新しい IoT シミュレーションを作成し、実行します。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * アクティブなシミュレーションと履歴のシミュレーションをすべて表示する
> * 新しいシミュレーションを作成して実行する
> * シミュレーションのメトリックを表示する
> * シミュレーションを停止する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、Azure サブスクリプションにデバイス シミュレーションのインスタンスがデプロイされている必要があります。

まだデバイス シミュレーションをデプロイしていない場合は、GitHub の[デバイス シミュレーションのデプロイ](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)に関する記事を参照してください。

## <a name="view-simulations"></a>シミュレーションを表示する

メニュー バーの **[シミュレーション]** を選択します。 **[シミュレーション]** ページには、使用可能なすべてのシミュレーションについての情報が表示されます。 このページでは、現在実行されているシミュレーションと、以前に実行したシミュレーションを表示できます。 以前のシミュレーションを再実行するには、シミュレーション タイルをクリックしてシミュレーションの詳細を開きます。

![シミュレーション](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>シミュレーションを作成する

シミュレーションを作成するには、右上隅の **[+ 新しいシミュレーション]** をクリックします。

シミュレーションは、1 つまたは複数のデバイス モデルで構成されます。 デバイス モデルでは、シミュレートするデバイスの動作、テレメトリ、およびメッセージ形式を定義します。

デバイス モデルを追加するには、**[+ Add a device type]\(+ デバイスの種類の追加\)** をクリックし、一覧でデバイス モデルを選択します。 シミュレーションには、複数のデバイス モデルを指定することができます。 各デバイス モデルに、異なるデバイス数とメッセージ頻度を設定できます。

新しいシミュレーションのフォームの設定を完了したら、**[シミュレーションの開始]** をクリックしてシミュレーションを開始します。

シミュレートされるデバイスの数によっては、シミュレーションの構成と開始に数分かかる場合があります。

![新しいシミュレーション](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>シミュレーションを停止する

**[シミュレーションの開始]** をクリックすると、シミュレーションの詳細ページが表示されます。 この詳細ページには、シミュレーションのライブの統計情報と IoT Hub からのメトリックが表示されます。 **[シミュレーション]** ページのシミュレーションのタイルをクリックして、この詳細ページに移動することもできます。

シミュレーションを停止するには、右上の操作バーで **[シミュレーションの停止]** をクリックします。

![シミュレーションの停止](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、シミュレーションを作成、実行、および停止する方法について説明しました。 シミュレーションの詳細を表示する方法も説明しました。 シミュレーションの実行方法の詳細を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [シミュレートされたカスタム デバイスの作成](iot-accelerators-device-simulation-create-custom-device.md)
