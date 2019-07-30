---
title: Azure IoT Central デバイス ブリッジを構築する |Microsoft Docs
description: 他の IoT クラウド (Sigfox、Particle、The Things Network など) を IoT Central アプリに接続するための IoT Central デバイス ブリッジを構築します。
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: c98574bc3447482429d7a028c6b47197e08e2e38
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850229"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>IoT Central デバイス ブリッジを構築して他の IoT クラウドを IoT Central に接続する

*このトピックでは、管理者に適用されます。*

IoT Central デバイス ブリッジは、Sigfox、Particle、The Things Network などの他のクラウドを IoT Central アプリに接続するオープン ソース ソリューションです。 Sigfox の LPWAN (Low-Power-Wide Area Network: 低消費電力広域通信網) に接続された資産追跡デバイスや Particle Device Cloud の大気監視デバイス、TTN の土壌水分監視デバイスなど、どのようなデバイスを使用している場合でも、IoT Central デバイス ブリッジを使用して、IoT Central のパワーを直接利用できます。 デバイス ブリッジでは、IoT Central アプリを通じて他のクラウドに送信されるデバイスのデータを転送 るすることによって、他の IoT クラウドを IoT Central に接続します。 IoT Central アプリでは、データに対する規則の作成と分析の実行、Microsoft Flow と Azure Logic Apps でのワークフローの作成、データのエクスポートなどを実行できます。 GitHub から [IoT Central デバイス ブリッジ](https://aka.ms/iotcentralgithubdevicebridge)を取得してください。

## <a name="what-is-it-and-how-does-it-work"></a>その概要とそのしくみ
IoT Central デバイス ブリッジは、GitHub のオープン ソース ソリューションです。 カスタム Azure Resource Manager テンプレートといくつかの Azure リソースを Azure サブスクリプションにデプロイする [Deploy to Azure (Azure へのデプロイ)] ボタンが用意されています。 リソースには次のものがあります。
-   Azure 関数アプリ
-   Azure Storage アカウント
-   従量課金プラン
-   Azure Key Vault

関数アプリはデバイス ブリッジの重要な要素です。 それは、単純な Webhook の統合によって、他の IoT プラットフォームまたは任意のカスタム プラットフォームから HTTP POST 要求を受信します。 Sigfox、Particle、および TTN の各クラウドに接続する方法を示す例が提供されています。 お使いのプラットフォームで関数アプリに HTTP POST 要求を送信できる場合は、カスタムの IoT クラウドに接続するようにこのソリューションを簡単に拡張できます。
関数アプリでは、IoT Central で受け入れられる形式にデータを変換し、DPS API を使用してそれを転送します。

![Azure 関数のスクリーンショット](media/howto-build-iotc-device-bridge/azfunctions.png)

IoT Central アプリで、転送されたメッセージ内のデバイス ID によってデバイスが認識された場合は、そのデバイスの新しい測定結果が表示されます。 IoT Central アプリで認識されたことがないデバイス ID が見つかった場合は、関数アプリによって、そのデバイス ID を持つ新しいデバイスの登録が試行され、デバイスは "関連付けられていないデバイス" として IoT Central アプリに表示されます。 

## <a name="how-do-i-set-it-up"></a>設定方法
手順は、GitHub リポジトリの README ファイルに詳細に記載されています。 

## <a name="pricing"></a>価格
Azure リソースは Azure サブスクリプションで提供されます。 価格に関する詳細については、[README ファイル](https://aka.ms/iotcentralgithubdevicebridge)をお読みください。

## <a name="next-steps"></a>次の手順
ここでは、IoT Central デバイス ブリッジを構築する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイスの管理](howto-manage-devices.md)
