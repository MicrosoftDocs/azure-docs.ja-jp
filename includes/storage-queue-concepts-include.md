---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028064"
---
## <a name="what-is-queue-storage"></a>Queue storage とは

Azure Queue Storage は、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。 Queue storage は、多くの場合、非同期的な処理用に作業のバックログを作成するために使用されます。

## <a name="queue-service-concepts"></a>Queue サービスの概念

Azure Queue サービスには、次のコンポーネントが含まれます。

![Azure Queue サービス コンポーネント](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **ストレージ アカウント:** Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの詳細については、「[ストレージ アカウントの概要](../articles/storage/common/storage-account-overview.md)」を参照してください。
* **キュー:** キューは、メッセージのセットを格納します。 すべてのメッセージはキューに 格納されている必要があります。 キュー名は小文字で入力する必要があります。 キューの名前付け規則については、「 [Naming Queues and Metadata (キューとメタデータの名前付け規則)](/rest/api/storageservices/Naming-Queues-and-Metadata)」を参照してください。
* **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。 メッセージをキューで保持できる最長時間は 7 日間です。 バージョン 2017-07-29 以降では、最大有効期間を任意の正の数にすることができます。また、-1 は、メッセージが期限切れにならないことを示します。 このパラメーターを省略すると、既定の有効期間は 7 日になります。
* **URL 形式**: キューは、次の URL 形式を使用してアドレス指定できます:   http://`<storage account>`.queue.core.windows.net/`<queue>`

    次の URL を使用すると、図のいずれかのキューをアドレス指定できます。

    `http://myaccount.queue.core.windows.net/incoming-orders`
