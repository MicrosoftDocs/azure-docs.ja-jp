---
title: 'チュートリアル: Azure Stack Edge Pro のコンピューティングを使用して詳細なデプロイのためのデータのフィルター処理と分析を行う | Microsoft Docs'
description: Azure Stack Edge Pro 上にコンピューティング ロールを構成し、それを使用して、Azure に送信する前のデータを詳細なデプロイ フロー用に変換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 8946dfca9a416009effb45cad1e81348dd900f98
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97968301"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>チュートリアル:詳細なデプロイ フローのために Azure Stack Edge Pro でデータを変換する

このチュートリアルでは、Azure Stack Edge Pro デバイス上に詳細なデプロイ フロー用のコンピューティング ロールを構成する方法について説明します。 コンピューティング ロールを構成すると、Azure に送信する前に Azure Stack Edge Pro でデータを変換できるようになります。

コンピューティングは、デバイスでのシンプルまたは詳細なデプロイ フロー用に構成できます。

| 条件 | 単純なデプロイ                                | 詳細なデプロイ                   |
|------------------|--------------------------------------------------|---------------------------------------|
| 対象者     | IT 管理者                                | 開発者                            |
| Type             | Azure Stack Edge サービスを使ってモジュールをデプロイする      | IoT Hub サービスを使ってモジュールをデプロイする |
| デプロイされるモジュール | Single                                           | チェーンされたモジュールまたは複数のモジュール           |


この手順の所要時間は約 20 分から 30 分です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * トリガーの追加
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

 
## <a name="prerequisites"></a>前提条件

お客様の Azure Stack Edge Pro デバイスでコンピューティング ロールを設定する前に、次のことを確認してください。

- [Azure Stack Edge Pro の接続、設定、アクティブ化](azure-stack-edge-deploy-connect-setup-activate.md)に関するページで説明されているとおり、お客様の Azure Stack Edge Pro デバイスをアクティブ化していること。


## <a name="configure-compute"></a>コンピューティングを構成する

Azure Stack Edge Pro でコンピューティングを構成するために、IoT Hub リソースを作成します。

