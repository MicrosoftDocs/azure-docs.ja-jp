---
title: チュートリアル - Azure IoT Edge を使用して Azure Machine Learning をデバイスにデプロイする
description: このチュートリアルでは、Azure Machine Learning モデルを作成してから、それをモジュールとしてエッジ デバイスにデプロイします
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 292c82624103fc2eae46d8aecb5e85b2181e7938
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463053"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>チュートリアル:Azure Machine Learning を IoT Edge モジュールとして展開する (プレビュー)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Notebooks を使用して、機械学習モジュールを開発し、Azure IoT Edge を実行している Linux デバイスにデプロイします。
IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされたマシンの温度データに基づいてデバイスが失敗するタイミングを予測する、Azure Machine Learning モジュールを展開する方法について説明します。 IoT Edge 上の Azure Machine Learning の詳細については、[Azure Machine Learning に関するドキュメント](../machine-learning/how-to-deploy-and-where.md)を参照してください。

>[!NOTE]
>Azure IoT Edge 上の Azure Machine Learning モジュールはパブリック プレビュー段階にあります。

このチュートリアルで作成した Azure Machine Learning モジュールは、デバイスによって生成された環境データを読み取り、メッセージに異常か否かのラベル付けを行います。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Azure Machine Learning モジュールを作成する。
> * Azure コンテナー レジストリにモジュール コンテナーをプッシュする。
> * Azure Machine Learning モジュールを IoT Edge デバイスに展開する。
> * 生成されたデータを表示する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* クイックスタートを使用して、[Linux デバイス](quickstart-linux.md)または [Windows デバイス](quickstart.md)を設定できます。
* Azure Machine Learning モジュールでは、Windows コンテナーはサポートされません。
* Azure Machine Learning モジュールでは、ARM プロセッサはサポートされません。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure Machine Learning ワークスペース。 [Azure portal での Azure Machine Learning の使用の開始](../machine-learning/tutorial-1st-experiment-sdk-setup.md)に関するページの手順に従って作成し、その使用方法について学習します。
  * ワークスペース名、リソース グループ、およびサブスクリプション ID をメモしておきます。 これらの値はすべて、Azure Portal のワークスペースの概要で確認できます。 チュートリアルの後半でこれらの値を使用して、ワークスペース リソースに Azure Notebooks ファイルを接続します。

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning モジュールを作成してデプロイする

このセクションでは、トレーニング済みの機械学習モデル ファイルを Azure Machine Learning コンテナーに変換します。 Docker イメージに必要なすべてのコンポーネントは、[Azure IoT Edge 用 AI ツールキットの Git リポジトリ](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)にあります。 これらの手順に従って、そのリポジトリを Microsoft Azure Notebooks にアップロードしてコンテナーを作成し、それを Azure Container Registry にプッシュします。

