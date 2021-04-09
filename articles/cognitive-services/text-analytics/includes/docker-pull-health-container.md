---
title: 医療コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965159"
---
Text Analytics for Health のパブリック プレビューへのアクセスを要求するには、[Cognitive Services 要求フォーム](https://aka.ms/csgate)に記入して送信します。  このアプリケーションは、コンテナーとホストされた Web API のパブリック プレビューの両方に適用されます。
このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、Azure Cognitive Services チームがそれをレビューして、プライベート コンテナー レジストリにアクセスするための条件を満たしていることを確認します。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * コンテナーの実行に使用する Azure リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。 
> * Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。

承認されると、プライベート コンテナー レジストリにアクセスするための資格情報を含むメールが送信されます。  オンボードの電子メールで指定されている資格情報と共に docker login コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリに接続します。


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、Microsoft のプライベート コンテナー レジストリからこのコンテナー イメージをダウンロードします。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
