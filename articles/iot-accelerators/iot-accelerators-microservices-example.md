---
title: マイクロサービスを変更して再展開する - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視のマイクロサービスを変更して再展開する方法を示します
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004112"
---
# <a name="customize-and-redeploy-a-microservice"></a>マイクロサービスをカスタマイズして再展開する

このチュートリアルでは、リモート監視ソリューションの[マイクロサービス](https://azure.com/microservices)を編集し、マイクロサービスのイメージをビルドし、Docker Hub にイメージを展開してから、リモート監視ソリューションでそれを使う方法を示します。 この概念を説明するため、このチュートリアルでは、マイクロサービス API を呼び出して状態メッセージを "Alive and Well" から "New Edits Made Here!" に変更する基本的なシナリオを使います。

リモート監視ソリューションは、Docker Hub からプルされた Docker イメージを使ってビルドされるマイクロサービスを使います。 

このチュートリアルでは、以下の内容を学習します。

>[!div class="checklist"]
> * リモート監視ソリューションでマイクロサービスを編集してビルドします
> * Docker イメージをビルドします
> * Docker イメージを Docker Hub にプッシュします
> * 新しい Docker イメージをプルします
> * 変更を視覚化します 

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、次が必要です。

>[!div class="checklist"]
> * [リモート監視ソリューション アクセラレータをローカルでデプロイする](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Docker アカウント](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - API の応答を表示するために必要です

## <a name="call-the-api-and-view-response-status"></a>API を呼び出して応答の状態を表示する

ここでは、既定の IoT ハブ マネージャー マイクロサービス API を呼び出します。 API は、後でマイクロサービスをカスタマイズすることにより変更する状態メッセージを返します。

1. お使いのコンピューターでリモート監視ソリューションがローカルに実行していることを確認してください。
2. ダウンロードした Postman を探して開きます。
3. Postman で、[GET] に「`http://localhost:8080/iothubmanager/v1/status`」と入力します。
4. 返される値では、"Status":"OK:Alive and Well" と表示されます。

    ![Postman に表示された Alive and Well メッセージ](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>状態を変更して、イメージをビルドします。

Iot Hub Manager マイクロサービスの状態メッセージを "New Edits Made Here!" に変更し、 この新しい状態を含む Docker イメージをリビルドします。 ここで問題が発生する場合は、「[トラブルシューティング](#Troubleshoot)」セクションをご覧ください。

1. ターミナルが開いていることを確認し、リモート監視ソリューションを複製したディレクトリに移動します。 
1. ディレクトリを、"azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services" に変更します。
1. 好みのテキスト エディターまたは IDE で、StatusService.cs を開きます。 
1. 次のコードを見つけます。

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    これを次のコードに変更して、保存します。

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. ターミナルに戻り、今度は "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker" ディレクトリに変更します。
6. 新しい Docker イメージをビルドするには、次のように入力します。

    ```sh
    sh build
    ```
    
    または、Windows では:
    
    ```cmd
    ./build.cmd
    ```

7. 新しいイメージが正常に作成されたことを確認するには、次のように入力します。

    ```cmd/sh
    docker images 
    ```

リポジトリは、"azureiotpcs/iothub-manager-dotnet" にする必要があります。

![成功した Docker イメージ](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>イメージにタグを付けてプッシュする
新しい Docker イメージを Docker Hub にプッシュする前に、イメージにタグを付ける必要があります。 ここで問題が発生する場合は、「[トラブルシューティング](#Troubleshoot)」セクションをご覧ください。

1. 次のように入力して、作成した Docker イメージのイメージ ID を調べます。

    ```cmd/sh
    docker images
    ```

2. イメージに "testing" というタグを付けるには、次のように入力します。

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. 新しくタグを付けたイメージを Docker Hub にプッシュするには、次のように入力します。

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. インターネット ブラウザーを開き、[Docker Hub](https://hub.docker.com/) に移動して、サインインします。
5. 新しくプッシュした Docker イメージが Docker Hub に表示されます。
![Docker Hub の Docker イメージ](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>リモート監視ソリューションを更新する
次に、ローカルの docker-compose.yml を更新し、新しい Docker イメージを Docker Hub からプルする必要があります。 ここで問題が発生する場合は、「[トラブルシューティング](#Troubleshoot)」セクションをご覧ください。

1. ターミナルに戻り、"azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local" ディレクトリに変更します。
2. 好みのテキスト エディターまたは IDE で、docker-compose.yml を開きます。
3. 次のコードを見つけます。

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    これを次の図のように変更して、変更します。

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>新しい応答状態を表示する
最後に、リモート監視ソリューションのローカル インスタンスを再展開し、Postman で新しい状態応答を表示します。

1. ターミナルに戻り、"azure-iot-pcs-remote-monitoring-dotnet/scripts/local" ディレクトリに変更します。
2. ターミナルに次のコマンドを入力して、リモート監視ソリューションのローカル インスタンスを起動します。

    ```cmd/sh
    docker-compose up
    ```

3. ダウンロードした Postman を探して開きます。
4. Postman で、[GET] に「`http://localhost:8080/iothubmanager/v1/status`」という要求を入力します。 ここで、"Status":"OK:New Edits Made Here!" と表示されます。

![Postman に表示された New Edits Made Here メッセージ](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>トラブルシューティング

問題が発生する場合は、ローカル コンピューター上の Docker イメージとコンテナーを削除してみてください。

1. すべてのコンテナーを削除するには、まず実行中のすべてのコンテナーを停止する必要があります。 ターミナルを開き、次のように入力します。

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. すべてのイメージを削除するには、ターミナルを開いて次のように入力します。 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. コンピューター上にコンテナーがあるかどうかは、次のように入力して確認できます。

    ```cmd/sh
    docker ps -aq 
    ```

    すべてのコンテナーが正常に削除されている場合は、何も表示されません。

4. コンピューター上にイメージがあるかどうかは、次のように入力して確認できます。

    ```cmd/sh
    docker images
    ```

    すべてのイメージが正常に削除されている場合は、何も表示されません。

## <a name="next-steps"></a>次の手順

このチュートリアルで説明する内容は次のとおりです。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * リモート監視ソリューションでマイクロサービスを編集してビルドします
> * Docker イメージをビルドします
> * Docker イメージを Docker Hub にプッシュします
> * 新しい Docker イメージをプルします
> * 変更を視覚化します 

次に、[リモート監視ソリューションのデバイス シミュレーター マイクロサービスをカスタマイズ](iot-accelerators-microservices-example.md)してみてください

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

