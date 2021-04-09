---
title: Azure Functions のストレージに関する考慮事項
description: Azure Functions のストレージ要件と、格納済みデータの暗号化について説明します。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: c4ffb622482585e35337caf8e43b69e0f3b0385c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517265"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Functions のストレージに関する考慮事項

Azure Functions では、Function App インスタンスを作成するときに Azure ストレージ アカウントが必要になります。 次のストレージ サービスは、お使いの Function App によって利用できます。


|ストレージ サービス  | 機能の使用法  |
|---------|---------|
| [Azure BLOB Storage](../storage/blobs/storage-blobs-introduction.md)     | バインドの状態と関数キーを管理します。  <br/>[Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)からも使用されます。 |
| [Azure Files](../storage/files/storage-files-introduction.md)  | [従量課金プラン](consumption-plan.md)や [Premium プラン](functions-premium-plan.md)で、関数アプリ コードを格納して実行するために使用されるファイル共有。 |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  [Durable Functions 上のタスク ハブ](durable/durable-functions-task-hubs.md)から使用されます。       |

> [!IMPORTANT]
> 従量課金/Premium ホスティング プランを使用する場合、関数コード ファイルおよびバインディング構成ファイルは、メイン ストレージ アカウントの Azure File Storage に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツは削除され、復元できません。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 これは、Functions ではトリガーの管理や関数実行のログ記録などの操作に Azure Storage を使用しているためです。 一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントには、BLOB 専用ストレージ アカウントと Azure Premium Storage が含まれています。

ストレージ アカウントの種類の詳細については、「[Azure Storage サービスの概要](../storage/common/storage-introduction.md#core-storage-services)」を参照してください。 

お使いの Function App で既存のストレージ アカウントを使用することは可能ですが、必ずこれらの要件を満たしている必要があります。 Azure portal で、Function App の作成フローの一部として作成されたストレージ アカウントでは、これらのストレージ アカウント要件を満たしていることが保証されます。 ポータルでは、関数アプリの作成中に既存のストレージ アカウントを選択すると、サポートされていないアカウントが除外されます。 このフローでは、作成している関数アプリと同じリージョンにある既存のストレージ アカウントのみを選択できます。 詳細については、「[ストレージ アカウントの場所](#storage-account-location)」を参照してください。

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>ストレージ アカウントに関するガイダンス

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、関数アプリは実行されません。 ストレージ関連の問題をトラブルシューティングするには、[ストレージ関連の問題をトラブルシューティングする方法](functions-recover-storage-account.md)に関する記事を参照してください。 関数アプリによって使用されるストレージ アカウントには、次の追加の考慮事項が適用されます。

### <a name="storage-account-location"></a>ストレージ アカウントの場所

最適なパフォーマンスを得るには、関数アプリで同じリージョンのストレージ アカウントを使用する必要があります。これにより、待ち時間が短縮されます。 Azure portal によって、このベスト プラクティスが適用されます。 何らかの理由で、関数アプリとは異なるリージョンでストレージ アカウントを使用する必要がある場合は、ポータルの外部で関数アプリを作成する必要があります。 

### <a name="storage-account-connection-setting"></a>ストレージ アカウント接続の設定

ストレージ アカウント接続は、[AzureWebJobsStorage アプリケーション設定](./functions-app-settings.md#azurewebjobsstorage)の中で管理されます。 

ストレージ キーを再生成する場合は、ストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](../storage/common/storage-account-create.md)をご覧ください。

### <a name="shared-storage-accounts"></a>共有のストレージ アカウント

複数の Function App では、同じストレージ アカウントを問題なく共有できます。 たとえば、Visual Studio では、Azure ストレージ エミュレーターを使用して複数のアプリを開発できます。 この場合、エミュレーターは単一のストレージ アカウントのように動作します。 お使いの Function App で使用されているものと同じストレージ アカウントは、アプリケーション データを格納するためにも使用できます。 ただし、運用環境では、この手法が常に適切であるとは限りません。

### <a name="optimize-storage-performance"></a>ストレージ パフォーマンスの最適化

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>ストレージ データの暗号化

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>リージョンのデータ所在地

すべての顧客データを 1 つのリージョン内に留める必要がある場合、関数アプリに関連付けられているストレージ アカウントは、[リージョン内冗長性](../storage/common/storage-redundancy.md)が与えられたアカウントにする必要があります。 リージョン内で冗長性を持つストレージ アカウントは、[Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) でも使用される必要があります。

プラットフォームで管理されるその他の顧客データは、内部負荷分散型の App Service Environment (ASE) でホストしているとき、そのリージョン内にのみ格納されます。 詳細については、[ASE のゾーン冗長性](../app-service/environment/zone-redundancy.md#in-region-data-residency)に関するページを参照してください。

## <a name="mount-file-shares"></a>ファイル共有をマウントする

"_この機能は現在、Linux で実行されている場合にのみ使用できます。_ " 

既存の Azure Files 共有を Linux 関数アプリにマウントすることができます。 Linux 関数アプリに共有をマウントすることにより、既存の機械学習モデルや、関数内のその他のデータを活用できます。 既存の共有を Linux 関数アプリにマウントするには、[`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) コマンドを使用できます。 

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
