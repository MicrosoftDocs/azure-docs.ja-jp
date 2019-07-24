---
title: Azure Storage Explorer を使用して Data Lake Storage Gen2 のアクセス許可を設定する
description: このハウツーでは、Azure Data Lake Storage Gen2 対応のストレージ アカウント内のファイルとディレクトリに対するアクセス許可を Azure Storage Explorer で設定する方法について説明します。
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: d1e941e41871de4a11641254f40c53285b096807
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546313"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で Azure Storage Explorer を使用してファイルとディレクトリ レベルのアクセス許可を設定する

Azure Data Lake Storage Gen2 に格納されているファイルでは、アクセス許可とアクセス制御リスト (ACL) のきめ細かい管理がサポートされています。 細かいアクセス許可と ACL 管理を組み合わせることで、データへのアクセスをきめ細かいレベルで管理できます。

この記事では、Azure Storage Explorer を使用して以下を行う方法について説明します。

> [!div class="checklist"]
> * ファイル レベルのアクセス許可を設定する
> * ディレクトリ レベルのアクセス許可を設定する
> * アクセス制御リストにユーザーまたはグループを追加する

## <a name="prerequisites"></a>前提条件

このプロセスを最適な方法で表現できるように、[Azure Storage Explorer のクイック スタート](data-lake-storage-Explorer.md)を完了する必要があります。 これにより、ストレージ アカウントが最も適切な状態 (ファイル システムが作成され、データがアップロードされた状態) になります。

## <a name="managing-access"></a>アクセスの管理

ファイルシステムのルートにアクセス許可を設定することができます。 これを行うには、(接続文字列を使用するのではなく) その操作のアクセス権を持つ自分の個人アカウントで Azure Storage Explorer にログインする必要があります。 ファイルシステムを右クリックし、**[アクセス許可の管理]** を選択し、**[アクセス許可の管理]** ダイアログ ボックスを開きます。

![Microsoft Azure Storage Explorer - ディレクトリ アクセスを管理する](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

**[アクセス許可の管理]** ダイアログ ボックスでは、所有者と所有者グループのアクセス許可を管理できます。 また、アクセス制御リストに新しいユーザーとグループを追加して、アクセス許可を管理することもできます。

新しいユーザーまたはグループをアクセス制御リストに追加するには、**[ユーザーまたはグループの追加]** フィールドを選択します。

リストに追加する、対応する Azure Active Directory (AAD) エントリを入力し、**[追加]** を選択します。

ユーザーまたはグループが **[ユーザーとグループ:]** フィールドに表示され、そのアクセス許可の管理を始めることができます。

> [!NOTE]
> ベスト プラクティスであり、推奨される方法は、AAD にセキュリティ グループを作成し、個々のユーザーではなくグループに対するアクセス許可を保守することです。 この推奨事項やその他のベスト プラクティスの詳細については、[Data Lake Storage Gen2 のベスト プラクティス](data-lake-storage-best-practices.md)に関するページを参照してください。

割り当てることができるアクセス許可には、アクセス ACL と既定の ACL という 2 つのカテゴリがあります。

* **アクセス**:アクセス ACL では、オブジェクトへのアクセスを制御します。 ファイルとディレクトリの両方がアクセス ACL を持っています。

* **既定**:ディレクトリに関連付けられた ACL のテンプレートです。この ACL によって、そのディレクトリの下に作成されるすべての子項目のアクセス ACL が決まります。 ファイルには既定の ACL がありません。

これらの両方のカテゴリ内には、ファイルまたはディレクトリに割り当てることができるアクセス許可が 3 つあります。**読み取り**、**書き込み**、**実行**です。

>[!NOTE]
> ここで選択しても、ディレクトリ内に現在ある項目に対してアクセス許可は設定されません。 ファイルが既に存在する場合は、個々の項目に移動して、アクセス許可を手動で設定する必要があります。

個々のディレクトリだけでなく、個々のファイルに対するアクセス許可を管理できるので、アクセス制御を微調整することができます。 ディレクトリとファイルの両方のアクセス許可を管理する場合のプロセスは、前述の方法と同じです。 アクセス許可を管理するファイルまたはディレクトリを右クリックし、同じプロセスに従います。

## <a name="next-steps"></a>次の手順

このハウツーでは、**Azure Storage Explorer** を使用してファイルとディレクトリのアクセス許可を設定する方法について説明しました。 既定の ACL、アクセス ACL、その動作、対応するアクセス許可など、ACL の詳細については学ぶには、その主題に関する概念の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)
