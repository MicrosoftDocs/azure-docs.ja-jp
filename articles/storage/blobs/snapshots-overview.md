---
title: BLOB のスナップショット
titleSuffix: Azure Storage
description: BLOB の読み取り専用スナップショットを作成して、特定の時点での BLOB データをバックアップする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/19/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4c6c2774e0d71ec33449565efab797c040aa264f
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640601"
---
# <a name="blob-snapshots"></a>BLOB のスナップショット

スナップショットは、ある時点で作成された読み取り専用の BLOB です。

> [!NOTE]
> BLOB バージョン管理 (プレビュー) は、BLOB の以前のバージョンを維持するための代替手段になります。 詳細については、「[BLOB バージョン管理 (プレビュー)](versioning-overview.md)」を参照してください。

## <a name="about-blob-snapshots"></a>BLOB スナップショットについて

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

スナップショットが作成された日時を示す **DateTime** 値が BLOB の URI に追加される点を除き、BLOB のスナップショットはベース BLOB とまったく同じです。 たとえば、ページ BLOB の URI が `http://storagesample.core.blob.windows.net/mydrives/myvhd` の場合、スナップショットの URI は `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` のようになります。

> [!NOTE]
> すべてのスナップショットがベース BLOB の URI を共有します。 ベース BLOB とスナップショットの唯一の違いは、追加される **DateTime** 値です。
>

BLOB に対するスナップショットの数に制限はありません。 スナップショットは、個別に、またはベース BLOB の [Delete Blob](/rest/api/storageservices/delete-blob) 操作の一部として明示的に削除されるまで保持されます。 ベース BLOB に関連付けられたスナップショットを列挙して、現在のスナップショットを追跡できます。

BLOB のスナップショットを作成すると、BLOB のシステム プロパティが同じ値でスナップショットにコピーされます。 また、スナップショットの作成時に別のメタデータを指定しない限り、ベース BLOB のメタデータもスナップショットにコピーされます。 作成したスナップショットの読み取り、コピー、削除はできますが、変更はできません。

ベース BLOB に関連付けられているリースはスナップショットにコピーされません。 スナップショットはリースを取得することはできません。

VHD ファイルは、VM ディスクの現時点の情報と状態の格納に使用します。 ディスクを VM 内から切断するか、VM をシャットダウンしてから、その VHD ファイルのスナップショットを撮ることができます。 このスナップショットを後に使用して、その時点での VHD ファイルを取得して VM を再作成することができます。

## <a name="understand-how-snapshots-accrue-charges"></a>スナップショットの課金方法について

スナップショット (BLOB の読み取り専用コピー) を作成すると、別途データ ストレージ料金がアカウントに課金される場合があります。 不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

### <a name="important-billing-considerations"></a>課金に関する重要な考慮事項

スナップショットの作成時に考慮する必要のある重要なポイントを以下に示します。

- ストレージ アカウントには、一意のブロックまたはページに対する料金が発生します。そのブロックまたはページが BLOB とスナップショットのどちらに含まれているかは関係ありません。 BLOB に関連付けられているスナップショットについてアカウントに追加料金が発生するのは、そのベースになっている BLOB を更新したときです。 ベース BLOB を更新すると、BLOB はスナップショットから分化します。 この場合、各 BLOB またはスナップショットの一意のブロックまたはページに対して課金されます。
- ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。 そのブロックに割り当てられているブロック ID とデータが、スナップショット側と同じであっても変わりません。 ブロックが再度コミットされると、そのブロックはスナップショット内の対応するブロックから分化し、そのデータに対する料金が発生します。 これは、まったく同じデータでページ BLOB 内のページを更新した場合にも当てはまります。
- BLOB の内容全体を上書きするメソッドを呼び出してブロック BLOB を更新すると、BLOB 内のすべてのブロックが置き換えられます。 その BLOB に関連付けられているスナップショットがある場合、ベース BLOB とスナップショットのすべてのブロックが分化し、両方の BLOB のすべてのブロックが課金対象となります。 これは、ベース BLOB 内のデータとスナップショット内のデータとがまったく同一であっても変わりません。
- 2 つのブロックに格納されているデータが同一であるかどうかを判断する方法は、Azure Blob service には用意されていません。 アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。 料金は一意のブロックに対して発生するため、スナップショットがある BLOB を更新すると、一意のブロックが増え、追加料金が発生することを考慮することが重要です。

### <a name="minimize-costs-with-snapshot-management"></a>スナップショット管理によりコストを最小限に抑える

不要な料金を回避するために、スナップショットを慎重に管理することをお勧めします。 次のベスト プラクティスに従えば、スナップショットの記憶域から発生するコストを最小限に抑えられます。

- まったく同じデータで更新する場合も含め、BLOB を更新するときは必ずその BLOB に関連付けられているスナップショットを削除してから作成し直すようにします (アプリケーションの設計上、スナップショットを維持しなければならない場合を除く)。 BLOB のスナップショットを削除してから作成し直すことにより、BLOB とスナップショットの分化を確実に防ぐことができます。
- BLOB のスナップショットを維持する場合は、BLOB を更新するときに BLOB 全体を上書きするメソッドを呼び出さないでください。 代わりに、コストを抑えるために、可能な限り少ない数のブロックを更新してください。

### <a name="snapshot-billing-scenarios"></a>スナップショットの課金シナリオ

ブロック BLOB とそのスナップショットについて料金が発生するしくみを次のシナリオで説明します。

#### <a name="scenario-1"></a>シナリオ 1

シナリオ 1 では、スナップショットが作成されてからベース BLOB は更新されていません。したがって、料金の発生対象は、一意のブロックである 1、2、3 のみです。

![Azure Storage のリソース](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>シナリオ 2

シナリオ 2 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 更新されたのはブロック 3 です。格納されているデータと ID が同じでも、スナップショット内のブロック 3 とは異なります。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>シナリオ 3

シナリオ 3 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、スナップショット側はブロック 3 のままです。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>シナリオ 4

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。 このため、8 つある一意のブロックすべてについての料金がアカウントに課金されます。

![Azure Storage のリソース](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> BLOB 全体を上書きするメソッドを呼び出さずに、個別のブロックを更新してコストを抑えるようにします。

## <a name="next-steps"></a>次のステップ

- [.NET での BLOB スナップショットの作成と管理](snapshots-manage-dotnet.md)
- [増分スナップショットを使用した Azure 非管理 VM ディスクのバックアップ](../../virtual-machines/windows/incremental-snapshots.md)
