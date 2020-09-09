---
title: インクルード ファイル
description: インクルード ファイル
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "67181611"
---
> [!div class="op_single_selector"]
> * [Windows 上の C](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [Linux 上の C](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Raspberry Pi の C](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (汎用)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Raspberry Pi の Node.js](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

このチュートリアルでは、次のテレメトリを、リモート監視の[ソリューション アクセラレータ](../articles/iot-accelerators/about-iot-accelerators.md)に送信する **Chiller** デバイスを実装します。

* 気温
* 圧力
* 湿度

わかりやすくするために、コードでは、**Chiller** に対してサンプル テレメトリ値を生成します。 このサンプルを拡張するには、実際のセンサーをデバイスに接続し、実際のテレメトリを送信します。

サンプル デバイスでは、次の操作も行います。

* メタデータをソリューションに送信して、機能を記述する。
* ソリューションの **[デバイス]** ページからトリガーされたアクションに応答する。
* ソリューションの **[デバイス]** ページから送信された構成変更に応答する。

このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。

## <a name="before-you-start"></a>開始する前に

デバイス用のコードを作成する前に、リモート監視ソリューション アクセラレータをデプロイし、そのソリューションに新しい実在のデバイスを追加します。

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータをデプロイする

このチュートリアルで作成する **Chiller** デバイスは、[リモート監視](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)ソリューション アクセラレータのインスタンスにデータを送信します。 リモート監視ソリューション アクセラレータを Azure アカウントにまだプロビジョニングしていない場合は、「[Deploy the remote monitoring solution accelerator (リモート監視ソリューション アクセラレータをデプロイする)](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)」を参照してください

リモート監視ソリューションのデプロイ プロセスが完了したら、 **[起動]** をクリックしてブラウザーでソリューション ダッシュボードを開きます。

![ソリューション ダッシュボード](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>デバイスをリモート監視ソリューションに追加する

> [!NOTE]
> ソリューションにデバイスを既に追加している場合は、この手順を省略して構いません。 ただし、次の手順では、デバイスの接続文字列が必要です。 デバイスの接続文字列は、[Azure Portal](https://portal.azure.com) から、または [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI ツールを使用して取得できます。

デバイスがソリューション アクセラレータに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。 ソリューションにデバイスを追加するときに、これらの資格情報を含むデバイスの接続文字列を保存する機会が与えられます。 このチュートリアルの後半で、クライアント アプリケーションにデバイスの接続文字列を含めます。

デバイスをリモート監視ソリューションに追加するには、ソリューションの **[デバイス エクスプローラー]** ページで次の手順を実行します。

1. **[+ 新規デバイス]** を選択し、 **[デバイスの種類]** で **[実際]** を選択します。

    ![実デバイスの追加](media/iot-suite-selector-connecting/devicesprovision.png)

1. デバイス ID として「**Physical-chiller**」と入力します。 **[対称キー]** オプションと **[キーの自動生成]** オプションを選択します。

    ![デバイス オプションを選択する](media/iot-suite-selector-connecting/devicesoptions.png)

1. **[適用]** を選択します。 **デバイス ID**、**主キー**、**接続文字列の主キー**の値をメモします。

    ![資格情報の取得](media/iot-suite-selector-connecting/credentials.png)

これで、実在のデバイスをリモート監視ソリューション アクセラレータに追加して、デバイスの接続文字列をメモできました。 以降のセクションでは、デバイスの接続文字列を使用してソリューションに接続するクライアント アプリケーションを実装します。

クライアント アプリケーションでは、組み込みの **Chiller** デバイス モデルが実装されます。 ソリューション アクセラレータのデバイス モデルは、デバイスについて次の情報を指定します。

* デバイスがソリューションにレポートするプロパティ。 たとえば、**Chiller** デバイスは、そのファームウェアと位置に関する情報をレポートします。
* デバイスがソリューションに送信するテレメトリの種類。 たとえば、**Chiller** デバイスは、温度、湿度、および気圧の値を送信します。
* ソリューションからスケジュールしてデバイスで実行できるメソッド。 たとえば、**Chiller** デバイスは、**Reboot**、**FirmwareUpdate**、**EmergencyValveRelease**、および **IncreasePressure** メソッドを実装する必要があります。
