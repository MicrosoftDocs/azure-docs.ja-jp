---
title: インクルード ファイル
description: インクルード ファイル
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708268"
---
## <a name="create-azure-context"></a>Azure コンテキストを作成する

Docker コマンドを使用して Azure Container Instances でコンテナーを実行するには、最初に Azure にログインします。

```bash
docker login azure
```

メッセージが表示されたら、Azure の資格情報を入力または選択します。


`docker context create aci` を実行して、ACI コンテキストを作成します。 このコンテキストにより、Azure サブスクリプションとリソース グループに Docker が関連付けられて、コンテナー インスタンスを作成、管理できるようになります。 たとえば、*myacicontext* というコンテキストを作成するには、次のように入力します。

```
docker context create aci myacicontext
```

プロンプトが表示されたら、Azure サブスクリプション ID を選択してから、既存のリソース グループを選択するか、**新しいリソース グループを作成** します。 新しいリソース グループを選択すると、システムによって生成された名前で作成されます。 Azure のコンテナー インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。


`docker context ls` を実行して、Docker コンテキストに ACI コンテキストを追加したことを確認します。

```
docker context ls
```