---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147255"
---
**既定のマネージド ID** は、システムによって割り当てられたマネージド ID、またはユーザーが割り当てた最初のマネージドID です。

実行中、ID は次の 2 つの方法で利用されます。

1. ユーザーのストレージ マウント、コンテナー レジストリ、データストアを設定するために、システムによって ID が使用されます。

    * この場合、システムは既定のマネージド ID を使用します。

1. 送信済み実行のコード内からリソースにアクセスするために、ユーザーによって ID が利用されます。

    * この場合、資格情報の取得に使用するマネージド ID に対応する *client_id* を指定します。
    * または、*DEFAULT_IDENTITY_CLIENT_ID* 環境変数を使用して、ユーザーが割り当てた ID のクライアント ID を取得します。

    たとえば、既定のマネージド ID を使用してデータストアのトークンを取得するには、次のようにします。

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```