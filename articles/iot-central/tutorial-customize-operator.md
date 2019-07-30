---
title: Azure IoT Central でオペレーター用ビューをカスタマイズする | Microsoft Docs
description: 作成者として、Azure IoT Central アプリケーションのオペレーター用ビューをカスタマイズします。
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ced771002ca9f542f89dbf74ba4a4745ad2a0339
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850177"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>チュートリアル:Azure IoT Central オペレーターのビューをカスタマイズする

このチュートリアルでは、アプリケーションのオペレーター用ビューをカスタマイズする方法を作成者向けに説明します。 作成者としてアプリケーションに変更を行う場合、Microsoft Azure IoT Central アプリケーションのオペレーター用ビューをプレビューできます。

このチュートリアルでは、コネクテッド空調デバイスの関連情報がオペレーターに表示されるよう、アプリケーションをカスタマイズします。 カスタマイズを行うことで、オペレーターがアプリケーションに接続された空調デバイスを管理できるようになります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス ダッシュボードを構成する
> * デバイス設定のレイアウトを構成する
> * デバイス プロパティのレイアウトを構成する
> * オペレーターとしてデバイスをプレビューする
> * 既定のアプリケーション ダッシュボードを構成する
> * 既定のアプリケーション ダッシュボードをオペレーターとしてプレビューする

## <a name="prerequisites"></a>前提条件

開始する前に、前の 2 つのチュートリアルを完了しておく必要があります。

* [Azure IoT Central アプリケーションで新しいデバイスの種類を定義する](tutorial-define-device-type.md)。
* [デバイスのルールとアクションを構成する](tutorial-configure-rules.md)。

## <a name="configure-your-device-dashboard"></a>デバイス ダッシュボードを構成する

