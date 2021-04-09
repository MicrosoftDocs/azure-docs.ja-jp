---
title: チュートリアル:Azure IoT Central で水質のモニタリング アプリを作成する
description: チュートリアル:Azure IoT Central アプリケーション テンプレートを使用して水質のモニタリング アプリケーションを作成する方法について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017241"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>チュートリアル:Azure IoT Central で水質のモニタリング アプリケーションを作成する

このチュートリアルでは、Azure IoT Central で水質のモニタリング アプリケーションを作成する手順を説明します。 Azure IoT Central の **水質のモニタリング** アプリケーション テンプレートからアプリケーションを作成します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * **水質のモニタリング** テンプレートを使用して、水質のモニタリング アプリケーションを作成します。
> * オペレーター ダッシュボードを探索およびカスタマイズします。
> * 水質のモニタリング デバイス テンプレートを探索します。
> * シミュレートされたデバイスを探索します。
> * ルールを探索して構成します。
> * ジョブを構成します。
> * ホワイト ラベルを使用してアプリケーション ブランドをカスタマイズします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションを用意しておくことをお勧めします。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Azure IoT Central で水質のモニタリング アプリケーションを作成する

このセクションでは、Azure IoT Central の **水質のモニタリング** テンプレートを使用して、水質のモニタリング アプリケーションを作成します。

