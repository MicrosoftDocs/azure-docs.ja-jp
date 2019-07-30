---
title: Docker Compose コンテナーのレシピ
titleSuffix: Azure Cognitive Services
description: 複数の Cognitive Services コンテナーをデプロイする方法について説明します。 この手順では、Docker Compose を使用して複数の Docker コンテナー イメージを調整する方法について説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445791"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Docker Compose でプライベート ネットワークに複数のコンテナーを使用する

複数の Cognitive Services コンテナーをデプロイする方法について説明します。 この手順では、Docker Compose を使用して複数の Docker コンテナー イメージを調整する方法について説明します。

> [Docker Compose](https://docs.docker.com/compose/) は、マルチコンテナー Docker アプリケーションを定義して実行するためのツールです。 Compose では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、1 つのコマンドを使用して、構成からすべてのサービスを作成して開始します。

状況によっては、1 つのホスト コンピューター上で複数のコンテナー イメージを調整する方法が適している場合があります。 この記事では、テキスト認識サービスと Form Recognizer サービスの両方を取り上げます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。

* Azure サブスクリプションを使用してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* [Docker エンジン](https://www.docker.com/products/docker-engine)。Docker CLI がコンソール ウィンドウで動作することを確認します。
* 適切な価格レベルの Azure リソース。 すべての価格レベルでこのコンテナーを使用するわけではありません。
  * F0 または Standard 価格レベルのみの **Computer Vision** リソース。
  * F0 または Standard 価格レベルのみの **Form Recognizer** リソース。
  * S0 価格レベルの **Cognitive Services** リソース。

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、[Cognitive Services Speech コンテナー要求フォーム](https://aka.ms/speechcontainerspreview/)に記入して送信します。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose ファイル

YAML ファイルには、デプロイされるすべてのサービスが定義されています。 これらのサービスは `DockerFile` または既存のコンテナー イメージのいずれかに依存しています。ここでは 2 つのプレビュー イメージを使用します。 次の YAML ファイルをコピーして貼り付け、*docker-compose.yaml* という名前で保存します。 次の _docker-compose.yml_ ファイルに適切な _apikey_、_billing_、および _endpoint URI_ の値を指定します。

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> ホスト マシン上に、`volumes` ノード以下に指定されているディレクトリを作成します。 これが必要な理由は、ボリューム バインディングを使用してイメージをマウントする前にディレクトリが存在する必要があるためです。

## <a name="start-the-configured-docker-compose-services"></a>構成された Docker Compose サービスを開始する

Docker Compose ファイルを使用すると、定義されたすべてのサービスのライフサイクル (サービスの開始/停止と再構築、サービス状態の表示、ストリーミングのログ記録) を管理できます。 プロジェクト ディレクトリ (*docker-compose.yaml* ファイルがある場所) からコマンドライン インターフェイスを開きます。

> [!NOTE]
> エラーを回避するために、ホスト マシンと **Docker エンジン**がドライブを適切に共有していることを確認します。 たとえば、*e:\publicpreview* が *docker-compose.yaml* のディレクトリとして使用されている場合、"*E ドライブ*" を Docker と共有します。

コマンドライン インターフェイスから次のコマンドを実行して、*docker-compose.yaml* に定義されているすべてのサービスを開始 (または再開) します。

```console
docker-compose up
```

この構成で `docker-compose up` コマンドを初めて実行すると、**Docker** では `services` ノード以下に構成されたイメージがプルされます。つまり、ダウンロード/マウントされます。

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

イメージがダウンロードされ、次にイメージ サービスが開始されます。

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>サービスの可用性を確認する

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

出力例を次に示します。

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>テキスト認識コンテナーをテストする

ホスト コンピューター上でブラウザーを開き、*docker-compose.yaml* から指定されたポートを使用して `localhost` に移動します (たとえば、`http://localhost:5021/swagger/index.html`)。 API のテスト機能を使用して、テキスト認識エンドポイントをテストできます。

![テキスト認識 Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Form Recognizer コンテナーをテストする

ホスト コンピューター上でブラウザーを開き、*docker-compose.yaml* から指定されたポートを使用して `localhost` に移動します (たとえば、`http://localhost:5010/swagger/index.html`)。 API のテスト機能を使用して、Form Recognizer エンドポイントをテストできます。

![Form Recognizer Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services コンテナー](../cognitive-services-container-support.md)