作成者はデバイス ダッシュボードに表示される情報を定義できます。 [アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルでは、折れ線グラフとその他の情報を **Connected Air Conditioner** ダッシュボードに追加しました。

1. **Connected Air Conditioner** デバイス テンプレートを編集するには、左側のナビゲーション メニューにある **[デバイス テンプレート]** を選択します。

    ![[デバイス テンプレート] ページ](media/tutorial-customize-operator/devicetemplates.png)

2. デバイス ダッシュボードをカスタマイズするには、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルで作成した **Connected Air Conditioner (1.0.0)** デバイス テンプレートを選択します。

3. ダッシュボードを編集するには、 **[ダッシュボード]** タブを選択します。

4. ダッシュボードに主要業績評価指標 (KPI) タイルを追加するには、 **[KPI]** を選択します。

    KPI を定義するには、次の表の情報を使用します。

    | Setting     | 値 |
    | ----------- | ----- |
    | EnableAdfsAuthentication        | Maximum temperature |
    | 時間範囲  | Past 1 week |
    | 測定タイプ | テレメトリ |
    | Measurement | 温度 |
    | 集計 | 最大値 |
    | 表示  | Enabled |

    ![KPI を追加する](media/tutorial-customize-operator/addkpi.png)

5. **[保存]** を選択します。 ダッシュボードに KPI タイルが表示されます。

    ![KPI タイル](media/tutorial-customize-operator/temperaturekpi.png)

6. ダッシュボード上のタイルを移動させたり、サイズ変更したりするには、そのタイルの上にマウス ポインターを移動させます。 タイルを新しい場所にドラッグしたり、サイズ変更したりできます。

## <a name="configure-your-settings-layout"></a>設定のレイアウトを構成する

作成者は、オペレーター用のデバイス設定ビューを構成することもできます。 オペレーターは、デバイス設定タブを使用してデバイスを構成します。 たとえば、オペレーターはコネクテッド空調機の目標温度を設定するために設定タブを使用します。

1. コネクテッド空調機の設定のレイアウトを編集するには、 **[設定]** タブを選択します。

2. 設定タイルの移動とサイズ変更を行えます。

    ![設定のレイアウトを編集する](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>プロパティのレイアウトを構成する

ダッシュボードと設定に加えて、オペレーター用のデバイス プロパティ ビューを構成することもできます。 オペレーターはデバイス プロパティ タブを使用してデバイス メタデータを管理します。 たとえばオペレーターは、デバイスのシリアル番号を表示したり、製造元の連絡先の詳細を更新したりするためにプロパティ タブを使用します。

1. コネクテッド空調機のプロパティのレイアウトを編集するには、 **[プロパティ]** タブを選択します。

2. プロパティ フィールドの移動とサイズ変更を行えます。

    ![プロパティのレイアウトを編集する](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>デバイスをプレビューする

オペレーター用のダッシュボード タブ、設定タブ、プロパティ タブをカスタマイズするには、 **[デバイス テンプレート]** ページを使用します。 デバイス テンプレートを表示および使用するには、**Device Explorer** ページを使用します。

1. オペレーターとして Connected Air Conditioner テンプレートを表示および使用するには、**Device Explorer** ページに移動し、IoT Central でテンプレートから生成された、シミュレートされたデバイスを選択します。

    ![デバイス テンプレートを表示および使用する](media/tutorial-customize-operator/usetemplate.png)

2. このデバイスの場所を更新するには、 **[プロパティ]** を選択して、[場所] タイルの値を編集します。 次に、 **[保存]** を選択します。

    ![プロパティ値を編集する](media/tutorial-customize-operator/editproperty.png)

3. 設定をコネクテッド空調機に送信するには、 **[設定]** を選択してタイルの設定値を変更し、 **[更新]** を選択します。

    ![設定をデバイスに送信する](media/tutorial-customize-operator/sendsetting.png)

    デバイスが新しい設定値を認識すると、タイルに表示される設定が**同期済み**になります。

4. オペレーターは、作成者が構成したデバイス ダッシュボードを表示できます。

    ![オペレーター用のデバイス ダッシュボード ビュー](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>既定のダッシュボードを構成する

作成者またはオペレーターが Azure IoT Central アプリケーションにサインインすると、アプリケーション ダッシュボードが表示されます。 作成者は、オペレーターにとって最も有益で関連性の高い内容が含まれるよう、既定のダッシュボードの内容を構成できます。

> [!NOTE]
> ユーザーは、個人用ダッシュボードを独自に作成し、その 1 つを既定のダッシュボードとして選択することもできます。

1. 既定のアプリケーション ダッシュボードをカスタマイズするには、 **[ダッシュボード]** ページに移動して、ページの右上にある **[編集]** を選択します。 パネルが表示され、ダッシュボードに追加できるオブジェクトのライブラリが示されます。

    ![[ダッシュボード] ページ](media/tutorial-customize-operator/builderhome.png)

2. ダッシュボードをカスタマイズするには、 **[ライブラリ]** からタイルを追加します。 **[リンク]** を選択し、組織の Web サイトの詳細を追加します。 次に、 **[保存]** を選択します。

    ![ダッシュボードにリンクを追加する](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > リンクは Azure IoT Central アプリケーション内のページに追加することもできます。 たとえば、デバイス ダッシュボードまたは設定のページにリンクを追加することができます。

3. 必要に応じて **[イメージ]** を選択し、ダッシュボードに表示するイメージをアップロードします。 イメージには、選択したときの移動先となる URL を指定できます。

    ![ダッシュボードにイメージを追加する](media/tutorial-customize-operator/addimage.png)

    詳細については、[イメージを準備して Azure IoT Central アプリケーションにアップロードする方法](howto-prepare-images.md)に関するページを参照してください。

## <a name="preview-the-dashboard"></a>ダッシュボードをプレビューする

アプリケーション ダッシュボードをオペレーターとしてプレビューするには、ページの右上にある **[完了]** を選択します。

![デザイン モードを切り替える](media/tutorial-customize-operator/operatorviewhome.png)

リンク タイルとイメージ タイルを選択すると、作成者として設定した URL に移動できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションのオペレーター用ビューをカスタマイズする方法について説明しました。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * デバイス ダッシュボードを構成する
> * デバイス設定のレイアウトを構成する
> * デバイス プロパティのレイアウトを構成する
> * オペレーターとしてデバイスをプレビューする
> * 既定のホーム ページを構成する
> * オペレーターとして既定のホーム ページをプレビューする

これで、アプリケーションのオペレーター用ビューをカスタマイズする方法についての説明は終了です。次の手順として、以下が推奨されます。

* [(オペレーターとして) デバイスを監視する](tutorial-monitor-devices.md)
* [(オペレーターおよびデバイス開発者として) 新しいデバイスをアプリケーションに追加する](tutorial-add-device.md)