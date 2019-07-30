---
title: Azure Functions を使用して Batch ジョブをトリガーする
description: チュートリアル - Storage Blob に追加されたときに、スキャン済みのドキュメントに OCR を適用する
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc
ms.openlocfilehash: d5a5197227ff62ca0c610e2c4e269480690d3faf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343093"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>チュートリアル:Azure Functions を使用して Batch ジョブをトリガーする

このチュートリアルでは、Azure Functions を使用して Batch ジョブをトリガーする方法を学習します。 Azure Storage Blob コンテナーに追加されたドキュメントに Azure Batch を介して光学式文字認識 (OCR) を適用する例を見ていきます。 OCR 処理を効率化するために、BLOB コンテナーにファイルが追加されるたびに Batch OCR ジョブを実行する Azure 関数を構成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure Batch アカウントおよびリンクされた Azure ストレージ アカウント。 アカウントを作成およびリンクする方法の詳細については、「[Batch アカウントを作成する](quick-create-portal.md#create-a-batch-account)」を参照してください。
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Batch Explorer を使用して Batch プールと Batch ジョブを作成する

このセクションでは、Batch Explorer を使用して、OCR タスクを実行する Batch プールと Batch ジョブを作成します。 

### <a name="create-a-pool"></a>プールを作成する

1. ご自分の Azure 資格情報を使用して、Batch Explorer にサインインします。
1. 左側のバーにある **[Pools]\(プール\)** 、検索フォームの上にある **[Add]\(追加\)** ボタンの順に選択して、プールを作成します。 
    1. ID と表示名を選択します。 この例では、`ocr-pool` を使用します。
    1. スケールの種類を **[Fixed size]\(固定サイズ\)** に設定し、専用ノードの数を 3 に設定します。
    1. オペレーティング システムとして **[Ubuntu 18.04-LTS]** を選択します。
    1. 仮想マシンのサイズとして [`Standard_f2s_v2`] を選択します。
    1. 開始タスクを有効にし、コマンド `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"` を追加します。 必ずユーザー ID を **[Task default user (Admin)]\(タスクの既定のユーザー (管理者)\)** として設定します。これにより、開始タスクに `sudo` を使用したコマンドを含めることができます。
    1. **[OK]** を選択します。
### <a name="create-a-job"></a>ジョブを作成する

1. 左側のバーにある **[Jobs]\(ジョブ\)** 、検索フォームの上にある **[Add]\(追加\)** ボタンの順に選択して、プール上にジョブを作成します。 
    1. ID と表示名を選択します。 この例では、`ocr-job` を使用します。
    1. プールを `ocr-pool` またはご自分のプールに対して選択した名前に設定します。
    1. **[OK]** を選択します。


## <a name="create-blob-containers"></a>BLOB コンテナーを作成する

ここで、OCR Batch ジョブの実際の入力および出力ファイルを格納する BLOB コンテナーを作成します。

1. ご自分の Azure 資格情報を使用して、Storage Explorer にサインインします。
1. ご自分の Batch アカウントにリンクされているストレージ アカウントを使用し、「[BLOB コンテナーを作成する](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#create-a-blob-container)」の手順に従って、2 つの BLOB コンテナー (1 つは入力ファイル用、1 つは出力ファイル用) を作成します。

この例では、入力コンテナーは `input` という名前で、そこには OCR が適用されていないドキュメントがすべて処理のために最初にアップロードされます。 出力コンテナーは `output` という名前で、そこには OCR が適用された処理済みのドキュメントが Batch ジョブによって書き込まれます。  
    * この例では、入力コンテナーを `input`、出力コンテナーを `output` と呼ぶことにします。  
    * 入力コンテナーには、OCR が適用されていないドキュメントがすべて最初にアップロードされます。  
    * 出力コンテナーには、OCR が適用されたドキュメントが Batch ジョブによって書き込まれます。  

Storage Explorer 内で、ご自分の出力コンテナーの Shared Access Signature を作成します。 これを行うには、出力コンテナーを右クリックし、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。 **[Permissions]\(アクセス許可\)** の下にある **[Write]\(書き込み\)** チェック ボックスをオンにします。 それ以外のアクセス許可は必要ありません。  

## <a name="create-an-azure-function"></a>Azure Function の作成

このセクションでは、ファイルがご自分の入力コンテナーにアップロードされるたびに OCR Batch ジョブをトリガーする Azure 関数を作成します。

1. 「[Azure Blob Storage によってトリガーされる関数の作成](https://docs.microsoft.com/azure/azure-functions/functions-create-storage-blob-triggered-function)」の手順に従って、関数を作成します。
    1. ストレージ アカウントを求められたら、ご自分の Batch アカウントにリンクしたのと同じストレージ アカウントを使用します。
    1. **[ランタイム スタック]** で [.NET] を選択します。 Batch .NET SDK を活用するために、C# で関数を作成します。
1. BLOB によってトリガーされる関数を作成したら、その関数内で GitHub の [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) および [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) を使用します。
    * `run.csx` は、ご自分の入力 BLOB コンテナーに新しい BLOB が追加されると実行されます。
    * `function.proj` は、Batch .NET SDK など、ご自分の関数コード内で外部ライブラリを列挙します。
1. `run.csx` ファイルの `Run()` 関数内にある変数のプレースホルダーの値を変更して、ご自分の Batch およびストレージの資格情報を反映させます。 ご自分の Batch およびストレージ アカウントの資格情報は、Azure portal 内にあるご自分の Batch アカウントの **[キー]** セクションで確認できます。
    * Azure portal 内にあるご自分の Batch アカウントの **[キー]** セクションでご自分の Batch およびストレージ アカウントの資格情報を取得します。 

## <a name="trigger-the-function-and-retrieve-results"></a>関数をトリガーし、結果を取得する

スキャン済みファイルの一部またはすべてを GitHub 上の [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) ディレクトリからご自分の入力コンテナーにアップロードします。 Batch Explorer を監視して、各ファイルのタスクが `ocr-pool` に追加されることを確認します。 数秒後に、OCR が適用されたファイルが出力コンテナーに追加されます。 その後、ファイルは Storage Explorer 上で表示および取得できるようになります。

さらに、Azure Functions Web エディター ウィンドウの下部にあるログ ファイルを確認できます。そこには、ご自分の入力コンテナーにアップロードしたすべてのファイルに関する、このようなメッセージが表示されます。

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-06-21T20:02:35.129 [Information] Name of output text file: <outputTxtFile>
2019-06-21T20:02:35.130 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Storage Explorer からご使用のローカル マシンに出力ファイルをダウンロードするには、まず対象のファイルを選択した後、上部のリボンにある **[Download]\(ダウンロード\)** を選択します。 

> [!TIP]
> ダウンロードしたファイルは、PDF リーダーで開くと、検索できます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。 

> [!div class="checklist"]
> * Batch Explorer を使用して、プールおよびジョブを作成する
> * Storage Explorer を使用して、BLOB コンテナーと Shared Access Signature (SAS) を作成する
> * BLOB によってトリガーされる Azure 関数を作成する
> * Storage に入力ファイルをアップロードする
> * タスクの実行を監視する
> * 出力ファイルを取得する

* .NET API を使用して Batch ワークロードのスケジュール設定と処理を行う他の例については、[GitHub のサンプル](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)を参照してください。 

* Batch ワークロードの実行に使用できる Azure Functions の他のトリガーについては、[Azure Functions のドキュメント](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)を参照してください。
