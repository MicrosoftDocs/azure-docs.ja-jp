---
title: Azure IoT Central アプリケーションで請求書を表示し、試用版を従量課金制に変換する | Microsoft Docs
description: 管理者として、Azure IoT Central アプリケーションで請求書を表示し、試用版から従量課金制に変換する方法を学習します
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 16a58bfc3fa245ed1ede19b0439419ab4590234e
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729205"
---
# <a name="view-your-bill-in-iot-central-application"></a>IoT Central アプリケーションでの請求書の表示

この記事では、管理者として Azure IoT Central アプリケーションの管理セクションで請求書を表示する方法について説明し、さらに、試用版を従量課金制に変換する方法についても説明します。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。

## <a name="view-your-bill"></a>課金状況の表示

請求書を表示するには、**課金**ページの**管理**セクションに移動します。 新しいタブに Azure 課金ページが開き、Azure IoT Central アプリケーションごとに請求書を確認することができます。

## <a name="convert-your-trial-to-pay-as-you-go"></a>試用版の従量課金制への変換

- **試用版**アプリケーションは、有効期限が切れるまでの 7 日間は無料となります。 有効期限内で、いつでも従量課金制に変更することができます。
- **従量課金制**アプリケーションは、デバイスごとに課金され、1 つのサブスクリプションにつき、最初の 5 個のデバイスは無料です。

価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

[課金] セクションで、試用版アプリケーションを従量課金制に変換できます。

このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. **[管理]** セクションの **[課金]** ページに移動します。

    ![試用版の状態](media/howto-administer/freetrialbilling.png)

1. **[Convert to Pay-As-You-Go]\(従量課金制に変換\)** を選択します。

    ![試用版を変換する](media/howto-administer/convert.png)

1. 適切な Azure Active Directory を選択してから、従量課金制アプリケーションで使用する Azure サブスクリプションを選びます。

1. **[変換]** を選択すると、ご利用のアプリケーションが従量課金制アプリケーションとなり、課金が開始されます。

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでの請求書の表示方法を学習したので、次の手順として、Azure IoT Central での[アプリケーション UI のカスタマイズ](howto-customize-ui.md)について学習することをお勧めします。