1. Azure portal で、Azure Stack Edge リソースの **[概要]** に移動します。 右ペインで **[IoT Edge]** タイルを選択します。

    ![コンピューティングの開始](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. **[IoT Edge サービスを有効にする]** タイルの **[追加]** を選択します。 この操作によって IoT Edge サービスが有効になり、IoT Edge モジュールをデバイス上にローカルにデプロイできるようになります。

    ![コンピューティングの開始 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. **[IoT Edge サービスの作成]** で、次の情報を入力します。

   
    |フィールド  |値  |
    |---------|---------|
    |サブスクリプション     |IoT Hub リソースのサブスクリプションを選択します。 Azure Stack Edge リソースで使用されているものと同じサブスクリプションを選択できます。        |
    |Resource group     |IoT Hub リソースのリソース グループの名前を入力します。 Azure Stack Edge リソースで使用されているものと同じリソース グループを選択できます。         |
    |IoT Hub     | **[新規]** または **[既存]** を選択します。 <br> 既定では、IoT リソースの作成には Standard レベル (S1) が使用されます。 Free レベルの IoT リソースを使用するには、それを作成してから既存のリソースを選択します。      |
    |名前     |既定値をそのまま使用するか、自分の IoT Hub リソースの名前を入力します。         |

    ![コンピューティングの開始 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. **[確認および作成]** を選択します。 IoT Hub リソースの作成には数分かかります。 IoT Hub リソースの作成後、 **[概要]** が更新されて、IoT Edge サービスが実行中であることが示されます。 

    Edge デバイスで IoT Edge サービスが構成されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。 IoT Edge ランタイムは、この IoT Edge デバイス上でも動作しています。 現時点では、お客様の IoT Edge デバイスに対して使用できるのは Linux プラットフォームのみです。

    Edge コンピューティング ロールが構成されたことを確認するには、 **[IoT Edge サービス] > [プロパティ]** を選択して、IoT デバイスと IoT Edge デバイスを表示します。 

    ![コンピューティングの開始 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>共有を追加する

このチュートリアルの詳細なデプロイでは、2 つの共有が必要になります。1 つは Edge 共有で、もう 1 つは Edge ローカル共有です。

1. 次の手順を実行して、Edge 共有をデバイスに追加します。

    1. ご自分の Azure Stack Edge リソースで、 **[IoT Edge] > [共有]** に移動します。
    2. **[共有]** ページで、コマンド バーの **[+ 共有の追加]** を選択します。
    3. **[共有の追加]** ブレードで、共有名を指定して共有の種類を選択します。
    4. Edge 共有をマウントするには、 **[Edge コンピューティングで共有を使用する]** チェック ボックスをオンにします。
    5. **[ストレージ アカウント]** 、 **[ストレージ サービス]** 、既存のユーザーを選択して、 **[作成]** を選択します。

        ![Edge 共有の追加](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Edge 共有が作成された後、作成成功通知を受け取ります。 共有の一覧が更新されて、新しい共有が反映されます。

2. 前の手順の内容をすべて繰り返し、 **[Edge ローカル共有として構成]** のチェック ボックスをオンにして、Edge デバイスに Edge ローカル共有を追加します。 ローカル共有内のデータはデバイスに残ります。

    ![Edge ローカル共有の追加](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. **[共有]** ブレードに、更新された共有の一覧が表示されます。

    ![更新された共有の一覧](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. 新しく作成されたローカル共有のプロパティを表示するには、一覧から共有を選択します。 **[Edge コンピューティング モジュールのローカル マウント ポイント]** ボックスで、この共有に対応する値をコピーします。

    このローカル マウント ポイントはモジュールのデプロイ時に使用します。

    ![[Edge コンピューティング モジュールのローカル マウント ポイント] ボックス](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. 作成した Edge 共有のプロパティを表示するには、一覧から共有を選択します。 **[Edge コンピューティング モジュールのローカル マウント ポイント]** ボックスで、この共有に対応する値をコピーします。

    このローカル マウント ポイントはモジュールのデプロイ時に使用します。

    ![カスタム モジュールを追加する](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>トリガーの追加

1. Azure Stack Edge リソースに移動し、 **[IoT Edge] > [トリガー]** に移動します。 **[+ トリガーの追加]** を選択します。

    ![トリガーの追加](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. **[トリガーの追加]** ブレードで、次の値を入力します。

    |フィールド  |値  |
    |---------|---------|
    |トリガー名     | トリガーの一意の名前。         |
    |トリガーの種類     | **[ファイル]** トリガーを選択します。 ファイル トリガーは、入力共有へのファイルの書き込みなど、ファイル イベントが発生するたびに起動します。 一方、スケジュール済みのトリガーは、お客様によって定義されたスケジュールに基づいて起動します。 この例では、ファイル トリガーが必要です。    |
    |Input share (入力共有)     | 入力共有を選択します。 この例では、Edge ローカル共有が入力共有です。 ここで使用されるモジュールによって、Edge ローカル共有から Edge 共有にファイルが移動され、そこでクラウドにアップロードされます。        |

    ![トリガーを追加する 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. トリガーが作成された後、通知を受け取ります。 トリガーの一覧が更新されて、新しく作成されたトリガーが表示されます。 作成したトリガーを選択します。

    ![トリガーを追加する 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. サンプルのルートをコピーして保存します。 このサンプルのルートを変更して後で IoT Hub で使います。

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![トリガーを追加する 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>モジュールを追加する

この Edge デバイスにはカスタム モジュールがありません。 カスタム モジュールまたはあらかじめ構築されたモジュールを追加できます。 カスタム モジュールを作成する方法については、[Azure Stack Edge Pro デバイス用の C# モジュールの開発](azure-stack-edge-create-iot-edge-module.md)に関するページを参照してください。

このセクションでは、[Azure Stack Edge Pro 用の C# モジュールの開発](azure-stack-edge-create-iot-edge-module.md)に関するページでお客様が作成したカスタム モジュールを IoT Edge デバイスに追加します。 このカスタム モジュールによって、Edge デバイス上の Edge ローカル共有からファイルが受け取られ、デバイス上の Edge (クラウド) 共有にそれらが移動されます。 その後、クラウド共有から、そのクラウド共有に関連付けられた Azure ストレージ アカウントにファイルがプッシュされます。

1. Azure Stack Edge リソースに移動し、 **[IoT Edge] > [概要]** に移動します。 **[モジュール]** タイルの **[Go to Azure IoT Hub]\(Azure IoT Hub に移動\)** を選択します。

    ![詳細なデプロイを選択する](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. IoT Hub リソース内で **[IoT Edge デバイス]** に移動した後、自分の IoT Edge デバイスを選択します。

    ![IoT Hub で IoT Edge デバイスに移動する](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. **[デバイスの詳細]** で、 **[モジュールの設定]** を選択します。

    ![[モジュールの設定] リンク](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. **[モジュールの追加]** で以下を実行します。

    1. カスタム モジュールのコンテナー レジストリの設定で、名前、アドレス、ユーザー名、パスワードを入力します。
    名前、アドレス、および一覧に示された資格情報は、一致する URL を使用してモジュールを取得するために使用されます。 このモジュールをデプロイするには、 **[Deployment modules]\(デプロイ モジュール)** で **[IoT Edge module]\(IoT Edge モジュール)** を選択します。 この IoT Edge モジュールは、お客様の Azure Stack Edge Pro デバイスに関連付けられている IoT Edge デバイスにデプロイできる Docker コンテナーです。

        ![[モジュールの設定] ページ](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. IoT Edge カスタム モジュールの設定を指定します。 次の値を入力します。
     
        |フィールド  |値  |
        |---------|---------|
        |名前     | モジュールの一意の名前。 このモジュールは、お客様の Azure Stack Edge Pro に関連付けられている IoT Edge デバイスにデプロイできる Docker コンテナーです。        |
        |イメージの URI     | モジュールの対応するコンテナー イメージのイメージ URI。        |
        |資格情報が必要です     | チェック ボックスをオンにすると、一致する URL が含まれているモジュールの取得にユーザー名とパスワードが使用されます。        |
    
        **[コンテナーの作成オプション]** ボックスに、Edge 共有と Edge ローカル共有に関する前の手順でコピーした Edge モジュールに対するローカル マウント ポイントを入力します。

        > [!IMPORTANT]
        > ここで使用するパスはコンテナーにマウントされるので、コンテナーの機能で必要なものと一致する必要があります。 [カスタム モジュールの作成](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code)に関する記事に従っている場合、そのモジュール内で指定されているコードではコピーされたパスが必要です。 これらのパスを変更しないでください。
    
        **[コンテナーの作成オプション]** ボックスで、次のサンプルを貼り付けることができます。
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        ご自分のモジュールに使用される環境変数を指定します。 環境変数では、ご自分のモジュールが実行される環境の定義に役立つオプション情報を指定します。

        ![[コンテナーの作成オプション] ボックス](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. 必要に応じて、Edge ランタイムの詳細設定を構成し、 **[次へ]** をクリックします。

        ![カスタム モジュールを追加する 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. **[ルートの指定]** で、モジュール間のルートを設定します。  
   
   ![ルートの指定](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    *route* は、前にコピーした次のルート文字列に置き換えることができます。 この例では、クラウド共有にデータをプッシュするローカル共有の名前を入力します。 `modulename` は、モジュールの名前に置き換えます。 **[次へ]** を選択します。
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![[ルートの指定] セクション](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. **[Review deployment]\(デプロイの確認\)** ですべての設定を確認してから **[送信]** を選択し、デプロイのためにモジュールを送信します。

   ![[モジュールの設定] ページ 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    このアクションにより、モジュールのデプロイが始まります。 デプロイが完了すると、モジュールの **[ランタイムの状態]** は **[実行中]** になります。

    ![カスタム モジュールを追加する 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>データ変換を検証して転送する

最後の手順では、モジュールが接続され、想定どおりに実行されていることを確認します。 モジュールのランタイムの状態は、IoT Hub リソース内のお客様の IoT Edge デバイスに対して実行中である必要があります。

データの変換を検証して Azure に転送するには、次の手順のようにします。
 
1. エクスプローラーで、先ほど作成した Edge ローカル共有と Edge 共有の両方に接続します。

   ![データ変換を検証する](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. データをローカル共有に追加します。

   ![データ変換を検証する 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    そのデータはクラウド共有に移動されます。

    ![データ変換を検証する 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    データは次に、クラウド共有からストレージ アカウントにプッシュされます。 データを表示するには、ストレージ アカウントに移動してから、 **[Storage Explorer]** を選択します。 ストレージ アカウントにアップロードされたデータを見ることができます。

    ![データ変換を検証する 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
検証プロセスが完了しました。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * コンピューティングを構成する
> * 共有を追加する
> * トリガーの追加
> * コンピューティング モジュールを追加する
> * データ変換を検証して転送する

お客様の Azure Stack Edge Pro デバイスを管理する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [ローカル Web UI を使用して Azure Stack Edge Pro を管理する](azure-stack-edge-manage-access-power-connectivity-mode.md)
