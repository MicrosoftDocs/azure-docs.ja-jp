---
title: Azure IoT Central の個人用ダッシュボードの作成 | Microsoft Docs
description: ユーザーが個人用ダッシュボードを作成して管理する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c048ae8c0daba0e467a9243f4dd83f8d95921e10
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502654"
---
# <a name="create-and-manage-personal-dashboards"></a>個人用ダッシュボードの作成と管理

**ダッシュボード**は、最初にアプリケーションに移動するときに読み込まれるページです。 アプリケーションの**ビルダー**では、すべてのユーザーの既定のアプリケーション ダッシュボードを定義します。 この既定のダッシュボードを、パーソナライズした自分のアプリケーション ダッシュボードに置き換えることができます。 別のデータを表示する複数のダッシュボードを持っておいて切り替えることができます。

## <a name="create-dashboard"></a>ダッシュボードの作成

次のスクリーンショットで、**Sample Contoso** テンプレートから作成されたアプリケーションのダッシュボードを示しています。 既定のアプリケーションのダッシュボードを個人用ダッシュボードに置き換えることができます。 これを行うには、ページの右上部にある **[+ 新規]** を選択します。

!["Sample Contoso" テンプレートに基づくアプリケーションのダッシュボード](media/howto-personalize-dashboard/defaultdashboard.png)

**[+ 新規]** を選択して、ダッシュボード エディターを開きます。 このエディターでは、ダッシュボードに名前を指定し、ライブラリから項目を選択することができます。 ライブラリには、ダッシュボードのカスタマイズに使用できるタイルとダッシュボードのプリミティブが含まれています。

![ダッシュボード ライブラリ](media/howto-personalize-dashboard/dashboardeditor.png)

たとえば、 **[デバイス設定とプロパティ]** タイルを追加して、自分が管理するデバイスの設定とプロパティの値を表示できます。 これを行うには、まず **[デバイス テンプレート]** を選択し、次に **[デバイス インスタンス]** を選択します。 次に、タイルのタイトルを指定し、表示する**設定**または**プロパティ**を選択します。 次のスクリーンショットでは、タイルに追加するために選択された**ファン速度**の設定を示しています。 **[完了]** を選択して、ダッシュボードへの変更を保存します。

![設定とプロパティの詳細が表示された [Configure Device Details]\(デバイスの詳細の構成\) フォーム](media/howto-personalize-dashboard/dashboardsetting.png)

個人用ダッシュボードを表示すると、デバイスの**ファン速度**設定用の新しいタイルが表示されるようになりました。

![タイルの設定とプロパティが表示された [Dashboard]\(ダッシュボード\) タブ](media/howto-personalize-dashboard/personaldashboard.png)

ライブラリ内の他のタイルの種類を調べて、個人用ダッシュボードをカスタマイズする方法を確認することができます。

Azure IoT Central でタイルを使用する方法の詳細については、[ダッシュボード タイルの使用](howto-use-tiles.md)に関する記事をご覧ください。

## <a name="manage-dashboards"></a>ダッシュボードの管理

複数の個人用ダッシュボードを持っておいて切り替えたり、既定のアプリケーションのダッシュボードを選択したりすることができます。

![ダッシュボードの切り替え](media/howto-personalize-dashboard/switchdashboards.png)

個人用ダッシュボードを編集し、不要になったものは削除できます。

![ダッシュボードの削除](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>次の手順

個人用ダッシュボードの作成と管理の方法を習得したので、次のようにすることができます。

> [!div class="nextstepaction"]
> [アプリケーション設定を管理する方法を学習する](howto-manage-preferences.md)
