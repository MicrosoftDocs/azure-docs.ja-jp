---
title: 医療コンテナー インスタンスを確認する
titleSuffix: Azure Cognitive Services
description: 医療コンテナー インスタンスを確認する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 1a8ce0bc94c61a0cfe6cdad11375763ba954957d
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122416"
---
### <a name="verify-that-a-container-is-running"></a>コンテナーが実行中であることを確認する

1. **[概要]** タブを選択し、IP アドレスをコピーします。
1. 新しいブラウザー タブを開き、IP アドレスを入力します。 たとえば、「`http://<IP-address>:5000 (http://55.55.55.55:5000`)」と入力します。 コンテナーのホーム ページが表示され、コンテナーが実行中であることを知らせます。

    ![コンテナーのホーム ページを表示してコンテナーが実行中であることを確認します](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **[Service API Description]\(サービス API の説明\)** リンクを選択して、コンテナーの Swagger ページに移動します。

1. いずれかの **POST** APIを選択して **[試してみる]** を選択します。入力の例を含むパラメーターが表示されます。

また、コンテナーが実行中であることを確認するために使用できる URL がいくつかあります。

|Request|目的|
|--|--|
|`http://localhost:5000/`|コンテナーには、ホーム ページが用意されています。|
|`http://localhost:5000/ready`|GET で要求することで、コンテナーがモデルに対するクエリを受け取る準備ができていることを確認できます。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。|
|`http://localhost:5000/status`|/ready エンドポイントと同様に、GET で要求することで、モデルに対するクエリを発生させずにコンテナーが実行されていることを確認できます。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。|
|`http://localhost:5000/swagger`|この URL を通じて、コンテナーからエンドポイントの完全なドキュメント一式と `Try it now` 機能が提供されます。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |
|`http://localhost:5000/demo`| ブラウザーを通じてこの機能を要求すると、入力テキスト サンプルのクエリや指定したクエリの結果を対話形式で視覚化できます。  |

コンテナーにクエリを送信するには、この要求 URL (`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`) を使用します。
