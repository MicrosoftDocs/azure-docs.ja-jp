---
title: チュートリアル:Azure IoT Central を使用して接続された廃棄物管理アプリを作成する
description: チュートリアル:Azure IoT Central アプリケーション テンプレートを使用して、コネクテッド廃棄物管理アプリケーションを作成する方法について学習する
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727568"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>チュートリアル:接続された廃棄物管理アプリを作成する

このチュートリアルでは、Azure IoT Central を使用して、コネクテッド廃棄物管理アプリケーションを作成する方法について説明します。 

具体的には、次の方法を学習します。 

> [!div class="checklist"]
> * Azure IoT Central の "*コネクテッド廃棄物管理*" テンプレートを使用してアプリを作成します。
> * オペレーター ダッシュボードを探索してカスタマイズします。 
> * コネクテッド廃棄物処理デバイス テンプレートを探索します。
> * シミュレートされたデバイスを探索します。
> * ルールを探索して構成します。
> * ジョブを構成します。
> * アプリケーションのブランドをカスタマイズします。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお勧めします。 また、7 日間無料の試用版を使用することもできます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="create-your-app-in-azure-iot-central"></a>Azure IoT Central でのアプリの作成

このセクションでは、Azure IoT Central でコネクテッド廃棄物管理テンプレートを使用してアプリを作成します。 その方法は次のとおりです。

