---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011664"
---
> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

次の場合にデバイス ツインを使用します。

* ソリューション バックエンドからデバイス メタデータを格納する。

* デバイス アプリで利用できる機能や状態 (たとえば、使用される接続方法) などの現在の状態に関する情報を報告する。

* デバイス アプリとバックエンド アプリの間で実行時間の長いワークフロー (ファームウェアや構成の更新など) の状態を同期する。

* デバイス メタデータ、構成、または状態を照会する。

デバイス ツインは、同期のほか、デバイスの構成と状態の照会に対応しています。 デバイス ツインを使用するタイミングの詳細については、[デバイス ツインの理解](../articles/iot-hub/iot-hub-devguide-device-twins.md)に関するドキュメントを参照してください。

デバイス ツインは IoT ハブに格納され、次のものを含みます。

* "*タグ*": ソリューション バックエンドからのみアクセスできるデバイス メタデータです。

* "*必要なプロパティ*": ソリューション バックエンドから変更でき、デバイス アプリから監視できる JSON オブジェクトです。

* "*報告されるプロパティ*": デバイス アプリから変更でき、ソリューション バックエンドから読み取り可能な JSON オブジェクトです。 タグとプロパティに配列を含めることはできませんが、オブジェクトは入れ子にできます。

![デバイス ツインの機能を示した画像](./media/iot-hub-selector-twin-get-started/twin.png)

さらに、ソリューション バックエンドは、上記のすべてのデータに基づいてデバイス ツインに対してクエリを実行できます。
デバイス ツインの詳細については、[デバイス ツイン](../articles/iot-hub/iot-hub-devguide-device-twins.md)に関するページを、クエリについては [IoT Hub クエリ言語](../articles/iot-hub/iot-hub-devguide-query-language.md)のリファレンスを参照してください。


このチュートリアルでは、次の操作方法について説明します。

* デバイス ツインに "*タグ*" を追加するバックエンド アプリのほか、接続チャネルをデバイス ツインの "*報告されるプロパティ*" として報告するシミュレーション対象デバイス アプリを作成します。

* 以前に作成したタグとプロパティのフィルターを使用して、バックエンド アプリからデバイスに対してクエリを実行します。
