---
title: "Azure Time Series Insights 環境に IoT Hub イベント ソースを追加する方法 | Microsoft Docs"
description: "このチュートリアルでは、IoT Hub に接続されたイベント ソースを Time Series Insights 環境に追加する方法を説明します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: 3b98728ced81256d05b1bed2c92fc66c5ca61b98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-add-an-iot-hub-event-source"></a>IoT Hub イベント ソースを追加する方法

このチュートリアルでは、Azure Portal を使用して、IoT Hub から読み取りを行うイベント ソースを Time Series Insights 環境に追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

IoT Hub を作成済みであり、これにイベントを書き込んでいること。 IoT Hub の詳細については、<https://azure.microsoft.com/services/iot-hub/> を参照してください。

> [コンシューマー グループ] 各 Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 詳細については、[IoT Hub 開発者ガイド](../iot-hub/iot-hub-devguide.md)をご覧ください。

## <a name="choose-an-import-option"></a>インポート オプションを選択する

イベント ソースの設定は、手動で入力するか、使用可能な IoT Hub からいずれかの IoT Hub を選択することができます。
**[インポート オプション]** セレクターで、次のいずれかのオプションを選択します。

* IoT Hub 設定を手動で行う
* 使用可能なサブスクリプションの IoT Hub を使用する

### <a name="select-an-available-iot-hub"></a>使用可能な IoT Hub を選択する

次の表は、[新しいイベント ソース] タブの各オプションと、使用可能な IoT Hub をイベント ソースとして選択したときの説明を示しています。

| プロパティ名 | Description |
| --- | --- |
| イベント ソース名 | イベント ソースの名前。 この名前は、Time Series Insights 環境内で一意である必要があります。
| から | **IoT Hub** を選択して IoT Hub イベント ソースを作成します。
| サブスクリプション ID | この IoT Hub が作成されたサブスクリプションを選択します。
| IoT Hub 名 | IoT Hub の名前を選択します。
| IoT Hub ポリシー名 | [IoT Hub の設定] タブにある共有アクセス ポリシーを選択します。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続**アクセス許可が "*必要*" です。
| IoT Hub コンシューマー グループ | IoT Hub からデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。

### <a name="provide-iot-hub-settings-manually"></a>IoT Hub 設定を手動で行う

次の表は、[新しいイベント ソース] タブの各プロパティと、設定を手動で入力したときの説明を示しています。

| プロパティ名 | Description |
| --- | --- |
| イベント ソース名 | イベント ソースの名前。 この名前は、Time Series Insights 環境内で一意である必要があります。
| から | **IoT Hub** を選択して IoT Hub イベント ソースを作成します。
| サブスクリプション ID | この IoT Hub が作成されたサブスクリプション。
| リソース グループ | この IoT Hub が作成されたサブスクリプション。
| IoT Hub 名 | IoT Hub の名前。 IoT Hub を作成した場合は、特定の名前も付けられています。
| IoT Hub ポリシー名 | [IoT Hub の設定] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続**アクセス許可が "*必要*" です。
| IoT Hub ポリシー キー | Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。
| IoT Hub コンシューマー グループ | IoT Hub からデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。

## <a name="next-steps"></a>次のステップ

1. 環境にデータ アクセス ポリシーを追加するために[データ アクセス ポリシーを定義する](time-series-insights-data-access.md)
1. [Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境にアクセスする