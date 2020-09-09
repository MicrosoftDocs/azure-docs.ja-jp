---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556177"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az-account-list) コマンドを使用して、サブスクリプションの一覧を取得します。

    ```
    az account list --output table
    ```

2. サブスクリプション ID または切り替え先の名前を指定して、`az account set` を実行します。

    ```
    az account set --subscription "My Demos"
    ```
