---
title: Azure IoT Central アプリケーションで請求書を管理し、無料の料金プランから切り替える | Microsoft Docs
description: 管理者として Azure IoT Central アプリケーションで請求書を管理し、無料の料金プランから標準の料金プランに切り替える方法を学習します。
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96549023"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT Central アプリケーションでの請求書の管理

この記事では、管理者が Azure IoT Central の課金を管理する方法について説明します。 無料の料金プランから標準の料金プランにアプリケーションを切り替えることや、料金プランをアップグレードまたはダウングレードすることができます。

**[管理]** セクションにアクセスするには、"*管理者*" ロールであるか、課金を表示できる "*カスタム ユーザー ロール*" を持っている必要があります。 Azure IoT Central アプリケーションを作成したユーザーには、自動的に **管理者** ロールが割り当てられます。

## <a name="move-from-free-to-standard-pricing-plan"></a>無料から標準に料金プランを切り替える

- 無料の料金プランが使用されているアプリケーションは 7 日間の無料期間後に失効します。 データを失わないために、失効前の任意の時点で標準の料金プランに切り替えることができます。
- 標準の料金プランが使用されているアプリケーションはデバイスごとに課金され、アプリケーションごとに最初の 2 台のデバイスが無料になります。

価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

価格セクションで、無料から標準の料金プランにアプリケーションを切り替えることができます。

このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. **[管理]** セクションの **[価格]** ページに移動します。

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="試用版の状態":::

1. **[Convert to a paid plan]\(有料プランに切り替える\)** を選択します。

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="試用版を変換する":::

1. 適切な Azure Active Directory を選択し、有料プランが使用されるアプリケーションに使用する Azure サブスクリプションを選択します。

1. **[変換]** を選択すると、アプリケーションで有料プランが使用され、課金が開始されます。

> [!Note]
> 既定では、 *[Standard 2]\(標準 2\)* 料金プランに切り替えられます。

## <a name="how-to-change-your-application-pricing-plan"></a>アプリケーションの料金プランを変更する方法

標準の料金プランが使用されているアプリケーションはデバイスごとに課金され、アプリケーションごとに最初の 2 台のデバイスが無料になります。

料金セクションで、Azure IoT の料金プランをいつでもアップグレードまたはダウングレードできます。

1. **[管理]** セクションの **[価格]** ページに移動します。

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="料金プランのアップグレード":::

1. **[プラン]** を選択し、 **[保存]** を選択してアップグレードまたはダウングレードします。

## <a name="view-your-bill"></a>課金状況の表示

1. 適切な Azure Active Directory を選択し、有料プランが使用されるアプリケーションに使用する Azure サブスクリプションを選択します。

1. **[変換]** を選択すると、アプリケーションで有料プランが使用され、課金が開始されます。

> [!Note]
> 既定では、 *[Standard 2]\(標準 2\)* 料金プランに切り替えられます。

## <a name="next-steps"></a>次のステップ

Azure IoT Central アプリケーションでの請求書の管理方法を学習したので、次の手順として、Azure IoT Central での[アプリケーション UI のカスタマイズ](howto-customize-ui.md)について学習することをお勧めします。