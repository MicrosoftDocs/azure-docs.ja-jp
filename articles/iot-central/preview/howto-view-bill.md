---
title: Azure IoT Central アプリケーションで請求書を管理し、試用版を従量課金制に変換する | Microsoft Docs
description: 管理者として、Azure IoT Central アプリケーションで請求書を管理し、試用版から従量課金制に変換する方法を学習します
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1c1643fb8c8394ce3ce50e5926f05fd4d366f6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976419"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT Central アプリケーションでの請求書の管理

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

この記事では、管理者として Azure IoT Central アプリケーションの管理セクションで請求書を管理する方法について説明し、さらに、試用版を従量課金制に変換する方法についても説明します。

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

    ![試用版の状態](media/howto-view-bill/freetrialbilling.png)

1. **[Convert to Pay-As-You-Go]\(従量課金制に変換\)** を選択します。

    ![試用版を変換する](media/howto-view-bill/convert.png)

1. 適切な Azure Active Directory を選択してから、従量課金制アプリケーションで使用する Azure サブスクリプションを選びます。

1. **[変換]** を選択すると、ご利用のアプリケーションが従量課金制アプリケーションとなり、課金が開始されます。

## <a name="next-steps"></a>次の手順

Azure IoT Central アプリケーションでの請求書の管理方法を学習したので、次の手順として、Azure IoT Central での[アプリケーション UI のカスタマイズ](howto-customize-ui.md)について学習することをお勧めします。