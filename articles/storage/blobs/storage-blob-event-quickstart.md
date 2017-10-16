---
title: "Azure Blob Storage のイベントをカスタム Web エンドポイントにルーティングする (プレビュー) | Microsoft Docs"
description: "Blob Storage のイベントをサブスクライブするには、Azure Event Grid を使用します。"
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/18/2017
ms.topic: article
ms.service: storage
ms.openlocfilehash: 1a489f199bcc955fd14e82d16670d854305b00c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-preview"></a>Blob Storage のイベントをカスタム Web エンドポイントにルーティングする (プレビュー)

Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、Azure CLI を使用して Blob Storage のイベントをサブスクライブし、イベントをトリガーして結果を表示します。 

> [!IMPORTANT]
> このチュートリアルの作業を行うには、Blob Storage イベント プレビューへの登録が必要となります。  プレビュー プログラムの詳細については、[こちら](storage-blob-event-overview.md#join-the-preview)を参照してください。

通常、webhook や Azure 関数など、イベントに応答するエンドポイントが、イベントの送信先になります。 この記事では、紹介している例を単純化するために、メッセージをただ収集するだけの URL に対してイベントを送信します。 この URL は、[RequestBin](https://requestb.in/) というオープン ソースのサードパーティ ツールを使用して作成します。

> [!NOTE]
> **RequestBin** は、高いスループットが要求される用途を意図したオープン ソース ツールではありません。 ここでは、あくまでデモンストレーションを目的としてこのツールを使用しています。 一度に複数のイベントをプッシュすると、一部のイベントがツールから見えない場合があります。

この記事の手順の最後に、イベント データがエンドポイントに送信済みであることを確認できます。

![イベント データ](./media/storage-blob-event-quickstart/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

この記事で、CLI をローカルにインストールして使用する場合、最新バージョンの Azure CLI (2.0.14 以降) が実行されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 

次の例では、`<resource_group_name>` という名前のリソース グループを場所 *westcentralus* に作成します。  `<resource_group_name>` を、リソース グループの一意の名前に置き換えます。

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-blob-storage-account"></a>BLOB ストレージ アカウントの作成

Azure Storage を使用するには、ストレージ アカウントが必要です。  Blob Storage のイベントは現在、BLOB ストレージ アカウントでのみ利用できます。

BLOB ストレージ アカウントとは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 BLOB ストレージ アカウントは、既存の汎用ストレージ アカウントと同様で、現在使用されているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。

> [!NOTE]
> プレビュー リリースでは、Blob Storage のイベントの利用は場所が **westcentralus** のストレージ アカウントに限られます。

`<storage_account_name>` は、ご利用のストレージ アカウントの一意の名前に、`<resource_group_name>` は、先ほど作成したリソース グループに置き換えてください。

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

BLOB ストレージ アカウントからのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 ここではイベントに応答するコードを作成する代わりに、皆さんが確認できるように、メッセージを収集するエンドポイントを作成します。 RequestBin は、エンドポイントを作成してそこに送信された要求を確認することができるオープン ソースのサードパーティ ツールです。 [RequestBin](https://requestb.in/) にアクセスし、**[Create a RequestBin]\(RequestBin の作成\)** をクリックします。  Bin URL をコピーしてください。トピックをサブスクライブするときにこの URL が必要になります。

## <a name="subscribe-to-your-blob-storage-account"></a>BLOB ストレージ アカウントのサブスクライブ

どのイベントを追跡するかは、トピックをサブスクライブすることによって Event Grid に伝えます。次の例では、作成した BLOB ストレージ アカウントをサブスクライブし、RequestBin からの URL をイベント通知のエンドポイントとして渡しています。 `<event_subscription_name>` は、実際のイベント サブスクリプションの一意の名前に、`<URL_from_RequestBin>` は、前のセクションで得た値に置き換えてください。 サブスクライブ時にエンドポイントを指定することによって、そのエンドポイントに対するイベントのルーティングが Event Grid によって行われます。 `<resource_group_name>` と `<storage_account_name>` には、先ほど作成した値を使用します。 

```azurecli-interactive
az eventgrid resource event-subscription create \
--endpoint <URL_from_RequestBin> \
--name <event_subscription_name> \
--provider-namespace Microsoft.Storage \
--resource-type storageAccounts \
--resource-group <resource_group_name> \
--resource-name <storage_account_name>
```

## <a name="trigger-an-event-from-blob-storage"></a>Blob Storage からのイベントのトリガー

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 まず、ストレージ アカウントの名前とキーを構成し、コンテナーを作成したら、ファイルを作成してアップロードします。 ここでも、`<storage_account_name>` と `<resource_group_name>` には、先ほど作成した値を使用します。

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

以上でイベントがトリガーされ、そのメッセージが、Event Grid によってサブスクライブ時に構成したエンドポイントに送信されました。 先ほど作成した RequestBin URL にブラウザーでアクセスします。 または、開いている RequestBin ブラウザーで、最新の情報に更新するボタンをクリックしてください。 先ほど送信したイベントが表示されます。 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  }
}]

```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
引き続きこのストレージ アカウントとイベント サブスクリプションを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 引き続き使用する予定がない場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

`<resource_group_name>` は、先ほど作成したリソース グループに置き換えます。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>次のステップ

トピックを作成し、イベントをサブスクライブする方法がわかったら、Blob Storage のイベントについて、また Event Grid でできることについて、さらに情報を収集しましょう。

- [Blob Storage のイベント処理](storage-blob-event-overview.md)
- [Event Grid について](../../event-grid/overview.md)
