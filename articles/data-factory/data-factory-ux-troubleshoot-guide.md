---
title: Azure Data Factory UX のトラブルシューティング
description: Azure Data Factory UX の問題をトラブルシューティングする方法について説明します。
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382839"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Azure Data Factory UX の問題のトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory UX の一般的なトラブルシューティング方法について説明します。

## <a name="adf-ux-not-loading"></a>ADF UX が読み込まれない

> [!NOTE]
> Azure Data Factory UX では、Microsoft Edge と Google Chrome が正式にサポートされています。 他の Web ブラウザーを使用すると、予期しない、またはドキュメントに記載されていない動作が発生する可能性があります。

### <a name="third-party-cookies-blocked"></a>サードパーティの Cookie がブロックされた

ADF UX では、ブラウザーの Cookie を使用してユーザー セッションを維持し、対話型の開発と監視のエクスペリエンスを実現します。 シークレット セッションを使用しているか、広告ブロック機能が有効になっていると、ブラウザーでサードパーティの Cookie がブロックされる可能性があります。 サードパーティの Cookie をブロックすると、空白のページ (https://adf.azure.com/accesstoken.html ) へリダイレクトされる、またはサードパーティの Cookie がブロックされていることを示す警告メッセージが表示されるなど、ポータルの読み込み時に問題が発生する可能性があります。 この問題を解決するには、次の手順に従って、ブラウザーでサードパーティの Cookie オプションを有効にします。

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>すべての Cookie を許可する

1. ブラウザーで **chrome://settings/cookies** にアクセスします。
1. **[Allow all cookies]\(Cookie をすべて許可する\)** オプションを選択します。 

    ![Chrome で Cookie をすべて許可する](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX のみに Cookie の使用を許可する
すべての Cookie を許可しない場合は、必要に応じて ADF UX のみを許可することもできます。
1. **chrome://settings/cookies** にアクセスします。
1. **[常に Cookie を使用できるサイト]** オプションの下にある **[追加]** を選択します。 

    ![Chrome の許可されたサイトに ADF UX を追加する](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. **adf.azure.com** サイトを追加し、 **[すべての Cookie]** オプションをオンにして保存します。 

    ![ADF UX サイトからのすべての Cookie を許可する](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

### <a name="microsoft-edge"></a>Microsoft Edge

1. ブラウザーで **edge://settings/content/cookies** にアクセスします。
1. **[Cookie データの保存と読み取りをサイトに許可する]** が有効になっていて、 **[サードパーティの Cookie をブロックする]** オプションが無効になっていることを確認します。 

    ![Edge で Cookie をすべて許可する](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX のみに Cookie の使用を許可する

すべての Cookie を許可しない場合は、必要に応じて ADF UX のみを許可することもできます。

1. **edge://settings/content/cookies** にアクセスします。
1. **[許可]** セクションの **[追加]** を選択して、**adf.azure.com** サイトを追加します。 

    ![Edge の許可されたサイトに ADF UX を追加する](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

## <a name="connection-failed-on-adf-ux"></a>ADF UX で接続が失敗した

**[Test Connection]** \(テスト接続\) や **[プレビュー]** などをクリックした後に、次のスクリーンショットのように、ADF UX で "接続に失敗しました" というエラーが表示されることがあります。

![接続できなかった](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

この場合は、最初にお使いのブラウザーで InPrivate ブラウズ モードで同じ操作を試します。

これでも動作しない場合は、ブラウザーで F12 キーを押し、**開発者ツール** を開きます。 **[ネットワーク]** タブに移動し、 **[Disable Cache]\(キャッシュを無効にする\)** をオンにして、失敗した操作を再試行して、(赤の) 失敗した要求を探します。

![失敗した要求](media/data-factory-ux-troubleshoot-guide/failed-request.png)

次に、失敗した要求の **[要求 URL]** の **ホスト名** (この場合は **dpnortheurope.svc.datafactory.azure.com**) を探します。

お使いのブラウザーのアドレス バーに、**ホスト名** を直接入力します。 ブラウザーに 404 と表示される場合、これは通常、お使いのクライアント側には問題はなく、ADF サービス側に問題があることを意味します。 サポート チケットを、ADF UX のエラー メッセージの **アクティビティ ID** と共に提出します。

![リソースが見つかりません](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

ブラウザーに以下のようなエラーが表示される場合は、これは通常クライアント側に問題があることを意味します。 さらにトラブルシューティングを続けます。

![クライアント側のエラー](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

**[コマンド プロンプト]** を開き、「**nslookup dpnortheurope.svc.datafactory.azure.com**」と入力します。 通常、次のような応答があります。

![コマンド応答 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

通常の DNS 応答が表示された場合は、このホスト名への HTTPS 接続がブロックされているかどうか、ファイアウォール設定を自分のローカルの IT サポートに問い合わせます。 問題を解決できない場合は、ADF UX のエラー メッセージの **アクティビティ ID** と共にサポート チケットを提出します。

これ以外のものが表示される場合は、通常、お使いの DNS サーバーでの DNS 名の解決で問題があることを意味します。 通常、ISP (インターネット サービス プロバイダー) または DNS (たとえば、Google DNS 8.8.8.8) を変更してみると解決できます。 これでも問題が解決しない場合は、さらに「**nslookup datafactory.azure.com**」と「**nslookup azure.com**」を試して、自分の DNS 解決がどのレベルで失敗しているかを確認し、トラブルシューティングのためにこの全情報を、自分のローカルの IT サポートまたは自分の ISP に送信できます。 問題が Microsoft 側にあるとされた場合は、ADF UX のエラー メッセージの **アクティビティ ID** と共にサポート チケットを提出します。

![コマンド応答 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>データセット内にリンクされたサービスの種類を変更する

ファイル形式のデータセットは、ファイルベースのすべてのコネクタで使用できます。たとえば、Parquet データセットを Azure BLOB または Azure Data Lake Storage Gen2 で構成できます。 各コネクタにより、アクティビティ上で、別のアプリ モデルを使用して、データ ストアのさまざまなセットに関連する設定がサポートされることにご注意ください。 

ADF 作成 UI では、アクティビティ (Copy、Lookup、GetMetadata、Delete アクティビティなど) でファイル形式のデータセットを使用し、データセットで現在とは異なる種類のリンクされたサービスを示す (ファイルシステムから ADLS Gen2 へ切り替えるなど) と、次の警告メッセージが表示されます。 これがクリーンなスイッチであることを確認するために、このデータセットを参照するパイプラインとアクティビティは、ユーザーの合意の下で、新しい種類も使用されるように変更されます。また、新しい種類と互換性のない既存のデータ ストア設定は、適用されなくなると消去されます。

各コネクタに対してサポートされているデータ ストア設定の詳細については、対応するコネクタに関する記事から [コピー アクティビティのプロパティ] に進み、詳細なプロパティ リストを確認してください。 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[File System](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、[SFTP](connector-sftp.md) を参照してください。

![警告メッセージ](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

* [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
* [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)