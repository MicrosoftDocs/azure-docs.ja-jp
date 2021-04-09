---
title: Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する
description: Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8cabb48e3620f1a17d1bb9b87e1646ce2793143b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761704"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Arc データ コントローラーに接続するためのユーザー名とパスワードを取得する

データ コントローラーのユーザー名とパスワードを取得する必要がある場合があります。 これらは実行するときに必要なコマンドです。 

```console
azdata login
```

クラスターの Kubernetes 管理者である場合。 そのため、Kubernetes シークレット ストアから、Azure Arc がそこで保持している情報を取得するコマンドを実行する特権を持ちます。

> [!NOTE]
>  データ コントローラーが作成される名前空間に別の名前を使用している場合は、次のコマンドの `-n arc` パラメーターを、データ コントローラーの作成先の名前空間の名前を使用するように変更します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

次のコマンドを実行して、ユーザー名を取得します。

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

次のコマンドを実行して、パスワードを取得します。

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

次のコマンドを実行して、ユーザー名を取得します。

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

次のコマンドを実行して、パスワードを取得します。

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>次のステップ

その他の[シナリオ](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)を試してみる
