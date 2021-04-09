---
title: Azure Storage Explorer BLOB バージョン管理ガイド | Microsoft Docs
description: Azure Storage Explorer の BLOB バージョン管理ガイダンス
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783829"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Storage Explorer BLOB バージョン管理ガイド

Microsoft Azure Storage Explorer では、BLOB のバージョンに簡単にアクセスし、管理することができます。 このガイドは、Storage Explorer での BLOB バージョン管理のしくみを理解するのに役立ちます。 次に進む前に、[BLOB のバージョン管理](../blobs/versioning-overview.md)に関する記事をよく読んでおくことをお勧めします。

## <a name="terminology"></a>用語

ここでは、この記事での用法を理解するのに役立ついくつかの定義を示します。

- 論理的な削除:代替の自動データ保護機能。 論理的な削除の詳細については、[こちら](../blobs/soft-delete-blob-overview.md)を参照してください。
- アクティブな BLOB:BLOB または BLOB バージョンは、アクティブな状態で作成されます。 操作できるのは、アクティブな状態の BLOB または BLOB バージョンだけです。
- 論理的に削除された BLOB:論理的に削除済みとしてマークされている BLOB または BLOB バージョン。 論理的に削除された BLOB は、その保持期間の間のみ保持されます。
- BLOB バージョン:BLOB のバージョン管理が有効な状態で作成された BLOB。 個々の BLOB バージョンはバージョン ID に関連付けられます。
- 現在のバージョン:現在のバージョンとしてマークされている BLOB バージョン。
- 以前のバージョン:現在のバージョンではない BLOB バージョン。
- バージョンがない BLOB:BLOB のバージョン管理が無効な状態で作成された BLOB。 バージョンがなくバージョン ID を持たない BLOB。

## <a name="view-blob-versions"></a>BLOB バージョンの表示

Storage Explorer では、BLOB を表示する 4 種類のビューをサポートしています。

| 表示 | アクティブでありバージョンがない BLOB | 論理的に削除されたバージョンがない BLOB | BLOB バージョン |
| ---- | :----------: | :-----------: | :------------------: |
| アクティブな BLOB | はい | いいえ | 現在のバージョンのみ |
| アクティブな BLOB と論理的に削除された BLOB | はい | はい | 現在のバージョンのみ |
| アクティブな BLOB と現在のバージョンがない BLOB | はい | いいえ | 現在のバージョンまたは最新のアクティブなバージョン |
| すべての BLOB と現在のバージョンがない BLOB | はい | はい | 現在のバージョンまたは最新バージョン |

### <a name="active-blobs"></a>アクティブな BLOB

このビューでは、Storage Explorer に次のものが表示されます。

- アクティブでありバージョンがない BLOB
- 現在のバージョン

### <a name="active-blobs-and-soft-deleted-blobs"></a>アクティブな BLOB と論理的に削除された BLOB

このビューでは、Storage Explorer に次のものが表示されます。

- アクティブでありバージョンがない BLOB
- 論理的に削除されたバージョンがない BLOB
- 現在のバージョン

### <a name="active-blobs-and-blobs-without-current-version"></a>アクティブな BLOB と現在のバージョンがない BLOB

このビューでは、Storage Explorer に次のものが表示されます。

- アクティブでありバージョンがない BLOB
- 現在のバージョン
- アクティブな以前のバージョンのうち最も新しいもの 

現在のバージョンはないがアクティブな以前のバージョンがある BLOB の場合、Storage Explorer では、アクティブな以前のバージョンのうち最も新しいものをその BLOB の表現として表示します。

### <a name="all-blobs-and-blobs-without-current-version"></a>すべての BLOB と現在のバージョンがない BLOB

このビューでは、Storage Explorer に次のものが表示されます。

- アクティブでありバージョンがない BLOB
- 論理的に削除されたバージョンがない BLOB
- 現在のバージョン
- 以前のバージョンのうち最も新しいもの 

現在のバージョンがない BLOB の場合、Storage Explorer では、以前のバージョンのうち最も新しいものをその BLOB の表現として表示します。

> [!Note]
> サービスの制限により、Storage Explorer では、BLOB バージョンを一覧表示するときに仮想ディレクトリの階層ビューを取得するために、いくつかの追加処理が必要になります。 次のビューでは、BLOB の一覧表示にかかる時間が長くなります。
> 
> - アクティブな BLOB と現在のバージョンがない BLOB
> - すべての BLOB と現在のバージョンがない BLOB

## <a name="manage-blob-versions"></a>BLOB バージョンの管理

### <a name="view-versions-of-a-blob"></a>BLOB のバージョンの表示

Storage Explorer には、BLOB のすべてのバージョンを表示するための **[バージョンの管理]** コマンドが用意されています。 BLOB のバージョンを表示するには、バージョンを表示する BLOB を選択し、ツール バーまたはコンテキスト メニューから **[履歴の管理] &rarr; [バージョンの管理]** を選択します。

### <a name="download-blob-versions"></a>BLOB バージョンのダウンロード

1 つ以上の BLOB バージョンをダウンロードするには、ダウンロードする BLOB バージョンを選択し、ツール バーまたはコンテキスト メニューから **[ダウンロード]** を選択します。

BLOB の複数のバージョンをダウンロードする場合、ダウンロードしたファイルのファイル名の先頭にバージョン ID が付きます。

### <a name="delete-blob-versions"></a>BLOB バージョンの削除

1 つ以上の BLOB バージョンを削除するには、削除する BLOB バージョンを選択し、ツール バーまたはコンテキスト メニューから **[削除]** を選択します。

BLOB バージョンには、論理的な削除のポリシーが適用されます。 論理的な削除が有効になっている場合、BLOB バージョンは論理的に削除されます。 特殊なケースの 1 つは、現在のバージョンの削除です。 現在のバージョンを削除すると、そのバージョンは自動的に、アクティブな以前のバージョンになります。

### <a name="promote-blob-version"></a>BLOB バージョンの昇格

以前のバージョンを現在のバージョンに昇格させることにより、BLOB の内容を復元できます。 昇格させる BLOB バージョンを選択し、ツール バーまたはコンテキスト メニューから **[バージョンの昇格]** を選択します。

バージョンのない BLOB が、昇格した BLOB バージョンによって上書きされます。 操作を確認する前に、そのデータが不要になったことを確認するか、自分自身でデータをバックアップしてください。 現在のバージョンは自動的に以前のバージョンになるため、Storage Explorer から確認を求められることはありません。

### <a name="undelete-blob-version"></a>BLOB バージョンの削除の取り消し

BLOB バージョンは、個別に削除を取り消すことはできません。 一度にすべて削除を取り消す必要があります。 BLOB のすべてのバージョンの削除を取り消すには、BLOB のいずれかのバージョンを選択して、ツール バーまたはコンテキスト メニューから **[選択項目の削除の取り消し]** を選択します。

### <a name="change-access-tier-of-blob-versions"></a>BLOB バージョンのアクセス層の変更

BLOB バージョンごとに、独自のアクセス層があります。 BLOB バージョンのアクセス層を変更するには、アクセス層を変更する BLOB バージョンを選択して、コンテキスト メニューから **[アクセス層の変更...]** を選択します。

## <a name="see-also"></a>参照

* [BLOB バージョン管理](../blobs/versioning-overview.md)
* [BLOB の論理的な削除](../blobs/soft-delete-blob-overview.md)