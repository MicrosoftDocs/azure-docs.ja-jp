---
author: baanders
description: Azure Digital Twins のインクルード ファイル - Cloud Shell 認証に関する既知の問題を示します
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290101"
---
>[!NOTE]
>Cloud Shell には現在、`az dt route`、`az dt model`、`az dt twin` の各コマンド グループに影響する**既知の問題**があります。
>
>解決するには、コマンドを実行する前に Cloud Shell で `az login` を実行するか、Cloud Shell ではなく[ローカル CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用します。 この件の詳細については、「[*トラブルシューティング: Azure Digital Twins の既知の問題*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)」を参照してください。