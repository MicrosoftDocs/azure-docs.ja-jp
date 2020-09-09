---
title: 価格と関連コスト
description: Azure Security Center for IoT に関連付けられたコストとその管理方法について説明します。
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
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: 98afd1eb1f8a73c2d6dd3f293a07cdb70780d238
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004768"
---
# <a name="pricing-and-associated-costs"></a>価格と関連コスト

この記事では、Azure Security Center for IoT の価格モデルについて説明し、すべての関連コストの概要を示して、それらを管理する方法を説明します。

## <a name="pricing"></a>価格

Azure Security Center for IoT の価格モデルは 2 つの部分で構成され、Azure Security Center for IoT で IoT Hub が[有効になる](quickstart-onboard-iot-hub.md)と請求されます。

- デバイスごとのコスト - IoT Hub ログの分析に基づく組み込みのセキュリティ機能。

- メッセージごとのコスト - IoT Edge またはリーフ デバイスからのセキュリティ メッセージに基づく拡張されたセキュリティ機能。

詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください。

## <a name="associated-costs"></a>関連コスト

Azure Security Center for IoT には、直接の価格には含まれない関連コストがあります。

- Log Analytics のストレージ コスト

特定のソリューション機能を無効にすることで、関連コストを削減できます。 設定を変更することで、無効化します。

設定を変更するには:

1. IoT Hub を開きます。

1. **[セキュリティ]** の **[概要]** をクリックします。

1. **[設定]** をクリックします。

次の表では、関連コストの概要と各オプションの影響を示します。

| オプション | 使用法 | 解説 |
| --- | --- | --- |
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
