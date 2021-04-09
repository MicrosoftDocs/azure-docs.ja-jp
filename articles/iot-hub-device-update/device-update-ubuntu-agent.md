---
title: Ubuntu Server 18.04 x64 Package agent を使用した Device Update for Azure IoT Hub のチュートリアル | Microsoft Docs
description: Ubuntu Server 18.04 x64 Package agent を使用して、Device Update for Azure IoT Hub を開始します。
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: f7e12567269304b33a98ff1eb9727cfdf0afbdc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418642"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Ubuntu Server 18.04 x64 上のパッケージ エージェントを使用した Device Update for Azure IoT Hub のチュートリアル

Device Update for IoT Hub では、イメージベースとパッケージベースの 2 つの形式の更新がサポートされています。

パッケージベースの更新は、デバイス上の特定のコンポーネントまたはアプリケーションのみを変更するターゲット指定の更新となります。 これにより、帯域幅の消費量が少なくなるほか、更新のダウンロードとインストールにかかる時間が短縮されます。 通常、パッケージ更新プログラムを使用すると、更新プログラムを適用する際のデバイスのダウンタイムを短縮し、イメージ作成のオーバーヘッドを回避できます。

このチュートリアルでは、Device Update for IoT Hub を通じて、エンド ツー エンドのパッケージベースの更新を行う手順について説明します。 このチュートリアルでは、Azure IoT Edge と Device Update パッケージ エージェントが実行されている Ubuntu Server 18.04 x64 を使用します。 このチュートリアルではサンプル パッケージの更新を扱いますが、同様の手順を使用すれば、他のパッケージ、たとえば Azure IoT Edge やそこで使用されているコンテナー エンジンを更新することもできます。

このチュートリアルで取り上げるツールと概念は、使用を検討している OS プラットフォーム構成が異なる場合にも当てはまります。 このエンド ツー エンドの更新プロセスの概要を完了したら、使用する更新の形式や OS プラットフォームを選択して、詳細に進みます。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Device Update エージェントとその依存関係をダウンロードしてインストールする
> * デバイスにタグを追加する
> * 更新プログラムをインポートする
> * デバイス グループを作成する
> * パッケージの更新をデプロイする
> * 更新プログラムのデプロイを監視する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* IoT Hub へのアクセス。 S1 (Standard) レベル以上を使用することをお勧めします。
* ご使用の IoT ハブにリンクされている Device Update インスタンスおよびアカウント。
  * [Device Update アカウントの作成とリンク](create-device-update-account.md)がまだ済んでいない場合は、ガイドに従って行ってください。
* [IoT Edge デバイスの接続文字列](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings)。

## <a name="prepare-a-device"></a>デバイスを準備する
### <a name="using-the-automated-deploy-to-azure-button"></a>自動化された [Azure へのデプロイ] ボタンを使用する

