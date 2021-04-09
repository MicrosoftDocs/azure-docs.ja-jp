---
title: HTTP によってトリガーされない Azure 関数を手動で実行する
description: HTTP 要求を使用して HTTP によってトリガーされない Azure 関数を実行する
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537703"
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

1. [Azure portal](https://portal.azure.com) で関数アプリに移動し、 **[アプリ キー]** 、`_master` キーの順に選択します。 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="コピーするマスター キーを見つけます。" border="true":::

1. **[キーの編集]** セクションで、キーの値をクリップボードにコピーして、 **[OK]** を選択します。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="マスター キーをクリップボードにコピーします。" border="true":::

1. *_master* キーをコピーした後、 **[Code + Test]\(コード + テスト\)** を選択し、 **[ログ]** を選択します。 Postman から手動で関数を実行すると、ログに記録された関数のメッセージがここに表示されます。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="ログからのメッセージが表示されている [Code + Test]\(コード + テスト\) ページを示すスクリーンショット。" border="true":::

> [!CAUTION]  
> マスター キーによって付与された関数 app の権限が昇格しているため、このキーを第三者と共有したり、アプリケーションに配布したりしないでください。 HTTPS エンドポイントにのみキーを送信する必要があります。

## <a name="call-the-function"></a>関数を呼び出す

Postman を開き、次の手順を実行します。

1. **[URL] テキスト ボックスに要求の場所** を入力します。
1. HTTP メソッドが **[POST]** に設定されていることを確認します。
1. **[Headers]\(ヘッダー\)** タブを選択します。
1. 最初のキーとして「**x-functions-key**」と入力して、(クリップボードから) マスター キーを値として貼り付けます。
1. 2 番目のキーとして「**Content-type**」と入力して、値として「**application/json**」と入力します。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Postman のヘッダーの設定。" border="true":::

1. **[本文]** タブを選択します。
1. 要求の本文として「 **{ "input": "test" }** 」と入力します。

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Postman の本文の設定。" border="true":::

1. **[送信]** を選択します。
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Postman を使用して要求を送信します。" border="true":::

    Postman から **202 Accepted** の状態が報告されます。

1. 次に、Azure portal で関数に戻ります。 ログを確認すると、関数の手動呼び出しによるメッセージが表示されます。

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="ログを表示して、マスター キーのテスト結果を確認します。" border="true":::

## <a name="next-steps"></a>次のステップ

- [Azure Functions のコードをテストするための戦略](./functions-test-a-function.md)
- [Azure Functions の Event Grid トリガーのローカル デバッグ](./functions-debug-event-grid-trigger-local.md)
