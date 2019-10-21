---
title: Python 関数に Azure Storage キュー バインドを追加する
description: Azure CLI と Functions Core Tools を使用して、Python 関数に Azure Storage キュー出力バインドを追加する方法について説明します。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839194"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Python 関数に Azure Storage キュー バインドを追加する

Azure Functions を使用すると、独自の統合コードを記述しなくても、Azure サービスやその他のリソースを関数に接続できます。 これらの*バインド*は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

この記事では、[前のクイック スタートの記事](functions-create-first-function-python.md)で作成した関数を Azure Storage キューと統合する方法を説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 この接続を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="prerequisites"></a>前提条件

この記事の作業を始める前に、[Python クイック スタートのパート 1](functions-create-first-function-python.md) の手順を完了しておきます。

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

これで、Storage の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Functions では、各種のバインドで、`direction`、`type`、および固有の `name` が function.json ファイル内で定義される必要があります。 これらの属性を定義する方法は、関数アプリの言語によって異なります。

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

出力バインドを使用すると、認証、キュー参照の取得、またはデータの書き込みに、Azure Storage SDK のコードを使用する必要がなくなります。 Functions ランタイムおよびキューの出力バインドが、ユーザーに代わってこれらのタスクを処理します。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

以前と同様に、次のコマンドを実行して、Functions ランタイムをローカルで起動します。

```bash
func host start
```

> [!NOTE]  
> 前のクイックスタートでは host.json で拡張バンドルを有効にしていたため、スタートアップ時に[ストレージ バインド拡張機能](functions-bindings-storage-blob.md#packages---functions-2x)が他の Microsoft バインド拡張機能と共に自動的にダウンロードされ、インストールされました。

ランタイム出力から `HttpTrigger` 関数の URL をコピーして、それをブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<yourname>` を追加して、要求を実行します。 前の記事のときと同じ応答がブラウザーに表示されるはずです。

今回は、出力バインドはさらにストレージ アカウントに `outqueue` という名前のキューを作成し、この同じ文字列のメッセージを追加します。

次に、Azure CLI を使用して新しいキューを表示し、メッセージが追加されたことを確認します。 また、[Microsoft Azure Storage Explorer][Azure Storage Explorer] または [Azure portal](https://portal.azure.com) を使用してキューを表示することもできます。

### <a name="set-the-storage-account-connection"></a>ストレージ アカウントの接続を設定する

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>ストレージ キューに対するクエリを実行する

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

ここで、更新された関数アプリを Azure に再発行します。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

デプロイした関数をテストするために、ここでも cURL またはブラウザーを使用できます。 次の例のように、前の手順と同様に、クエリ文字列 `&name=<yourname>` を URL に追加します。

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

[ストレージ キュー メッセージを調べて](#query-the-storage-queue)、出力バインドがキューに新しいメッセージを再度生成することを確認できます。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次の手順

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 Python を使用して Azure Functions を開発する方法の詳細については、「[Azure Functions の Python 開発者向けガイド](functions-reference-python.md)」と、[Azure Functions のトリガーとバインド](functions-triggers-bindings.md)に関するページを参照してください。 Python での完全な関数プロジェクトの例については、[Python 関数のサンプル](/samples/browse/?products=azure-functions&languages=python)を参照してください。 

次に、関数アプリに対して Application Insights の監視を有効にする必要があります。

> [!div class="nextstepaction"]
> [Application Insights との統合を有効にする](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/