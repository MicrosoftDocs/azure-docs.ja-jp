---
title: Azure Machine Learning をデバイスにデプロイする - Azure IoT Edge | Microsoft Docs
description: このチュートリアルでは、Azure Machine Learning モデルを作成してから、それをモジュールとしてエッジ デバイスにデプロイします
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 985f1f73fbfc8c75df8393615fca32f5d1c08b9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078314"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>チュートリアル:Azure Machine Learning を IoT Edge モジュールとして展開する (プレビュー)

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされたマシンの温度データに基づいてデバイスが失敗するタイミングを予測する、Azure Machine Learning モジュールを展開する方法について説明します。 IoT Edge 上の Azure Machine Learning service の詳細については、[Azure Machine Learning に関するドキュメント](../machine-learning/service/how-to-deploy-to-iot.md)を参照してください。

このチュートリアルで作成した Azure Machine Learning モジュールは、デバイスによって生成された環境データを読み取り、メッセージに異常か否かのラベル付けを行います。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Machine Learning モジュールを作成する
> * Azure コンテナー レジストリにモジュール コンテナーをプッシュする
> * Azure Machine Learning モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する

>[!NOTE]
>Azure IoT Edge 上の Azure Machine Learning モジュールはパブリック プレビュー段階にあります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) 用のクイック スタートに記載された手順に従うことで、Azure 仮想マシンを IoT Edge デバイスとして使用できます。
* Azure Machine Learning モジュールでは、Windows コンテナーはサポートされません。
* Azure Machine Learning モジュールでは、ARM プロセッサはサポートされません。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure Machine Learning ワークスペース。 [Azure portal での Azure Machine Learning の使用の開始](../machine-learning/service/quickstart-get-started.md)に関するページの手順に従って作成し、その使用方法について学習します。
   * ワークスペース名、リソース グループ、およびサブスクリプション ID をメモしておきます。 これらの値はすべて、Azure portal のワークスペースの概要で確認できます。 チュートリアルの後半でこれらの値を使用して、ワークスペース リソースに Azure ノートブックを接続します。 


### <a name="disable-process-identification"></a>プロセス ID を無効にする

>[!NOTE]
>
> プレビュー段階の Azure Machine Learning では、IoT Edge で既定で有効になっているプロセス ID セキュリティ機能はサポートされていません。
> これを無効にする手順は次のとおりです。 ただし、これは、運用環境での使用には適していません。 これらの手順は Linux デバイスでのみ必要になります。 

IoT Edge デバイス上でプロセス ID を無効にするには、IoT Edge デーモン構成の **connect** セクションで、**workload_uri** と **management_uri** の IP アドレスとポートを指定する必要があります。

最初に、IP アドレスを取得します。 コマンド ラインで「`ifconfig`」と入力し、**docker0** インターフェイスの IP アドレスをコピーします。

IoT Edge デーモン構成ファイルを編集します。

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

構成の **connect** セクションをお使いの IP アドレスで更新します。 例: 
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

構成の **listen** セクションに同じアドレスを入力します。 例: 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

構成ファイルを保存して閉じます｡

