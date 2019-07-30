---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e427a7b80b28f1cc70a02890152f9f2247a8bcd2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360295"
---
次の表は、Azure IoT Hub Device Provisioning Service のリソースに適用される制限の一覧です。

| Resource | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのデバイス プロビジョニング サービスの最大数 | 10 |
| 加入の最大数 | 1,000,000 |
| 登録の最大数 | 1,000,000 |
| 加入グループの最大数 | 100 |
| CA の最大数 | 25 |
| リンクされた IoT Hub の最大数 | 10 |
| メッセージの最大サイズ | 96 KB|


> [!NOTE]
> お使いのサブスクリプションのインスタンス数を増やすには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

> [!NOTE]
> お使いのプロビジョニング サービスの加入数と登録数を増やすには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

Device Provisioning Service は、次のクォータを超えた場合に、要求を調整します。

| スロットル | ユニットあたりの値 |
| --- | --- |
| Operations | 200/分/サービス |
| デバイス登録 | 200/分/サービス |
| デバイスのポーリング操作 | 5/10 秒/デバイス |
