---
title: 'クイックスタート: Go を使用してイベントを送受信する - Azure Event Hubs'
description: 'クイックスタート: この記事では、Azure Event Hubs からイベントを送信する Go アプリケーションを作成するためのチュートリアルを提供します。'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 59cd0d757108e7579ce389d216b0ee4d569e12fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87002456"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>クイックスタート: Go を使用して Event Hubs との間でイベントを送受信する
Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Go アプリケーションを作成し、イベント ハブとの間でイベントを送受信する方法について説明します。 

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Go がローカルにインストールされていること。 必要に応じて、[こちらの手順](https://golang.org/doc/install)に従います。
- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。
- **Event Hubs 名前空間とイベント ハブを作成する**。 [Azure portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。

## <a name="send-events"></a>送信イベント
このセクションでは、イベント ハブにイベントを送信する Go アプリケーションの作成方法を説明します。 

### <a name="install-go-package"></a>Go パッケージをインストールする

`go get` または `dep` を使用して、Event Hubs 用の Go パッケージを取得します。 次に例を示します。

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>コード ファイルにパッケージをインポートする

Go パッケージをインポートするには、次のコード例を使用します。

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>サービス プリンシパルの作成

「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」の手順に従って、新しいサービス プリンシパルを作成します。 次の名前を使用して指定された資格情報を環境内に保存します。 Azure SDK for Go と Event Hubs パッケージは、どちらもこれらの変数名を検索するために事前に構成されています。

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

次に、これらの資格情報を使用する Event Hubs クライアントに対して、認証プロバイダーを作成します。

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs クライアントを作成する

次のコードは、Event Hubs クライアントを作成します。

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>メッセージを送信するコードを記述する

次のスニペットでは、(1) を使用してターミナルから対話形式でメッセージを送信する、または (2) を使用してプログラム内でメッセージを送信します。

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>追加

イベント ハブのパーティションの ID を取得します。

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

アプリケーションを実行してイベントをイベント ハブに送信します。 

お疲れさまでした。 メッセージをイベント ハブに送信しました。

## <a name="receive-events"></a>受信イベント

### <a name="create-a-storage-account-and-container"></a>Storage アカウントとコンテナーの作成

パーティションのリースやイベント ストリーム内のチェックポイントなどの状態は、Azure Storage コンテナーを使用して受信者間で共有されます。 Go SDK を使用してストレージ アカウントとコンテナーを作成できますが、「[Azure ストレージ アカウントについて](../storage/common/storage-account-create.md)」の手順に従って作成することもできます。

Go SDK を使用して Storage の成果物を作成するためのサンプルは、[Go のサンプル リポジトリ](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)と、このチュートリアルに対応するサンプルで入手できます。

### <a name="go-packages"></a>Go パッケージ

メッセージを受信するには、`go get` または `dep` 使用して Event Hubs 用の Go パッケージを取得します。

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>コード ファイルにパッケージをインポートする

Go パッケージをインポートするには、次のコード例を使用します。

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>サービス プリンシパルの作成

「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」の手順に従って、新しいサービス プリンシパルを作成します。 次の名前で指定された資格情報を環境に保存します。両方の Azure SDK for Go と Event Hubs パッケージは両方ともこれらの変数名を検索するように事前構成されます。

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

次に、これらの資格情報を使用する Event Hubs クライアントに対して、認証プロバイダーを作成します。

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>メタデータ構造体の取得

Azure Go SDK を使用して、Azure 環境に関するメタデータを含む構造体を取得します。 その後の操作で、この構造体を使用して正しいエンドポイントを見つけます。

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>資格情報ヘルパーを作成する 

前の Azure Active Directory (AAD) の資格情報を使用して Storage 用に Shared Access Signature (SAS) 資格情報を作成する資格情報ヘルパーを作成します。 最後のパラメーターは、このコンストラクターに以前に使用したのと同じ環境変数を使用するように指示します。

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Check pointer と Leaser を作成する 

特定の受信者へのパーティションのリースを担当する **Leaser** と、他の受信者が適切なオフセットから読み取りを開始できるように、メッセージ ストリームへのチェックポイントの書き込みを担当する **Check pointer** を作成します。

現時点では、同じ Storage コンテナーを使用してリースとチェックポイントの両方を管理する、1 つの **StorageLeaserCheckpointer** を使用できます。 **StorageLeaserCheckpointer** がコンテナーに正しくアクセスするためには、ストレージ アカウントとコンテナー名の他に、前の手順で作成した資格情報と Azure 環境の構造体が必要です。

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>イベント プロセッサ ホストを構築する

これで、次のような、EventProcessorHost の構築に必要な部分が揃いました。 前述したように、同じ **StorageLeaserCheckpointer** が、Leaser と Check pointer の両方に使用されます。

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>ハンドラーを作成する 

次に、ハンドラーを作成し、それをイベント プロセッサ ホストに登録します。 ホストは、起動すると、このハンドラーとその他の指定されたハンドラーを受信メッセージに適用します。

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>メッセージを受信するコードを記述する

設定のたびに、`Start(context)` を使用してイベント プロセッサ ホストを起動して永続的に実行させることも、`StartNonBlocking(context)` を使用してメッセージが使用可能な間だけ実行することもできます。

このチュートリアルでは、次のように起動して実行します。`StartNonBlocking` を使用した例については、GitHub のサンプルを参照してください。

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
