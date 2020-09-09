---
title: Azure IoT Hub デバイスの顧客データ要求
description: Azure IoT Hub で管理されるデバイスのほとんどは個人用でありませんが、一部は個人用です。 この記事では、管理者がデバイスから個人データをエクスポートしたり、削除したりする方法について説明します。
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 0bb7b91cd31761b801ad469b8ce556b29e09fd3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074708"
---
# <a name="customer-data-request-features-for-azure-iot-hub-devices"></a>Azure IoT Hub デバイスの顧客データ要求機能

Azure IoT Hub は、企業のお客様を対象とする REST API ベースのクラウド サービスであり、百万単位のデバイスとパーティション分割された Azure サービスの間で、セキュリティで保護された双方向の通信を有効にします。

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

個々のデバイスには、テナント管理者によってデバイス識別子 (デバイス ID) が割り当てられます。 デバイスのデータは、割り当てられているデバイス ID に基づきます。 Microsoft は、デバイス ID とユーザーを関連付けることができる情報を保持せず、そのようなデータにアクセスすることもできません。

Azure IoT Hub で管理されているデバイスの多くは、オフィスの温度自動調節器や工場のロボットなどのように、個人用デバイスではありません。 しかし、お客様が一部のデバイスで個人を特定できるようにすることを検討し、自己判断で、自身の資産やデバイスと個人を結び付けるインベントリ追跡方法を維持する場合があります。 Azure IoT Hub はデバイスに関連付けられたすべてのデータを個人データであるかのように管理し、格納します。

テナント管理者は、Azure portal またはサービスの REST API を使って、デバイス ID に関連付けられているデータをエクスポートまたは削除することにより、情報の要求を満たすことができます。

Azure IoT Hub サービスのルーティング機能を使ってデバイスのメッセージを他のサービスに転送する場合、特定のデバイスに対する完全な要求を完了するためには、テナント管理者が各ルーティング エンドポイントに対してデータ要求を実行する必要があります。 詳しくは、各エンドポイントのリファレンス ドキュメントをご覧ください。 サポートされるエンドポイントについて詳しくは、「[リファレンス - IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)」をご覧ください。

Azure IoT Hub サービスの Azure Event Grid 統合機能を使う場合は、テナント管理者がそれらのイベントの各サブスクライバーに対してデータ要求を実行する必要があります。 詳しくは、「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](iot-hub-event-grid.md)」をご覧ください。

Azure IoT Hub サービスの Azure Monitor 統合機能を使って診断ログを作成する場合は、テナント管理者が格納されているログに対してデータ要求を実行する必要があります。 詳しくは、「[Azure IoT Hub の正常性の監視](iot-hub-monitor-resource-health.md)」をご覧ください。

## <a name="deleting-customer-data"></a>顧客データの削除

テナント管理者は、Azure portal の Azure IoT Hub 拡張機能の IoT デバイス ブレードを使って、デバイスを削除することができます。それにより、そのデバイスに関連付けられているデータが削除されます。

REST API を使ってデバイスの削除操作を実行することもできます。 詳しくは、[サービス - デバイスの削除](/rest/api/iothub/service/registrymanager/deletedevice)に関する記事をご覧ください。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

テナント管理者は、Azure portal の Azure IoT Hub 拡張機能の IoT デバイス ウィンドウ内でコピーと貼り付けを利用して、デバイスに関連付けられているデータをエクスポートできます。

REST API を使ってデバイスのエクスポート操作を実行することもできます。 詳しくは、[サービス - デバイスの取得](/rest/api/iothub/service/registrymanager/getdevice)に関する記事をご覧ください。

> [!NOTE]
> Microsoft のエンタープライズ サービスを使うと、システム生成ログと呼ばれる情報がいくつか生成されます。 Azure IoT Hub システムによって生成されたログの一部は、テナント管理者がアクセスしたりエクスポートしたりすることはできません。 これらのログは、個々のデバイスに関連するサービスと診断データ内で実行された実際のアクションで構成されています。

## <a name="links-to-additional-documentation"></a>その他のドキュメントへのリンク

Azure IoT Hub Service API の完全なドキュメントは、[IoT Hub Service API](https://docs.microsoft.com/rest/api/iothub/service/configuration) にあります。
