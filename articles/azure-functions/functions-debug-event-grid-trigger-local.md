---
title: Azure Functions の Event Grid のローカル デバッグ
description: Event Grid イベントによってトリガーされる Azure Functions をローカルでデバッグする方法について説明します
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94833063"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Functions の Event Grid トリガーのローカル デバッグ

この記事では、ストレージ アカウントによって発生した Azure Event Grid イベントを処理するローカル関数をデバッグする方法を示します。 

## <a name="prerequisites"></a>前提条件

- 既存の関数アプリを作成または使用する
- 既存のストレージ アカウントを作成または使用する
- [ngrok](https://ngrok.com/) をダウンロードして、Azure でローカル関数を呼び出せるようにする

## <a name="create-a-new-function"></a>新しい関数を作成する

Visual Studio で関数アプリを開き、ソリューション エクスプローラーでプロジェクト名を右クリックし、 **[追加] > [新しい Azure 関数]** の順にクリックします。

*[新しい Azure 関数]* ウィンドウで、 **[Event Grid トリガー]** 選択し、 **[OK]** をクリックします。

![新しい関数の作成](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

関数が作成されたら、コード ファイルを開いて、ファイルの上部にコメント アウトされた URL をコピーします。 この場所は、Event Grid トリガーを構成するときに使用されます。

![コピーの場所](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

次に、`log.LogInformation` で始まる行にブレークポイントを設定します。

![ブレークポイントの設定](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


次に、**F5 キーを押して** デバッグ セッションを開始します。

## <a name="allow-azure-to-call-your-local-function"></a>Azure でローカル関数を呼び出せるようにする

マシン上でデバッグ中の関数を中断するには、Azure でクラウドからローカル関数と通信する方法を有効にする必要があります。

[ngrok](https://ngrok.com/) ユーティリティでは、マシンで実行されている関数を Azure で呼び出すことができます。 次のコマンドを使用して *ngrok* を起動します。

```bash
ngrok http -host-header=localhost 7071
```
ユーティリティがセットアップされると、次のスクリーンショットのようなコマンド ウィンドウが表示されます。

!["ngrok" ユーティリティを起動した後のコマンド プロンプトを示すスクリーンショット。](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

*ngrok* が実行されたときに生成される **HTTPS** URL をコピーします。 この値は、Event Grid イベント エンドポイントを構成するときに使用されます。

## <a name="add-a-storage-event"></a>ストレージ イベントを追加する

Azure portal を開き、ストレージ アカウントに移動し、 **[イベント]** オプションをクリックします。

![ストレージ アカウント イベントを追加する](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

*[イベント]* ウィンドウで、 **[イベント サブスクリプション]** ボタンをクリックします。 *[イベント サブスクリプション]* ウィンドウで、 *[エンドポイントの種類]* ドロップダウンを選択し、 **[Web Hook]** を選択します。

![サブスクリプションの種類を選択する](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

エンドポイントの種類が構成されたら、 **[エンドポイントを選択する]** をクリックしてエンドポイント値を構成します。

![エンドポイントの種類を選択する](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*[サブスクライバー エンドポイント]* の値は 3 つの異なる値から構成されます。 プレフィックスは、*ngrok* によって生成される HTTPS URL です。 URL の残りの部分は、関数コード ファイルにある関数名から取得され、末尾に関数名が付加されます。 関数コード ファイルの URL 以降では、*ngrok* URL が `http://localhost:7071` に置き換えられ、関数名が `{functionname}` に置き換えられます。

次のスクリーンショットは、最後の URL がどのように表示されるかを示しています。

![エンドポイントの選択](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

適切な値を入力したら、 **[選択内容の確認]** をクリックします。

> [!IMPORTANT]
> *ngrok* を起動するたびに、HTTPS URL が再生成され、値が変更されます。 そのため、*ngrok* を使用して Azure に関数を公開するたびに、新しいイベント サブスクリプションを作成する必要があります。

## <a name="upload-a-file"></a>ファイルをアップロードする

これで、ストレージ アカウントにファイルをアップロードして、ローカル関数で処理する Event Grid イベントをトリガーすることができます。 

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を開いて、ストレージ アカウントに接続します。 

- **BLOB コンテナー** を展開します 
- 右クリックし、 **[BLOB コンテナーの作成]** を選択します。
- コンテナーに **test** という名前を付けます
- *test* コンテナーを選択します
- **[アップロード]** ボタンをクリックします
- **[ファイルのアップロード]** をクリックします
- ファイルを選択して、BLOB コンテナーにアップロードしま

## <a name="debug-the-function"></a>関数をデバッグする

新しいファイルがストレージ コンテナーにアップロードされることを Event Grid が認識すると、ローカル関数でブレークポイントがヒットします。

![ngrok を起動する](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースをクリーンアップするには、ストレージ アカウント内の **test** コンテナーを削除します。

## <a name="next-steps"></a>次のステップ

- [Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions の Event Grid トリガー](./functions-bindings-event-grid.md)
