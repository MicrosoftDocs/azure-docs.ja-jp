---
title: HTTP によってトリガーされない Azure 関数を手動で実行する
description: HTTP 要求を使用して HTTP によってトリガーされない Azure 関数を実行する
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 61bece83697a4907a7bf3c881003f4da9b0e8a84
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466884"
---
# <a name="manually-run-a-non-http-triggered-function"></a>HTTP によってトリガーされない関数を手動で実行する

この記事では、特殊な形式の HTTP 要求を介して、HTTP によってトリガーされない関数を手動で実行する方法について説明します。

状況によっては、間接的にトリガーされる Azure 関数を "オンデマンドで" 実行する必要があります。  間接的なトリガーの例として、[スケジュールに対する関数](./functions-create-scheduled-function.md)、[別リソースのアクション](./functions-create-storage-blob-triggered-function.md)の結果として実行される関数などがあります。 

次の例では [Postman](https://www.getpostman.com/) が使用されていますが、[cURL](https://curl.haxx.se/)、[Fiddler](https://www.telerik.com/fiddler) などの同様のツールを使用して HTTP 要求を送信できます。

## <a name="define-the-request-location"></a>要求の場所を定義する

HTTP によってトリガーされない関数を実行するには、関数を実行するために Azure に要求を送信する方法が必要です。 この要求の送信に使用される URL には特定の形式があります。

![要求の場所の定義: ホスト名 + フォルダー パス + 関数名](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **ホスト名:** 関数アプリの発行場所。関数アプリの名前と、*azurewebsites.net* またはカスタム ドメインから構成されます。
- **フォルダー パス:** HTTP によってトリガーされない関数に HTTP 要求を介してアクセスするには、フォルダー *admin/functions* から要求を送信する必要があります。
- **関数名:** 実行する関数の名前。

関数を実行する Azure への要求で、関数のマスター キーと共に Postman でこの要求の場所を使用します。

> [!NOTE]
> ローカルで実行する場合、関数のマスター キーは必要ありません。 `x-functions-key` ヘッダーを省略して、直接[関数を呼び出す](#call-the-function)ことができます。

## <a name="get-the-functions-master-key"></a>関数のマスター キーを取得する

Azure portal で関数に移動し、**[管理]** をクリックし、**[ホスト キー]** セクションを見つけます。 *_master* 行で **[コピー]** ボタンをクリックして、マスター キーをクリップボードにコピーします。

![関数の [管理] 画面からマスター キーをコピーする](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

マスター キーをコピーしたら、関数名をクリックしてコード ファイル ウィンドウに戻ります。 次に、**[ログ]** タブをクリックします。Postman から手動で関数を実行すると、ログに記録された関数のメッセージがここに表示されます。

> [!CAUTION]  
> マスター キーによって付与された関数 app の権限が昇格しているため、このキーを第三者と共有したり、アプリケーションに配布したりしないでください。

## <a name="call-the-function"></a>関数を呼び出す

Postman を開き、次の手順を実行します。

1. **[URL] テキスト ボックスに要求の場所**を入力します。
2. HTTP メソッドが **[POST]** に設定されていることを確認します。
3. **[ヘッダー]** タブを**クリック**します。
4. 最初の **[キー]** に「**x-functions-key**」と入力し、**[値]** ボックスに (クリップボードから) マスター キーを貼り付けます。
5. 2 番目の **[キー]** に「**Content-Type**」と入力し、**[値]** に「**application/json**」と入力します。

    ![Postman のヘッダーの設定](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **[本文]** タブを**クリック**します。
7. 要求の本文に「**{ "input": "test" }**」と入力します。

    ![Postman の本文の設定](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. **[送信]** をクリックします。

    ![Postman を使用して要求を送信する](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman から **202 Accepted** の状態が報告されます。

次に、Azure portal で関数に戻ります。 *ログ* ウィンドウに移動すると、関数の手動呼び出しの結果によるメッセージが表示されます。

![手動呼び出しの関数ログの結果](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>次の手順

- [Azure Functions のコードをテストするための戦略](./functions-test-a-function.md)
- [Azure Functions の Event Grid トリガーのローカル デバッグ](./functions-debug-event-grid-trigger-local.md)
