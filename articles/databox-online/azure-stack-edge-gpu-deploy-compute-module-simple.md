---
title: IoT Edge モジュールを使用して GPU 搭載の Azure Stack Edge Pro にコンピューティング ワークロードをデプロイする | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイスで事前に作成された IoT Edge モジュールを使用して、コンピューティング ワークロードを実行する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6c067fb5f39e82bb1601ce7b4d9dc5e2ce4ac624
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440141"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>チュートリアル:Azure Stack Edge Pro GPU で IoT Edge モジュールを使用してコンピューティング ワークロードを実行する

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

このチュートリアルでは、Azure Stack Edge Pro GPU デバイスで IoT Edge モジュールを使用して、コンピューティング ワークロードを実行する方法について説明します。 コンピューティングを構成すると、デバイスはデータを Azure に送信する前に変換できます。

この手順の所要時間はおおよそ 10 分から 15 分です。


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

 
## <a name="prerequisites"></a>前提条件

Azure Stack Edge Pro GPU デバイスでコンピューティング ロールを設定する前に、次のことを確認してください。

- [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関するページで説明されているように、Azure Stack Edge Pro デバイスがアクティブ化されていること。
- データに対して実行できる IoT Edge モジュールがあること。 このチュートリアルでは、`filemove2` モジュールを使用して、デバイス上の Edge ローカル共有から Edge 共有にデータを移動します。データは、Edge 共有から Azure Storage アカウントに転送されます。


## <a name="configure-compute"></a>コンピューティングを構成する

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>共有を追加する

このチュートリアルのシンプルなデプロイでは、2 つの共有が必要になります。1 つは Edge 共有で、もう 1 つは Edge ローカル共有です。

1. Edge 共有をデバイスに追加するには、次の手順を実行します。

    1. Azure Stack Edge リソースで、 **[クラウド ストレージ ゲートウェイ] > [共有]** の順に移動します。
    2. コマンド バーで、 **[+ 共有の追加]** を選択します。
    3. **[共有の追加]** ブレードで、共有名を指定して共有の種類を選択します。
    4. Edge 共有をマウントするには、 **[Edge コンピューティングで共有を使用する]** チェック ボックスをオンにします。
    5. **[ストレージ アカウント]** 、 **[ストレージ サービス]** 、既存のユーザーを選択して、 **[作成]** を選択します。

        ![Edge 共有の追加](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > NFS 共有をコンピューティングにマウントするには、コンピューティング ネットワークを NFS 仮想 IP アドレスと同じサブネット上に構成する必要があります。 コンピューティング ネットワークを構成する方法の詳細については、[Azure Stack Edge Pro でのコンピューティング ネットワークの有効化](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)に関するページを参照してください。

    Edge 共有が作成されると、作成の成功に関する通知が表示されます。 共有の一覧は更新されているかもしれませんが、共有の作成が完了するまで待つ必要があります。

2. デバイスに Edge ローカル共有を追加するには、前のすべての手順を繰り返し、 **[Edge ローカル共有として構成]** のチェック ボックスをオンにします。 ローカル共有内のデータはデバイスに残ります。

    ![Edge ローカル共有の追加](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    ローカルの NFS 共有を作成した場合は、次のリモート同期 (rsync) コマンド オプションを使用して、ファイルを共有にコピーします。

    `rsync <source file path> < destination file path>`

    `rsync` コマンドの詳細については、[`Rsync`](https://www.computerhope.com/unix/rsync.htm) に関するドキュメントを参照してください。
 
3. **[クラウド ストレージ ゲートウェイ] > [共有]** に移動して、共有の更新された一覧を確認します。

    ![更新された共有の一覧](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>モジュールを追加する

カスタム モジュールまたはあらかじめ構築されたモジュールを追加できます。 デバイスには、事前に構築されたモジュールもカスタム モジュールも付属していません。 カスタム モジュールを作成する方法については、[Azure Stack Edge Pro デバイス用の C# モジュールの開発](azure-stack-edge-j-series-create-iot-edge-module.md)に関するページを参照してください。

このセクションでは、[Azure Stack Edge Pro 用の C# モジュールの開発](azure-stack-edge-j-series-create-iot-edge-module.md)に関するページでお客様が作成したカスタム モジュールを IoT Edge デバイスに追加します。 このカスタム モジュールによって、Edge デバイス上の Edge ローカル共有からファイルが受け取られ、デバイス上の Edge (クラウド) 共有にそれらが移動されます。 その後、クラウド共有から、そのクラウド共有に関連付けられた Azure ストレージ アカウントにファイルがプッシュされます。

モジュールを追加するには、次の手順を実行します。

1. **[IoT Edge] > [モジュール]** に移動します。 コマンド バーで、 **[+ モジュールの追加]** を選択します。 

2. **[モジュールの追加]** ブレードで、次の値を入力します。

    
    |フィールド  |値  |
    |---------|---------|
    |名前     | モジュールの一意の名前。 このモジュールは、お客様の Azure Stack Edge Pro に関連付けられている IoT Edge デバイスにデプロイできる Docker コンテナーです。        |
    |イメージの URI     | モジュールの対応するコンテナー イメージのイメージ URI。        |
    |資格情報が必要です     | チェック ボックスをオンにすると、一致する URL が含まれているモジュールの取得にユーザー名とパスワードが使用されます。        |
    |Input share (入力共有)     | 入力共有を選択します。 この例では、Edge ローカル共有が入力共有です。 ここで使用されるモジュールによって、Edge ローカル共有から Edge 共有にファイルが移動され、そこでクラウドにアップロードされます。        |
    |Output share (出力共有)     | 出力共有を選択します。 この例では、Edge 共有が出力共有です。        |
    |トリガーの種類     | **[ファイル]** または **[スケジュール]** から選択します。 ファイル トリガーは、入力共有へのファイルの書き込みなど、ファイル イベントが発生するたびに起動します。 スケジュールされたトリガーは、お客様によって定義されたスケジュールに基づいて起動します。         |
    |トリガー名     | トリガーの一意の名前。         |
    |環境変数| モジュールが実行される環境の定義に役立つオプション情報。   |

    ![モジュールの構成と追加](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. **[追加]** を選択します。 モジュールが追加されます。 **[IoT Edge] > [モジュール]** ページが更新され、モジュールがデプロイされたことが示されます。 自分が追加したモジュールのランタイムの状態は、 *[実行中]* になっているはずです。

    ![デプロイされたモジュール](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>データ変換を検証して転送する

最後の手順では、モジュールが実行され、想定どおりにデータが処理されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

モジュールが実行され、想定どおりにデータが処理されていることを確認するには、次の手順を実行します。


1. エクスプローラーで、先ほど作成した Edge ローカル共有と Edge 共有の両方に接続します。 手順を確認してください。 

    ![Edge ローカル共有と Edge クラウド共有に接続する](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. データをローカル共有に追加します。

    ![Edge ローカル共有にコピーされたファイル](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   そのデータはクラウド共有に移動されます。

    ![Edge クラウド共有に移動されたファイル](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   データは次に、クラウド共有からストレージ アカウントにプッシュされます。 データを表示するには、ポータルで Storage Explorer または Azure Storage を使用します。

    ![ストレージ アカウントのデータを確認する](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
検証プロセスが完了しました。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

お客様の Azure Stack Edge Pro デバイスを管理する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Stack Edge Pro を管理する](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
