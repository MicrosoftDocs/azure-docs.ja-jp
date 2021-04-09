---
title: Live Video Analytics を Azure Stack Edge にデプロイする
description: この記事では、Live Video Analytics を Azure Stack Edge にデプロイするときに役立つ手順を示します。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: d49167890009d58b21c3678cb89f608bad665abd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730271"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Live Video Analytics を Azure Stack Edge にデプロイする

この記事では、Live Video Analytics を Azure Stack Edge にデプロイするときに役立つ手順を示します。 設定とアクティブ化が済んだら、デバイスで Live Video Analytics をデプロイする準備が整います。 

Live Video Analytics では、IoT Hub を介してデプロイを行います。しかし、Azure Stack Edge リソースについては Kubernetes API が公開されており、お客様はこれを使用して、Live Video Analytics とやり取りできる IoT Hub 非対応ソリューションを追加でデプロイできます。 

> [!TIP]
> カスタム デプロイに Kubernetes (K8s) API を使用するのは、高度なユースケースです。 エッジ モジュールを作成して各 Azure Stack Edge リソースにデプロイする際には IoT Hub を使用するようにし、Kubernetes API は使用しないことをお客様にお勧めします。 この記事では、IoT Hub を使用して Live Video Analytics モジュールをデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件

* [所有者特権](../../role-based-access-control/built-in-roles.md#owner)がある Azure サブスクリプション。
* [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) リソース
   
* [IoT ハブ](../../iot-hub/iot-hub-create-through-portal.md)
* Live Video Analytics モジュール用の[サービス プリンシパル](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal)。

   IoT Hub が利用可能なこれらのリージョンのうち、いずれかを使用します。米国東部 2、米国中部、米国中北部、東日本、米国西部 2、米国中西部、カナダ東部、英国南部、フランス中部、フランス南部、スイス北部、スイス西部、西日本。
* ストレージ アカウント

    General Purpose v2 (GPv2) ストレージ アカウントを使用することをお勧めします。  
    [汎用 v2 ストレージ アカウント](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)についてご確認ください。
* 開発用マシン上の [Visual Studio Code](https://code.visualstudio.com/)。 [Azure IoT Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)があることを確認します。
* 開発用マシンが接続されているネットワークで、ポート 5671 経由の Advanced Message Queuing Protocol が許可されていることを確認します。 このセットアップにより、Azure IoT Tools が Azure IoT Hub と通信できるようになります。

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Live Video Analytics を使用するための Azure Stack Edge の構成

Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 詳細については、[Azure Stack Edge と詳細な設定手順](../../databox-online/azure-stack-edge-deploy-prep.md)を参照してください。 最初に、以下のリンクの指示に従います。

* [Azure Stack Edge および Data Box Gateway リソースの作成](../../databox-online/azure-stack-edge-deploy-prep.md)
* [インストールと設定](../../databox-online/azure-stack-edge-deploy-install.md)
* [接続とアクティブ化](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Azure Stack Edge への IoT ハブのアタッチ](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件の有効化

続行する前に以下を確認します。

* 自分の Azure Stack Edge リソースをアクティブにしていること。
* Azure Stack Edge リソースにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。
* Kubernetes クラスターをデプロイするには、[ローカル Web UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) を使用して自分の Azure Stack Edge リソースを構成する必要があります。 
    
    * コンピューティングを有効にするには、自分のデバイスのローカル Web UI で [コンピューティング] ページに移動します。
    
        * コンピューティングを有効にしたいネットワーク インターフェイスを選択します。 [有効化] を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイスでデバイスの仮想スイッチが作成されます。
        * Kubernetes テスト ノードの IP と Kubernetes 外部サービスの IP は空白のままにします。
        * [適用] を選択します (この操作には約 2 分かかります)。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text="Azure Stack Edge ローカル UI でのコンピューティングに関する前提条件":::

        * Kubernetes API および Azure Stack Edge リソース用に DNS を更新していない場合は、Windows のホスト ファイルを更新できます。
        
            * 管理者としてテキスト エディターを開きます
            * "C:\Windows\System32\drivers\etc\hosts" のファイルを開きます
            * Kubernetes API デバイス名の IPv4 とホスト名をファイルに追加します。 (この情報は、Azure Stack Edge ポータルの [デバイス] セクションで確認できます。)
            * 保存して閉じる

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Azure portal を使用した Live Video Analytics Edge モジュールのデプロイ

ここでは、[IoT Hub を使用した Live Video Analytics のデプロイ](deploy-iot-edge-device.md)に関するページの特定の手順だけを実行します。

1. ユーザーとグループの作成手順はスキップして、「[Live Video Analytics Edge モジュールをデプロイする](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module)」に移動します。 そこに記載されている手順に従ってください。
1. [コンテナーの作成オプション] では、環境変数を設定する必要はありません。 そのため、この手順はスキップします。
1. [コンテナーの作成オプション] タブを開きます。

   * 次の JSON をコピーしてボックスに貼り付け、モジュールによって生成されるログ ファイルのサイズを制限します。
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 共有メモリの転送に gRPC プロトコルを使用する場合は、Live Video Analytics と推論ソリューションの間の共有メモリ アクセスに、ホスト IPC モードを使用します。
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON の "Binds" セクションには、2 つのエントリがあります。 上記のバインド セクションに記載されているディレクトリは、LVA によって自動的に作成されます。  
        エッジ デバイスのバインドは自由に更新してかまいませんが、その際は、それらのディレクトリがデバイスに存在することを確認してください。
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": これは、永続的なアプリケーション構成データをコンテナーからバインドし、エッジ デバイスに格納するために使用されます。
    * "/var/media:/var/media": これにより、エッジ デバイスとコンテナーの間でメディア フォルダーがバインドされます。 これは、エッジ デバイスへのビデオ クリップの格納をサポートするメディア グラフ トポロジを実行するときに、ビデオ記録を格納するために使用されます。
        
1. ドキュメントの手順を続行して、[モジュール ツインの設定] に入力を行います。
1. **[次へ]** を選択します。[ルート] でルートのセクションに進みます。 ルートを指定します。

    既定のルートをそのまま保持して、[次へ] を選択します:[確認と作成] で、[レビュー] セクションに進みます。
1. [デプロイの確認と検証](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>(省略可能) Docker ボリューム マウントの設定

作業ディレクトリ内のデータを表示したい場合は、これらの手順に従ってデプロイ前に Docker ボリューム マウントを設定します。 

これらの手順では、ゲートウェイ ユーザーの作成とファイル共有の設定を行って、Live Video Analytics の作業ディレクトリと Live Video Analytics のメディア フォルダーのコンテンツを表示できるようにする方法について説明します。

> [!NOTE]
> バインド マウントもサポートされていますが、ボリューム マウントでは、データを表示できるほか、必要に応じてリモートでコピーできます。 バインドとボリューム、両方のマウントを使用することが可能です。ただし、それらで同じコンテナー パスを指定することはできません。

1. Azure portal を開いて、Azure Stack Edge リソースに移動します。
1. 共有にアクセスできる **ゲートウェイ ユーザー** を作成します。
    
    1. 左側のナビゲーション ペインで、 **[クラウド ストレージ ゲートウェイ]** をクリックします。
    1. 左側のナビゲーション ペインで **[ユーザー]** をクリックします。
    1. **[+ ユーザーの追加]** をクリックし、ユーザー名とパスワードを設定します。 (推奨: `lvauser`)。
    1. **[追加]** をクリックします。
    
1. Live Video Analytics で保持するための **ローカル共有** を作成します。

    1. **[クラウド ストレージ ゲートウェイ] -> [共有]** をクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: `lva`)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオンであることを確かめます。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="ローカル共有":::  

    > [!TIP]
    > 自分の Azure Stack Edge に接続された Windows クライアントを使用し、[このドキュメントで説明されている](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)次の手順に従って SMB 共有に接続します。    

1. ファイル同期ストレージ用にリモート共有を作成します。

    1. 最初に、同じリージョンに BLOB ストレージ アカウントを作成します。そのためには、 **[クラウド ストレージ ゲートウェイ] -> [ストレージ アカウント]** をクリックします。
    1. **[クラウド ストレージ ゲートウェイ] -> [共有]** をクリックします。
    1. **[+ Add Shares]\(+ 共有の追加)\** をクリックします。
    1. 共有名を設定します。 (推奨: media)。
    1. 共有の種類は SMB のままにします。
    1. **[Edge コンピューティングで共有を使用する]** チェック ボックスがオンであることを確かめます。
    1. **[Edge ローカル共有として構成]** チェック ボックスがオフであることを確かめます。
    1. 先ほど作成したストレージ アカウントを選択します。
    1. コンテナー名を設定します。
    1. ストレージの種類をブロック BLOB に設定します。
    1. [ユーザーの詳細] で、先ほど作成したユーザーに共有へのアクセス権を付与します。
    1. **[作成]** をクリックします。    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="リモート共有":::
    
    
1. ボリューム マウントを使用するよう、Live Video Analytics Edge モジュールの [コンテナーの作成オプション] を更新します (「[モジュールを追加する](deploy-iot-edge-device.md#add-modules)」のポイント 4 を参照してください)。

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>モジュールが動作していることの検証

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

モジュールが実行中であることを確認するには、以下を実行します。

1. Azure portal で、Azure Stack Edge リソースに戻ります。
1. [モジュール] タイルを選択します。 これにより、[モジュール] ブレードが開きます。 モジュールの一覧で、自分がデプロイしたモジュールを特定します。 自分が追加したモジュールのランタイムの状態は、[実行中] になっているはずです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="カスタム モジュール":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 拡張機能を構成する

Azure IoT Tools 拡張機能を使用して IoT ハブに接続するには、次の手順に従います。

1. Visual Studio Code で [表示]、[エクスプローラー] の順に選択します。 または、Ctrl + Shift + E キーを押します。
1. [エクスプローラー] タブの左下隅で、 [Azure IoT Hub] を選択します。
1. [その他のオプション] アイコンを選択して、コンテキスト メニューを表示します。 次に、 [Set IoT Hub Connection String]\(IoT Hub 接続文字列を設定する\) を選択します。
1. 入力ボックスが表示されたら、IoT Hub 接続文字列を入力します。 

   * 接続文字列を取得するには、Azure portal で自分の IoT ハブに移動し、左側のナビゲーション ペインで共有アクセス ポリシーをクリックします。
   * iothubowner をクリックし、共有アクセス キーを取得します。
   * [接続文字列] のプライマリ キーをコピーし、それを VS Code の入力ボックスに貼り付けます。

   接続文字列は次のようになります。<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   接続に成功した場合、エッジ デバイスの一覧が表示されます。 自分の Azure Stack Edge が表示されます。 これでコンテキスト メニューから IoT Edge デバイスを管理し、Azure IoT Hub を操作できるようになりました。 エッジ デバイスにデプロイされたモジュールを表示するには、Azure Stack デバイスの [モジュール] ノードを展開します。
    
## <a name="troubleshooting"></a>トラブルシューティング

* **Kubernetes API アクセス (kubectl)**

    * ドキュメントに従って、[Kubernetes クラスターにアクセス](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-create-kubernetes-cluster)できるように自分のマシンを構成します。
    * デプロイしたすべての IoT Edge モジュールでは、`iotedge` 名前空間が使用されます。 kubectl の使用時にそれを含めるようにしてください。  

* **モジュール ログ**

    `iotedge` ツールは、ログを取得するためにアクセスすることができません。 ログを表示したり、ファイルにパイプしたりするには、[kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) を使用する必要があります。 例: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **ポッドとノードのメトリック**

    ポッドとノードのメトリックを確認するには、[kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) を使用します。
    <br/>`kubectl top pods -n iotedge` 

* **モジュールのネットワーク**   
Azure Stack Edge でモジュールを検出するには、モジュールに createOptions のホスト ポート バインドがなければなりません。 このモジュールは、`moduleName:hostport` を介してアドレス指定できるようになります。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **ボリュームのマウント**

    空でない既存のディレクトリに対してコンテナーがボリュームのマウントを試行している場合、モジュールの起動は失敗します。

* **gRPC を使用する際の共有メモリ**

    Azure Stack Edge リソース上の共有メモリは、ホスト IPC を使用して、任意の名前空間のポッド全体でサポートされます。
    IoT Hub を介してデプロイを行うために、エッジ モジュール上の共有メモリを構成します。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* **(高度) ポッドのコロケーション**

    K8s を使用し、gRPC 経由で Live Video Analytics と通信するカスタム推論ソリューションをデプロイしている場合は、必ず Live Video Analytics モジュールと同じノードにポッドがデプロイされている必要があります。

    * **オプション 1** - コロケーションにノード アフィニティと組み込みノード ラベルを使用する。

    ノードにラベルを設定するためのアクセス権をユーザーが持っていないため、現在は NodeSelector カスタム構成を利用できないように思えるかもしれません。 しかし、お客様のトポロジと名前付け規則によっては、[組み込みノード ラベル](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)を使用できる場合もあります。 Azure Stack Edge リソースと Live Video Analytics を参照する nodeAffinity セクションは、コロケーションを実現するために推論ポッド マニフェストに追加できます。
    * **オプション 2** - コロケーションにポッド アフィニティを使用する (推奨)。
Kubernetes では、同じノードでポッドをスケジュールできる[ポッド アフィニティ](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)をサポートしています。 コロケーションを実現するために、Live Video Analytics モジュールを参照する podAffinity セクションを推論ポッド マニフェストに追加できます。

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```
* **`rtspsim` モジュールの使用中に発生する 404 エラー コード**  
このコンテナーは、そこに含まれる 1 つのフォルダーからのみビデオを読み取ります。 コンテナー イメージ内の既存のフォルダーに外部のフォルダーをマッピング (バインド) した場合、そのコンテナー イメージに存在するファイルが、Docker によって非表示にされます。  
 
    たとえば、バインディングがない状態で、コンテナーに次のファイルが存在するとします。  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    また、ホストには次のファイルが存在するとします。
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    ところが、次のバインディングを配置マニフェスト ファイルに追加すると、/live/mediaServer/media の内容が、ホスト側と一致するように Docker によって上書きされます。
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>次のステップ

ライブ ビデオ ストリームの分析は、モジュールを使用し、ダイレクト メソッドを呼び出すことによって行うことができます。 モジュールでの[ダイレクト メソッドの呼び出し](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)。