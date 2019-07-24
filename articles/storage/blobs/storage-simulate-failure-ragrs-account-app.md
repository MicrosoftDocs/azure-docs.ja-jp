---
title: チュートリアル:Azure で読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする | Microsoft Docs
description: 読み取りアクセス geo 冗長ストレージにアクセスする際のエラーをシミュレートする
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650242"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>チュートリアル:読み取りアクセス冗長ストレージにアクセスする際のエラーをシミュレートする

このチュートリアルは、シリーズの第 2 部です。 ここでは、失敗をシミュレートすることによって、[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) の利点について学習します。

失敗をシミュレートするには、[Fiddler](#simulate-a-failure-with-fiddler) または [Static Routing](#simulate-a-failure-with-an-invalid-static-route) を使用できます。 どちらの方法でも、[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対する要求の失敗をシミュレートして、アプリケーションが代わりにセカンダリ エンドポイントから読み取りを行うようにすることができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * [fiddler](#simulate-a-failure-with-fiddler) または[無効な静的ルート](#simulate-a-failure-with-an-invalid-static-route)で失敗をシミュレートする 
> * プライマリ エンドポイントの復元をシミュレートする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、第 1 部のチュートリアル「[Azure Storage を使用してアプリケーション データを高可用にする][previous-tutorial]」を完了してください。

Fiddler を使用して失敗をシミュレートするには 

* [Fiddler](https://www.telerik.com/download/fiddler) をダウンロードしてインストールする

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler で失敗をシミュレートする

Fiddler を使用して失敗をシミュレートするには、RA-GRS ストレージ アカウントのプライマリ エンドポイントに対する要求について失敗した応答を挿入します。

以降のセクションでは、fiddler を使用して失敗とプライマリ エンドポイントの回復をシミュレートする方法について説明します。

### <a name="launch-fiddler"></a>Fiddler を起動する

Fiddler を開き、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。

![Fiddler のルールをカスタマイズする](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor が起動し、**SampleRules.js** ファイルが表示されます。 このファイルを使用して Fiddler をカスタマイズします。

`OnBeforeResponse` 関数に次のコード サンプルを貼り付けます。 新しいコードをコメント アウトして、作成したロジックがすぐに実装されないようにします。

完了したら、**[File]\(ファイル\)**、**[Save]\(保存\)** の順に選択して変更を保存します。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>アプリケーションの中断

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

IDE またはシェルでアプリケーションを実行します。

アプリケーションでプライマリ エンドポイントから読み取りが開始されたら、コンソール ウィンドウで**任意のキー**を押してアプリケーションを一時停止します。

![シナリオ アプリ](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

IDE またはシェルでアプリケーションを実行します。

サンプルを制御するため、失敗をシミュレートするためにアプリケーションを中断する必要はありません。 サンプルを実行し、「**P**」と入力して、ファイルが自分のストレージ アカウントにアップロードされたことを確認します。

![シナリオ アプリ](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションが一時停止している間に、Fiddler で保存したカスタム ルールをコメント解除します。

このコード サンプルでは、RA-GRS ストレージ アカウントに対する要求を探し、パスにファイルの名前 `HelloWorld` が含まれる場合は、`503 - Service Unavailable` の応答コードを返します。

Fiddler に移動し、**[Rules]\(ルール\)** -> **[Customize Rules]\(ルールのカスタマイズ\)** を選択します。

次の行をコメント解除し、`STORAGEACCOUNTNAME` をストレージ アカウントの名前で置き換えます。 **[File]\(ファイル\)** -> **[Save]\(保存\)** を選択して変更を保存します。 

> [!NOTE]
> サンプル アプリケーションを Linux で実行する場合は、**CustomRule.js** ファイルを編集するたびに Fiddler を再起動して、カスタム ロジックを Fiddler がインストールできるようにする必要があります。

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

アプリケーションを再開するために**任意のキー**を押します。

アプリケーションの実行が再開されると、プライマリ エンドポイントに対する要求は失敗するようになります。 アプリケーションはプライマリ エンドポイントへの再接続を 5 回試行します。 5 回の試行というエラーしきい値を超えると、セカンダリの読み取り専用エンドポイントのイメージを要求します。 アプリケーションは、セカンダリ エンドポイントからイメージの取得を 20 回正常に完了すると、プライマリ エンドポイントへの接続を試行します。 まだプライマリ エンドポイントに到達できない場合、アプリケーションはセカンダリ エンドポイントからの読み取りを再開します。

このパターンは、前のチュートリアルで説明した[ブレーカー](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)です。

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

失敗が組み込まれたので、「**G**」と入力して失敗をテストします。

プライマリ パイプラインではなくセカンダリ パイプラインを使用していることがわかります。

---

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

前の手順で選択した Fiddler のカスタム ルールがあるので、プライマリ エンドポイントに対する要求は失敗します。

プライマリ エンドポイントが機能することをもう一度シミュレートするには、`503` エラーを挿入するロジックを削除します。

アプリケーションを一時停止するために、**任意のキー**を押します。

Fiddler に移動し、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。 

既定の関数はそのままにして、`OnBeforeResponse` 関数のカスタム ロジックをコメントアウトするか削除します。

**[File]\(ファイル\)**、**[Save]\(保存\)** の順に選択して変更を保存します。

![カスタマイズしたルールを削除する](media/storage-simulate-failure-ragrs-account-app/figure5.png)

完了したら、**任意のキー**を押してアプリケーションを再開します。 読み取り回数が 999 回になるまで、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

前の手順で選択した Fiddler のカスタム ルールがあるので、プライマリ エンドポイントに対する要求は失敗します。

プライマリ エンドポイントが機能することをもう一度シミュレートするには、`503` エラーを挿入するロジックを削除します。

Fiddler に移動し、**[Rules]\(ルール\)**、**[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。既定の関数はそのままにして、`OnBeforeResponse` 関数のカスタム ロジックをコメントアウトするか削除します。

**[File]\(ファイル\)**、**[Save]\(保存\)** の順に選択して変更を保存します。

完了したら、「**G**」と入力してダウンロードをテストします。 今度は再びプライマリ パイプラインを使用していることがアプリケーションから報告されます。

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>無効な静的ルートで失敗をシミュレートする

[read-access geo-redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対するすべての要求について無効な静的ルートを作成することができます。 このチュートリアルでは、ストレージ アカウントに要求をルーティングするためのゲートウェイとしてローカル ホストを使用します。 ローカル ホストをゲートウェイとして使用すると、ストレージ アカウントのプライマリ エンドポイントに対するすべての要求がホスト内でループバックされ、その後エラーとなります。 失敗とプライマリ エンドポイントの回復を無効な静的ルートでシミュレートするには、次の手順に従います。 

### <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

IDE またはシェルでアプリケーションを実行します。 アプリケーションでプライマリ エンドポイントから読み取りが開始されたら、コンソール ウィンドウで**任意のキー**を押してアプリケーションを一時停止します。

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

サンプルを制御するため、失敗をテストするためにアプリケーションを中断する必要はありません。

サンプルを実行し、「**P**」と入力して、ファイルが自分のストレージ アカウントにアップロードされたことを確認します。

---

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションを一時停止し、Windows で管理者としてコマンド プロンプトを起動するか、Linux で root としてターミナルを実行します。

コマンド プロンプトまたはターミナルから次のコマンドを入力して、ストレージ アカウントのプライマリ エンドポイント ドメインに関する情報を取得します。

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 `STORAGEACCOUNTNAME` をストレージ アカウントの名前に置き換えます。 ストレージ アカウントの IP アドレスを後で使用できるようテキスト エディターにコピーします。

ローカル ホストの IP アドレスを取得するには、Windows のコマンド プロンプトから「`ipconfig`」と入力するか、または Linux のターミナルから「`ifconfig`」と入力します。 

宛先ホストの静的ルートを追加するには、Windows のコマンド プロンプトまたは Linux のターミナルから次のコマンドを入力します。 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route add <destination_ip> <gateway_ip>`

`<destination_ip>` の部分はストレージ アカウントの IP アドレスに、`<gateway_ip>` の部分はローカル ホストの IP アドレスに置き換えてください。

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

アプリケーションを再開するために**任意のキー**を押します。

アプリケーションの実行が再開されると、プライマリ エンドポイントに対する要求は失敗するようになります。 アプリケーションは、プライマリ エンドポイントへの再接続を 5 回試行します。 5 回の試行というエラーしきい値を超えると、セカンダリの読み取り専用エンドポイントのイメージを要求します。 セカンダリ エンドポイントからイメージの取得が 20 回正常に完了した後に、アプリケーションはプライマリ エンドポイントへの接続を試行します。 まだプライマリ エンドポイントに到達できない場合、アプリケーションはセカンダリ エンドポイントからの読み取りを再開します。 このパターンは、前のチュートリアルで説明した[ブレーカー](/azure/architecture/patterns/circuit-breaker)です。

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

失敗が組み込まれたので、「**G**」と入力して失敗をテストします。 プライマリ パイプラインではなくセカンダリ パイプラインを使用していることがわかります。

---

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

プライマリ エンドポイントが再び機能するようになった状態をシミュレートするには、ルーティング テーブルから、プライマリ エンドポイントの静的ルートを削除します。 これにより、プライマリ エンドポイントに対する要求はすべて、デフォルト ゲートウェイを介してルーティングできるようになります。

宛先ホスト (ストレージ アカウント) の静的ルートを削除するには、Windows のコマンド プロンプトまたは Linux のターミナルから次のコマンドを入力します。

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET、Python、および Java v7](#tab/dotnet-python-java-v7)

**任意のキー**を押してアプリケーションを再開します。 読み取り回数が 999 回になるまで、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

「**G**」と入力してダウンロードをテストします。 今度は再びプライマリ パイプラインを使用していることがアプリケーションから報告されます。

![アプリケーションを再開する](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>次の手順

シリーズの第 2 部では、失敗をシミュレートして読み取りアクセス geo 冗長ストレージをテストする方法について学習しました。

RA-GRS ストレージのしくみとそれに関連するリスクの詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [RA-GRS を使用した HA アプリの設計](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
