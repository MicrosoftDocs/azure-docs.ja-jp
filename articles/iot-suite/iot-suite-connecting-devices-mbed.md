---
title: "mbed で C を使用してデバイスを接続する | Microsoft Docs"
description: "C で記述され、mbed デバイスで実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: df9772796796f7383aafc583b01f299a53679d88
ms.openlocfilehash: 12535cbb6fa63c24dd63903380d697f8f38db6f9
ms.lasthandoff: 02/27/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>デバイスをリモート監視構成済みソリューションに接続する (mbed)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>C のサンプル ソリューションをビルドして実行する

次に、[mbed 対応の Freescale FRDM-K64F][lnk-mbed-home] デバイスをリモート監視ソリューションに接続する手順を説明します。

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>mbed デバイスをネットワークおよびデスクトップ コンピューターに接続する

1. イーサネット ケーブルを使用して mbed デバイスをネットワークに接続する方法を説明します。 サンプル アプリケーションでは、インターネットへのアクセスが必要なため、この手順は必須です。

1. mbed デバイスをデスクトップ PC に接続する方法の詳細については、「[mbed の概要][lnk-mbed-getstarted]」を参照してください。

1. デスクトップ PC で Windows を実行している場合は、「[PC の構成][lnk-mbed-pcconnect]」を参照して、mbed デバイスへのシリアル ポート アクセスを構成します。

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>mbed プロジェクトを作成してサンプル コードをインポートする

mbed プロジェクトにサンプル コードをいくつか追加するには、次の手順に従います。 リモート監視のスタート プロジェクトをインポートし、AMQP プロトコルではなく MQTT プロトコルを使用するようにプロジェクトを変更します。 現時点では、IoT Hub のデバイス管理機能を使用するには MQTT プロトコルを使用する必要があります。

1. Web ブラウザーで、mbed.org の [開発者向けサイト](https://developer.mbed.org/)に移動します。 サインアップしていない場合は、アカウントを作成するオプションが表示されます (アカウントの作成は無料です)。 既にサインアップしている場合は、アカウントの資格情報を使用してログインします。 次に、ページの右上隅の **[Compiler]** をクリックします。 このアクションにより、*ワークスペース* インターフェイスが表示されます。

1. 使用しているハードウェア プラットフォームがウィンドウの右上隅に表示されていることを確認するか、右上隅にあるアイコンをクリックしてハードウェア プラットフォームを選択します。

1. メイン メニューの **[インポート]** をクリックします。 **[Click here to import from URL (URL からインポートするにはここをクリック)]** をクリックします。
   
    ![mbed ワークスペースへのインポートを開始する][6]

1. ポップアップ ウィンドウで、サンプル コードのリンク「https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/」を入力し、**[インポート]** をクリックします。
   
    ![mbed ワークスペースにサンプル コードをインポートする][7]

1. mbed コンパイラのウィンドウで、このプロジェクトをインポートしたことでさまざまなライブラリもインポートされたことを確認できます。 ライブラリには、Azure IoT チームが提供および管理するライブラリ ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)) もあれば、mbed ライブラリ カタログで入手可能なサードパーティのライブラリもあります。
   
    ![mbed プロジェクトを表示する][8]

1. **[Program Workspace (プログラム ワークスペース)]** で、**iothub\_amqp\_transport** ライブラリを右クリックし、**[削除]**、**[OK]** の順にクリックして確定します。

1. **[Program Workspace (プログラム ワークスペース)]** で、**azure\_amqp\_c** ライブラリを右クリックし、**[削除]**、**[OK]** の順にクリックして確定します。

1. **[Program Workspace (プログラム ワークスペース)]** で **remote_monitoring** プロジェクトを右クリックし、**[インポート ライブラリ]**、**[URL から]** の順に選択します。
   
    ![mbed ワークスペースへのライブラリのインポートを開始する][6]

1. ポップアップ ウィンドウで、MQTT トランスポート ライブラリのリンク「https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/」を入力し、**[インポート]** をクリックします。
   
    ![mbed ワークスペースにライブラリをインポートする][12]

1. 前の手順を繰り返して、https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/ から MQTT ライブラリを追加します。

1. ワークスペースは、次のようになります。

    ![mbed ワークスペースを表示する][13]

1. remote\_monitoring\remote_monitoring.c ファイルを開き、既存の `#include` ステートメントを次のコードで置き換えます。

    ```
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. remote\_monitoring\remote\_monitoring.c ファイルの残りのコードをすべて削除します。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

**remote\_monitoring\_run** 関数を呼び出すコードを追加し、デバイス アプリケーションをビルドして実行します。

1. **remote\_monitoring\_run** 関数を呼び出す次のコードを備えた **main** 関数を remote\_monitoring.c ファイルの末尾に追加します。
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **[コンパイル]** をクリックしてプログラムをビルドします。 警告は無視してかまいません。ただし、ビルドでエラーが発生する場合は、続行する前にそのエラーを修正してください。

1. ビルドが成功した場合、mbed コンパイラの Web サイトによって、プロジェクト名の付いた .bin ファイルが生成され、ローカル コンピューターにダウンロードされます。 .bin ファイルをデバイスにコピーします。 .bin ファイルをデバイスに保存すると、デバイスが再起動し、.bin ファイルに含まれるプログラムが実行されます。 mbed デバイスのリセット ボタンを押すことで、いつでもプログラムを手動で再起動することができます。

1. PuTTY などの SSH クライアント アプリケーションを使用して、デバイスに接続します。 Windows デバイス マネージャーを確認すると、デバイスで使用されているシリアル ポートを特定できます。
   
    ![][11]

1. PuTTY で、接続タイプとして **[シリアル]** をクリックします。 通常、デバイスは 9600 ボーで接続するため、 **[Speed]** ボックスに「9600」と入力します。 次に、 **[Open]**をクリックします。

1. プログラムの実行が開始されます。 接続時にプログラムが自動的に開始されない場合は、ボードのリセットが必要になることがあります (Ctrl キーを押しながら Break キーを押すか、ボードのリセット ボタンを押します)。
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