1. Azure Notebooks プロジェクトに移動します。 [Azure portal](https://portal.azure.com) の Azure Machine Learning ワークスペースから、またはご自分の Azure アカウントを使用して [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) にサインインすることで、そこに移動できます。

2. **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** を選択します。

3. GitHub リポジトリ名として `Azure/ai-toolkit-iot-edge` を指定します。 プロジェクトをプライベートのままにしておく場合は、 **[パブリック]** ボックスをオフにします。 **[インポート]** を選択します。

4. インポートが完了したら、新しい **ai-toolkit-iot-edge** プロジェクトに移動して、 **[IoT Edge anomaly detection tutorial]\(IoT Edge 異常検出チュートリアル\)** フォルダーを開きます。

5. プロジェクトが実行されていることを確認します。 実行されていない場合は、 **[Run on Free Compute]\(無料のコンピューティングで実行\)** を選択します。

   ![無料のコンピューティングで実行](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. **aml_config/config.json** ファイルを開きます。

7. 構成ファイルを編集し、Azure サブスクリプション ID、サブスクリプション内のリソース グループ、ご利用の Azure Machine Learning ワークスペース名の値が含まれるようにします。 これらすべての値は、Azure のワークスペースの **[概要]** セクションから取得できます。

8. 構成ファイルを保存します。

9. **00-anomaly-detection-tutorial.ipynb** ファイルを開きます。

10. メッセージが表示されたら、 **[Python 3.6]** カーネル、 **[Set Kernel]\(カーネルの設定\)** の順に選択します。

11. コメントの指示に従って、ノートブックの最初のセルを編集します。 構成ファイルに追加したのと同じリソース グループ、サブスクリプション ID、およびワークスペース名を使用します。

12. ノートブックのセルを選んで **[実行]** を選択するか、`Shift + Enter` キーを押して、セルを実行します。

    >[!TIP]
    >異常検出チュートリアル ノートブックのセルの一部は省略可能です。IoT ハブなど、一部のユーザーが持っている、あるいはまだ持っていない可能性のあるリソースが作成されるためです。 最初のセルに既存のリソース情報を配置する場合、新しいリソースを作成するセルを実行すると、エラーが発生します。これは、Azure では重複するリソースが作成されないためです。 これは問題ありません。エラーを無視することも、これらの省略可能なセクションをすべてスキップすることもできます。

ノートブック内のすべての手順を完了することで、異常検出モデルをトレーニングし、それを Docker コンテナー イメージとして構築し、そのイメージを Azure Container Registry にプッシュしました。 これで、モデルをテストし、最後に IoT Edge デバイスにデプロイしました。

## <a name="view-container-repository"></a>コンテナー リポジトリを表示する

コンテナー イメージが正常に作成され、ご自身の機械学習環境に関連付けられている Azure コンテナー レジストリに格納されたことを確認します。 前のセクションで使用したノートブックでは自動的に IoT Edge デバイスにコンテナー イメージとレジストリ資格情報が提供されますが、後から自分で情報を見つけられるように、それらの格納先を知っておく必要があります。

1. [Azure portal](https://portal.azure.com) で、Machine Learning service ワークスペースに移動します。

2. **[概要]** セクションには、ワークスペースの詳細と、それに関連するリソースがリストされます。 **[レジストリ]** 値を選択します。これは、後ろに乱数が続くワークスペース名である必要があります。

3. コンテナー レジストリの **[サービス]** で **[リポジトリ]** を選択します。 前のセクションで実行したノートブックによって作成された、**tempanomalydetection** というリポジトリが表示されるはずです。

4. **[tempanomalydetection]** を選択します。 リポジトリに 1 つのタグがあることがわかります: **1**

   これでレジストリ名、リポジトリ名およびタグがわかったので、コンテナーの完全なイメージ パスがわかります。 イメージのパスは、 **\<registry_name\>.azurecr.io/tempanomalydetection:1** のようになります。 イメージ パスを使用して、IoT Edge デバイスにこのコンテナーをデプロイすることができます。

5. コンテナー レジストリの **[設定]** で **[アクセス キー]** を選択します。 **ログイン サーバー**、管理者ユーザーの **ユーザー名**、および **パスワード** を含む、多くのアクセス資格情報が表示されるはずです。

   これらの資格情報は、レジストリからコンテナー イメージをプルするアクセス許可を IoT Edge デバイスに付与するために、デプロイ マニフェストに含めることができます。

これで、Machine Learning コンテナー イメージの格納場所がわかりました。 次のセクションでは、お使いの IoT Edge デバイス上でモジュールとして実行されているコンテナーを確認する手順について説明します。

## <a name="view-the-generated-data"></a>生成されたデータを表示する

各 IoT Edge モジュールによって生成されているメッセージ、およびご自身の IoT ハブに配信されたメッセージを表示することができます。

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge デバイスのデータを表示する

IoT Edge デバイスでは、すべてのモジュールそれぞれから送信されているメッセージを表示できます。

`iotedge` コマンドを実行するには、管理者特権のアクセス許可に `sudo` の使用が必要になることがあります。 デバイスからサインアウトし、もう一度サインインすると、アクセス許可が自動的に更新されます。

1. IoT Edge デバイス上のすべてのモジュールを確認します。

   ```cmd/sh
   iotedge list
   ```

2. 特定のデバイスから送信されているメッセージを確認します。 前のコマンド出力のモジュール名を使用します。

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT ハブに届くデータを表示する

IoT ハブが受信する device-to-cloud メッセージは、[Visual Studio Code 用の Azure IoT Hub の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して表示できます。

次の手順は、IoT ハブに届く device-to-cloud メッセージを監視するように Visual Studio Code を設定する方法を示しています。

1. Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションで、 **[デバイス]** を展開して IoT デバイスの一覧を表示します。

2. IoT Edge デバイスの名前を右クリックし、 **[組み込みイベント エンドポイントの監視を開始します]** を選択します。

3. 5 秒ごとに tempSensor から送られるメッセージを確認します。 メッセージ本文には **anomaly** というプロパティが含まれています。このプロパティには、machinelearningmodule によって true または false の値が指定されます。 モジュールの実行に成功した場合、**AzureMLResponse** プロパティには "OK" という値が含まれます。

   ![メッセージ本文内の Azure Machine Learning の応答](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Machine Learning を使用する IoT Edge モジュールを展開しました。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Custom Vision サービスを使用して画像を分類する](tutorial-deploy-custom-vision.md)
