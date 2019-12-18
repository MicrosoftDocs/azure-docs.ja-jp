---
title: チュートリアル - プライマリ リージョンからデータを読み取る際のエラーをシミュレートする
titleSuffix: Azure Storage
description: 読み取りアクセス geo 冗長ストレージ (RA-GRS) がストレージ アカウントで有効になっているときにプライマリ リージョンからのデータの読み取りで発生するエラーをシミュレートします。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 44c5d037797d845aa9c68af2d7b8e5e45bf418fb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892449"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>チュートリアル:プライマリ リージョンからデータを読み取る際のエラーをシミュレートする

このチュートリアルは、シリーズの第 2 部です。 ここでは、失敗をシミュレートすることによって、[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) の利点について学習します。

エラーをシミュレートするには、[静的ルーティング](#simulate-a-failure-with-an-invalid-static-route)または [Fiddler](#simulate-a-failure-with-fiddler) のいずれかを使用できます。 どちらの方法でも、[読み取りアクセス geo 冗長ストレージ](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対する要求エラーをシミュレートして、アプリケーションが代わりにセカンダリ エンドポイントから読み取りを行うようにすることができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの実行と一時停止
> * [無効な静的ルート](#simulate-a-failure-with-an-invalid-static-route)または [Fiddler](#simulate-a-failure-with-fiddler) を使用してエラーをシミュレートする
> * プライマリ エンドポイントの復元をシミュレートする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、第 1 部のチュートリアル「[Azure Storage を使用してアプリケーション データを高可用にする][previous-tutorial]」を完了してください。

静的ルーティングを使用してエラーをシミュレートするには、管理者特権でのコマンド プロンプトを使用します。

Fiddler を使用してエラーをシミュレートするには、Fiddler をダウンロードして[インストール](https://www.telerik.com/download/fiddler)します。

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>無効な静的ルートで失敗をシミュレートする

[read-access geo-redundant](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) ストレージ アカウントのプライマリ エンドポイントに対するすべての要求について無効な静的ルートを作成することができます。 このチュートリアルでは、ストレージ アカウントに要求をルーティングするためのゲートウェイとしてローカル ホストを使用します。 ローカル ホストをゲートウェイとして使用すると、ストレージ アカウントのプライマリ エンドポイントに対するすべての要求がホスト内でループバックされ、その後エラーとなります。 失敗とプライマリ エンドポイントの回復を無効な静的ルートでシミュレートするには、次の手順に従います。

### <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

[前のチュートリアル][previous-tutorial]の手順に従ってサンプルを起動し、テスト ファイルをダウンロードします。それがプライマリ ストレージからダウンロードされていることを確認します。 ターゲット プラットフォームに応じて、サンプルを手動で一時停止するか、プロンプトで待機できます。

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションが一時停止している間に、管理者としてコマンド プロンプトを起動する (Windows の場合) か、root としてターミナルを実行します (Linux の場合)。

コマンド プロンプトまたはターミナルから、次のコマンドの `STORAGEACCOUNTNAME` を自分のストレージ アカウントの名前に置き換えて入力し、ストレージ アカウントのプライマリ エンドポイント ドメインに関する情報を取得します。

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

ストレージ アカウントの IP アドレスを後で使用できるようテキスト エディターにコピーします。

ローカル ホストの IP アドレスを取得するには、Windows のコマンド プロンプトから「`ipconfig`」と入力するか、または Linux のターミナルから「`ifconfig`」と入力します。

宛先ホスト用の静的ルートを追加するには、Windows のコマンド プロンプトまたは Linux ターミナルから、次のコマンドの `<destination_ip>` を自分のストレージ アカウントの IP アドレスに、`<gateway_ip>` を自分のローカル ホストの IP アドレスに置き換えて入力します。

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

サンプルを実行中のウィンドウで、アプリケーションを再開するか適切なキーを押してサンプル ファイルをダウンロードします。それがセカンダリ ストレージからダウンロードされていることを確認します。 その後、サンプルをもう一度一時停止するか、プロンプトで待機できます。

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

プライマリ エンドポイントが再び機能するようになった状態をシミュレートするには、無効な静的ルートをルーティング テーブルから削除します。 これにより、プライマリ エンドポイントに対する要求はすべて、デフォルト ゲートウェイを介してルーティングできるようになります。 Windows のコマンド プロンプトまたは Linux のターミナルから次のコマンドを入力します。

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

アプリケーションを再開するか適切なキーを押してサンプル ファイルをダウンロードします。今回は、それが再びプライマリ ストレージからダウンロードされていることを確認します。

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler で失敗をシミュレートする

Fiddler を使用して失敗をシミュレートするには、RA-GRS ストレージ アカウントのプライマリ エンドポイントに対する要求について失敗した応答を挿入します。

以降のセクションでは、fiddler を使用して失敗とプライマリ エンドポイントの回復をシミュレートする方法について説明します。

### <a name="launch-fiddler"></a>Fiddler を起動する

Fiddler を開き、 **[Rules]\(ルール\)** 、 **[Customize Rules]\(ルールのカスタマイズ\)** の順に選択します。

![Fiddler のルールをカスタマイズする](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor が起動し、**SampleRules.js** ファイルが表示されます。 このファイルを使用して Fiddler をカスタマイズします。

次のサンプル コードの `STORAGEACCOUNTNAME` を自分のストレージ アカウントの名前に置き換えて `OnBeforeResponse` 関数内に貼り付けます。 サンプルによっては、`HelloWorld` を、ダウンロードするテスト ファイルの名前 (または `sampleFile` などのプレフィックス) に置き換えなければならない場合があります。 この新しいコードは、すぐに実行されないようにコメント アウトされています。

完了したら、 **[File]\(ファイル\)** 、 **[Save]\(保存\)** の順に選択して変更を保存します。 ScriptEditor ウィンドウは、次の手順で使用するため、開いたままにしておきます。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![カスタマイズしたルールを貼り付ける](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>アプリケーションの開始と一時停止

[前のチュートリアル][previous-tutorial]の手順に従ってサンプルを起動し、テスト ファイルをダウンロードします。それがプライマリ ストレージからダウンロードされていることを確認します。 ターゲット プラットフォームに応じて、サンプルを手動で一時停止するか、プロンプトで待機できます。

### <a name="simulate-failure"></a>エラーをシミュレートする

アプリケーションが一時停止している間に、Fiddler に戻り、`OnBeforeResponse` 関数内に保存したカスタム ルールのコメントを解除します。 **[ファイル]** と **[保存]** を選択して、ルールが有効になるように変更を保存します。 このコードでは、RA-GRS ストレージ アカウントに対する要求を探し、パスにサンプル ファイルの名前が含まれている場合は、応答コード `503 - Service Unavailable` を返します。

サンプルを実行中のウィンドウで、アプリケーションを再開するか適切なキーを押してサンプル ファイルをダウンロードします。それがセカンダリ ストレージからダウンロードされていることを確認します。 その後、サンプルをもう一度一時停止するか、プロンプトで待機できます。

### <a name="simulate-primary-endpoint-restoration"></a>プライマリ エンドポイントの復元をシミュレートする

Fiddler で、カスタム ルールを削除するか、もう一度コメント アウトします。 **[ファイル]** と **[保存]** を選択して、ルールが有効ではなくなったことを確認します。

サンプルを実行中のウィンドウで、アプリケーションを再開するか適切なキーを押してサンプル ファイルをダウンロードします。それが再びプライマリ ストレージからダウンロードされていることを確認します。 この後、サンプルを終了できます。

## <a name="next-steps"></a>次の手順

シリーズの第 2 部では、失敗をシミュレートして読み取りアクセス geo 冗長ストレージをテストする方法について学習しました。

RA-GRS ストレージのしくみとそれに関連するリスクの詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [RA-GRS を使用した HA アプリの設計](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
