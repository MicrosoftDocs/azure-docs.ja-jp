---
ms.openlocfilehash: 98a3965160e7ab6c86ba8c6d3a4dfd75af4c6dbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750771"
---
このチュートリアルでは、次の Azure リソースが必要です。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](../../../../../iot-edge/how-to-install-iot-edge.md)がインストール済み)

このクイックスタートでは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに必要なリソースをデプロイすることをお勧めします。 これを行うには、次のステップに従います。

1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/) を開きます。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションの選択を求められます。 **[ストレージの作成]** を選択して、Cloud Shell のセッション情報用のストレージ アカウントを作成します。 このストレージ アカウントは、Azure Media Services アカウントで使用するためにスクリプトによって作成されるアカウントとは別のものです。
1. Cloud Shell ウィンドウの左側にあるドロップダウン メニューから **[Bash]** をご利用の環境として選択します。

    ![環境セレクター](../../../media/quickstarts/env-selector.png)
1. 次のコマンドを実行します。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    スクリプトが正常に完了すると、必要なすべてのリソースがご利用のサブスクリプションに表示されます。 このスクリプトによって合計 12 個のリソースが設定されます。
    1. **ストリーミング エンドポイント** - 記録された AMS アセットの再生に利用されます。
    1. **仮想マシン** - エッジ デバイスとして機能する仮想マシンです。
    1. **ディスク** - メディアや成果物を格納する目的で仮想マシンにアタッチされるストレージ ディスクです。
    1. **ネットワーク セキュリティ グループ** - Azure 仮想ネットワーク内の Azure リソースが送受信するネットワーク トラフィックにフィルターを適用する目的で使用されます。
    1. **ネットワーク インターフェイス** - Azure Virtual Machine がインターネットや Azure、その他各種リソースと通信できるようにします。
    1. **bastion 接続** - ブラウザーと Azure portal を使用して仮想マシンに接続することができます。
    1. **パブリック IP アドレス** - Azure リソースからインターネットへの通信と、公開されている Azure サービスへの通信が可能になります。
    1. **Azure ネットワーク** - 仮想マシンなどのさまざまな種類の Azure リソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。 詳細については、[仮想ネットワーク](../../../../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。
    1. **IoT Hub** - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。
    1. **Media Services アカウント** - Azure におけるメディア コンテンツの管理とストリーミングに使用されます。
    1. **ストレージ アカウント** - 1 つのプライマリ ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数のセカンダリ ストレージ アカウントを持つことができます。 詳細については、[Azure Storage アカウントの Azure Media Services アカウント](../../../../latest/storage-account-concept.md)に関するページを参照してください。
    1. **コンテナー レジストリ** - プライベート Docker コンテナー イメージおよび関連する成果物の格納と管理に使用されます。
1. スクリプトが完了したら、中かっこを選択してフォルダー構造を展開します。 *~/clouddrive/lva-sample* ディレクトリに、いくつかのファイルがあるのを確認できます。 このクイックスタートで注目するのは、次のものです。

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - このファイルには、Visual Studio Code がエッジ デバイスにモジュールをデプロイする際に使用するプロパティが格納されています。
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code は、このファイルを使用してサンプル コードを実行します。
     
    これらのファイルは、次のセクションで Visual Studio Code に開発環境を設定するときに必要になります。 差し当たりローカル ファイルとしてコピーしておいてください。
    
    ![アプリケーション設定](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 作成された Azure リソースで問題が発生した場合は、 **[トラブルシューティング ガイド](../../../troubleshoot-how-to.md#common-error-resolutions)** を参照して、よく発生する問題を解決してください。