---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 19f3a99c087c0755a82ce7940326708fd1f6a3e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673133"
---
### <a name="prerequisite-check"></a>前提条件のチェック

+ ターミナルまたはコマンド ウィンドウで `func --version` を実行して、Azure Functions Core Tools のバージョンが 2.7.1846 以降であることを確認します。

+ `az --version` を実行して、Azure CLI バージョンが 2.0.76 以降であることを確認します。

+ `az login` を実行して Azure にサインインし、アクティブなサブスクリプションを確認します。

::: zone pivot="programming-language-python"  
+ `python --version` (Linux と macOS の場合) または `py --version` (Windows の場合) を実行して、使用している Python のバージョンが 3.8.x、3.7.x、または 3.6.x であることを確認します。
::: zone-end