1. [Azure IoT Central ホームページ](https://aka.ms/iotcentral)に移動します。

    Azure サブスクリプションを持っている場合は、そのサブスクリプションへのアクセス用の資格情報を使用してサインインします。 持っていない場合は、Microsoft アカウントを使用してサインインします。

    ![組織のアカウントにサインインする](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Azure IoT Central の一番左のペインで **[Build]\(構築\)** を選択し、 **[Government]\(政府\)** タブを選択します。政府機関ペインに、いくつかの政府機関のアプリケーション テンプレートが表示されます。

    ![政府機関のアプリケーション テンプレート](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. **[Water quality monitoring]\(水質のモニタリング\)** アプリケーション テンプレートを選択します。 このアプリケーション テンプレートには、水質モニタリング デバイス テンプレート、シミュレートされたデバイス、オペレーター ダッシュボード、事前構成済みのモニタリング ルールが含まれています。

1. **[Create app]\(アプリの作成\)** を選択します。 **[新規アプリケーション]** ペインが開き、次の要素が表示されます。

    * **アプリケーション名**:既定では、アプリケーション名は、**Water quality monitoring** の後に IoT Central が生成する一意の ID 文字列が続く形式です。 必要に応じて、表示名を入力したり、後でアプリケーション名を変更したりできます。
    * **URL**: 必要な URL を入力したり、後で URL 値を変更したりできます。
    * Azure サブスクリプションをお持ちの場合は、 **[ディレクトリ]** 、 **[Azure サブスクリプション]** 、 **[リージョン]** の値を入力します。 サブスクリプションをお持ちでない場合は、 **[7-day free trial]\(7 日間の無料試用版\)** を有効にし、必須の連絡先情報を入力できます。

    ディレクトリとサブスクリプションの詳細については、[アプリケーションの作成のクイック スタート](../core/quick-deploy-iot-central.md)に関するページを参照してください。

1. ページの左下にある **[作成]** ボタンを選択します。

    ![Azure IoT Central の [新規アプリケーション] ページ](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Azure IoT Central の [新規アプリケーション] の課金情報](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

これで、Azure IoT Central の **水質のモニタリング** テンプレートを使用して、水質のモニタリング アプリケーションが作成されました。

新しいアプリケーションには、次の構成済みコンポーネントが用意されています。

* オペレーター ダッシュボード
* 水質のモニタリング デバイス テンプレート
* シミュレートされた水質のモニタリング デバイス
* ルールおよびジョブ
* ホワイト ラベルを使用したブランド化

アプリケーションは、いつでも変更できます。

次に、アプリケーションを探索して、いくつかのカスタマイズを行います。

## <a name="explore-and-customize-the-operator-dashboard"></a>オペレーター ダッシュボードの探索とカスタマイズ

アプリケーションを作成した後、 **[Wide World water quality dashboard]\(Wide World の水質ダッシュボード\)** ペインが開きます。

   ![水質のモニタリング ダッシュボード](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

ビルダーは、オペレーターが使用するダッシュボードのビューを作成したりカスタマイズしたりすることができます。 カスタマイズしてみる前に、ダッシュボードを探索します。

ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。

ダッシュボードには、次の種類のタイルが含まれています。

* **[Wide World Water Utility] 画像タイル**:ダッシュボードの左上隅にある最初のタイルは、Wide World という名前の架空の公益事業を示す画像です。 独自の画像を使用するようにタイルをカスタマイズすることも、タイルを削除することもできます。

* **平均 pH KPI タイル**:**過去 30 分間の平均 pH** などの KPI タイルがダッシュボード ペインの上部に表示されます。 KPI タイルをカスタマイズし、別の種類や時間の範囲に設定できます。

* **[Water monitoring area map]\(水質モニタリング エリア マップ\)** :Azure IoT Central では Azure Maps が使用されています。これをアプリケーション内で直接設定して、デバイスの場所を表示できます。 また、アプリケーションからの位置情報をデバイスにマップし、Azure Maps を使用して、その情報をマップに表示することもできます。 マップの上にカーソルを移動し、コントロールを試してみてください。

* **平均 pH 分布のヒートマップ グラフ**:さまざまな視覚化グラフを選択して、アプリケーションに最適な方法でデバイス テレメトリを表示できます。

* **重要な品質指標の折れ線グラフ**:デバイス テレメトリを、ある時間範囲にわたる折れ線グラフとしてプロットして視覚化することができます。  

* **化学薬品濃度の棒グラフ**:デバイス テレメトリを棒グラフで視覚化できます。

* **アクション ボタン**:ダッシュボードには、オペレーターが監視ダッシュボードから直接開始できるアクションのタイルが含まれています。 このようなアクションの例として、デバイスのプロパティのリセットが挙げられます。

* **プロパティ一覧タイル**:ダッシュボードには、しきい値情報、デバイスの正常性情報、メンテナンス情報を表す複数のプロパティ タイルがあります。

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ

ビルダーは、オペレーターが使用するダッシュボードのビューをカスタマイズすることができます。

1. **[編集]** を選択して、 **[Wide World water quality dashboard]\(Wide World の水質ダッシュボード\)** ペインをカスタマイズします。 ダッシュボードは、 **[編集]** メニューでコマンドを選択することによってカスタマイズできます。 ダッシュボードが編集モードになったら、新しいタイルを追加したり既存のファイルを構成したりできます。

    ![ダッシュボードを編集する](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. **[+ 新規]** を選択して、構成可能な新しいダッシュボードを作成します。 複数のダッシュボードを作成し、ダッシュボード メニューからダッシュボード間を移動できます。

## <a name="explore-a-water-quality-monitoring-device-template"></a>水質のモニタリング デバイス テンプレートを探索します

Azure IoT Central のデバイス テンプレートには、デバイスの機能が定義されています。 指定可能な機能は、テレメトリ、プロパティ、およびコマンドです。 ビルダーは、接続されたデバイスの機能を表す Azure IoT Central のデバイス テンプレートを定義できます。 また、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。

作成した水質のモニタリング アプリケーションには、水質のモニタリング デバイス テンプレートが付属しています。

デバイス テンプレートを表示するには:

1. Azure IoT Central でアプリケーションの一番左のペインにある **[Device templates]\(デバイス テンプレート\)** を選択します。
1. デバイス テンプレートの一覧から、 **[Water Quality Monitor]\(水質のモニタリング\)** を選択します。 そのデバイス テンプレートが開きます。

    ![デバイス テンプレート](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

次のデバイス テンプレート設定のカスタマイズを練習します。

1. デバイス テンプレート メニューから **[カスタマイズ]** を選択します。
1. テレメトリの種類 **[Temperature]\(温度\)** に移動します。
1. **[Display name]\(表示名\)** の値を **[Reported temperature]\(報告された温度\)** に変更します。
1. 測定単位を変更するか、**最小値** と **最大値** を設定します
1. **[保存]** を選択します。

#### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する

1. デバイス テンプレート メニューから **[クラウド プロパティ]** を選択します。
1. 新しいクラウド プロパティを追加するには、 **[+ クラウド プロパティの追加]** を選択します。 Azure IoT Central では、デバイスに関連するが、デバイスから送信されることを想定しないプロパティを追加できます。 そのようなプロパティの 1 つの例として、設置区域、アセット情報、またはメンテナンス情報に固有のアラートのしきい値があります。
1. **[保存]** を選択します。

### <a name="explore-views"></a>ビューを探索する

水質モニタリング デバイス テンプレートには、定義済みのビューが用意されています。 このビューには、オペレーターにデバイス データがどのように表示されるか、およびオペレーターによってクラウドのプロパティがどのように設定されるかが定義されています。 ビューを探索し、変更を行ってみます。

  ![デバイス テンプレートのビュー](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>デバイス テンプレートを公開する

何らかの変更を行った場合は、必ず **[Publish]\(公開\)** を選択してデバイス テンプレートを公開してください。

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成

1. 新しいデバイス テンプレートを作成するには、 **[+ 新規]** を選択し、作成プロセスに従います。
1. カスタムのデバイス テンプレートを作成するか、Azure IoT デバイス カタログからデバイス テンプレートを選択します。

## <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する

アプリケーション テンプレートから作成した水質のモニタリング アプリケーションには、2 つのシミュレートされたデバイスがあります。 これらのデバイスは、水質のモニタリング デバイス テンプレートにマップされます。

### <a name="view-the-devices"></a>デバイスを表示する

1. アプリケーションの一番左のペインで **[Devices]\(デバイス\)** を選択します。

   ![デバイス](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. シミュレートされたデバイスを 1 つ選択します。

    ![デバイス 1 を選択する](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. **[クラウドのプロパティ]** タブで、 **[Acidity (pH) threshold]\(酸性度 (pH) のしきい値\)** の値を **8** から **9** に変更します。
1. **[デバイスのプロパティ]** タブと **[Device Dashboard]\(デバイス ダッシュボード\)** タブを調べます。

> [!NOTE]
> すべてのタブが **デバイス テンプレート ビュー** から構成されています。

### <a name="add-new-devices"></a>新しいデバイスを追加する

**[デバイス]** タブで、 **[+ 新規]** を選択して新しいデバイスを追加します。

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、デバイス テレメトリを自動的に監視するルールを作成できます。 これらのルールは、いずれかの条件が満たされたときにアクションをトリガーします。 実行できる 1 つのアクションとして、電子メール通知の送信があります。 それ以外には、Power Automate アクションや、他のサービスにデータを送信する Webhook アクションなどが可能です。

作成した水質のモニタリング アプリケーションには、2 つの事前構成済みのルールがあります。

### <a name="view-rules"></a>ルールを表示する

1. アプリケーションの一番左のペインで **[ルール]** を選択します。

   ![ルール](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. **[High pH alert]\(高 pH アラート\)** を選択します。これは、アプリケーションに事前構成されたルールの 1 つです。

   ![高 pH アラート ルール](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **[High pH alert]\(高 pH アラート\)** ルールは、酸性度 (pH) の条件が 8 を超えていることを確認するために構成されています。

次に、ルールにメール アクションを追加します。

1. **[+ Email]\(+ メール\)** を選択します。
1. **[表示名]** ボックスに、「**High pH alert**」と入力します。
1. **[宛先]** ボックスに、Azure IoT Central アカウントに関連付けられているメール アドレスを入力します。
1. 必要に応じて、メールのテキストに含めるメモを入力します。
1. **[完了]** を選択して、アクションを完了します。
1. **[保存]** を選択して、新しいルールを保存してアクティブにします。

構成された条件が満たされると、数分以内にメールを受信するはずです。

> [!NOTE]
> アプリケーションから、条件が満たされるたびにメールが送信されます。 ルールからの自動電子メールの受信を停止する場合は、そのルールの **[Disable]\(無効にする\)** を選択します。
  
新しいルールを作成するには、アプリケーションの一番左のペインで **[ルール]** を選択し、 **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

Azure IoT Central のジョブを使用すると、デバイスに対する、または複数のデバイスのクラウド プロパティに対する更新をトリガーできます。 また、ジョブを使用して、複数のデバイス上でデバイス コマンドをトリガーすることもできます。 Azure IoT Central によって、ワークフローが自動化されます。

1. アプリケーションの一番左のペインで **[ジョブ]** を選択します。
1. **[+ 新規]** を選択し、1 つまたは複数のジョブを構成します。

## <a name="customize-your-application"></a>アプリケーションのカスタマイズ

ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

1. **[管理]**  >  **[アプリケーションのカスタマイズ]** を選択します。
1. **[アプリケーション ロゴ]** で **[変更]** を選択し、ロゴとしてアップロードする画像を選択します。
1. **[Browser icon]\(ブラウザー アイコン\)** で **[変更]** を選択し、ブラウザーのタブに表示される画像を選択します。
1. **[ブラウザーの色]** で、既定値を HTML 16 進数のカラー コードで置き換えることができます。
1. **[設定]** を選択して、 **[テーマ]** の値を変更します。

   ![アプリケーションのカスタマイズ](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>アプリケーション イメージを更新する

1. **[Administration]\(管理\)**  >  **[アプリケーションの設定]** を選択します。

1. **[画像の選択]** ボタンを使用して、アプリケーション イメージとしてアップロードするイメージを選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. アプリケーションの一番左のペインで **[Administration]\(管理\)** タブを開きます。
1. **[アプリケーション設定]** を選択し、 **[削除]** ボタンを選択します。

    ![アプリケーションを削除する](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>次のステップ

* 項目ごとに詳しい情報を確認できます。 

> [!div class="nextstepaction"]
> [水質のモニタリングの概念](./concepts-waterqualitymonitoring-architecture.md)
