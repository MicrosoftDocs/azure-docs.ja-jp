---
title: 医療コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108989"
---
コンテナーへのアクセスを要求するには、[Cognitive Services コンテナー要求フォーム](https://aka.ms/cognitivegate)に記入して送信します。

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

オンボードの電子メールで指定されている資格情報と共に docker login コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリに接続します。

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、Microsoft のプライベート コンテナー レジストリからこのコンテナー イメージをダウンロードします。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
