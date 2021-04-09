---
title: クイック スタート:ポータルで Azure Storage キューを作成する
description: Azure portal を使用してキューを作成します。 その後、Azure portal を使用して、メッセージを追加し、メッセージのプロパティを表示し、メッセージをデキューします。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 24b1d16ab5f13a9aef1faa5bc7a3579ad1a5aa5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585549"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>クイック スタート:Azure portal でキューを作成してメッセージを追加する

このクイック スタートでは、[Azure portal](https://portal.azure.com/) を使用して Azure Storage にキューを作成し、メッセージを追加およびデキューする方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>キューを作成する

Azure portal でキューを作成するには、次の手順のようにします。

1. Azure Portal で新しいストレージ アカウントに移動します。
2. ストレージ アカウントの左側のメニューで、 **[Queue Storage]** セクションまでスクロールし、 **[キュー]** を選択します。
3. **[+ キュー]** ボタンを選択します。
4. 新しいキューの名前を入力します。 キューの名前は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。
6. **[OK]** を選択してキューを作成します。

    ![Azure portal でキューを作成する方法を示すスクリーンショット](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>メッセージを追加する

次に、新しいキューにメッセージを追加します。 メッセージの許容される最大サイズは 64 KB です。

1. ストレージ アカウントのキューの一覧から新しいキューを選択します。
1. **[+ メッセージの追加]** ボタンを選択して、キューにメッセージを追加します。 **[メッセージ テキスト]** フィールドにメッセージを入力します。
1. メッセージの有効期限を指定します。 **[有効期限]** フィールドに入力できる有効な値は 1 秒から 7 日です。 明示的に削除されるまでメッセージをキューに保持する場合は、 **[メッセージの有効期限なし]** を選択します。
1. メッセージを Base64 としてエンコードするかどうかを示します。 バイナリ データはエンコードすることをお勧めします。
1. **[OK]** ボタンを選択して、メッセージを追加します。

    ![メッセージをキューに追加する方法を示すスクリーンショット](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>メッセージのプロパティを表示する

メッセージを追加した後、キュー内のすべてのメッセージの一覧が Azure portal に表示されます。 メッセージ ID、メッセージの内容、メッセージの挿入日時、メッセージの有効期限を見ることができます。 また、このメッセージがデキューされた回数を確認することもできます。

![メッセージのプロパティを示すスクリーンショット](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>メッセージをデキューする

Azure portal では、キューの先頭からメッセージをデキューすることができます。 メッセージをデキューすると、メッセージは削除されます。

デキューでは常に、キュー内の最も古いメッセージが削除されます。

![ポータルからのメッセージのデキュー方法を示すスクリーンショット](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure portal でキューを作成し、メッセージを追加し、メッセージのプロパティを表示して、メッセージをデキューする方法について説明しました。

> [!div class="nextstepaction"]
> [Azure Queue Storage とは](storage-queues-introduction.md)
