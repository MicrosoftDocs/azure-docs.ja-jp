---
title: Azure DPS デバイスの顧客データ要求機能
description: 個人の Azure Device Provisioning Service (DPS) で管理されているデバイス向けに、この記事では、個人データをエクスポートまたは削除する方法について管理者に説明しています。
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967229"
---
# <a name="summary-of-customer-data-request-features"></a>顧客データ要求機能の概要

Azure IoT Hub Device Provisioning Service は、企業のお客様を対象とする REST API ベースのクラウド サービスであり、デバイスで開始されてクラウドで終了する、セキュリティで保護された、Azure IoT Hub へのデバイスのシームレスで自動的なゼロタッチ プロビジョニングを可能にします。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

個々のデバイスには、テナント管理者によって登録 ID とデバイス ID が割り当てられます。 デバイスからのデータおよびデバイスについてのデータは、これらの ID に基づいています。 Microsoft は、デバイスと個人を関連付けることができる情報を保持せず、そのようなデータにアクセスすることもできません。

Device Provisioning Service で管理されているデバイスの多くは、オフィスの温度自動調節器や工場のロボットなどのように、個人用デバイスではありません。 しかし、お客様が一部のデバイスで個人を特定できるようにすることを検討し、自己判断で、自身の資産やデバイスと個人を結び付けるインベントリ追跡方法を維持する場合があります。 Device Provisioning Service は、デバイスに関連付けられたすべてのデータを、個人データであるかのように管理し、格納します。

テナント管理者は、Azure portal またはサービスの REST API を使って、デバイス ID または登録 ID に関連付けられているデータをエクスポートまたは削除することにより、情報の要求を満たすことができます。

> [!NOTE]
> Device Provisioning Service によって Azure IoT Hub にプロビジョニングされたデバイスには、Azure IoT Hub サービスに格納された追加のデータがあります。 特定のデバイスに対する完全な要求を完了するには、[Azure IoT Hub のリファレンス ドキュメント](../iot-hub/iot-hub-customer-data-requests.md)をご覧ください。

## <a name="deleting-customer-data"></a>顧客データの削除

Device Provisioning Service は、登録と登録レコードを保持しています。 登録にはプロビジョニングを許可されたデバイスについての情報が含まれ、登録レコードではプロビジョニング プロセスが既に行われたデバイスが示されています。

テナント管理者は、Azure portal から登録を削除することができ、それによって関連付けられている登録レコードも削除されます。

詳しくは、「[Azure Portal でデバイス登録を管理する方法](how-to-manage-enrollments.md)」をご覧ください。

REST API を使って、登録と登録レコードの削除操作を実行することもできます。

* 1 台のデバイスの登録情報を削除するには、「[Device Enrollment - Delete](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment)」(デバイス登録 - 削除) の API を使うことができます。
* デバイスのグループの登録情報を削除するには、「[Device Enrollment Group - Delete](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup)」(デバイス登録グループ - 削除) の API を使うことができます。
* プロビジョニングされたデバイスに関する情報を削除するには、「[Registration State - Delete Registration State](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate)」(登録状態 - 登録状態の削除) の API を使うことができます。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

Device Provisioning Service は、登録と登録レコードを保持しています。 登録にはプロビジョニングを許可されたデバイスについての情報が含まれ、登録レコードではプロビジョニング プロセスが既に行われたデバイスが示されています。

テナント管理者は、Azure portal で登録と登録レコードを表示し、コピーと貼り付けを使ってそれをエクスポートすることができます。

登録の管理方法について詳しくは、「[Azure Portal でデバイス登録を管理する方法](how-to-manage-enrollments.md)」をご覧ください。

REST API を使って、登録と登録レコードのエクスポート操作を実行することもできます。

* 1 台のデバイスの登録情報をエクスポートするには、「[Device Enrollment - Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment)」(デバイス登録 - 取得) の API を使うことができます。
* デバイスのグループの登録情報をエクスポートするには、「[Device Enrollment Group - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup)」(デバイス登録グループ - 取得) の API を使うことができます。
* プロビジョニングされたデバイスに関する情報をエクスポートするには、「[Registration State - Get Registration State](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate)」(登録状態 - 登録状態の取得) の API を使うことができます。

> [!NOTE]
> Microsoft のエンタープライズ サービスを使うと、システム生成ログと呼ばれる情報がいくつか生成されます。 Device Provisioning Service システムによって生成されたログの一部は、テナント管理者がアクセスしたりエクスポートしたりすることはできません。 これらのログは、個々のデバイスに関連するサービスと診断データ内で実行された実際のアクションで構成されています。

## <a name="links-to-additional-documentation"></a>その他のドキュメントへのリンク

Device Provisioning Service API の完全なドキュメントは、[https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) にあります。

Azure IoT Hub の[顧客データ要求機能](../iot-hub/iot-hub-customer-data-requests.md).