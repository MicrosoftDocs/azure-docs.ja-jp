---
title: IoT Edge for Linux on Windows にデプロイする - Azure
description: この記事では、IoT Edge for Linux on Windows デバイスにデプロイする方法に関するガイダンスを取り上げます。
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 9ec28c62ca804137ede3cd60d1980e55fbaa2807
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618136"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>IoT Edge for Linux on Windows (EFLOW) デバイスにデプロイする

この記事では、[IoT Edge for Linux on Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows) がインストールされているエッジ デバイスに Live Video Analytics をデプロイする方法について説明します。 このドキュメントの手順を終えると、ビデオでの動きを検出してそのようなイベントをクラウド内の IoT ハブに出力する[メディア グラフ](media-graph-concept.md)を実行できるようになります。 そのメディア グラフを、高度なシナリオに対応するよう置き換えれば、Live Video Analytics の強力な機能を Windows ベースの IoT Edge デバイスで利用することができます。

## <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > サービス プリンシパルを作成するためのアクセス許可を与えられた Azure サブスクリプションが必要です (**owner role** には、そのアクセス許可があります)。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* [EFLOW の概要](https://aka.ms/AzEFLOW-docs)に関するページを参照してください。

## <a name="deployment-steps"></a>デプロイメントの手順

このドキュメントの全体的な流れを次に示します。EFLOW がインストールされている Windows デバイスで Live Video Analytics を実行する準備が、簡単な 5 つの手順ですべて整います。

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT Edge for Linux on Windows (EFLOW) の図":::

1. Windows デバイスに [EFLOW をインストール](https://aka.ms/AzEFLOW-install)します。 

    1. Windows PC を使用している場合、[Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) 開始ページにある接続の一覧に、Windows Admin Center を実行している PC を表すローカル ホスト接続が表示されます。 
    1. その他の管理対象のサーバー、PC、またはクラスターもここに表示されます。
    1. Windows Admin Center を使用して、ローカル デバイスまたはリモート マネージド デバイス上に、Azure EFLOW をインストールして管理できます。 このガイドでは、ローカル ホスト接続が、Azure IoT Edge for Linux on Windows のデプロイにおけるターゲット デバイスとして使用されました。 そのため、localhost も IoT Edge デバイスとして表示されています。

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="デプロイ手順 - Windows Admin Center":::
1. IoT Edge デバイスをクリックしてそれに接続すると、[概要] タブと [コマンド シェル] タブが表示されます。[コマンド シェル] タブでは、エッジ デバイスに対するコマンドを実行できます。
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="デプロイ手順 - Azure IoT Edge Manager":::
1. コマンド シェルに移動して次のコマンドを入力します。
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Live Video Analytics モジュールは、エッジ デバイス上で、特権のない[ローカル ユーザー アカウント](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)を使用して実行されます。 また、アプリケーションの構成データを格納するための[特定のローカル フォルダーも必要](deploy-iot-edge-device.md#granting-permissions-to-device-storage)となります。 最後に、この使用法ガイドでは、分析のためにビデオ フィードをリアルタイムで LVA モジュールに中継する [RTSP シミュレーター](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)を活用しています。 このシミュレーターは入力として、事前に録画されたビデオ ファイルを input ディレクトリから受け取ります。 
    
    上で使用した prep-device スクリプトにより、これらのタスクが自動化されます。そのため、1 つのコマンドを実行すれば、関連するすべての入力と構成フォルダー、ビデオ入力ファイル、特権付きのユーザー アカウントがシームレスに作成されます。 コマンドが正常に完了すると、次のフォルダーがエッジ デバイス上に作成されます。 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    /home/lvaedgeuser/samples/input フォルダーにあるビデオ ファイル (*.mkv) に注目してください。これらが分析対象の入力ファイルとなります。 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="分析":::
1. エッジ デバイスの設定とハブへの登録が完了し、適切なフォルダー構造が作成された状態で実行することに成功したら、次に、別途以下の Azure リソースを設定し、LVA モジュールをデプロイします。 

    * ストレージ アカウント
    * Azure Media Services アカウント

    ここでは、[Live Video Analytics リソースのセットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに必要なリソースをデプロイすることをお勧めします。 これを行うには、次のステップに従います。

    1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/) を開きます。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションの選択を求められます。 **[ストレージの作成]** を選択して、Cloud Shell のセッション情報用のストレージ アカウントを作成します。 このストレージ アカウントは、Azure Media Services アカウントで使用するためにスクリプトによって作成されるアカウントとは別のものです。
    1. Cloud Shell ウィンドウの左側にあるドロップダウン メニューから [Bash] をご利用の環境として選択します。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash 環境":::
    1. 次のコマンドを実行します。

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        IoT Edge デバイスとして独自のエッジ デバイスを選ぶように求められたら必ず **Y** を選択してください。そのデバイスと IoT ハブはどちらも作成済みです。 さらに、IoT ハブの名前と IoT Edge デバイスの ID を入力するよう求められます。どちらも、Azure portal にログインし、自分の IoT ハブをクリックして、IoT Hub ポータル ブレードの [IoT Edge] オプションに移動することで入手できます。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="IoT Edge デバイスの確認":::

    完了したら、IoT Edge デバイスのコマンド シェルに再度ログオンして、次のコマンドを実行します。
    
    `sudo iotedge list`
    
    次の 4 つのモジュールがエッジ デバイスにデプロイされ、実行されていることがわかります。 リソース作成スクリプトによって、IoT Edge モジュール (edgeAgent と edgeHub)、RTSP ビデオ フィードのシミュレーションを提供する RTSP シミュレーター モジュールと共に、LVA モジュールがデプロイされることに注意してください。
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="状態の確認":::
1. モジュールのデプロイと設定を終えたら、初めての LVA メディア グラフを EFLOW で実行する準備は完了です。 次の手順を実行すると、以下に示すような単純なモーション検出グラフを実行し、その結果を視覚化することができます。

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="モーション検出グラフ":::

    1. Azure IoT Tools 拡張機能を[構成](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)します。
    
        > [!Note]
        > 次のパスを使用します: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json`
    1. この[ダイレクト メソッド呼び出し](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)を使用して、トポロジを設定し、グラフをインスタンス化してアクティブ化します。
    1. ハブで[結果を確認](get-started-detect-motion-emit-events-quickstart.md#observe-results)します。
    1. [クリーンアップ メソッド](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)を呼び出します。
    1. 今後必要がない場合は、リソースを削除します。

        > [!IMPORTANT]
        > 削除しなかった場合、リソースは引き続きアクティブな状態となり、Azure のコストが発生します。
    
## <a name="next-steps"></a>次のステップ

* 関連するビデオをクラウドで記録しながらモーション検出を試します。 [モーション検出と Media Services へのビデオ クリップの記録](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video)に関するクイックスタートの手順に従ってください。
* [ライブ ビデオの AI](use-your-model-quickstart.md#overview) を実行します (前提条件である設定は、上記で既に完了しているのでスキップしてかまいません)。
* [VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)を使用して、その他のメディア グラフを確認します。
* RTSP シミュレーターを使用する代わりに、RTSP をサポートする [IP カメラ](https://en.wikipedia.org/wiki/IP_camera)を使用します。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products/)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。

