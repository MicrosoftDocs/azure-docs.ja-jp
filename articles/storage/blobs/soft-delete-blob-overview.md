---
title: BLOB の論理的な削除
titleSuffix: Azure Storage
description: BLOB の論理的な削除では、お客様のデータを保護して、アプリケーションまたは別のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに簡単に復旧できるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a370a7f04e0e43b96e4a574313c4f24c4990ab6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390359"
---
# <a name="soft-delete-for-blobs"></a>BLOB の論理的な削除

BLOB の論理的な削除では、お客様のデータが誤って変更または削除されないように保護します。 ストレージ アカウントに対して BLOB の論理的な削除が有効になっている場合、そのストレージ アカウント内の BLOB、BLOB バージョン、およびスナップショットは、削除された後も、指定した保持期間中は復旧することができます。

アプリケーションまたは別のストレージ アカウントのユーザーによってデータが誤って変更または削除される可能性がある場合、Microsoft では論理的な削除を有効にすることをお勧めします。 論理的な削除を有効にする方法について詳しくは、[BLOB の論理的な削除の有効化と管理](./soft-delete-blob-enable.md)に関する記事をご覧ください。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>BLOB の論理的な削除について

ストレージ アカウントで BLOB の論理的な削除が有効になっている場合は、オブジェクトが削除された後も、指定したデータ保持期間中は復旧することができます。 この保護の範囲は、上書きされたために消去されたすべての BLOB データ (ブロック BLOB、追加 BLOB、ページ BLOB) に及びます。

次の図は、BLOB の論理的な削除が有効になっている場合に、削除された BLOB を復元する方法を示しています。

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="論理的に削除された BLOB を復元する方法を示す図":::

BLOB の論理的な削除は有効になっているが、BLOB のバージョン管理が有効になっていない場合、既存の BLOB またはスナップショット内のデータが削除されると、上書きされたデータの状態を保存するために、論理的に削除されたスナップショットが生成されます。 指定された保持期間が経過すると、オブジェクトは完全に削除されます。

