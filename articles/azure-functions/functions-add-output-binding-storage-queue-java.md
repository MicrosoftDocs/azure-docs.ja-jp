---
title: Java 関数を Azure Storage に接続する
description: Queue storage の出力バインドを使用して、HTTP によってトリガーされる Java 関数を Azure Storage に接続する方法を学習します。
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422656"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Java 関数を Azure Storage に接続する

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

この記事では、[前のクイック スタートの記事](./create-first-function-cli-java.md?tabs=bash,browser)で作成した関数を Azure Storage キューと統合する方法を説明します。 この関数に追加する出力バインドは、HTTP 要求のデータをキュー内のメッセージに書き込みます。

ほとんどのバインドでは、バインドされているサービスにアクセスするために関数が使用する、保存されている接続文字列が必要です。 この接続を簡単にするために、関数アプリで作成したストレージ アカウントを使用します。 このアカウントへの接続は、既に `AzureWebJobsStorage` という名前のアプリ設定に保存されています。  

## <a name="prerequisites"></a>前提条件

この記事の作業を始める前に、[Java クイックスタートのパート 1](./create-first-function-cli-java.md?tabs=bash,browser) の手順を完了しておきます。

## <a name="download-the-function-app-settings"></a>関数アプリの設定をダウンロードする

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>拡張バンドルを有効にする

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

これで、Storage の出力バインドをプロジェクトに追加できるようになります。

## <a name="add-an-output-binding"></a>出力バインディングを追加する

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>出力バインディングを使用するコードを追加する

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

これで、新しい出力バインドをローカルで試す準備ができました。

## <a name="run-the-function-locally"></a>関数をローカルで実行する

前と同様に、次のコマンドを使用してプロジェクトをビルドし、Functions ランタイムをローカルで起動します。

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> host.json で拡張バンドルを有効にしていたため、スタートアップ時に[ストレージ バインド拡張機能](functions-bindings-storage-blob.md#add-to-your-functions-app)が他の Microsoft バインド拡張機能と共に自動的にダウンロードされ、インストールされました。

前と同様に、新しいターミナル ウィンドウで cURL を使用して、コマンド ラインから関数をトリガーします。

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

今回は、出力バインドはさらにストレージ アカウントに `outqueue` という名前のキューを作成し、この同じ文字列のメッセージを追加します。

次に、Azure CLI を使用して新しいキューを表示し、メッセージが追加されたことを確認します。 また、[Microsoft Azure Storage Explorer][Azure Storage Explorer] または [Azure portal](https://portal.azure.com) を使用してキューを表示することもできます。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>プロジェクトを再デプロイする 

公開したアプリを更新するには、次のコマンドを再度実行します。  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

ここでも、cURL を使用して、デプロイした関数をテストすることができます。 前と同様に、次の例のように、POST 要求の本文で値 `AzureFunctions` を URL に渡します。

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

再度 [Storage キュー メッセージを調べて](#query-the-storage-queue)、出力バインドが期待どおりにキューに新しいメッセージを生成することを確認できます。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 Python を使用して Azure Functions を開発する方法の詳細については、「[Azure Functions の Java 開発者向けガイド](functions-reference-java.md)」と、[Azure Functions のトリガーとバインド](functions-triggers-bindings.md)に関するページを参照してください。 Java での完全な関数プロジェクトの例については、[Java 関数のサンプル](/samples/browse/?products=azure-functions&languages=Java)を参照してください。 

次に、関数アプリに対して Application Insights の監視を有効にする必要があります。

> [!div class="nextstepaction"]
> [Application Insights との統合を有効にする](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
