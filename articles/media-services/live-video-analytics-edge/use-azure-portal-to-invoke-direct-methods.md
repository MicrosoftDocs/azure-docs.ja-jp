---
title: Azure portal を使用してダイレクト メソッドを呼び出す方法
description: この記事は、Azure portal を使用したダイレクト メソッドの呼び出しに関する概要です。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492656"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Azure portal を使用してダイレクト メソッドを呼び出す方法

IoT Hub には、クラウドからエッジ デバイス上の[ダイレクト メソッド](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules)を呼び出す機能が備わっています。 Live Video Analytics on IoT Edge (LVA) モジュールでは、ライブ ビデオを分析するためにさまざまなワークフローを定義、デプロイ、インスタンス化するために使用できる、いくつかの[ダイレクト メソッド](./direct-methods.md)が公開されています。

この記事では、Azure portal を使用して Live Video Analytics for an IoT Edge モジュールでダイレクト メソッド呼び出しを呼び出す方法について学習します。

## <a name="prerequisites"></a>前提条件

* 次に記載されているいずれかの方法を使用して、エッジ デバイスで Live Video Analytics on IoT Edge モジュールを実行している: [クイック スタート: Live Video Analytics on IoT Edge](./get-started-detect-motion-emit-events-quickstart.md)に関するページ、または[ポータル](./deploy-iot-edge-device.md)を使用。

* [Live Video Analytics](./overview.md) および[メディア グラフの概念](./media-graph-concept.md)を理解します。

## <a name="invoking-direct-methods-via-azure-portal"></a>Azure portal を使用したダイレクト メソッドの呼び出し

LVA モジュールによって公開されている各[ダイレクト メソッド](./direct-methods.md)は、Azure portal を介して呼び出すことができます。 以下の手順は、1 つのダイレクト メソッドの詳細について示しています。 同様の手順を使用して、他のダイレクト メソッドを呼び出すことができます。 しかし、各ダイレクト メソッドには特定の JSON 本文が必要です。

`GraphTopologyList` メソッド呼び出しを使用して、Live Video Analytics on IoT Edge モジュールに現在配置されているすべてのグラフ トポロジの一覧を取得します。 このダイレクト メソッドを呼び出すには、次の手順を使用します。

1. Azure portal にログインします
1. ポータル ホーム ページで関連するリソース グループを見つけて、IoT Hub を見つけます。または、IoT Hub がわかっている場合は、それを選択します。
    ![ポータル ホーム ページのリソース グループ](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. [IoT Hub] ページで [デバイスの自動管理] の [IoT Edge] を選択して、さまざまなデバイス ID を一覧表示します。 関連するデバイス ID を選択して、デバイスで実行されているモジュールの一覧を表示します。
    ![IoT Hub ページ](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Live Video Analytics on IoT Edge モジュールを選択して、その構成ページを表示します。<br><br>
    ![Live Video Analytics on IoT Edge モジュールを選択して、その構成ページを表示する](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. [ダイレクト メソッド] メニュー オプションを選択します。 <br><br>
    ![[ダイレクト メソッド] メニュー オプションをクリックする](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > 現在のページで確認できるように、[接続文字列] セクションに値を追加する必要がある場合があります。 [設定名] セクションは何も非表示にしたり変更したりする必要はありません。 パブリックにしておいてかまいません。

1. **[メソッド名]** ボックスに「*GraphTopologyList*」と入力します。
1. 以下の JSON をコピーして **[ペイロード]** フィールドに貼り付けます。
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. ページの上部にある **[メソッドの呼び出し]** ボタンを選択します。<br><br>
    ![[メソッドの呼び出し] ボタン](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. **[結果]** 領域に、状態が 200 であることを示すメッセージが表示されます。<br><br>
    ![接続タイムアウト](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Responses

| 条件             | 状態コード | 詳細なエラー コード |
|-----------------------|-------------|---------------------|
| Success               | 200         | 該当なし                 |
| 一般的なユーザー エラー   | 400 番台   |                     |
| 一般的なサーバー エラー | 500 番台   |                     |

## <a name="next-steps"></a>次のステップ

ダイレクト メソッドの詳細については、「[ダイレクト メソッド](./direct-methods.md)」ページで見つけることができます。

> [!NOTE]
> グラフ インスタンスによって特定のトポロジがインスタンス化されるため、グラフ インスタンスを作成する前に適切なトポロジを確実に設定してください。

[クイック スタート: モーションの検出とイベントの生成](./get-started-detect-motion-emit-events-quickstart.md)に関するページは、実行するダイレクト メソッド呼び出しの正確なシーケンスを理解するのに適した参照先です。
