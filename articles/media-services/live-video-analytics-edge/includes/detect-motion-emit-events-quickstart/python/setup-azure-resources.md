---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691189"
---
このチュートリアルでは、次の Azure リソースが必要です。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](../../../../../iot-edge/how-to-install-iot-edge-linux.md)がインストール済み)

このクイックスタートでは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに必要なリソースをデプロイすることをお勧めします。 これを行うには、次のステップに従います。

1. [Azure Cloud Shell](https://shell.azure.com) を開きます。
1. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションの選択を求められます。 **[ストレージの作成]** を選択して、Cloud Shell のセッション情報用のストレージ アカウントを作成します。 このストレージ アカウントは、Azure Media Services アカウントで使用するためにスクリプトによって作成されるアカウントとは別のものです。
1. Cloud Shell ウィンドウの左側にあるドロップダウン メニューから **[Bash]** をご利用の環境として選択します。

    ![環境セレクター](../../../media/quickstarts/env-selector.png)
1. 次のコマンドを実行します。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    スクリプトが正常に終了すれば、必要なすべてのリソースがご利用のサブスクリプションに表示されます。
1. スクリプトが完了したら、中かっこを選択してフォルダー構造を展開します。 *~/clouddrive/lva-sample* ディレクトリに、いくつかのファイルがあるのを確認できます。 このクイックスタートで注目するのは、次のものです。

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - このファイルには、Visual Studio Code がエッジ デバイスにモジュールをデプロイする際に使用するプロパティが格納されています。
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code は、このファイルを使用してサンプル コードを実行します。
     
    これらのファイルは、次のセクションで Visual Studio Code に開発環境を設定するときに必要になります。 差し当たりローカル ファイルとしてコピーしておいてください。
    
    ![アプリケーション設定](../../../media/quickstarts/clouddrive.png)
    