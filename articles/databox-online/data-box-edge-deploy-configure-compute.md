---
title: 'チュートリアル: Azure Data Box Edge のコンピューティングを使用してデータのフィルター処理、分析を行う| Microsoft Docs'
description: Edge でコンピューティング ロールを構成し、Azure への送信前にそれをデータの変換に使用する方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277307"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>チュートリアル:Azure Data Box Edge を使用してデータを変換する

このチュートリアルでは、お客様の Azure Data Box Edge デバイスでコンピューティング ロールを構成する方法について説明します。 コンピューティング ロールを構成すると、Data Box Edge は Azure への送信前にデータを変換できます。

この手順の所要時間はおおよそ 10 分から 15 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

 
## <a name="prerequisites"></a>前提条件

お客様の Data Box Edge デバイスでコンピューティング ロールを設定する前に、次のことを確認してください。

- [Azure Data Box Edge の接続、設定、アクティブ化](data-box-edge-deploy-connect-setup-activate.md)に関するページで説明されているとおり、お客様の Data Box Edge デバイスをアクティブ化したこと。


## <a name="configure-compute"></a>コンピューティングを構成する

Data Box Edge でコンピューティングを構成するために、IoT Hub リソースを作成します。

1. Azure portal で、自分の Data Box Edge リソースの [概要] に移動します。 右側のウィンドウの **[コンピューティング]** タイルで **[開始]** を選択します。

    ![コンピューティングの開始](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. **[Edge コンピューティングの構成]** タイルで、 **[コンピューティングの構成]** を選択します。
3. **[Edge コンピューティングの構成]** ブレードで、次を入力します。

   
    |フィールド  |値  |
    |---------|---------|
    |IoT Hub     | **[新規]** または **[既存]** を選択します。 <br> 既定では、IoT リソースの作成には Standard レベル (S1) が使用されます。 Free レベルの IoT リソースを使用するには、それを作成してから既存のリソースを選択します。 <br> いずれの場合も、IoT Hub リソースでは、Data Box Edge リソースによって使用されるのと同じサブスクリプションとリソース グループが使用されます。     |
    |名前     |自分の IoT Hub リソースの名前を入力します。         |

    ![コンピューティングの開始](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. **作成** を選択します。 IoT Hub リソースの作成には数分かかります。 IoT Hub リソースが作成された後、 **[コンピューティングの構成]** タイルが更新され、コンピューティングの構成が表示されます。 Edge コンピューティング ロールが構成されたことを確認するには、 **[コンピューティングの構成]** タイルの **[View Compute]\(コンピューティングの表示\)** を選択します。
    
    ![コンピューティングの開始](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Data Box Edge デバイスに IoT Hub が関連付けられる前に **[コンピューティングの構成]** ダイアログを閉じた場合、IoT Hub は作成されますが、コンピューティングの構成には表示されません。 
    
    Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。 IoT Edge ランタイムは、この IoT Edge デバイス上でも動作しています。 現時点では、お客様の IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。


## <a name="add-shares"></a>共有を追加する

このチュートリアルのシンプルなデプロイでは、2 つの共有が必要になります。1 つは Edge 共有で、もう 1 つは Edge ローカル共有です。

1. 次の手順を実行して、Edge 共有をデバイスに追加します。

    1. 自分の Data Box Edge リソースで、 **[Edge コンピューティング]、[開始]** の順に移動します。
    2. **[共有の追加]** タイルで、 **[追加]** を選択します。
    3. **[共有の追加]** ブレードで、共有名を指定して共有の種類を選択します。
    4. Edge 共有をマウントするには、 **[Edge コンピューティングで共有を使用する]** チェック ボックスをオンにします。
    5. **[ストレージ アカウント]** 、 **[ストレージ サービス]** 、既存のユーザーを選択して、 **[作成]** を選択します。

        ![Edge 共有の追加](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    ローカルの NFS 共有を作成した場合は、次のリモート同期 (rsync) コマンド オプションを使用して、ファイルを共有にコピーします。

    `rsync <source file path> < destination file path>`

    rsync コマンドの詳細については、[rsync に関するドキュメント](https://www.computerhope.com/unix/rsync.htm)を参照してください。

    Edge 共有が作成されると、作成の成功に関する通知が表示されます。 共有の一覧は更新されているかもしれませんが、共有の作成が完了するまで待つ必要があります。

2. 前の手順の内容をすべて繰り返し、 **[Edge ローカル共有として構成]** のチェック ボックスをオンにして、Edge デバイスに Edge ローカル共有を追加します。 ローカル共有内のデータはデバイスに残ります。

    ![Edge ローカル共有の追加](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. **[共有の追加]** を選択して、共有の更新された一覧を確認します。

    ![更新された共有の一覧](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>モジュールを追加する

カスタム モジュールまたはあらかじめ構築されたモジュールを追加できます。 この Edge デバイスにはカスタム モジュールがありません。 カスタム モジュールを作成する方法については、[Data Box Edge デバイス用の C# モジュールの開発](data-box-edge-create-iot-edge-module.md)に関するページを参照してください。

このセクションでは、[Data Box Edge 用の C# モジュールの開発](data-box-edge-create-iot-edge-module.md)に関するページでお客様が作成したカスタム モジュールを IoT Edge デバイスに追加します。 このカスタム モジュールによって、Edge デバイス上の Edge ローカル共有からファイルが受け取られ、デバイス上の Edge (クラウド) 共有にそれらが移動されます。 その後、クラウド共有から、そのクラウド共有に関連付けられた Azure ストレージ アカウントにファイルがプッシュされます。

1. **[Edge コンピューティング]、[開始]** の順に移動します。 **[モジュールの追加]** タイルで、シナリオの種類として **[シンプル]** を選択します。 **[追加]** を選択します。
2. **[Configure and add module]\(モジュールの構成と追加\)** ブレードで、以下の値を入力します。

    
    |フィールド  |値  |
    |---------|---------|
    |名前     | モジュールの一意の名前。 このモジュールは、お客様の Data Box Edge に関連付けられている IoT Edge デバイスにデプロイできる Docker コンテナーです。        |
    |イメージの URI     | モジュールの対応するコンテナー イメージのイメージ URI。        |
    |資格情報が必要です     | チェック ボックスをオンにすると、一致する URL が含まれているモジュールの取得にユーザー名とパスワードが使用されます。        |
    |Input share (入力共有)     | 入力共有を選択します。 この例では、Edge ローカル共有が入力共有です。 ここで使用されるモジュールによって、Edge ローカル共有から Edge 共有にファイルが移動され、そこでクラウドにアップロードされます。        |
    |Output share (出力共有)     | 出力共有を選択します。 この例では、Edge 共有が出力共有です。        |
    |トリガーの種類     | **[ファイル]** または **[スケジュール]** から選択します。 ファイル トリガーは、入力共有へのファイルの書き込みなど、ファイル イベントが発生するたびに起動します。 スケジュールされたトリガーは、お客様によって定義されたスケジュールに基づいて起動します。         |
    |トリガー名     | トリガーの一意の名前。         |
    |環境変数| モジュールが実行される環境の定義に役立つオプション情報。   |

    ![モジュールの構成と追加](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. **[追加]** を選択します。 モジュールが追加されます。 **[モジュールの追加]** タイルが更新され、モジュールがデプロイされたことが示されます。 

    ![デプロイされたモジュール](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>データ変換を検証して転送する

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

モジュールが実行中であることを確認するには、以下を実行します。

1. **[モジュールの追加]** タイルを選択します。 これにより、 **[モジュール]** ブレードが開きます。 モジュールの一覧で、自分がデプロイしたモジュールを特定します。 自分が追加したモジュールのランタイムの状態は、 *[実行中]* になっているはずです。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  エクスプローラーで、先ほど作成した Edge ローカル共有と Edge 共有の両方に接続します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  データをローカル共有に追加します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    そのデータはクラウド共有に移動されます。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    データは次に、クラウド共有からストレージ アカウントにプッシュされます。 データを確認するには、Storage Explorer に移動します。

    ![データ変換を検証する](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
検証プロセスが完了しました。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

お客様の Data Box Edge デバイスを管理する方法を学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Edge を管理する](data-box-edge-manage-access-power-connectivity-mode.md)
