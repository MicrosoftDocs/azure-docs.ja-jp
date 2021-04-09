---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010462"
---
1. Visual Studio Code で、F1 キーを選択してコマンド パレットを開きます。 コマンド パレットで、次のコマンドを探して実行します: **Azure Functions: Deploy to function app**。

1. サインしていない場合は、**Azure にサインイン** するよう求められます。 ブラウザーからサインインしたら、Visual Studio Code に戻ります。 複数のサブスクリプションがある場合は、自分の関数アプリが含まれている **サブスクリプションを選択** します。

1. Azure で既存の関数アプリを選択します。 関数アプリ内のすべてのファイルが上書きされるという警告が表示された場合は、 **[デプロイ]** を選択して警告を確認して続行します。

プロジェクトが再構築され、再パッケージされて、Azure にアップロードされます。 既存のプロジェクトが新しいパッケージに置き換えられて、関数アプリが再起動します。