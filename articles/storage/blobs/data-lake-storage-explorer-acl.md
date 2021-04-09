---
title: 'Storage Explorer: Azure Data Lake Storage Gen2 で ACL を設定する'
description: Azure Storage Explorer を使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントでアクセス制御リスト (ACL) を管理します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100656379"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer を使用して Azure Data Lake Storage Gen2 での ACL を管理する

この記事では、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、階層型名前空間 (HNS) が有効になっているストレージ アカウントでアクセス制御リスト (ACL) を管理する方法を説明します。

Storage Explorer を使用して、ディレクトリとファイルの ACL を表示して更新することができます。 ACL の継承は、親ディレクトリの下に作成された新しい子項目に対して既に利用可能です。 ただし、親ディレクトリの既存の子項目に対して ACL 設定を再帰的に適用することもできます。各子項目に対してこれらの変更を個別に行う必要はありません。 

この記事では、ファイルまたはディレクトリの ACL を変更する方法と、ACL 設定を子ディレクトリに再帰的に適用する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](../common/storage-account-create.md)の手順に従います。

- ローカル コンピューターにインストールされた Azure Storage Explorer。 Windows、Macintosh、または Linux 用の Azure Storage Explorer をインストールする方法については、「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」をご覧ください。

> [!NOTE]
> Storage Explorer では、Azure Data Lake Storage Gen2 を操作するときに BLOB (blob) と Data Lake Storage Gen2 (dfs) の[エンドポイント](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage)が使用されます。 Azure Data Lake Storage Gen2 へのアクセスがプライベート エンドポイントを使用して構成されている場合は、ストレージ アカウント用に 2 つのプライベート エンドポイント (1 つはターゲット サブリソース `blob` 用で、もう 1 つはターゲット サブリソース `dfs` 用) が作成されていることを確認します。

## <a name="sign-in-to-storage-explorer"></a>Storage Explorer にサインインする

Storage Explorer を初めて起動すると、 **[Microsoft Azure Storage Explorer - 接続]** ウィンドウが表示されます。 Storage Explorer にはストレージ アカウントに接続する方法がいくつか用意されていますが、現在のところ、ACL の管理にサポートされている方法は 1 つのみです。

|タスク|目的|
|---|---|
|Azure アカウントを追加する | Azure に対する認証を行うための組織のサインイン ページにリダイレクトします。 現在のところ、これが ACL を管理および設定する場合にサポートされている唯一の認証方法です。|
|接続文字列または Shared Access Signature URI を使用する | SAS トークンまたは共有の接続文字列でコンテナーまたはストレージ アカウントに直接アクセスするために使用できます。 |
|ストレージ アカウントの名前とキーを使用する| ストレージ アカウントの名前とキーを使用して、Azure Storage に接続します。|

**[Add an Azure Account\(Azure アカウントの追加\)]** を選択し、 **[サインイン]** をクリックします。画面上のプロンプトに従って Azure アカウントにサインインします。

![Microsoft Azure Storage Explorer を示すスクリーンショット。[Azure アカウントの追加] オプションと [サインイン] ボタンが強調表示されています。](media/storage-quickstart-blobs-storage-explorer/connect.png)

接続が完了すると、Azure Storage Explorer が読み込まれて **[Explorer]** タブが表示されます。 このビューには、すべての Azure ストレージ アカウント、[Azure ストレージ エミュレーター](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)、[Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) アカウント、または [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 環境を使用して構成されたおよびローカル ストレージの分析情報が表示されます。

![[Microsoft Azure Storage Explorer - 接続] ウィンドウ](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>ACL を管理する

コンテナー、ディレクトリ、またはファイルを右クリックし、 **[アクセス制御リストの管理]** をクリックします。  次のスクリーンショットは、ディレクトリを右クリックしたときに表示されるメニューを示しています。

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer でのディレクトリの右クリック](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

**[アクセスの管理]** ダイアログ ボックスでは、所有者と所有者グループのアクセス許可を管理できます。 また、アクセス制御リストに新しいユーザーとグループを追加して、アクセス許可を管理することもできます。

> [!div class="mx-imgBorder"]
> ![[アクセスの管理] ダイアログ ボックス](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

新しいユーザーまたはグループをアクセス制御リストに追加するには、 **[追加]** ボタンを選択します。 次に、リストに追加する、対応する Azure Active Directory (Azure AD) エントリを入力し、 **[追加]** を選択します。  ユーザーまたはグループが **[ユーザーとグループ:]** フィールドに表示され、そのアクセス許可の管理を始めることができます。

> [!NOTE]
> ベスト プラクティスであり、推奨される方法は、Azure AD にセキュリティ グループを作成し、個々のユーザーではなくグループに対するアクセス許可を保守することです。 この推奨事項やその他のベスト プラクティスの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-explorer-acl.md)」を参照してください。

チェック ボックス コントロールを使用して、アクセスと既定の ACL を設定します。 ACL のこれらの種類の違いの詳細については、「[ACL の種類](data-lake-storage-access-control.md#types-of-acls)」を参照してください。

## <a name="apply-acls-recursively"></a>ACL を再帰的に適用する

また、親ディレクトリの既存の子項目に対して ACL エントリを再帰的に適用できます。各子項目に対してこれらの変更を個別に行う必要はありません。

ACL エントリを再帰的に適用するには、コンテナーまたはディレクトリを右クリックし、 **[アクセス制御リストの伝達]** をクリックします。  次のスクリーンショットは、ディレクトリを右クリックしたときに表示されるメニューを示しています。

> [!div class="mx-imgBorder"]
> ![ディレクトリの右クリックとアクセス制御の伝達設定の選択](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>次のステップ

Data Lake Storage Gen2 アクセス許可モデルについて学びます。

> [!div class="nextstepaction"]
> 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](./data-lake-storage-access-control-model.md)」
