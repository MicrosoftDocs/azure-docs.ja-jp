---
title: カスタムのシミュレートされたデバイスを作成する - Azure | Microsoft Docs
description: このチュートリアルでは、デバイス シミュレーションを使用して、シミュレーションで使用するためのカスタムのシミュレートされたデバイスを作成します。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "61450544"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>チュートリアル: カスタムのシミュレートされたデバイスの作成

このチュートリアルでは、デバイス シミュレーションを使用して、シミュレーションで使用するためのカスタムのシミュレートされたデバイスを作成します。 デバイス シミュレーションの作業を開始するには、付属しているサンプルのシミュレートされたデバイスのいずれかを使用できます。 この記事で説明するように、カスタムのシミュレートされたデバイスを作成することもできます。 カスタマイズ オプションの詳細については、「[Create an advanced device model (高度なデバイス モデルを作成する)](iot-accelerators-device-simulation-advanced-device.md)」を参照してください。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * シミュレートされたデバイス モデルの一覧を表示する
> * カスタムのシミュレートされたデバイスを作成する
> * デバイス モデルを複製する
> * デバイス モデルを削除する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、Azure サブスクリプションにデバイス シミュレーションのインスタンスがデプロイされている必要があります。

デバイス シミュレーションをまだデプロイしていない場合は、[Azure での IoT デバイス シミュレーションのデプロイと実行](quickstart-device-simulation-deploy.md)に関するクイック スタートを完了する必要があります。

## <a name="open-device-simulation"></a>デバイス シミュレーションを開く

ご利用のブラウザーでデバイス シミュレーションを実行するには、まず、[Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com) に移動します。

Azure サブスクリプションの資格情報を使用してサインインするように求められる場合があります。

次に、[Azure での IoT デバイス シミュレーションのデプロイと実行](quickstart-device-simulation-deploy.md)に関するクイック スタートでデプロイしたデバイス シミュレーションのタイルの **[起動]** をクリックします。

## <a name="view-your-device-models"></a>デバイス モデルを表示する

メニュー バーで **[Device models]\(デバイス モデル\)** を選択します。 **[Device models]\(デバイス モデル\)** ページに、デバイス シミュレーションのこのインスタンスで利用可能なすべてのデバイス モデルの一覧が表示されます。

![デバイス モデル](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>デバイス モデルを作成する

ページの右上隅にある **[+ Add Device Models]\(+ デバイス モデルの追加\)** をクリックします。

![デバイス モデルを追加する](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

このチュートリアルでは、温度と湿度の両方のデータを送信する、シミュレートされた冷蔵庫を作成します。

フォームに次の情報を入力します。

| 設定             | 値                                                |
| ------------------- | ---------------------------------------------------- |
| Device model name (デバイス モデル名)   | Refrigerator (冷蔵庫)                                         |
| モデルの説明   | A refrigerator with temperature and humidity sensors (温度計および湿度計付きの冷蔵庫) |
| Version             | 1.0                                                  |

> [!NOTE]
> デバイス モデルの名前は、一意である必要があります。

**[+ Add data point]\(+ データ ポイントの追加\)** をクリックし、次の値で温度と湿度のデータ ポイントを追加します。

| Data Point (データ ポイント)          | 動作        | Min Value (最小値) | Max Value (最大値) | ユニット |
| ------------------- | --------------- | --------- | --------- | ---- |
| 気温         | ランダム          | -50       | 100       | F    |
| 湿度            | ランダム          | 0         | 100       | %    |

**[保存]** をクリックしてデバイス モデルを保存します。

![デバイス モデルを作成する](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

冷蔵庫がデバイス モデルの一覧に表示されるようになりました。 冷蔵庫を見るために、 **[次へ]** をクリックして別のページに移動しなくてはならない場合があります。

## <a name="clone-a-device-model"></a>デバイス モデルを複製する

デバイス モデルを複製すると、既存のデバイス モデルのコピーを作成することができます。 その後、特定のニーズを満たすように、コピーを編集することができます。 複製することにより、類似のデバイス モデルを作成する必要がある場合に時間を節約できます。

デバイス モデルを複製するには、モデルの横にあるボックスをオンにし、操作バーの **[複製]** をクリックします。

![デバイス モデルを削除する](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>デバイス モデルを削除する

任意のカスタム デバイス モデルを削除することができます。 デバイス モデルを削除するには、モデルの横にあるボックスをオンにし、操作バーの **[削除]** をクリックします。

![デバイス モデルを削除する](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カスタム デバイス モデルを作成、複製、および削除する方法について説明しました。 デバイス モデルの詳細については、以下のハウツー記事を参照してください。

> [!div class="nextstepaction"]
> [高度なデバイス モデルを作成する](iot-accelerators-device-simulation-advanced-device.md)