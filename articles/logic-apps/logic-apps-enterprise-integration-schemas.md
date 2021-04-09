---
title: スキーマによる XML の検証
description: Enterprise Integration Pack を備えた Azure Logic Apps で XML ドキュメントを検証するスキーマを追加する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75979373"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack を備えた Azure Logic Apps でスキーマを使用して XML を検証する

ドキュメントが有効な XML を使用していること、Azure Logic Apps のエンタープライズ統合シナリオ用に事前に定義された形式の想定データが含まれていることを確認するために、ロジック アプリにスキーマを使用できます。 スキーマを使用して、ロジック アプリが企業間取引 (B2B) シナリオで交換するメッセージを検証することもできます。

統合アカウントとアーティファクト (スキーマなど) に関連する制限については、[Azure Logic Apps の制限と構成の情報](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* エンタープライズ統合および企業間取引 (B2B) ソリューションのためのスキーマとその他のアーティファクトを格納する[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 

  スキーマが [2 MB 以下](#smaller-schema)の場合は、Azure portal からスキーマを統合アカウントに直接追加できます。 ただし、スキーマが 2 MB を超えていて、[スキーマのサイズ制限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)を超えていない場合は、スキーマを Azure ストレージ アカウントにアップロードできます。 
  そのスキーマを統合アカウントに追加するには、統合アカウントからストレージ アカウントにリンクする方法があります。 
  このタスクを実行するには、以下が必要です。 

  * スキーマ用の BLOB コンテナーを作成する [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。 [ストレージ アカウントの作成方法についてはこちら](../storage/common/storage-account-create.md)を参照してください。 

  * スキーマを格納する BLOB コンテナー。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 
  後で統合アカウントにスキーマを追加するときに、コンテナーのコンテンツ URI が必要です。

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)。ストレージ アカウントと BLOB コンテナーの管理に使用できます。 
  Storage Explorer を使用するには、次のいずれかの方法を選択します。
  
    * Azure portal でストレージ アカウントを検索して選択します。 
    ストレージ アカウント メニューから **[Storage Explorer]** を選択します。

    * デスクトップ バージョンの場合、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 
    次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 
    詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)」を参照してください。

スキーマの作成時と追加時にロジック アプリは必要ありません。 ただし、スキーマを使用するには、ロジック アプリからそのスキーマを格納する統合アカウントにリンクする必要があります。 [統合アカウントにロジック アプリをリンクする方法についてはこちら](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)を参照してください。 ロジック アプリを用意していない場合は、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を確認してください。

## <a name="add-schemas"></a>スキーマを追加する

1. Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

1. 統合アカウントを検索して開くには、Azure のメイン メニューで **[すべてのサービス]** を選択します。 検索ボックスに「integration account」と入力します。 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. 次のように、スキーマの追加先となる統合アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[スキーマ]** タイルを選択します。

   ![[スキーマ] を選択する](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. **[スキーマ]** ページが開いたら、 **[追加]** を選択します。

   ![[追加] の選択](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

スキーマ (.xsd) ファイルのサイズに基づいて、[最大 2 MB](#smaller-schema)、または [2 MB を超える最大 8 MB](#larger-schema) のスキーマをアップロードする手順を実行します。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>最大 2 MB のスキーマを追加する

1. **[スキーマの追加]** で、スキーマの名前を入力します。 
   **[小さいファイル]** を選択したままにします。 **[スキーマ]** ボックスの横にあるフォルダー アイコンを選択します。 次のように、アップロードするスキーマを検索して選択します。

   ![小さいスキーマをアップロードする](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. 準備ができたら、 **[OK]** を選択します。

   スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2 MB を超えるスキーマを追加する

より大きなスキーマを追加するには、スキーマを Azure ストレージ アカウントの Azure BLOB コンテナーにアップロードします。 スキーマを追加する手順は、ご使用の BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかによって変わります。 そのため、まず最初に BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかを確認します。このとき、「[BLOB コンテナーのパブリック アクセス レベルを設定する](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)」の手順に従います。

#### <a name="check-container-access-level"></a>コンテナーのアクセス レベルを確認する

1. Azure ストレージ エクスプローラーを開きます。 Explorer ウィンドウで、Azure サブスクリプションがまだ展開されていない場合は展開します。

1. **[ストレージ アカウント]** > {*自分のストレージ アカウント*} > **[BLOB コンテナー]** の順に展開します。 BLOB コンテナーを選択します。

1. BLOB コンテナーのショートカット メニューから **[Set Public Access Level]\(パブリック アクセス レベルの設定\)** を選択します。

   * BLOB コンテナーが少なくともパブリック アクセス権を持っている場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持つコンテナーにアップロードする](#public-access)」の手順を実行します

     ![パブリック アクセス権](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * BLOB コンテナーがパブリック アクセス権を持っていない場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持たないコンテナーにアップロードする](#public-access)」の手順を実行します

     ![パブリック アクセス権なし](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>パブリック アクセス権を持つコンテナーにアップロードする

1. スキーマをストレージ アカウントにアップロードします。 
   右側のウィンドウで **[アップロード]** を選択します。

1. アップロードを完了したら、アップロードしたスキーマを選択します。 ツール バーで **[URL のコピー]** を選択し、スキーマの URL をコピーします。

1. **[スキーマの追加]** ウィンドウが開いている Azure portal に戻ります。 
   アセンブリの名前を入力します。 
   **[大きいファイル (2 MB 超)]** を選択します。 

   **[スキーマ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、スキーマの URL を貼り付けます。 
   スキーマの追加を完了します。

スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[スキーマ]** タイルに、アップロードされたスキーマの数が表示されます。

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>パブリック アクセス権を持たないコンテナーにアップロードする

1. スキーマをストレージ アカウントにアップロードします。 
   右側のウィンドウで **[アップロード]** を選択します。

1. アップロードが完了したら、スキーマの共有アクセス署名 (SAS) を生成します。 
   スキーマのショートカット メニューから **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

1. **[Shared Access Signature]** ウィンドウで **[Generate container-level shared access signature URI]\(コンテナーレベルの共有アクセス署名 URI の生成\)**  >  **[作成]** の順に選択します。 
   SAS URL が生成されたら、 **[URL]** に進み、 **[コピー]** を選択します。

1. **[スキーマの追加]** ウィンドウが開いている Azure portal に戻ります。 **[大きいファイル]** を選択します。

   **[スキーマ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、前に生成した SAS URI を貼り付けます。 スキーマの追加を完了します。

スキーマのアップロードが完了したら、 **[スキーマ]** 一覧にスキーマが表示されます。 統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[スキーマ]** タイルに、アップロードされたスキーマの数が表示されます。

## <a name="edit-schemas"></a>スキーマを編集する

既存のスキーマを更新するには、目的の変更を含む新しいスキーマ ファイルをアップロードする必要があります。 ただし、編集対象の既存のスキーマをまずダウンロードすることができます。

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> で、統合アカウントをまだ開いていない場合は検索して開きます。

1. Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 
   検索ボックスに「integration account」と入力します。 
   **[統合アカウント]** を選択します。

1. スキーマを更新する統合アカウントを選択します。

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[スキーマ]** タイルを選択します。

1. **[スキーマ]** ページが開いたら、スキーマを選択します。 
   まずスキーマをダウンロードして編集するには、 **[ダウンロード]** を選択し、スキーマを保存します。

1. 更新したスキーマをアップロードする準備ができたら、 **[スキーマ]** ページで更新するスキーマを選択して **[更新]** を選択します。

1. アップロードする更新済みスキーマを探して選択します。 
   スキーマ ファイルのアップロードが完了したら、 **[スキーマ]** 一覧に更新されたスキーマが表示されます。

## <a name="delete-schemas"></a>スキーマを削除する

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> で、統合アカウントをまだ開いていない場合は検索して開きます。

1. Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 
   検索ボックスに「integration account」と入力します。 
   **[統合アカウント]** を選択します。

1. スキーマを削除する統合アカウントを選択します。

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[スキーマ]** タイルを選択します。

1. **[スキーマ]** ページが開いたら、スキーマを選択し、 **[削除]** を選択します。

1. スキーマの削除を確定するには、 **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md)
* [マップについての詳細情報](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [変換についての詳細情報](../logic-apps/logic-apps-enterprise-integration-transform.md)
