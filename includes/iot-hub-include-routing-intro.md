---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85128214"
---
[メッセージ ルーティング](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)を使うと、IoT デバイスから組み込みイベント ハブ互換エンドポイントまたはカスタム エンドポイント (BLOB ストレージ、Service Bus キュー、Service Bus トピック、Event Hubs など) に、利用統計情報を送信できます。 カスタム メッセージ ルーティングを構成するには、特定の条件と一致するルートをカスタマイズするための[ルーティング クエリ](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)を作成できます。 設定が済むと、受信データは IoT Hub によってエンドポイントに自動的にルーティングされるようになります。 メッセージが定義されているルーティング クエリのいずれとも一致しない場合、メッセージは既定のエンドポイントにルーティングされます。

この 2 部構成のチュートリアルでは、IoT Hub を使用してこれらのカスタム ルーティング クエリを設定および使用する方法を学習します。 IoT デバイスから、BLOB ストレージや Service Bus キューなどの複数のエンドポイントのいずれかにメッセージをルーティングします。 Service Bus キューへのメッセージは、ロジック アプリによって取得されて、メールで送信されます。 カスタム メッセージ ルーティングが定義されていないメッセージは、既定のエンドポイントに送信された後、Azure Stream Analytics によって取得され、Power BI の視覚化に表示されます。

このチュートリアルのパート 1 とパート 2 を完了するには、以下のタスクを実行します。

**パート I: リソースを作成してメッセージ ルーティングを設定する**
> [!div class="checklist"]
> * リソースを作成する (IoT ハブ、ストレージ アカウント、Service Bus キュー、シミュレートされたデバイス)。 この操作は、Azure portal、Azure Resource Manager テンプレート、Azure CLI、または Azure PowerShell を使用して実行できます。
> * ストレージ アカウントと Service Bus キューに対するエンドポイントとメッセージ ルートを IoT Hub で構成する。

**パート II: メッセージをハブに送信して、ルーティングされた結果を表示する**
> [!div class="checklist"]
> * メッセージが Service Bus キューに追加されるとトリガーされてメールを送信するロジック アプリを作成します。
> * 異なるルーティング オプションでハブにメッセージを送信する IoT デバイスをシミュレートするアプリをダウンロードして実行します。
> * 既定のエンドポイントに送信されたデータに対する Power BI の視覚エフェクトを作成します。
> * 結果を表示します ...
> * ... Service Bus キューおよびメール内の結果。
> * ... ストレージ アカウント内の結果。
> * ... Power BI の視覚エフェクト内の結果。

## <a name="prerequisites"></a>前提条件

* このチュートリアルのパート 1:
  - Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* このチュートリアルのパート 2:
  - このチュートリアルのパート 1 を完了し、リソースが引き続き利用可能である必要があります。
  - [Visual Studio](https://www.visualstudio.com/) のインストール。
  - 既定のエンドポイントの Stream Analytics を分析するために Power BI アカウントにアクセスできる。 ([Power BI を無料で試す](https://app.powerbi.com/signupredirect?pbi_source=web))
  - 通知メールを送信するための職場または学校アカウントを持っている。
  - ポート 8883 がファイアウォールで開放されていることを確認してください。 このチュートリアルのサンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
