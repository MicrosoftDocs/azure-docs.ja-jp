---
title: Azure IoT Central アプリケーション ダッシュボードを構成する | Microsoft Docs
description: 開発者として、既定の Azure IoT Central アプリケーション ダッシュボードを構成する方法を学習します。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850211"
---
# <a name="configure-the-application-dashboard"></a>アプリケーション ダッシュボードの構成

**ダッシュボード**は、アプリケーションにアクセスできるユーザーがアプリケーションの URL に移動したときに読み込まれるページです。 **Sample Contoso** または **Sample Devkits** アプリケーション テンプレートを選択してアプリケーションを作成した場合、アプリケーションには定義済みのダッシュボードが含まれます。 **Custom Application** アプリケーション テンプレートを選択した場合、ダッシュボードは空になります。

> [!NOTE]
> 既定のアプリケーション ダッシュボードの代わりに、ユーザーが[独自の個人用ダッシュボードを作成](howto-personalize-dashboard.md)し、それを使用することもできます。

## <a name="add-tiles"></a>タイルを追加する

次のスクリーンショットで、**Sample Contoso** テンプレートから作成されたアプリケーションのダッシュボードを示しています。 アプリケーションの既定のダッシュボードをカスタマイズするには、ページの右上にある **[編集]** を選択します。

!["Sample Contoso" テンプレートに基づくアプリケーションのダッシュボード](media/howto-configure-homepage/image1a.png)

**[編集]** を選択すると、ダッシュボード ライブラリ パネルが開きます。 ライブラリには、ダッシュボードのカスタマイズに使用できるタイルとダッシュボードのプリミティブが含まれています。

![ダッシュボード ライブラリ](media/howto-configure-homepage/image2a.png)

たとえば、 **[デバイス設定とプロパティ]** タイルを追加して、デバイスの一連の設定とプロパティ値を表示することもできます。 これを行うには、まず **[デバイス テンプレート]** を選択し、次に **[デバイス インスタンス]** を選択します。 その後、タイルのタイトルを指定し、表示する**設定**または**プロパティ**を選択します。 次のスクリーンショットでは、タイルに追加するために選択された設定とプロパティを示しています。 **[完了]** を選択して、ダッシュボードへの変更を保存します。

![設定とプロパティの詳細が表示された [Configure Device Details]\(デバイスの詳細の構成\) フォーム](media/howto-configure-homepage/image3a.png)

オペレーターが既定のアプリケーション ダッシュボードを表示すると、デバイスの**温度設定**を含んだ新しいタイルが表示されます。

![タイルの設定とプロパティが表示された [Dashboard]\(ダッシュボード\) タブ](media/howto-configure-homepage/image4a.png)

ライブラリ内の他のタイルの種類を調べて、既定のアプリケーション ダッシュボードをカスタマイズする方法を確認することができます。

Azure IoT Central でタイルを使用する方法の詳細については、「[ダッシュボード タイルの使用](howto-use-tiles.md)」を参照してください。

## <a name="next-steps"></a>次の手順

これで、Azure IoT Central の既定のアプリケーション ダッシュボードを構成する方法を習得したので、次を実行できます。

> [!div class="nextstepaction"]
> [イメージを準備してアップロードする](howto-prepare-images.md)