management_uri アドレスで環境変数 IOTEDGE_HOST を作成します (永続的に設定するには、それを `/etc/environment` に追加します)。 例: 

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```

IoT Edge サービスを再起動して、変更を反映させます。

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning モジュールを作成してデプロイする

このセクションでは、トレーニング済みの機械学習モデル ファイルを Azure Machine Learning service コンテナーに変換します。 Docker イメージに必要なすべてのコンポーネントは、[Azure IoT Edge 用 AI ツールキットの Git リポジトリ](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)にあります。 以下の手順に従って、そのリポジトリを Microsoft Azure Notebooks にアップロードしてコンテナーを作成し、それを Azure Container Registry にプッシュします。


1. Azure Notebooks プロジェクトに移動します。 [Azure portal](https://portal.azure.com) の Azure Machine Learning service ワークスペースから、または Azure アカウントで [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) にサインインすることでそれらを取得できます。

2. **[Upload GitHub Repo]\(GitHub リポジトリのアップロード\)** を選択します。

3. GitHub リポジトリ名として `Azure/ai-toolkit-iot-edge` を指定します。 プロジェクトをプライベートのままにしておく場合は、**[パブリック]** ボックスをオフにします。 **[インポート]** を選択します。 

4. インポートが完了したら、新しい **ai-toolkit-iot-edge** プロジェクトに移動して、**[IoT Edge anomaly detection tutorial]\(IoT Edge 異常検出チュートリアル\)** フォルダーを開きます。 

5. プロジェクトが実行されていることを確認します。 実行されていない場合は、**[Run on Free Compute]\(無料のコンピューティングで実行\)** を選択します。

   ![無料のコンピューティングで実行](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. **aml_config/config.json** ファイルを開きます。

7. 構成ファイルを編集し、Azure サブスクリプション ID、サブスクリプション内のリソース グループ、ご利用の Azure Machine Learning service ワークスペース名の値が含まれるようにします。 これらすべての値は、Azure のワークスペースの **[概要]** セクションから取得できます。 

8. 構成ファイルを保存します。

9. **00-anomaly-detection-tutorial.ipynb** ファイルを開きます。

10. メッセージが表示されたら、**[Python 3.6]** カーネル、**[Set Kernel]\(カーネルの設定\)** の順に選択します。

11. コメントの指示に従って、ノートブックの最初のセルを編集します。 構成ファイルに追加したのと同じリソース グループ、サブスクリプション ID、およびワークスペース名を使用します。

12. ノートブックのセルを選んで **[実行]** を選択するか、`Shift + Enter` キーを押して、セルを実行します。

    >[!TIP]
    >異常検出チュートリアル ノートブックのセルの一部は省略可能です。IoT ハブなど、一部のユーザーが持っている、あるいはまだ持っていない可能性のあるリソースが作成されるためです。 最初のセルに既存のリソース情報を配置する場合、新しいリソースを作成するセルを実行すると、エラーが発生します。これは、Azure では重複するリソースが作成されないためです。 これは問題ありません。エラーを無視することも、これらの省略可能なセクションをすべてスキップすることもできます。 

ノートブックのすべての手順が完了すると、異常検出モデルをトレーニングし、それを Docker コンテナー イメージとして構築し、そのイメージを Azure Container Registry にプッシュしたことになります。 これで、モデルをテストし、最後に IoT Edge デバイスにデプロイしました。 

## <a name="view-container-repository"></a>コンテナー リポジトリを表示する

コンテナー イメージが正常に作成され、ご自身の機械学習環境に関連付けられている Azure コンテナー レジストリに格納されたことを確認します。 前のセクションで使用したノートブックでは自動的に IoT Edge デバイスにコンテナー イメージとレジストリ資格証明が提供されますが、後から自分で情報を見つけられるように、それらの格納先を知っておく必要があります。 

1. [Azure portal](https://portal.azure.com) で、Machine Learning service ワークスペースに移動します。 

2. **[概要]** セクションには、ワークスペースの詳細と、それに関連するリソースがリストされます。 **[レジストリ]** 値を選択します。これは、後ろに乱数が続くワークスペース名である必要があります。 

3. コンテナー レジストリで、**[リポジトリ]** を選択します。 前のセクションで実行したノートブックによって作成された、**tempanomalydetection** というリポジトリが表示されるはずです。 

4. **[tempanomalydetection]** を選択します。 リポジトリに 1 つのタグ **1** があることがわかります。 

   これで、レジストリ名前、リポジトリ名、タグ、およびコンテナーの完全なイメージ パスがわかりました。 イメージ パスは **\<registry_name\>.azurecr.io/tempanomalydetection:1** のようになります。 イメージ パスを使用して、IoT Edge デバイスにこのコンテナーをデプロイすることができます。 

5. コンテナー レジストリで、**[アクセス キー]** を選択します。 **ログイン サーバー**、管理者ユーザーの**ユーザー名**、および**パスワード**を含む、多くのアクセス資格情報が表示されるはずです。

   これらの資格情報は、レジストリからコンテナー イメージをプルするアクセス許可を IoT Edge デバイスに付与するために、配置マニフェストに含めることができます。 

これで、Machine Learning コンテナー イメージの格納場所がわかりました。 次のセクションでは、IoT Edge デバイスにデプロイされたモジュールとして、どのように実行されるかを確認する手順について説明します。 

## <a name="view-generated-data"></a>生成されたデータを表示する

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

[Visual Studio Code 用の Azure IoT Hub Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (以前の Azure IoT Toolkit の拡張機能) を使用して、IoT ハブが受け取る device-to-cloud メッセージを表示できます。

次の手順は、IoT ハブに届く device-to-cloud メッセージを監視するように Visual Studio Code を設定する方法を示しています。

1. Visual Studio Code で、**[IoT Hub Devices]\(IoT Hub デバイス\)** を選択します。

2. **[...]** を選択した後、メニューで **[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\)** を選択します。

   ![IoT Hub 接続文字列を設定する](./media/tutorial-deploy-machine-learning/set-connection.png)

3. ページの上部に表示されるテキスト ボックスに、IoT Hub の iothubowner の接続文字列を入力します。 IoT Hub デバイスの一覧に、お使いの IoT Edge デバイスが表示されます。

4. もう一度 **[...]** を選択した後で、**[Start monitoring D2C message]\(D2C メッセージの監視を開始する\)** を選択します。

5. 5 秒ごとに tempSensor から送られるメッセージを確認します。 メッセージ本文には、machinelearningmodule によって true または false 値が提供される **anomaly** というプロパティが含まれています。 **AzureMLResponse** プロパティには、モジュールの実行に成功した場合、"OK" という値が含まれます。

   ![メッセージ本文内の Azure Machine Learning service の応答](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Machine Learning を使用する IoT Edge モジュールを展開しました。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Custom Vision サービスを使用して画像を分類する](tutorial-deploy-custom-vision.md)

