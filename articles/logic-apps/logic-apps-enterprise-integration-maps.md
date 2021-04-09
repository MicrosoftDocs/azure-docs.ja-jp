---
title: XSLT マップを使用した XML 変換
description: Enterprise Integration Pack を備えた Azure Logic Apps で、XML を変換するための XSLT マップを追加します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570787"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack を備えた Azure Logic Apps でマップを使用して XML を変換する

Azure Logic Apps でのエンタープライズ統合シナリオ用に XML データの形式を変換する必要がある場合は、ロジック アプリでマップを使用できます (より具体的に言うと、Extensible Stylesheet Language Transformation (XSLT) マップ)。 マップとは、データを XML ドキュメントから別の形式に変換する方法について記述した XML ドキュメントのことです。 

たとえば、YYYMMDD という日付形式を使用している顧客から、B2B の注文や請求書を定期的に受け取っているとします。 ただし、あなたの組織では MMDDYYY という日付形式を使用しているとします。 そのような場合には、日付形式を YYYMMDD から MMDDYYY に変換するマップを定義して使用できます。その後、顧客活動データベースに注文や請求書の詳細を保存できます。

統合アカウントとアーティファクト (マップなど) に関連する制限については、「[Azure Logic Apps の制限と構成情報](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* エンタープライズ統合および企業間取引 (B2B) ソリューションのためのマップとその他のアーティファクトを格納する[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

* マップで外部アセンブリを参照する場合は、"*アセンブリとマップの両方*" を統合アカウントにアップロードする必要があります。 [*最初にアセンブリをアップロード*](#add-assembly)し、その後、そのアセンブリを参照するマップをアップロードするようにしてください。

  アセンブリが 2 MB 以下の場合は、Azure portal からアセンブリを統合アカウントに "*直接*" 追加できます。 ただし、アセンブリやマップが 2 MB よりも大きく、かつ[アセンブリやマップのサイズ制限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)を超えていない場合には、次のオプションを利用できます。

  * アセンブリの場合は、アセンブリをアップロードできる Azure BLOB コンテナーと、そのコンテナーの場所が必要になります。 これにより、後でアセンブリを統合アカウントに追加する際に、その場所を指定できるようになります。 
  このタスクを実行するには、次の項目が必要です。

    | Item | 説明 |
    |------|-------------|
    | [Azure Storage アカウント](../storage/common/storage-account-overview.md) | このアカウントには、アセンブリの Azure BLOB コンテナーを作成します。 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)を確認してください。 |
    | BLOB コンテナー | このコンテナーに、アセンブリをアップロードできます。 このコンテナーの場所は、アセンブリを統合アカウントに追加するときにも必要になります。 [BLOB コンテナーの作成方法についてはこちら](../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。 |
    | [Azure 記憶域エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md) | このツールを使用すると、ストレージ アカウントと BLOB コンテナーをより簡単に管理できます。 Storage Explorer を使用するには、[Azure Storage Explorer をダウンロードしてインストール](https://www.storageexplorer.com/)します。 次に、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」の手順に従って Storage Explorer をストレージ アカウントに接続します。 詳しくは、「[クイック スタート: Azure Storage Explorer を使用してオブジェクト ストレージ内に BLOB を作成する](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)」を参照してください。 <p>または、Azure portal でストレージ アカウントを検索して選択します。 ストレージ アカウント メニューから **[Storage Explorer]** を選択します。 |
    |||

  * マップの場合は、現在のところ、[Azure Logic Apps REST API のマップ](/rest/api/logic/maps/createorupdate)を使用することで、より大きなマップを追加できます。

マップの作成時と追加時にロジック アプリは必要ありません。 ただし、マップを使用するには、ロジック アプリからそのマップを格納する統合アカウントにリンクする必要があります。 [統合アカウントにロジック アプリをリンクする方法についてはこちら](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)を参照してください。 ロジック アプリを用意していない場合は、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を確認してください。

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>参照先アセンブリを追加する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. 統合アカウントを検索して開くには、Azure のメイン メニューで **[すべてのサービス]** を選択します。 
   検索ボックスに「integration account」と入力します。 
   **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 次のように、アセンブリの追加先となる統合アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[アセンブリ]** タイルを選択します。

   !["アセンブリ" を選択する](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. **[アセンブリ]** ページが開いたら、 **[追加]** を選択します。

   ![[アセンブリ] ページの [追加] ボタンが強調して示されているスクリーンショット。](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

アセンブリ ファイルのサイズに基づいて、[最大 2 MB](#smaller-assembly)、または [2 MB を超える最大 8 MB](#larger-assembly) のアセンブリをアップロードする手順を実行します。
統合アカウント内のアセンブリの数に関する制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)に関するページをご覧ください。

> [!NOTE]
> アセンブリを変更する場合、マップに変更があるかどうかに関わらず、マップも更新する必要があります。

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>最大 2 MB のアセンブリを追加する

1. **[アセンブリの追加]** で、アセンブリの名前を入力します。 **[小さいファイル]** を選択したままにします。 **[アセンブリ]** ボックスの横にあるフォルダー アイコンを選択します。 次のように、アップロードするアセンブリを検索して選択します。

   ![小さいアセンブリをアップロードする](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   **[アセンブリ名]** プロパティには、アセンブリを選択した後にアセンブリのファイル名が自動的に表示されます。

1. 準備ができたら、 **[OK]** を選択します。

   アセンブリ ファイルのアップロードが完了すると、 **[アセンブリ]** の一覧にアセンブリが表示されます。

   ![アップロードされたアセンブリの一覧](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[アセンブリ]** タイルに、アップロードされたアセンブリの数が次のように表示されます。

   ![アップロードされたアセンブリ](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2 MB 以上のアセンブリを追加する

より大きなアセンブリを追加するには、アセンブリを Azure ストレージ アカウントの Azure BLOB コンテナーにアップロードします。 アセンブリを追加する手順は、お使いの BLOB コンテナーにパブリック読み取りアクセス権が与えられているかどうかによって変わります。 そのため、まず最初に BLOB コンテナーがパブリック読み取りアクセス権を持っているかどうかを確認します。このとき、「[BLOB コンテナーのパブリック アクセス レベルを設定する](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)」の手順に従います。

#### <a name="check-container-access-level"></a>コンテナーのアクセス レベルを確認する

1. Azure ストレージ エクスプローラーを開きます。 Explorer ウィンドウで、Azure サブスクリプションがまだ展開されていない場合は展開します。

1. **[ストレージ アカウント]** > {*自分のストレージ アカウント*} > **[BLOB コンテナー]** の順に展開します。 BLOB コンテナーを選択します。

1. BLOB コンテナーのショートカット メニューから **[Set Public Access Level]\(パブリック アクセス レベルの設定\)** を選択します。

   * BLOB コンテナーが少なくともパブリック アクセス権を持っている場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持つコンテナーにアップロードする](#public-access-assemblies)」の手順を実行します

     ![パブリック アクセス権](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * BLOB コンテナーがパブリック アクセス権を持っていない場合は、 **[キャンセル]** を選択し、このページで後述する「[パブリック アクセス権を持たないコンテナーにアップロードする](#no-public-access-assemblies)」の手順を実行します

     ![パブリック アクセス権なし](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>パブリック アクセス権を持つコンテナーにアップロードする

1. ストレージ アカウントにアセンブリをアップロードします。 
   右側のウィンドウで **[アップロード]** を選択します。

1. アップロードを完了したら、アップロードしたアセンブリを選択します。 ツール バーで **[URL のコピー]** を選択し、アセンブリの URL をコピーします。

1. **[アセンブリの追加]** ウィンドウが開いている Azure portal に戻ります。 
   アセンブリの名前を入力します。 
   **[大きいファイル (2 MB 超)]** を選択します。

   **[アセンブリ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、アセンブリの URL を貼り付けます。 
   アセンブリの追加を完了します。

アセンブリのアップロードが完了すると、 **[アセンブリ]** の一覧にスキーマが表示されます。
統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[アセンブリ]** タイルに、アップロードされたアセンブリの数が表示されます。

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>パブリック アクセス権を持たないコンテナーにアップロードする

1. ストレージ アカウントにアセンブリをアップロードします。 
   右側のウィンドウで **[アップロード]** を選択します。

1. アップロードが完了したら、アセンブリの共有アクセス署名 (SAS) を生成します。 
   アセンブリのショートカット メニューから **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

1. **[Shared Access Signature]** ウィンドウで **[Generate container-level shared access signature URI]\(コンテナーレベルの共有アクセス署名 URI の生成\)**  >  **[作成]** の順に選択します。 
   SAS URL が生成されたら、 **[URL]** に進み、 **[コピー]** を選択します。

1. **[アセンブリの追加]** ウィンドウが開いている Azure portal に戻ります。 
   アセンブリの名前を入力します。 
   **[大きいファイル (2 MB 超)]** を選択します。

   **[アセンブリ]** ボックスではなく、 **[コンテンツ URI]** ボックスが表示されます。

1. **[コンテンツ URI]** ボックスに、前に生成した SAS URI を貼り付けます。 アセンブリの追加を完了します。

アセンブリのアップロードが完了すると、 **[スキーマ]** の一覧にアセンブリが表示されます。 統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[アセンブリ]** タイルに、アップロードされたアセンブリの数が表示されます。

## <a name="create-maps"></a>マップを作成する

マップとして使用できる XSLT ドキュメントを作成するには、[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) を使用することにより、Visual Studio 2015 で BizTalk 統合プロジェクトを作成することができます。 そのプロジェクトで、統合マップ ファイルを作成できます。これにより、2 つの XML スキーマ ファイルの間でアイテムを視覚的にマップできます。 このプロジェクトをビルドすると、XSLT ドキュメントが生成されます。
統合アカウント内のマップの数に関する制限については、[Azure Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)に関するページをご覧ください。 

## <a name="add-maps"></a>マップを追加する

マップから参照するすべてのアセンブリをアップロードしたら、次にマップをアップロードできます。

1. まだサインインしていない場合は、Azure アカウントの資格情報を使用して [Azure portal](https://portal.azure.com) にサインインします。 

1. 統合アカウントがまだ開いていない場合は、Azure のメイン メニューで **[すべてのサービス]** を選択します。 
   検索ボックスに「integration account」と入力します。 
   **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. 次のように、マップの追加先となる統合アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[マップ]** タイルを選択します。

   !["マップ" を選択する](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. **[マップ]** ページが開いたら、 **[追加]** を選択します。

   ![[追加] の選択](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>最大 2 MB のマップを追加する

1. **[マップの追加]** で、マップの名前を入力します。 

1. **[マップの種類]** で、種類を選択します。例:**Liquid**、**XSLT**、**XSLT 2.0**、**XSLT 3.0**。

1. **[小さいファイル]** を選択したままにします。 **[マップ]** ボックスの横にあるフォルダー アイコンを選択します。 次のように、アップロードするマップを検索して選択します。

   ![マップをアップロードする](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   **[名前]** プロパティを空のままにした場合は、マップ ファイルを選択した後に、マップのファイル名がそのプロパティに自動的に表示されます。 
   ただし、任意の一意名を使用することもできます。

1. 準備ができたら、 **[OK]** を選択します。 
   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧にマップが表示されます。

   ![アップロードされたマップの一覧](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   統合アカウントの **[概要]** ページの **[コンポーネント]** の下にある **[マップ]** タイルに、アップロードされたマップの数が次のように表示されます。

   ![アップロードされたマップ](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>2 MB 以上のマップを追加する

現在のところ、大規模なマップを追加するには [Azure Logic Apps REST API のマップ](/rest/api/logic/maps/createorupdate)を使用します。

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>マップを編集する

既存のマップを更新するには、目的の変更を含む新しいマップ ファイルをアップロードする必要があります。 ただし、編集対象の既存のマップをまずダウンロードすることができます。

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は検索して開きます。

1. Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 検索ボックスに「integration account」と入力します。 **[統合アカウント]** を選択します。

1. マップを更新する統合アカウントを選択します。

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[マップ]** タイルを選択します。

1. **[マップ]** ページが開いたら、マップを選択します。 
   まずマップをダウンロードして編集するには、 **[ダウンロード]** を選択し、マップを保存します。

1. 更新したマップをアップロードする準備ができたら、 **[マップ]** ページで更新するマップを選択して **[更新]** を選択します。

1. アップロードする更新済みマップを探して選択します。 
   マップ ファイルのアップロードが完了すると、 **[マップ]** の一覧に更新されたマップが表示されます。

## <a name="delete-maps"></a>マップを削除する

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は検索して開きます。

1. Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 
   検索ボックスに「integration account」と入力します。 
   **[統合アカウント]** を選択します。

1. マップを削除する統合アカウントを選択します。

1. 統合アカウントの **[概要]** ページの **[コンポーネント]** で、 **[マップ]** タイルを選択します。

1. **[マップ]** ページが開いたら、マップを選択し、 **[削除]** を選択します。

1. マップの削除を確定するには、 **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [スキーマについての詳細情報](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [変換についての詳細情報](../logic-apps/logic-apps-enterprise-integration-transform.md)
