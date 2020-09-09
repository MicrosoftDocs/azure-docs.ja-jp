---
title: チュートリアル:IoT Central でソーラー パネル監視アプリを作成する
description: チュートリアル:Azure IoT Central アプリケーション テンプレートを使用してソーラーパネル アプリケーションを作成する方法について説明します。
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 5a48ea0747a110720a29a9c34fa2dca0f5a74f16
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310004"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>チュートリアル:ソーラーパネル監視アプリ テンプレートの作成とチュートリアル 



このチュートリアルでは、ソーラー パネル監視アプリケーションの作成プロセスについて説明します。これには、シミュレートされたデータを含むサンプル デバイス モデルが含まれます。 このチュートリアルでは、次のことについて説明します。


> [!div class="checklist"]
> * ソーラー パネル アプリを無料で作成する
> * アプリケーションのチュートリアル
> * リソースをクリーンアップする


サブスクリプションをお持ちでない場合は、[無料試用版アカウントを作成](https://azure.microsoft.com/free)します

## <a name="prerequisites"></a>前提条件
- なし
- Azure サブスクリプションをお勧めしますが、試すことは必須ではありません。


## <a name="create-a-solar-panel-monitoring-app"></a>ソーラー パネル監視アプリを作成する 

このアプリケーションは、次の 3 つの簡単な手順で作成できます。

1. [Azure IoT Central のホーム ページ](https://apps.azureiotcentral.com)を開き、 **[ビルド]** をクリックして新しいアプリケーションを作成します。 

2. **[エネルギー]** タブを選択し、 **[Solar panel monitoring]\(ソーラー パネル監視\)** アプリケーション タイルの **[アプリの作成]** をクリックします。 

    > [!div class="mx-imgBorder"]
    > ![アプリをビルドする](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **[アプリの作成]** を選択すると、 **[新しいアプリケーション]** フォームが開きます。 次の図に示すように、必要な詳細を入力します。
    * **アプリケーション名**:IoT Central アプリケーションの名前を選択します。 
    * **URL**: IoT Central URL を選択すると、プラットフォームによってその一意性が検証されます。
    * **7 日間の無料試用版**:Azure サブスクリプションが既にある場合は、既定の設定をお勧めします。 Azure サブスクリプションをお持ちでない場合は、無料試用版から始めてください。
    * **課金情報**:アプリケーション自体は無料です。 アプリのリソースをプロビジョニングするには、ディレクトリ、Azure サブスクリプション、リージョンの詳細が必要です。
    * ページの下部にある **[作成]** ボタンをクリックすると、アプリが 1 分ほどで作成されます。
        ![[新しいアプリケーション] フォーム](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![[新しいアプリケーション] フォームの課金情報](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>アプリケーションとシミュレートされたデータを検証する

新しく作成されたソーラー パネル アプリは自分のアプリなので、いつでも変更できます。 変更する前に、アプリがデプロイされ、期待どおりに動作することを確認しましょう。

アプリの作成とデータのシミュレーションを確認するには、 **[ダッシュボード]** にアクセスします。 いくつかのデータを含むタイルが表示される場合、アプリのデプロイは成功しています。 データのシミュレーションには、データの生成に数分かかる場合があるため、1 から 2 分待ちます。 

## <a name="application-walk-through"></a>アプリケーションのチュートリアル
正常にデプロイされたアプリ テンプレートには、サンプルのスマート メーター デバイス、デバイス モデル、およびダッシュボードが付属しています。

Adatum は、ソーラー パネルを監視および管理する架空のエネルギー会社です。 ソーラー パネル監視ダッシュボードには、ソーラー パネルのプロパティ、データ、およびサンプル コマンドが表示されます。 これにより、オペレーターおよびサポート チームは、サポート インシデントに備えて、事前に次のアクティビティを実行できます。
* マップ上の最新のパネル情報とその設置場所を確認する
* パネルの状態と接続の状態を事前に確認する
* エネルギー生成と温度の傾向を確認して異常なパターンをキャッチする
* 計画と課金のためにエネルギー生成の合計を追跡する
* コマンドと制御操作 (パネルのアクティブ化やファームウェア バージョンの更新など)。 テンプレートでは、コマンド ボタンには使用可能な機能が表示され、実際のコマンドは送信されません。

> [!div class="mx-imgBorder"]
> ![ソーラー パネル監視ダッシュボード](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>デバイス
このアプリには、サンプルのソーラー パネル デバイスが付属しています。 デバイスの詳細を確認するには、 **[デバイス]** タブをクリックします。

> [!div class="mx-imgBorder"]
> ![ソーラー パネル デバイス](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


サンプル デバイス **SP0123456789** リンクをクリックすると、デバイスの詳細が表示されます。 **[プロパティの更新]** ページ上でデバイスの書き込み可能なプロパティを更新し、ダッシュボードで更新された値を視覚化できます。 

> [!div class="mx-imgBorder"]
> ![ソーラー パネルのプロパティ](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>デバイス テンプレート
**[デバイス テンプレート]** タブをクリックして、ソーラー パネル デバイス モデルを表示します。 モデルには、データ、プロパティ、コマンド、およびビューの事前定義インターフェイスがあります。

> [!div class="mx-imgBorder"]
> ![ソーラー パネル デバイス テンプレート](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする
このアプリケーションの引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. 左側のペインで、[管理] タブを開きます。
2. [アプリケーションの設定] を選択し、ページの下部にある [削除] ボタンをクリックします。 

    > [!div class="mx-imgBorder"]
    > ![アプリケーションを削除する](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>次のステップ
* ソーラー パネル アプリのアーキテクチャの詳細について[概念に関する記事](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)を確認します
* ソーラー パネル アプリケーション テンプレートを無料で作成する: [ソーラー パネル アプリ](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* IoT Central の詳細について、[IoT Central の概要](https://docs.microsoft.com/azure/iot-central/)を参照する
