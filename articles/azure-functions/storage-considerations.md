---
title: Azure Functions のストレージに関する考慮事項
description: Azure Functions のストレージ要件と、格納済みデータの暗号化について説明します。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aefd9a35235a09d94973f383603349f6862bbdd9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318183"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions のストレージに関する考慮事項

Azure Functions では、Function App インスタンスを作成するときに Azure ストレージ アカウントが必要になります。 次のストレージ サービスは、お使いの Function App によって利用できます。


|ストレージ サービス  | 機能の使用法  |
|---------|---------|
| [Azure BLOB Storage](../storage/blobs/storage-blobs-introduction.md)     | バインドの状態と関数キーを管理します。  <br/>[Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)からも使用されます。 |
| [Azure Files](../storage/files/storage-files-introduction.md)  | [従量課金プラン](functions-scale.md#consumption-plan)や [Premium プラン](functions-scale.md#premium-plan)で、Function App コードを格納して実行するために使用されるファイル共有。 |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。       |

> [!IMPORTANT]
> 従量課金/Premium ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File ストレージに保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 これは、Functions ではトリガーの管理や関数実行のログ記録などの操作に Azure Storage を使用しているためです。 一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントには、BLOB 専用のストレージ アカウント、Azure Premium Storage、ZRS レプリケーションを使用する汎用ストレージ アカウントが含まれます。 これらのサポートされていないアカウントは、Function App の作成時に [ストレージ アカウント] ブレードから除外されます。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#core-storage-services)」を参照してください。 

お使いの Function App で既存のストレージ アカウントを使用することは可能ですが、必ずこれらの要件を満たしている必要があります。 Function App の作成フローの一部として作成されたストレージ アカウントでは、これらのストレージ アカウント要件を満たしていることが保証されます。  

## <a name="storage-account-guidance"></a>ストレージ アカウントに関するガイダンス

すべての Function App には、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、Function App は実行されません。 ストレージ関連の問題をトラブルシューティングするには、[ストレージ関連の問題をトラブルシューティングする方法](functions-recover-storage-account.md)に関する記事を参照してください。 Function App によって使用されるストレージ アカウントには、次の追加の考慮事項が適用されます。

### <a name="storage-account-connection-setting"></a>ストレージ アカウント接続の設定

ストレージ アカウント接続は、[AzureWebJobsStorage アプリケーション設定](./functions-app-settings.md#azurewebjobsstorage)の中で管理されます。 

ストレージ キーを再生成する場合は、ストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](../storage/common/storage-account-create.md)をご覧ください。

### <a name="shared-storage-accounts"></a>共有のストレージ アカウント

複数の Function App では、同じストレージ アカウントを問題なく共有できます。 たとえば、Visual Studio では、Azure ストレージ エミュレーターを使用して複数のアプリを開発できます。 この場合、エミュレーターは単一のストレージ アカウントのように動作します。 お使いの Function App で使用されているものと同じストレージ アカウントは、アプリケーション データを格納するためにも使用できます。 ただし、運用環境では、この手法が常に適切であるとは限りません。

### <a name="optimize-storage-performance"></a>ストレージ パフォーマンスの最適化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>ストレージ データの暗号化

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="mount-file-shares-linux"></a>ファイル共有をマウントする (Linux)

既存の Azure Files 共有を Linux Function App にマウントすることができます。 Linux Function App に共有をマウントすることにより、既存の機械学習モデルや、関数内のその他のデータを活用できます。 既存の共有を Linux Function App にマウントするには、[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) コマンドを使用できます。 

このコマンドで、`share-name` は既存の Azure Files 共有の名前で、`custom-id` は、Function App にマウントされたときに共有を一意に定義する任意の文字列にすることができます。 また、`mount-path` は、Function App で共有にアクセスするためのパスです。 `mount-path` は、`/dir-name` の形式にする必要があり、`/home` で開始することはできません。

完全な例については、[Python Function App の作成と Azure Files 共有のマウント](scripts/functions-cli-mount-files-storage-linux.md)に関する記事にあるスクリプトを参照してください。 

現時点では、`AzureFiles` の `storage-type` のみがサポートされています。 指定された Function App にマウントできるのは 5 つの共有のみです。 ファイル共有をマウントすると、コールド スタートの時間が少なくとも 200 ミリ秒から 300 ミリ秒長くなる可能性があり、ストレージ アカウントが別のリージョンにある場合はさらに長くなる可能性があります。

マウントされた共有は、指定された `mount-path` の関数コードで使用できます。 たとえば、`mount-path` が `/path/to/mount` の場合、次の Python の例に示すように、ファイル システム API でターゲット ディレクトリにアクセスできます。

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>次のステップ

Azure Functions ホスティングのオプションについて確認してください。

> [!div class="nextstepaction"]
> [Azure Functions のスケールとホスティング](functions-scale.md)
