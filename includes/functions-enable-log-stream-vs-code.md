---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 83226fb98f28d95d6244c93043e95424d2da7fe6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455420"
---
Azure で関数アプリのストリーミング ログを有効にするには:

1. F1 キーを押してコマンド パレットを開き、次のコマンドを検索して実行します: **Azure Functions:Start Streaming Logs**。

1. Azure で関数アプリを選択してから、 **[はい]** を選択して関数アプリのアプリケーションのログ記録を有効にします。

1. Azure で関数をトリガーすると、ログ データが Visual Studio Code の [出力] ウィンドウに表示されていることがわかります。

1. 完了したら、忘れずにコマンド **Azure Functions:Stop Streaming Logs** を実行して、関数アプリのログを無効にしてください。