ストレージ アカウントで BLOB のバージョン管理と BLOB の論理的な削除の両方が有効になっている場合、BLOB を削除すると、論理的に削除されたスナップショットではなく、新しいバージョンが作成されます。 新しいバージョンは論理的に削除されず、論理的な削除の保持期間が過ぎても削除されません。 BLOB の論理的に削除されたバージョンは、[Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を呼び出すことで、保持期間内に復元できます。 BLOB は、後で [Copy Blob](/rest/api/storageservices/copy-blob) 操作を呼び出して、そのいずれかのバージョンから復元できます。 BLOB のバージョン管理と論理的な削除の併用の詳細については、「[BLOB のバージョン管理と論理的な削除](versioning-overview.md#blob-versioning-and-soft-delete)」を参照してください。

論理的に削除されたオブジェクトは、明示的に一覧表示されない限り表示されません。

BLOB の論理的な削除には下位互換性があるため、アプリケーションに変更を加えなくても、この機能が提供する保護を利用することができます。 ただし、[データ復旧](#recovery)には新しい **Undelete Blob** API が導入されています。

BLOB の論理的な削除は、新規と既存の両方の汎用 v2、汎用 v1、BLOB ストレージ アカウントで使用できます。 Standard と Premium の両方の種類のアカウントがサポートされています。 BLOB の論理的な削除は、ホット、クール、アーカイブを含むすべてのストレージ層で使うことができます。 論理的な削除は、内部のページ BLOB であるアンマネージド ディスクに対して使用できますが、マネージド ディスクでは使用できません。

### <a name="configuration-settings"></a>構成設定

新しいアカウントを作成したとき、論理的な削除は既定で無効に設定されます。 既存のストレージ アカウントについても、論理的な削除は既定で無効に設定されます。 ストレージ アカウントでの論理的な削除は、いつでも有効または無効にすることができます。

論理的な削除を有効にするときは、保持期間を構成する必要があります。 リテンション期間は、論理的に削除されたデータが保存されていて復旧に利用できる期間を示します。 明示的に削除されたオブジェクトの場合は、データが削除された時点で保持期間のクロックが開始します。 データ上書き時に論理的な削除機能によって生成される論理的に削除されたバージョンまたはスナップショットの場合は、バージョンまたはスナップショットが生成された時点でクロックが開始します。 保持期間は 1 日から 365 日の範囲で指定できます。

論理的な削除のリテンション期間はいつでも変更できます。 更新された保持期間は、新たに削除されたデータにのみ適用されます。 以前に削除されたデータは、そのデータが削除されたときに構成されていた保持期間に基づいて期限が切れます。 論理的に削除されたオブジェクトの削除を試みても、有効期限に影響はありません。

論理的な削除を無効にしても、この機能が有効になっていたときに保存されていたストレージアカウント内の論理的に削除されたデータには、引き続きアクセスして復旧できます。

### <a name="saving-deleted-data"></a>削除されたデータの保存

論理的な削除は、オブジェクトが削除または上書きされる多くの場合に、データを保持します。

**Put Blob**、**Put Block List**、または **Copy Blob** を使って BLOB が上書きされると、書き込み操作前の BLOB の状態のバージョンまたはスナップショットが自動的に生成されます。 このオブジェクトは、論理的に削除されたオブジェクトが明示的に一覧表示されない限り表示されません。 論理的に削除されたオブジェクトを一覧表示する方法については、「[復旧](#recovery)」セクションをご覧ください。

![Put Blob、Put Block List、Copy Blob を使用して上書きされるとき、BLOB のスナップショットが格納されるしくみを示す図。](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

" *論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。 B0 が B1 で上書きされると、B0 の論理的に削除されたスナップショットが生成されます。B1 が B2 で上書きされると、B1 の論理的に削除されたスナップショットが生成されます。* "

> [!NOTE]  
> 論理的な削除によってコピー操作の上書き保護が行われるのは、コピー先 BLOB のアカウントで機能が有効になっている場合だけです。

> [!NOTE]  
> アーカイブ層の BLOB に対しては、論理的な削除の上書き保護は提供されません。 アーカイブ内の BLOB が任意の層の新しい BLOB で上書きされる場合、上書きされた BLOB は完全に有効期限が切れます。

スナップショットで **Delete Blob** が呼び出されると、そのスナップショットは論理的に削除されたものとしてマークされます。 新しいスナップショットは生成されません。

![Delete Blob の使用時、BLOB のスナップショットが論理削除されるしくみを示す図。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

" *論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。  **Snapshot Blob** が呼び出されると、B0 はスナップショットになり、B1 はアクティブな状態の BLOB です。B0 スナップショットが削除されると、論理的に削除済みとしてマークされます。* "

ベース BLOB (それ自体がスナップショットではないすべての BLOB) で **Delete Blob** を呼び出すと、その BLOB は論理的に削除済みとしてマークされます。 以前の動作との整合性のため、アクティブなスナップショットがある BLOB で **Delete Blob** を呼び出すと、エラーが返されます。 論理的に削除されたスナップショットのある BLOB で **Delete Blob** を呼び出すと、エラーは返されません。 論理的な削除が有効になっているときでも、BLOB とそのすべてのスナップショットを 1 回の操作で削除することができます。 これを行うと、ベース BLOB とスナップショットが論理的に削除済みとしてマークされます。

![Delete Blob がベース BLOB で呼び出されるときの動作を示す図。](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

" *論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。 ここで、 **Delete Blob** を呼び出して、B2 とそれに関連付けられているすべてのスナップショットを削除します。アクティブな BLOB である B2 および関連付けられているすべてのスナップショットが、論理的に削除済みとしてマークされます。* "

> [!NOTE]  
> 論理的に削除された BLOB が上書きされると、書き込み操作前の BLOB の状態の論理的に削除されたスナップショットが、自動的に生成されます。 新しい BLOB は、上書きされた BLOB の階層を継承します。

コンテナーまたはアカウントの削除の場合、または BLOB メタデータおよび BLOB プロパティが上書きされる場合、論理的な削除ではデータは保存されません。 削除からストレージ アカウントを保護するには、Azure Resource Manager を使ってロックを設定できます。 詳しくは、Azure Resource Manager の記事「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」をご覧ください。  コンテナーが誤って削除されないようにするには、ストレージ アカウントに対してコンテナーの論理的な削除を構成します。 詳細については、「[コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)」を参照してください。

次の表では、論理的な削除を有効にしたときに想定される動作の詳細を示します。

| REST API の操作 | リソースの種類 | 説明 | 動作の変更点 |
|--------------------|---------------|-------------|--------------------|
| [削除](/rest/api/storagerp/StorageAccounts/Delete) | Account | ストレージ アカウントおよびそれに含まれるすべてのコンテナーと BLOB を削除します。                           | 変更はありません。 削除されたアカウントのコンテナーと BLOB は復旧できません。 |
| [Delete Container](/rest/api/storageservices/delete-container) | コンテナー | コンテナーおよびそれに含まれるすべての BLOB を削除します。 | 変更はありません。 削除されたコンテナーの BLOB は復旧できません。 |
| [Put Blob](/rest/api/storageservices/put-blob) | ブロック BLOB、追加 BLOB、ページ BLOB | コンテナー内で新しい BLOB を作成するか、既存の BLOB を置き換えます | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB にも適用されますが、それが同じ種類 (ブロック、追加、ページ) の BLOB によって置き換えられる場合に限ります。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。 |
| [Delete Blob](/rest/api/storageservices/delete-blob) | ブロック BLOB、追加 BLOB、ページ BLOB | BLOB または BLOB のスナップショットを削除としてマークします。 BLOB またはスナップショットは、後でガベージ コレクションの間に削除されます。 | BLOB のスナップショットの削除に使った場合、そのスナップショットは論理的に削除済みとしてマークされます。 BLOB の削除に使った場合、その BLOB は論理的に削除済みとしてマークされます。 |
| [Copy Blob](/rest/api/storageservices/copy-blob) | ブロック BLOB、追加 BLOB、ページ BLOB | コピー元の BLOB を、同じストレージ アカウントまたは別のストレージ アカウントのコピー先 BLOB にコピーします。 | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB にも適用されますが、それが同じ種類 (ブロック、追加、ページ) の BLOB によって置き換えられる場合に限ります。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。 |
| [Put Block](/rest/api/storageservices/put-block) | ブロック blob | ブロック BLOB の一部としてコミットされる新しいブロックを作成します。 | アクティブな BLOB にブロックをコミットするために使う場合、変更はありません。 論理的に削除された BLOB にブロックをコミットするために使う場合、新しい BLOB が作成され、論理的に削除された BLOB の状態をキャプチャするためにスナップショットが自動的に生成されます。 |
| [Put Block List](/rest/api/storageservices/put-block-list) | ブロック blob | ブロック BLOB を構成するブロック ID のセットを指定することにより、BLOB をコミットします。 | 既存の BLOB の置き換えに使った場合、呼び出し前の BLOB の状態のスナップショットが自動的に生成されます。 これは、以前に論理的に削除された BLOB にも適用されますが、それがブロック BLOB である場合に限ります。 異なる種類の BLOB で置き換えられる場合は、既存の論理的に削除されたすべてのデータが完全に期限切れになります。 |
| [Put Page](/rest/api/storageservices/put-page) | ページ blob | ページの範囲をページ BLOB に書き込みます。 | 変更はありません。 この操作を使って上書きまたは消去されるページ BLOB のデータは保存されず、復旧できません。 |
| [Append Block](/rest/api/storageservices/append-block) | 追加 BLOB | 追加 BLOB の末尾にデータのブロックを書き込みます | 変更はありません。 |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | ブロック BLOB、追加 BLOB、ページ BLOB | BLOB に対して定義されているシステム プロパティの値を設定します。 | 変更はありません。 上書きされた BLOB のプロパティは復旧できません。 |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | ブロック BLOB、追加 BLOB、ページ BLOB | 1 つまたは複数の名前と値のペアとして指定された BLOB のユーザー定義メタデータを設定します。 | 変更はありません。 上書きされた BLOB のメタデータは復旧できません。 |

**Put Page** を呼び出してページ BLOB の範囲を上書きまたは消去しても、スナップショットは自動的に生成されない点に注意することが重要です。 仮想マシンのディスクは、ページ BLOB を利用していて、**Put Page** を使ってデータを書き込みます。

### <a name="recovery"></a>Recovery

論理的に削除されたベース BLOB に対して [Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を呼び出すと、それとそれに関連付けられているすべての論理的に削除されたスナップショットが、アクティブとして復元されます。 アクティブなベース BLOB に対して **Undelete Blob** 操作を呼び出すと、それに関連付けられているすべての論理的に削除されたスナップショットが、アクティブとして復元されます。 アクティブとして復元されるスナップショットは、ユーザー生成のスナップショットと同じように処理されます。ベース BLOB を上書きすることはありません。

特定の論理的に削除されたスナップショットに BLOB を復元するには、ベース BLOB に対して **Undelete Blob** を呼び出します。 その後、アクティブになった BLOB にスナップショットをコピーできます。 新しい BLOB にスナップショットをコピーすることもできます。

![Undelete Blob の使用時の動作を示す図。](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

" *論理的に削除されたデータはグレーで、アクティブなデータは青で示されています。上にあるデータほど書き込まれたのが古いデータです。 この例では、BLOB B に対して **Undelete Blob** が呼び出されており、それにより、ベース BLOB B1 と、それに関連付けられているすべてのスナップショット (ここでは B0 のみ) がアクティブとして復元されます。2 番目のステップでは、B0 がベース BLOB にコピーされます。このコピー操作では、B1 の論理的に削除されたスナップショットが生成されます。* "

論理的に削除された BLOB および BLOB のスナップショットを表示するには、削除されたデータを **List Blobs** に含めます。 論理的に削除されたベース BLOB だけを表示することも、論理的に削除された BLOB のスナップショットを含めることもできます。 すべての論理的に削除されたデータについて、データが削除された日時と、データが完全に期限切れになるまでの日数を表示できます。

### <a name="example"></a>例

次に示すのは、論理的な削除が有効になっているときに、*HelloWorld* という名前の BLOB のアップロード、上書き、スナップショット作成、削除、復元を行う .NET スクリプトのコンソール出力です。

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

この出力を生成したアプリケーションについては、「[次のステップ](#next-steps)」セクションをご覧ください。

## <a name="pricing-and-billing"></a>価格と課金

論理的に削除されたデータはすべて、アクティブなデータと同じレートで課金されます。 構成されているリテンション期間の後で完全に削除されたデータについては請求されません。 スナップショットとその課金方法について詳しくは、「[スナップショットの課金方法について](./snapshots-overview.md)」をご覧ください。

スナップショットの自動生成に関するトランザクションには課金されません。 **Undelete Blob** のトランザクションは、書き込み操作のレートで課金されます。

一般的な Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

Microsoft では、論理的な削除を初めて有効にするときは、保持期間を短くしてこの機能が請求に及ぼす影響をよく理解することをお勧めしています。

頻繁に上書きされるデータに対して論理的な削除を有効にすると、ストレージ容量の料金が増えたり、BLOB を一覧表示するときの待ち時間が長くなったりすることがあります。 論理的な削除が無効な別のストレージ アカウントに頻繁に上書きされるデータを格納することで、この追加のコストと待機時間を軽減できます。

## <a name="faq"></a>よく寄せられる質問

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>論理的に削除されたスナップショットを使って BLOB を階層化するために、BLOB 層の設定 API を使用できますか?

はい。 論理的に削除されたスナップショットは、元の階層に残りますが、基本の BLOB は新しい層に移動されます。

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium ストレージ アカウントには BLOB ごとのスナップショットに 100 個の制限があります。 論理的に削除されたスナップショットは、この制限にカウントされますか?

いいえ。論理的に削除されたスナップショットは、この制限にはカウントされません。

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>論理的な削除を有効にしてある状態で、アカウント全体またはコンテナー全体を削除した場合、関連付けられているすべての BLOB が保存されますか?

いいえ。アカウント全体またはコンテナー全体を削除した場合は、関連付けられているすべての BLOB が完全に削除されます。 誤って削除されないようにストレージ アカウントを保護する方法の詳細については、「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」を参照してください。

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>削除されたデータの容量メトリックを見ることはできますか?

論理的に削除されたデータは、ストレージ アカウントの合計容量の一部として含まれます。 ストレージ容量の追跡と監視の詳細については、「[Storage Analytics](../common/storage-analytics.md)」を参照してください。

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>BLOB の論理的に削除されたスナップショットを読み取ってコピーできますか?  

はい。ただし、最初に BLOB で Undelete を呼び出す必要があります。

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>論理的な削除は、仮想マシンのディスクに対して使用できますか?  

論理的な削除は、内部のページ BLOB である Premium と Standard のアンマネージド ディスクの両方で使用できます。 論理的な削除は、**Delete Blob**、**Put Blob**、**Put Block List**、**Copy Blob** 操作によって削除されたデータの復旧にのみ役立ちます。 **Put Page** を呼び出すことで上書きされたデータは復旧できません。

Azure 仮想マシンからアンマネージド ディスクへの書き込みには、**Put Page** の呼び出しが使用されます。そのため、Azure VM からアンマネージド ディスクへの書き込みを、論理的な削除を使用して元に戻すことはできません。

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>論理的な削除を使うには、既存のアプリケーションを変更する必要がありますか?

論理的な削除は、お使いの API のバージョンに関係なく利用できます。 ただし、論理的に削除された BLOB と BLOB のスナップショットを一覧表示および復旧するには、[Azure Storage REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) の 2017-07-29 以降のバージョンを使う必要があります。 Microsoft では、常に最新バージョンの Azure Storage API を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

- [BLOB の論理的な削除の有効化](./soft-delete-blob-enable.md)
- [BLOB のバージョン管理](versioning-overview.md)