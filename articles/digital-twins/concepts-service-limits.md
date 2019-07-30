---
title: Azure Digital Twins パブリック プレビュー サービスの制限 | Microsoft Docs
description: Azure Digital Twins パブリック プレビュー サービスの制限について説明します。
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalther
ms.openlocfilehash: f3239fa5d21078795e7b063cb0364e905cba25c1
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846623"
---
# <a name="public-preview-service-limits"></a>パブリック プレビューのサービスの制限

パブリック プレビュー期間中の Azure Digital Twins には、サブスクリプション、インスタンス、レートに関して以下の一時的な制限があります。

これらの制約は、新しいサービスとその多くの機能についての学習を簡単にするために設けられています。

> [!NOTE]
> これらの制限は、一般公開 (GA) の時点で増やされたり除去されたりします。

## <a name="per-subscription-limits"></a>サブスクリプションごとの制限

パブリック プレビュー期間中、各 Azure サブスクリプションで作成または実行できる Azure Digital Twins インスタンスは、厳密に一度に 1 つだけです。

> [!TIP]
> インスタンスを削除すると、新しいインスタンスを作成できます。

## <a name="per-instance-limits"></a>インスタンスごとの制限

さらに、Azure Digital Twins の各インスタンスでは次のものを使用できます。

- サービスのプロビジョニング中に自動的に作成される組み込み **IoTHub** リソースが厳密に 1 つだけ。
- イベントの種類が **DeviceMessage** であるちょうど 1 つの **EventHub** エンドポイント。
- イベントの種類が **SensorChange**、**SpaceChange**、**TopologyOperation**、または **UdfCustom** である、最大 3 つの **EventHub**、**ServiceBus**、または **EventGrid** エンドポイント。

> [!NOTE]
> 上記の Azure IoT のエンティティの作成で通常定義されるパラメーターの一部は、パブリック プレビュー中には必要ではありません。
> - 最新の API の仕様については、[Swagger リファレンス ドキュメント](./how-to-use-swagger.md)を参照してください。

## <a name="azure-digital-twins-management-api-limits"></a>Azure Digital Twins Management API の制限

Azure Digital Twins Management API に対する要求のレート制限は次のとおりです。

- Azure Digital Twins Management API に対して 1 秒あたり 100 件の要求。
- 1 つの Azure Digital Twins Management API クエリで返されるオブジェクトは最大 1,000 個。

> [!IMPORTANT]
> 1,000 オブジェクトの制限を超過する場合は、エラーが発生し、クエリを簡素化する必要があります。

## <a name="user-defined-functions-rate-limits"></a>ユーザー定義関数のレート制限

次の制限では、Azure Digital Twins インスタンスに対して行うことができるすべてのユーザー定義関数呼び出しの合計数が設定されます。

- 1 秒あたり 400 回のクライアント ライブラリ呼び出し
- 1 秒あたり 100 回の **SendNotification** 呼び出し

> [!NOTE]
> 次の操作により、追加のレート制限が一時的に適用される場合があります。
> - トポロジ オブジェクト メタデータに対して行われる編集
> - ユーザー定義関数の定義に加えられた更新
> - 初めてテレメトリを送信するデバイス

## <a name="device-telemetry-limits"></a>デバイスのテレメトリ制限

以下の制限では、デバイスが Azure Digital Twins インスタンスに送信できる全メッセージの合計数が制限されます。

- 1 秒あたり 100 件のメッセージ

## <a name="next-steps"></a>次の手順

- Azure Digital Twins のサンプルを試すには、[使用可能な部屋を検索するクイック スタート](./quickstart-view-occupancy-dotnet.md)のページをご覧ください。