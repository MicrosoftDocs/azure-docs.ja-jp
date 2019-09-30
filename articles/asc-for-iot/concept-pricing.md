---
title: Azure Security Center for IoT のコストを理解する | Microsoft Docs
description: Azure Security Center for IoT に関連するコストとその管理方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: e742848df56e4e2be1b9edf42586e642f0c938cb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933799"
---
# <a name="pricing-and-associated-costs"></a>価格と関連コスト

この記事では、Azure Security Center for IoT の価格モデルについて説明し、すべての関連コストの概要を示して、それらを管理する方法を説明します。

## <a name="pricing"></a>価格

Azure Security Center for IoT の価格モデルは 2 つの部分で構成され、Azure Security Center for IoT で IoT Hub が[有効になる](quickstart-onboard-iot-hub.md)と請求されます。

- デバイスごとのコスト - IoT Hub ログの分析に基づく組み込みのセキュリティ機能。

- メッセージごとのコスト - IoT Edge またはリーフ デバイスからのセキュリティ メッセージに基づく拡張されたセキュリティ機能。

  >[!Note]
  > セキュリティ メッセージには、IoT Hub でのクォータ消費額もかかります。

詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください。

## <a name="associated-costs"></a>関連コスト

Azure Security Center for IoT には、直接的な価格には含まれない 2 種類の関連コストがあります。

- IoT Hub のクォータ消費額

- Log Analytics のストレージ コスト

設定を変更して特定の機能を無効にすることで、関連コストを削減できます。

設定を変更するには:

1. IoT Hub を開きます。

2. **[セキュリティ]** の **[概要]** をクリックします。

3. **[設定]** をクリックします。

次の表では、関連コストの概要と各オプションの影響を示します。

|     | 使用法 | Comment (コメント) |
| --- | --- | --- |
| **IoT Hub のクォータ消費額** |  |
| [デバイスのエクスポート](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) ジョブ (ツイン エクスポート) | 1 回 (1 日あたり) | "_ツイン メタデータ コレクション_" を無効にします |
| **Log Analytics ストレージ** |  |
| デバイスの推奨事項とアラート| サービスによって生成されるセキュリティ推奨事項とアラート | 非オプション |
| 生セキュリティ データ| セキュリティ エージェントによって収集される、IoT デバイスからの生セキュリティ データ | "_生デバイス セキュリティ イベントの保存_" を無効にします |
|

>[!Important]
> オプトアウトすると、Azure Security Center for IoT のセキュリティ機能の可用性に重大な影響があります。 
  
| オプトアウト | 影響 |
| --- | --- |
| "_ツイン メタデータ コレクション_" | [カスタム アラート](quickstart-create-custom-alerts.md)が無効になります |
| | IoT Edge マニフェストの推奨事項が無効になります |
| | デバイス ID に基づく推奨事項とアラートが無効になります |
| "_生デバイス セキュリティ イベントの保存_" | デバイス OS ベースラインの推奨事項の詳細を利用できなくなります |
| | [アラート](concept-security-alerts.md)と[推奨事項](concept-recommendations.md)の調査についての詳細を利用できなくなります |
|


## <a name="see-also"></a>関連項目

- [生セキュリティ データ](how-to-security-data-access.md)にアクセスする
- [デバイスを調査する](how-to-investigate-device.md)
- [セキュリティに関する推奨事項](concept-recommendations.md)を理解して調べる
- [セキュリティ アラート](concept-security-alerts.md)を理解して調べる
