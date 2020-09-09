---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905625"
---
コンテナー内から生成されたコンソール ログにアクセスできます。 まず、Cloud Shell で次のコマンドを実行して、コンテナーのログ記録をオンにします。

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

コンテナーのログ記録がオンになったら、次のコマンドを実行して、ログのストリームを確認します。

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

> [!NOTE]
> `https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。

任意のタイミングでログのストリーミングを停止するには、`Ctrl`+`C` と入力します。
