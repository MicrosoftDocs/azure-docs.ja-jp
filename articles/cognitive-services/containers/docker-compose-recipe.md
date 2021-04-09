---
title: Docker Compose を使用して複数のコンテナーをデプロイする
titleSuffix: Azure Cognitive Services
description: 複数の Cognitive Services コンテナーをデプロイする方法について説明します。 この記事では、Docker Compose を使用して複数の Docker コンテナー イメージをオーケストレーションする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996129"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Docker Compose を使用して複数のコンテナーをデプロイする

この記事では、複数の Azure Cognitive Services コンテナーをデプロイする方法について説明します。 具体的には、Docker Compose を使用して複数の Docker コンテナー イメージをオーケストレーションする方法について説明します。

> [Docker Compose](https://docs.docker.com/compose/) は、マルチコンテナー Docker アプリケーションを定義して実行するためのツールです。 Compose では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、1 つのコマンドを実行することによって、構成からすべてのサービスを作成して開始します。

1 台のホスト コンピューター上で複数のコンテナー イメージをオーケストレーションすると便利な場合があります。 この記事では、読み取りおよび Form Recognizer コンテナーをまとめます。

## <a name="prerequisites"></a>前提条件

この手順には、ローカルでインストールして実行する必要があるいくつかのツールが必要です。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/cognitive-services)を作成してください。
* [Docker エンジン](https://www.docker.com/products/docker-engine)。 Docker CLI がコンソール ウィンドウで動作することを確認します。
* 適切な価格レベルの Azure リソース。 このコンテナーでは次の価格レベルのみが有効です。
  * F0 または Standard 価格レベルのみの **Computer Vision** リソース。
  * F0 または Standard 価格レベルのみの **Form Recognizer** リソース。
  * S0 価格レベルの **Cognitive Services** リソース。
* ゲート プレビュー コンテナーを使用している場合、[オンライン要求フォーム](https://aka.ms/csgate/)に入力して使用する必要があります。

## <a name="docker-compose-file"></a>Docker Compose ファイル

YAML ファイルには、デプロイされるすべてのサービスが定義されています。 これらのサービスは、`DockerFile` または既存のコンテナー イメージのどちらかに依存します。 この例では、2 つのプレビュー イメージを使用します。 次の YAML ファイルをコピーして貼り付け、*docker-compose.yaml* という名前で保存します。 ファイルで適切な **apikey**、**billing**、および **EndpointUri** の値を指定します。

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> ホスト マシン上に、**volumes** ノード以下に指定されているディレクトリを作成します。 ボリューム バインドを使用してイメージをマウントする前にディレクトリが存在している必要があるので、この方法が必要です。

## <a name="start-the-configured-docker-compose-services"></a>構成された Docker Compose サービスを開始する

Docker Compose ファイルを使用すると、定義されたサービスのライフ サイクルのすべての段階 (サービスの開始、停止、再構築、サービス状態の表示、ログのストリーミング) を管理できます。 プロジェクト ディレクトリ (docker-compose.yaml ファイルがある場所) からコマンドライン インターフェイスを開きます。

> [!NOTE]
> エラーを避けるために、ホスト マシンと Docker エンジンがドライブを適切に共有していることを確認します。 たとえば、*E:\publicpreview* が *docker-compose.yaml* ファイルのディレクトリとして使用されている場合、ドライブ **E** を Docker と共有します。

コマンドライン インターフェイスから次のコマンドを実行して、*docker-compose.yaml* ファイルに定義されているすべてのサービスを開始 (または再開) します。

```console
docker-compose up
```

この構成を使用して **docker-compose up** コマンドを初めて実行するとき、Docker は **services** ノード下に構成されているイメージを取得してから、それらをダウンロードしてマウントします。

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
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
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
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

イメージがダウンロードされると、イメージ サービスが開始されます。

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
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>テスト コンテナー

ホスト コンピューター上でブラウザーを開き、 http://localhost:5021/swagger/index.html のように、*docker-compose.yaml* ファイルで指定されたポートを使用して **localhost** にアクセスします。 たとえば、API の **[使ってみる]** 機能を使用して、Form Recognizer エンドポイントをテストできます。 両方のコンテナーの swagger ページを使用およびテストできます。

![Form Recognizer コンテナー](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Cognitive Services コンテナー](../cognitive-services-container-support.md)
