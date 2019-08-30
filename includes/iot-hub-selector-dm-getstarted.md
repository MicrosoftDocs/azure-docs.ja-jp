---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558802"
---
> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

バックエンド アプリは、Azure IoT Hub のプリミティブ ([デバイス ツイン][lnk-devtwin]や[ダイレクト メソッド][lnk-c2dmethod]など) を使用して、デバイス上でデバイス管理アクションをリモートで開始して監視できます。 このチュートリアルでは、バックエンド アプリとデバイス アプリをどのように連携させると、IoT Hub を使用してデバイスの再起動をリモートで開始して監視できるかについて示します。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

ダイレクト メソッドを使用して、クラウド内のバックエンド アプリケーションからデバイス管理操作 (再起動、出荷時の設定に戻す、ファームウェアの更新など) を開始します。 デバイスは次の操作を担当します。

* IoT Hub から送信されたメソッド要求の処理。

* デバイスでの対応するデバイス固有の操作の開始。

* "*報告されるプロパティ*" を介した IoT Hub への状態更新の提供。

クラウドでバックエンド アプリを使用してデバイス ツインのクエリを実行することで、デバイス管理操作の進行状況を報告できます。

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
