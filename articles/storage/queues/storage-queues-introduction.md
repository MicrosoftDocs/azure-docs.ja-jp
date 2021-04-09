---
title: Azure Queue Storage の概要 - Azure Storage
description: 多数のメッセージを格納するためのサービスである Azure Queue Storage の概要を参照してください。 Queue Storage サービスには、URL 形式、ストレージ アカウント、キュー、およびメッセージが含まれます。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590581"
---
# <a name="what-is-azure-queue-storage"></a>Azure Queue Storage とは

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 メッセージには、HTTP または HTTPS を使用して、認証された呼び出しを介して世界中のどこからでもアクセスできます。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。

## <a name="queue-storage-concepts"></a>Queue Storage の概念

Queue Storage には次の構成要素があります。

![ストレージ アカウント、キュー、メッセージの関係を示す図。](./media/storage-queues-introduction/queue1.png)

- **URL 形式:** キューは、次の URL 形式を使用してアドレス指定できます。

  `https://<storage account>.queue.core.windows.net/<queue>`

  次の URL を使用すると、図のいずれかのキューをアドレス指定できます。

  `https://myaccount.queue.core.windows.net/images-to-download`

- **ストレージ アカウント:** Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、「[Standard Storage アカウントのスケーラビリティとパフォーマンスのターゲット](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」を参照してください。

- **キュー:** キューは、メッセージのセットを格納します。 キュー名は小文字で指定する **必要があります**。 キューの名前付けについては、「[キューとメタデータの名前付け](/rest/api/storageservices/naming-queues-and-metadata)」を参照してください。

- **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。 バージョン 2017-07-29 より前では、許可される最大有効期間は 7 日間です。 バージョン 2017-07-29 以降では、最大有効期間を任意の正の数にすることができます。また、-1 は、メッセージが期限切れにならないことを示します。 このパラメーターを省略すると、既定の有効期間は 7 日になります。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの作成](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [.NET を使用して Queue Storage を使用する](storage-dotnet-how-to-use-queues.md)
- [Java を使用して Queue Storage を使用する](storage-java-how-to-use-queue-storage.md)
- [Python を使用して Queue Storage を使用する](storage-python-how-to-use-queue-storage.md)
- [Node.js を使用して Queue Storage を使用する](storage-nodejs-how-to-use-queues.md)