Ubuntu 18.04 LTS 仮想マシンを短時間で設定しやすいように、このチュートリアルでは便宜上 [cloud-init](../virtual-machines/linux/using-cloud-init.md) ベースの [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用します。 Azure IoT Edge ランタイムと Device Update パッケージ エージェントの両方がインストールされた後、指定した IoT Edge デバイスのデバイス接続文字列 (前提条件) を使用して、プロビジョニング情報を基にデバイスの構成が自動的に行われます。 そのため、設定を行うために SSH セッションを開始する必要はありません。

1. 開始するには、以下のボタンをクリックします。

   [![iotedge-vm-deploy の [Deploy to Azure] ボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0-rc4%2FedgeDeploy.json)

1. 新しく起動されたウィンドウで、使用可能なフォームフィールドに入力します。

    > [!div class="mx-imgBorder"]
    > [![iotedge-vm-deploy テンプレートを示すスクリーンショット](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **サブスクリプション**:仮想マシンをデプロイするためのアクティブな Azure サブスクリプション。

    **[リソース グループ]** :仮想マシンとそれに関連付けられたリソースを格納する、既存または新しく作成されたリソース グループ。

    **[DNS ラベル プレフィックス]** :仮想マシンのホスト名のプレフィックスとして使用される、あなたが選択した必要値。

    **管理ユーザー名**: ユーザー名。デプロイ時にルート権限が与えられます。

    **[デバイス接続文字列]** :目的の [IoT Hub](../iot-hub/about-iot-hub.md) 内に作成された、デバイス向けの [デバイス接続文字列](../iot-edge/how-to-register-device.md)。

    **[VM サイズ]** :デプロイする仮想マシンの [サイズ](../cloud-services/cloud-services-sizes-specs.md)

    **[Ubuntu OS バージョン]** :ベース仮想マシンにインストールする Ubuntu OS のバージョン。 既定値は変更せず、そのままにしておいてください。あらかじめ Ubuntu 18.04-LTS に設定されます。

    **[場所]** :仮想マシンをデプロイする [地理的リージョン](https://azure.microsoft.com/global-infrastructure/locations/)。選択したリソース グループの場所が規定値となります。

    **[認証タイプ]** :**sshPublicKey** または **パスワード** のどちらか好きな方を選択します。

    **[管理パスワードまたはキー]** :認証タイプの選択に応じて、SSH 公開キーの値、またはパスワードの値。

    すべてのフィールドへの入力を終えたら、ページの下部にあるチェックボックスをオンにして使用条件に同意し、 **[購入]** を選択してデプロイを開始します。

1. デプロイが正常に完了したことを確認します。 デプロイの完了後、インストール後の処理と構成で IoT Edge と Device Package 更新エージェントがインストールされるまで数分待ちます。

   仮想マシン リソースが、選択したリソース グループにデプロイされている必要があります。  マシン名をメモしておきます。この名前は `vm-0000000000000` の形式である必要があります。 また、関連付けられている **DNS 名** をメモしておきます。この名前は `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com の形式である必要があります。

    **DNS 名** は、Azure portal 内に新しく展開された仮想マシンの **概要** セクションから取得できます。

    > [!div class="mx-imgBorder"]
    > [![Iotedge VM の DNS 名を示すスクリーンショット](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > 設定後にこの VM に SSH 接続する場合は、以下のコマンドで、関連付けられている **DNS 名** を使用します。`ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>(省略可) デバイスを手動で準備する
デバイスのインストールと構成を手動で行う次の手順は、この [cloud-init スクリプト](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt)によって自動化された手順に相当します。 これに従って物理デバイスを準備することができます。

1. [Azure IoT Edge ランタイムのインストール](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)手順に従います。
   > [!NOTE]
   > Device Update パッケージ エージェントは IoT Edge に依存しません。 ただし、ID を取得して IoT Hub に接続するために、IoT Edge (1.2.0 以上) と一緒にインストールされる IoT ID サービス デーモンに依存します。
   >
   > このチュートリアルでは取り上げませんが、[IoT ID サービス デーモンは Linux ベースの IoT デバイスにスタンドアロンでインストールすることができます](https://azure.github.io/iot-identity-service/packaging.html)。 インストールの順序が重要です。 Device Update パッケージ エージェントは、IoT ID サービスの "_後に_" インストールする必要があります。 そうしないと、承認済みのコンポーネントとしてパッケージ エージェントが登録されず、IoT Hub との接続が確立されません。

1. 次に、Device Update エージェントの .deb パッケージをインストールします。

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Device Update for Azure IoT Hub ソフトウェア パッケージには、次のライセンス条項が適用されます。
  * [Device Update for IoT Hub ライセンス](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [配信の最適化のクライアント ライセンス](https://github.com/microsoft/do-client/blob/main/LICENSE)

パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

## <a name="add-a-tag-to-your-device"></a>デバイスにタグを追加する

1. [Azure portal](https://portal.azure.com) にログインし、IoT Hub に移動します。

2. 左側のナビゲーション ペインの [IoT Edge] から、対象の IoT Edge デバイスを見つけて、[デバイス ツイン] に移動します。

3. デバイス ツインで、Device Update の既存のタグ値を null に設定することで削除します。

4. 次に示すように、Device Update の新しいタグ値を追加します。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>更新プログラムをインポートする

1. Github の [デバイス更新プログラムのリリース](https://github.com/Azure/iot-hub-device-update/releases)に関するページ移動し、[Assets] ドロップダウンをクリックします。

3. [`apt-update-import-samples.zip`] をクリックしてダウンロードします。

5. フォルダーの内容を抽出して、さまざまな更新プログラムのサンプルとそれに対応するインポー トマニフェストを見つけます。 

2. Azure portal で、IoT Hub の左側のナビゲーション バーから [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

3. [更新プログラム] タブを選択します。

4. [+ 新しい更新プログラムのインポート] を選択します。

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 前にダウンロードしたフォルダーで、`sample-package-update-1.0.1-importManifest.json` インポート マニフェストを選択します。 次に、[1 つまたは複数の更新プログラム ファイルの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 以前にダウンロードしたフォルダーから、`sample-1.0.1-libcurl4-doc-apt-manifest.json` apt マニフェスト更新ファイルを選択します。
この更新プログラムにより、使用可能な最新バージョンの `libcurl4-doc package` が、お使いのデバイスにインストールされます。

   または、前にダウンロードしたフォルダーで、`sample-package-update-2-2.0.1-importManifest.json` インポート マニフェスト ファイルと `sample-2.0.1-libcurl4-doc-7.58-apt-manifest.json` apt マニフェスト更新ファイルを選択することもできます。 これにより、特定バージョン (v7.58.0) の `libcurl4-doc package` が自分のデバイスにインストールされます。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="更新ファイルの選択を示すスクリーンショット。" lightbox="media/import-update/select-update-files.png":::

6. [ストレージ コンテナーの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 次に、適切なストレージ アカウントを選択します。

7. コンテナーを既に作成してある場合は、再利用できます (そうでない場合は、[+ コンテナー] を選択して、更新用の新しいストレージ コンテナーを作成します)。  使用するコンテナーを選択し、[選択] をクリックします。

   :::image type="content" source="media/import-update/container.png" alt-text="コンテナーの選択を示すスクリーンショット。" lightbox="media/import-update/container.png":::

8. [送信] を選択してインポート プロセスを開始します。

9. インポート プロセスが開始され、画面が [インポートの履歴] セクションに変わります。 [更新] を選択すると、インポート プロセスが完了するまで進行状況が表示されます。 更新プログラムのサイズに応じて、これは数分で完了する場合も、それよりも長くかかる場合もあります。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="更新プログラムのインポート シーケンスを示すスクリーンショット。" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. [状態] 列にインポートが正常に完了したと表示されたら、[デプロイの準備完了] ヘッダーを選択します。 インポートされた更新プログラムが一覧に表示されるはずです。

更新プログラムのインポートについて詳しくは、[こちら](import-update.md)をご覧ください。

## <a name="create-update-group"></a>更新プログラム グループを作成する

1. 以前に Device Update インスタンスに接続した IoT Hub にアクセスします。

1. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

1. ページの上部にある [グループ] タブを選択します。

1. [追加] ボタンを選択して、新しいグループを作成します。

1. 前の手順で作成した IoT Hub のタグを一覧から選択します。 [更新プログラム グループの作成] を選択します。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="タグの選択を示すスクリーンショット。" lightbox="media/create-update-group/select-tag.PNG":::

タグの追加と更新プログラム グループの作成に関する[詳細情報](create-update-group.md)

## <a name="deploy-update"></a>更新プログラムをデプロイする

1. グループが作成されると、デバイス グループで利用可能な新しい更新プログラムが表示され、 _[使用可能な更新プログラム]_ 列にその更新プログラムへのリンクが表示されます。 場合によっては、1 回更新する必要があります。

1. 利用可能な更新プログラムへのリンクをクリックします。

1. ターゲット グループとして正しいグループが選択されていることを確認し、デプロイをスケジュールします。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="更新プログラムを選択する" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > 既定の開始日時は、現在の時刻から 24 時間後です。 もっと早くデプロイしたい場合は、異なる日時を選択してください。

1. [Deploy update]\(更新プログラムをデプロイする\) を選択します。

1. コンプライアンス チャートを表示します。 更新が進行中であると表示されます。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="更新が進行中です" lightbox="media/deploy-update/update-in-progress.png":::

1. デバイスが正常に更新されると、コンプライアンス チャートと展開の詳細が、同じ内容を反映するように更新されていることがわかります。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新に成功しました" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>更新プログラムの展開を監視する

1. ページの上部にある [展開] タブを選択します。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="[展開] タブ" lightbox="media/deploy-update/deployments-tab.png":::

1. 作成した展開を選択して、展開の詳細を表示します。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

1. [更新] を選択し、最新の状態の詳細を表示します。 状態が [成功] に変わるまで、このプロセスを続行します。

これで、Ubuntu Server 18.04 x64 デバイス上で Device Update for IoT Hub を使用して、エンド ツー エンドのパッケージ更新が正常に完了しました。 

## <a name="bonus-steps"></a>ボーナス ステップ

1. 「更新プログラムをインポートする」セクションと「更新プログラムをデプロイする」セクションを繰り返します

3. 「更新プログラムをインポートする」の手順中に、前にダウンロードしたフォルダーで、`sample-package-update-1.0.2-importManifest.json` インポート マニフェスト ファイルと `sample-1.0.2-libcurl4-doc-remove-apt-manifest.json` apt マニフェスト更新ファイルを選択することもできます。 この更新プログラムによって、インストールされている `libcurl4-doc package` がデバイスから削除されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Device Update アカウント、インスタンス、IoT Hub、IoT Edge デバイス ([Azure へのデプロイ] ボタンで VM を作成した場合) をクリーンアップしてください。 これを行うには、個々のリソースに移動し、[削除] を選択します。 デバイス更新アカウントをクリーンアップする前に、デバイス更新インスタンスをクリーンアップする必要があることに注意してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+ でのイメージの更新のチュートリアル](device-update-raspberry-pi.md)
