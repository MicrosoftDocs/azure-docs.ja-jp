---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91822791"
---
コンテナーとの直接 SSH セッションを開くには、アプリが実行されている必要があります。

ブラウザーに次の URL を貼り付け、`<app-name>` をお使いのアプリの名前に置き換えます。

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

まだ認証されていない場合、接続するには Azure サブスクリプションで認証する必要があります。 認証されると、ブラウザー内シェルが表示され、コンテナー内でコマンドを実行することができます。

![SSH 接続](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
