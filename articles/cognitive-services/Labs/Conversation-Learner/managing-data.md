---
title: Conversation Learner を使用したユーザー データの管理 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner を使用したユーザー データの管理方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83c7e808e48733487e84d668236cdb327c21c44c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249080"
---
# <a name="managing-user-data"></a>ユーザー データの管理

このページでは、エンド ユーザーと会話を実施した場合に Conversation Learner クラウド サービスがログに記録する内容について説明します。  また、特定のユーザーに関連するすべてのログを取得または削除できるように、Conversation Learner のログとユーザー ID を関連付ける方法についても説明します。

## <a name="overview-of-end-user-data-logging"></a>エンド ユーザー データのログ記録の概要

Conversation Learner クラウド サービスは既定で、エンド ユーザーとボット間の対話を記録します。  ボットを改善して、ボットが不適切なエンティティを抽出したケースや、不適切なアクションを選択したケースを特定できるようにするために、これらのログは重要です。  その後、UI の "ログ会話" のページに移動し、修正を行い、この修正後の会話を新しいトレーニング会話として格納することで、これらのエラーを修正できます。 詳細については、「ログ会話」に関するチュートリアルを参照してください。

## <a name="how-to-disable-logging"></a>ログ記録を無効にする方法

エンド ユーザーとの会話を Conversation Learner モデルの [設定] ページに表示するかどうかを制御できます。  [Log Conversations]\(会話を記録する\) というチェック ボックスがあります。  このチェック ボックスをオフにすると、エンド ユーザーとの会話は記録されなくなります。

## <a name="what-is-logged"></a>ログに記録される内容 

ログ会話では、Conversation Learner がユーザーの入力、エンティティの値、選択されたアクション、および各発話のタイムスタンプを格納します。  これらのログは、一定の期間保存された後、破棄されます (詳細については、「既定値と境界」に関するヘルプ ページを参照してください)。  

Conversation Learner は、ログ記録された各会話に一意の ID を作成します。  Conversation Learner では、ログ記録された会話のユーザー ID を格納 "*しません*"。  

## <a name="associating-logged-dialogs-with-a-user-id"></a>ログ記録された会話とユーザー ID を関連付ける

ログ記録された会話をユーザーの ID と関連付けできることは、たとえば、ログ記録された会話を特定のユーザーから取得したり削除したりできるようにするなど、多くの場合に重要になります。  Conversation Learner はユーザー ID を格納しないので、この関連付けは、開発者のコードで維持される必要があります。  

このマッピングを作成するために、`EntityDetectionCallback` でログ記録された会話の ID を取得します。次に、ボットのストレージに、ユーザー ID とログ記録されたこの会話との関連を格納します。  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>HTTP 呼び出しのヘッダー

以下の各 HTTP 呼び出しに、次のヘッダーを追加します。

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

`<LUIS_AUTHORING_KEY>` は、Conversation Learner アプリケーションへのアクセスに使用される LUIS オーサリング キーです。

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>ログ記録された会話の生データを取得する方法

ログ会話の生データを取得するために、以下の HTTP 呼び出しを使用できます。

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

ここで、`<appId>` はこの Conversation Learner モデルの GUID であり、`<logDialgoId>` は取得するログ会話の ID です。  

> [!NOTE]
> ログ会話は開発者が編集し、トレーニング会話として格納できます。  これが完了すると、Conversation Learner は "元の" ログ会話の ID をトレーニング会話と共に格納します。  さらに、トレーニング会話は UI の中で "分岐する" ことができます。トレーニング会話が、関連付けられている元のログ会話の ID を保持している場合、そのトレーニング会話からの分岐は、同じログ会話の ID を使ってマーク付けされます。

ログ会話から派生したすべてのトレーニング会話を取得するには、以下の手順を実行します。

最初に、次のコマンドを実行して、すべてのトレーニング会話を取得します。

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

ここで、`<appId>` はこの Conversation Learner モデルの GUID です。  

これにより、すべてのトレーニング会話が返されます。  関連付けられている `sourceLogDialogId` をこの一覧で検索し、関連付けられている `trainDialogId` をメモします。 

ID 別の単一のトレーニング会話に対して、次のコマンドを実行します。

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

ここで、`<appId>` はこの Conversation Learner モデルの GUID であり、`<trainDialogId>` は取得するトレーニング会話の ID です。  

## <a name="how-to-delete-a-logged-dialog"></a>ログ記録された会話を削除する方法

指定された ID のログ会話を削除する場合、次の HTTP 呼び出しを使用できます。

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

ここで、`<appId>` はこの Conversation Learner モデルの GUID であり、`<logDialogId>` は削除するログ会話の ID です。 

指定された ID のトレーニング会話を削除する場合、次の HTTP 呼び出しを使用できます。

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

ここで、`<appId>` はこの Conversation Learner モデルの GUID であり、`<trainDialogId>` は削除するトレーニング会話の ID です。 
