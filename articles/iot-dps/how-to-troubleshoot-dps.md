---
title: Azure IoT Hub DPS との切断に関する診断とトラブルシューティング
description: Azure IoT Hub Device Provisioning Service (DPS) のデバイス接続に関する一般的なエラーを診断し、解決する方法について説明します
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: ba81be54592e591f734b05235bb3ce183a07a11c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709888"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service のトラブルシューティング

IoT デバイスの接続の問題は、構成証明のエラーや登録のエラーなど、考えられる障害点が多数あるため、トラブルシューティングが難しい場合があります。この記事では、[Azure Monitor](../azure-monitor/overview.md) でデバイスの接続の問題を検出してトラブルシューティングする方法についてのガイダンスを取り上げます。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure Monitor を使用してメトリックを確認したりアラートを設定したりする

次の手順では、IoT Hub Device Provisioning Service のメトリックを確認したり、メトリックに対してアラートを設定したりする方法について説明します。 

1. [Azure portal](https://portal.azure.com) にサインインします。

2. IoT Hub Device Provisioning Service に移動します。

3. **[メトリック]** を選びます。

4. 目的のメトリックを選択します。 
   <br />現在、DPS には、次の 3 つのメトリックがあります。

    | メトリックの名前 | 説明 |
    |-------|------------|
    | Attestation attempts (構成証明試行回数) | Device Provisioning Service に対して認証を試みたデバイスの数|
    | Registration attempts (登録試行回数) | 認証に成功した後、IoT Hub への登録を試みたデバイスの数|
    | Device assigned (割り当て済みデバイス) | IoT Hub に対して正常に割り当てられたデバイスの数|

5. 目的の集計方法を選択して、メトリックの視覚的な表示を作成します。 

6. メトリックのアラートを設定するには、メトリック ブレードの右上にある **[New alert rules]\(新しいアラート ルール\)** を選択します。同様に、**[アラート]** ブレードに移動して **[New alert rules]\(新しいアラート ルール\)** を選択できます。

7. **[条件の追加]** を選択し、プロンプトに従って目的のメトリックとしきい値を選択します。

詳細については、「[Azure Monitor のアラート](../azure-monitor/platform/alerts-overview.md)」を参照してください。

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Log Analytics を使用してエラーを確認、解決する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. IoT ハブに移動します。

3. **[診断設定]** を選択します。

4. **[診断を有効にする]** を選択します。

5. 収集したいログを有効にします。

    | ログの名前 | [説明] |
    |-------|------------|
    | DeviceOperations | デバイスの接続イベントに関連したログ |
    | ServiceOperations | サービス SDK に関連したイベント ログ (登録グループの作成や更新など)|

6. **[Log Analytics への送信]** を有効にします ([価格を参照](https://azure.microsoft.com/pricing/details/log-analytics/))。 

7. Azure portal で Device Provisioning Service リソースの **[ログ]** タブに移動します。

8. **[実行]** をクリックして最新のイベントを表示します。

9. 結果が存在する場合は、`OperationName`、`ResultType`、`ResultSignature`、`ResultDescription` (エラー メッセージ) を探して、エラーの詳細を確認します。


## <a name="common-error-codes"></a>一般的なエラー コード
この表を参照して、一般的なエラーを理解し、解決してください。

| エラー コード| 説明 | HTTP 状態コード |
|-------|------------|------------|
| 400 | 要求の本文が有効ではありません (解析できない、オブジェクトを検証できない、など)。| 400 形式が正しくありません |
| 401 | 承認トークンを検証できません (有効期限が切れている、要求の URI に該当しない、など)。 また、このエラー コードは、TPM 構成証明フローの過程でデバイスに返されます。 | 401 権限がありません|
| 404 | Device Provisioning Service インスタンスまたはリソース (登録など) が存在しません。 |404 見つかりません |
| 412 | 要求の ETag が、RFC7232 のとおりに、既存のリソースの ETag と一致しません。 | 412 前提条件が満たされていません |
| 429 | サービスによって操作がスロットルされています。 サービスの具体的な制限については、「[IoT Hub Device Provisioning Service の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)」を参照してください。 | 429 要求が多すぎます |
| 500 | 内部エラーが発生しました。 | 500 内部サーバー エラー|