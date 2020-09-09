---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146449"
---
### <a name="send-a-test-notification"></a>テスト通知を送信する

1. [Postman](https://www.postman.com/downloads/) で新しいタブを開きます。

1. **POST** の要求を設定し、次のアドレスを入力します。

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. 「[API キーを使用してクライアントを認証する](#authenticate-clients-using-an-api-key-optional)」セクションを完了した場合は、**apikey** 値を含めるように要求ヘッダーを構成してください。

   | Key                            | 値                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. **[Body]** に **[raw]** オプションを選択し、書式オプションの一覧から **[JSON]** を選択します。次に、いくつかのプレースホルダー **JSON** コンテンツを含めます。

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. **[コード]** ボタンを選択します。これはウィンドウの右上にある **[保存]** ボタンの下にあります。 **HTML** に対して表示する場合、要求は次の例のようになります (**apikey** ヘッダーが含まれているかどうかによって異なります)。

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. **PushDemo** アプリケーションを、ターゲット プラットフォーム (**Android** および **iOS**) の一方または両方で実行します。

    > [!NOTE]
    > **Android** でテストしている場合は、**デバッグ**で実行していないことを確認します。または、アプリケーションを実行してアプリが展開されている場合は、アプリを強制的に閉じてから、ランチャーからもう一度起動します。

1. **PushDemo** アプリで、 **[登録]** ボタンをタップします。

1. **[Postman](https://www.postman.com/downloads)** に戻り、 **[Generate Code Snippets] (コード スニペットの生成)** ウィンドウを閉じ (まだ閉じていない場合)、 **[送信]** ボタンをクリックします。

1. **[Postman](https://www.postman.com/downloads)** で **200 OK** 応答を受信し、**ActionA アクションの受信**を示すアラートがアプリに表示されたことを確認します。  

1. **PushDemo** アプリを閉じ、 **[Postman](https://www.postman.com/downloads)** でもう一度 **[送信]** ボタンをクリックします。

1. **[Postman](https://www.postman.com/downloads)** で再度 **200 OK** 応答が返されたことを確認します。 **PushDemo** アプリの通知領域に、正しいメッセージを含む通知が表示されていることを確認します。

1. 通知をタップしてアプリを開き、**ActionA アクションの受信**を示すアラートが表示されていることを確認します。

1. **[Postman](https://www.postman.com/downloads)** に戻り、前の要求本文を変更して、**action** 値に *action_a* ではなく *action_b* を指定したサイレント通知を送信します。

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. アプリを開いたままの状態で、 **[Postman](https://www.postman.com/downloads)** の **[送信]** ボタンをクリックします。

1. **[Postman](https://www.postman.com/downloads)** で **200 OK** 応答を受信し、**ActionA アクションの受信**ではなく **ActionB アクションの受信**を示すアラートがアプリに表示されたことを確認します。

1. **PushDemo** アプリを閉じ、 **[Postman](https://www.postman.com/downloads)** でもう一度 **[送信]** ボタンをクリックします。

1. **[Postman](https://www.postman.com/downloads)** で **200 OK** 応答を受信したこと、および通知領域にサイレント通知が表示されていないことを確認します。
