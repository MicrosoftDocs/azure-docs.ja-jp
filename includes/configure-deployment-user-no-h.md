---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: cd7fc7487a41979f37c9a55baeb0b8e172e808c4
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59587089"
---
Azure Cloud Shell で [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) コマンドを使用して、デプロイ資格情報を構成します。 FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。 ユーザー名とパスワードはアカウント レベルです。 "_Azure サブスクリプションの資格情報とは異なります。_"

次の例では、*\<username>* と *\<password>* を新しいユーザー名とパスワードで置き換えます (かっこも含めます)。 ユーザー名は Azure 内で一意になっている必要があります。 パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

パスワードが `null` と表示された状態で JSON 出力が返されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 デプロイ ユーザー名に、ローカルの Git プッシュを表す '@' 記号が含まれていてはなりません。

このデプロイ ユーザーの構成は、1 回だけ実行します。 すべての Azure デプロイでこれを使用できます。

> [!NOTE]
> ユーザー名とパスワードをメモします。 後で Web アプリをデプロイするときに必要になります。
>
>