1. [Azure IoT Central](https://aka.ms/iotcentral) に移動します。

    Azure サブスクリプションを持っている場合は、そのサブスクリプションへのアクセス用の資格情報を使用してサインインします。 持っていない場合は、Microsoft アカウントを使用してサインインします。

    ![Microsoft サインインのスクリーンショット](./media/tutorial-connectedwastemanagement/sign-in.png)

1. 左側のペインから **[ビルド]** を選択します。 次に、 **[Government]\(政府機関\)** タブを選択します。政府機関ページに、いくつかの政府機関アプリケーション テンプレートが表示されます。

    ![Azure IoT Central の [ビルド] ページのスクリーンショット。](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. **コネクテッド廃棄物管理** アプリケーション テンプレートを選択します。 このテンプレートには、コネクテッド廃棄物処理デバイス テンプレートのサンプル、シミュレートされたデバイス、オペレーター ダッシュボード、事前構成済みの監視ルールが含まれています。    

1. **[アプリの作成]** を選択して、 **[新しいアプリケーション]** ダイアログ ボックスを開きます。 次のフィールドに情報を入力します。
    * **アプリケーション名**: 既定では、**Connected waste management** に続けて、Azure IoT Central によって生成される一意の ID 文字列が付加されます。 必要に応じて、わかりやすいアプリケーション名を選んでください。 後でアプリケーション名を変更することもできます。
    * **URL**。 必要に応じて、目的の URL を選択できます。 URL は後から変更できます。 
    * **価格プラン**。 Azure サブスクリプションをお持ちの場合は、 **[課金情報]** ダイアログ ボックスの該当するフィールドに、ご自分のディレクトリ、Azure サブスクリプション、リージョンを入力してください。 サブスクリプションをお持ちでない場合は、 **[Free]\(無料\)** を選択して、7 日間の試用版サブスクリプションを有効にし、必須の連絡先情報を入力してください。  

    ディレクトリとサブスクリプションの詳細については、「[クイックスタート - Azure IoT Central アプリケーションを作成する](../core/quick-deploy-iot-central.md)」を参照してください。

1. ページの下部にある **[作成]** を選択します。 

    ![Azure IoT Central の [新しいアプリケーションの作成] ダイアログ ボックスのスクリーンショット。](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central の [課金情報] ダイアログ ボックスのスクリーンショット。](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
新しく作成されたアプリケーションには、事前構成された次のものが付属しています。
* オペレーター ダッシュボードのサンプル。
* 事前定義済みのコネクテッド廃棄物処理デバイス テンプレートのサンプル。
* シミュレートされたコネクテッド廃棄物処理デバイス。
* ルールおよびジョブ。
* ブランドのサンプル。 

これは自分のアプリケーションであるため、いつでも変更できます。 ここで、アプリケーションを探索して、いくつかのカスタマイズを行いましょう。  

## <a name="explore-and-customize-the-operator-dashboard"></a>オペレーター ダッシュボードの探索とカスタマイズ 

**[Wide World waste management dashboard]\(Wide World 廃棄物管理ダッシュボード\)** をご覧ください。これはアプリの作成後に表示されます。

   ![Wide World 廃棄物管理ダッシュボードのスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

ビルダーは、オペレーターのダッシュボードのビューを作成したりカスタマイズしたりすることができます。 まず、ダッシュボードを詳しく見ていきましょう。 

>[!NOTE]
>ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。 

ダッシュボードは、各種のタイルで構成されています。

* **Wide World Water Utility 画像タイル**: ダッシュボードの最初のタイルは、架空の公益水道事業 "Wide World Waste" の画像タイルです。 タイルをカスタマイズしたり、独自の画像を追加したり、削除したりできます。 

* **Waste bin (ごみ箱) 画像タイル**: 画像およびコンテンツ タイルを使用して、説明と共に、監視対象のデバイスの視覚的表現を作成できます。 

* **Fill level (充填レベル) KPI タイル**: このタイルには、ごみ箱の "*充填レベル*" センサーによってレポートされた値が表示されます。 ごみ箱の充填レベルとその他のセンサー ("*臭気メーター*"、"*重量*" など) は、リモートで監視できます。 オペレーターは、ごみ収集トラックの派遣などのアクションを実行できます。 

* **Waste monitoring area map (廃棄物監視エリア マップ)** : このタイルでは Azure Maps が使用されており、Azure IoT Central で直接構成できます。 マップ タイルにデバイスの場所が表示されます。 マップの上にポインターを置いて、マップ上でズームイン、ズームアウト、展開などのコントロールを試してみてください。

     ![コネクテッド廃棄物管理ダッシュボードのマップのスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Fill, odor, weight level (充填、臭気、重量レベル) 棒グラフ**: 1 つまたは複数の種類のデバイス テレメトリ データを棒グラフで視覚化できます。 棒グラフを展開することもできます。  

  ![コネクテッド廃棄物管理ダッシュボードの棒グラフのスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services (フィールド サービス)** : このダッシュボードには、Azure IoT Central アプリケーションから Dynamics 365 フィールド サービスと統合する方法へのリンクが含まれています。 たとえば、フィールド サービスを使用して、ごみ収集サービスを派遣するためのチケットを作成できます。 


### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ 

ダッシュボードは、 **[編集]** メニューを選択することによってカスタマイズできます。 新しいタイルを追加したり、既存のものを構成したりできます。 編集モードのダッシュボードは次のように表示されます。 

![コネクテッド廃棄物管理ダッシュボードの編集モードのスクリーンショット。](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

**[+ 新規]** を選択して新しいダッシュボードを作成し、最初から構成することもできます。 複数のダッシュボードを用意して、ダッシュボード メニューからダッシュボードを切り替えることができます。 

## <a name="explore-the-device-template"></a>デバイス テンプレートを探索する

Azure IoT Central のデバイス テンプレートでは、テレメトリ、プロパティ、コマンドなど、デバイスの機能を定義します。 ビルダーは、接続するデバイスの機能を表すデバイス テンプレートを定義できます。 

コネクテッド廃棄物管理アプリケーションには、コネクテッド廃棄物処理デバイス テンプレートのサンプルが付属しています。

デバイス テンプレートを表示するには:

1. Azure IoT Central で、アプリの左ペインから **[デバイス テンプレート]** を選択します。 

    ![アプリケーションのデバイス テンプレートを一覧表示する画面のスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. **[デバイス テンプレート]** リストで **[Connected Waste Bin]\(コネクテッド廃棄物処理\)** を選択します。

1. このデバイス テンプレートの機能を確認します。 **Fill level (充填レベル)** 、**Odor meter (臭気メーター)** 、**Weight (重量)** 、**Location (場所)** などのセンサーが定義されていることがわかります。

   ![コネクテッド廃棄物処理デバイス テンプレートの詳細を表示する画面のスクリーンショット](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

次のカスタマイズを行います。
1. デバイス テンプレート メニューから **[カスタマイズ]** を選択します。
1. **[Odor meter]\(臭気メーター\)** テレメトリ タイプを見つけます。
1. **[Odor meter]\(臭気メーター\)** の **[表示名]** を「**Odor level**」に更新します。
1. 測定単位を更新するか、**最小値** と **最大値** を設定してみましょう。
1. **[保存]** を選択します。 

### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する 

その方法は次のとおりです。
1. デバイス テンプレート メニューから **[クラウド プロパティ]** を選択します。
1. **[クラウド プロパティの追加]** を選択します。 Azure IoT Central では、デバイスには関連するものの、デバイスから送信されることは考えにくいようなプロパティを追加できます。 たとえば、設置区域、アセット情報、メンテナンス情報ごとのアラートしきい値がクラウド プロパティとして考えられます。 
1. **[保存]** を選択します。 
 
### <a name="views"></a>ビュー 
コネクテッド廃棄物処理デバイス テンプレートには、事前定義済みのビューが付属しています。 ビューを確認し、必要に応じて更新しましょう。 このビューには、オペレーターによるデバイス データの表示方法とクラウド プロパティの入力方法が定義されています。 

  ![コネクテッド廃棄物管理テンプレートのデバイス テンプレート ビューのスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>発行 

変更を行ったデバイス テンプレートは、必ず発行してください。 

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成 

新しいデバイス テンプレートを作成するには、 **[+ 新規]** を選択して、手順に従います。 カスタム デバイス テンプレートを最初から作成することも、Azure デバイス カタログからデバイス テンプレートを選択することもできます。 

## <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する

Azure IoT Central では、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。 

コネクテッド廃棄物管理アプリケーションには、コネクテッド廃棄物処理デバイス テンプレートに関連付けられた 2 つのシミュレートされたデバイスがあります。 

### <a name="view-the-devices"></a>デバイスを表示する

1. Azure IoT Central の左ペインから **[デバイス]** を選択します。 

   ![コネクテッド廃棄物管理テンプレートの [デバイス] のスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. **[Connected Waste Bin]\(コネクテッド廃棄物処理\)** デバイスを選択します。  

     ![コネクテッド廃棄物管理テンプレートの [Device Properties]\(デバイスのプロパティ\) のスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. **[Cloud Properties]\(クラウドのプロパティ\)** タブに移動します。 **[Bin full alert threshold]\(ごみ箱容量オーバー アラートしきい値\)** を **95** から **100** に更新します。 

**[Device Properties]\(デバイスのプロパティ\)** と **[Device Dashboard]\(デバイス ダッシュボード\)** のタブを調べます。 

> [!NOTE]
> すべてのタブはデバイス テンプレート ビューから構成されています。

### <a name="add-new-devices"></a>新しいデバイスを追加する

新しいデバイスは、 **[デバイス]** タブの **[+ 新規]** を選択することで追加できます。 

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、ルールを作成して、デバイスのテレメトリを自動的に監視し、1 つまたは複数の条件が満たされたときにアクションをトリガーできます。 アクションには、メール通知の送信や Power Automate アクションのトリガー、他のサービスにデータを送信する Webhook アクションの開始などがあります。

Connected waste management (接続された廃棄物管理) アプリケーションには、4 つのサンプル ルールがあります。

### <a name="view-rules"></a>ルールを表示する
1. Azure IoT Central の左ペインから **[Rules]\(ルール\)** を選択します。

   ![コネクテッド廃棄物管理テンプレートの [Rules]\(ルール\) のスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. **[Bin full alert]\(ごみ箱容量オーバーアラート\)** を選択します。

     ![ごみ箱容量オーバーアラートのスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **[Bin full alert]\(ごみ箱容量オーバー アラート\)** では、**充填レベルがごみ箱容量オーバー アラートしきい値以上** であるかどうかの条件がチェックされます。

    **[Bin full alert threshold]\(ごみ箱容量オーバー アラートしきい値\)** は、コネクテッド廃棄物処理デバイス テンプレートに定義されているクラウド プロパティです。 

次に、メール アクションを作成しましょう。

### <a name="create-an-email-action"></a>メール アクションを作成する

メール アクションの構成は、ルールの **[Actions]\(アクション\)** リストで行うことができます。
1. **[+ Email]\(+ メール\)** を選択します。 
1. **[表示名]** に「**High pH alert**」と入力します。
1. 自分の Azure IoT Central アカウントに関連付けられているメール アドレスを **[送信先]** に入力します。 
1. 必要に応じて、メールのテキストに含めるメモを入力します。
1. **[Done]\(完了\)**  >  **[Save]\(保存\)** の順に選択します。 

今後、構成した条件が満たされるとメールが送信されます。

>[!NOTE]
>アプリケーションから、条件が満たされるたびにメールが送信されます。 自動ルールからのメールの受信を停止するには、ルールを無効にします。 
  
新しいルールを作成するには、 **[Rules]\(ルール\)** の左ペインから **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

Azure IoT Central では、ジョブを使用して、複数のデバイスで、デバイスまたはクラウドのプロパティの更新をトリガーできます。 また、ジョブを使用して、複数のデバイス上でデバイス コマンドをトリガーすることもできます。 Azure IoT Central によって、ワークフローが自動化されます。 

1. Azure IoT Central の左ペインから **[ジョブ]** を選択します。 
1. **[+ 新規]** を選択し、1 つまたは複数のジョブを構成します。 

## <a name="customize-your-application"></a>アプリケーションのカスタマイズ 

ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

### <a name="change-the-application-theme"></a>アプリケーションのテーマを変更する

その方法は次のとおりです。
1. **[管理]**  >  **[アプリケーションのカスタマイズ]** の順に移動します。
1. **[変更]** を選択して、**アプリケーション ロゴ** 用にアップロードする画像を選択します。
1. **[変更]** を選択して、**ブラウザー アイコン** (ブラウザーのタブに表示される画像) 用にアップロードする画像を選択します。
1. HTML 16 進数のカラー コードを追加して、既定のブラウザーの色を置き換えることもできます。 その場合は **[ヘッダー]** と **[アクセント]** のフィールドを使用します。

   ![コネクテッド廃棄物管理テンプレートの [アプリケーションのカスタマイズ] のスクリーンショット。](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. アプリケーションの画像を変更することもできます。 **[管理]**  >  **[アプリケーションの設定]**  >  **[画像の選択]** の順に選択し、アプリケーションの画像としてアップロードする画像を選択します。
1. 最後に、アプリケーションのタイトルで **[設定]** を選択して、テーマを変更することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. 自分の Azure IoT Central アプリの左ペインから **[管理]** を選択します。
1. **[アプリケーションの設定]**  >  **[削除]** の順に選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [接続された廃棄物管理の概念](./concepts-connectedwastemanagement-architecture.md)
