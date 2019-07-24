---
title: デバイス シミュレーション ソリューションを試して実行する - Azure | Microsoft Docs
description: このクイック スタートでは、Azure IoT Device Simulation をデプロイし、シミュレーションを実行します
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 0fcd688c9e5164522a482a4375c524a97a48d400
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175626"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>クイック スタート:Azure で IoT デバイス シミュレーションをデプロイして実行する

このクイック スタートでは、IoT ソリューションをテストするために Azure IoT デバイス シミュレーションをデプロイする方法について説明します。 ソリューション アクセラレータをデプロイした後、サンプル シミュレーションを実行して、作業を開始します。

このクイック スタートを完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="deploy-device-simulation"></a>デバイス シミュレーションのデプロイ

デバイス シミュレーションを Azure サブスクリプションにデプロイするときは、いくつかの構成オプションを設定する必要があります。

Azure アカウントの資格情報を使用して、[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) にサインインします。

**[デバイス シミュレーション]** タイルをクリックします。

![デバイス シミュレーションの選択](./media/quickstart-device-simulation-deploy/devicesimulation.png)

デバイス シミュレーションの説明ページで、**[今すぐ試してみる]** をクリックします。

![[今すぐ試してみる] をクリックする](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

**[Create Device Simulation solution]\(デバイス シミュレーション ソリューションの作成\)** ページで、一意の **[Solution name]\(ソリューション名\)** を入力します。

ソリューション アクセラレータのデプロイに使用する**サブスクリプション**と**リージョン**を選択します。 通常は、最も近いリージョンを選択します。 サブスクリプションの[グローバル管理者またはユーザー](iot-accelerators-permissions.md)である必要があります。

ボックスをオンにして、デバイス シミュレーション ソリューションで使用する IoT ハブをデプロイします。 シミュレーションが使用する IoT ハブは、後でいつでも変更できます。

**[作成]** をクリックして、ソリューションのプロビジョニングを始めます。 このプロセスを実行するには、少なくとも 5 分かかります。

![デバイス シミュレーション ソリューションの詳細](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>ソリューションにサインインします。

プロビジョニング プロセスが完了したら、**[起動]** ボタンをクリックして、デバイス シミュレーションのインスタンスにサインインすることができます。

![デバイス シミュレーションを開く](./media/quickstart-device-simulation-deploy/choosenew.png)

**[同意]** をクリックしてアクセス許可の要求を受け入れると、ブラウザーにデバイス シミュレーション ソリューション ダッシュボードが表示されます。

初めて開くと、デバイス シミュレーション ダッシュボードに**ファースト ステップ** ガイドが表示されます。 サンプル シミュレーションを開くには、最初のタイルをクリックします。 **ファースト ステップ** ガイドを閉じた場合、**サンプルの簡単なシミュレーション**を開くには、ダッシュボードでそのタイルをクリックします。

![ソリューションのダッシュボード](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>サンプル シミュレーション

サンプル シミュレーションは、編集できません。 シミュレーションは、次の設定で構成されています。

| Setting             | 値                       |
| ------------------- | --------------------------- |
| IoT Hub を対象にする      | Use pre-provisioned IoT Hub (事前プロビジョニングされた IoT Hub を使用する) |
| デバイスのモデル        | トラック                       |
| Number of devices (デバイス数)   | 10                          |
| Telemetry frequency (テレメトリ頻度) | 10 秒                  |
| シミュレーション期間 | Run indefinitely (無期限に実行する)            |

![シミュレーションの構成](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>シミュレーションを実行する

**[シミュレーションの開始]** をクリックします。 シミュレーションは、構成されているように、無期限に実行されます。 いつでも、**[シミュレーションの停止]** をクリックしてシミュレーションを停止できます。 シミュレーションで、現在の実行の統計情報が表示されます。

![シミュレーションの実行](./media/quickstart-device-simulation-deploy/runningsimulation.png)

デバイス シミュレーション インスタンスから実行できるシミュレーションは、一度に 1 つだけです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

さらに調べる場合は、デバイス シミュレーションをデプロイしたままにします。

デバイス シミュレーションが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページでそのタイルをクリックし、**[ソリューションの削除]** をクリックして削除してください。

![ソリューションを削除する](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、デバイス シミュレーションをデプロイし、サンプル IoT デバイス シミュレーションを実行しました。

> [!div class="nextstepaction"]
> [1 つまたは複数のデバイスの種類があるシミュレーションを作成する](iot-accelerators-device-simulation-create-simulation.md)