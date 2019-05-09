---
title: Azure Data Factory を使用して Office 365 からデータを読み込む | Microsoft Docs
description: Azure Data Factory を使用して Office 365 からデータをコピーする
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445769"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Azure Data Factory を使用して Office 365 からデータを読み込む

この記事では、Data Factory を使用して _Office 365 から Azure BLOB ストレージにデータを読み込む_方法を示します。 同様の手順に従って Azure Data Lake Gen1 または Gen2 にデータをコピーすることもできます。 Office 365 からデータをコピーすること全般については、[Office 365 コネクタに関する記事](connector-office-365.md)を参照してください。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. 左側のメニューで、**[リソースの作成]** > **[データ + 分析]** > **[Data Factory]** の順に選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **[新しいデータ ファクトリ]** ページで、次の画像に示されているフィールドの値を指定します。
      
   ![[新しいデータ ファクトリ] ページ](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **名前**:Azure Data Factory のグローバルに一意の名前を入力します。 "データ ファクトリ名 \"LoadFromOffice365Demo\" は利用できません" というエラーが発生する場合は、データ ファクトリの別の名前を入力します。 たとえば、_**yourname**_**LoadFromOffice365Demo** という名前を使用できます。 データ ファクトリをもう一度作成してみます。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事をご覧ください。
    * **サブスクリプション**:データ ファクトリを作成する Azure サブスクリプションを選択します。 
    * **リソース グループ**:ドロップダウン リストから既存のリソース グループを選択するか、**[新規作成]** オプションを選択し、リソース グループの名前を入力します。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
    * **バージョン**:**[V2]** を選択します。
    * **場所**:データ ファクトリの場所を選択します。 サポートされている場所のみがドロップダウン リストに表示されます。 データ ファクトリによって使用されるデータ ストアは、他の場所やリージョンにあってもかまいません。 このようなデータ ストアには、Azure Data Lake Store、Azure Storage、Azure SQL Database などがあります。

3. **作成**を選択します。
4. 作成が完了したら、データ ファクトリに移動します。 次の画像のように **[データ ファクトリ]** ホーム ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/load-office-365-data/data-factory-home-page.png)

5. **[作成と監視]** タイルを選択して、別のタブでデータ統合アプリケーションを起動します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

1. [Let's get started]\(始めましょう\) ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。
 
    ![パイプラインの作成](./media/load-office-365-data/create-pipeline-entry.png)

2. パイプラインの **[全般]** タブで、パイプラインの**名前**として「CopyPipeline」と入力します。

3. [アクティビティ] ツール ボックスの [Move & Transform]\(移動と変換) で、ツール ボックスからパイプライン デザイナー画面に **[コピー]** アクティビティ をドラッグ アンド ドロップします。 アクティビティ名として "CopyFromOffice365ToBlob" を指定します。

### <a name="configure-source"></a>ソースの構成

1. パイプラインの **[ソース]** タブに移動し、**[+ 新規]** をクリックしてソース データセットを作成します。 

2. [新しいデータセット] ウィンドウで **[Office 365]** を選択し、**[完了]** を選択します。

    ![Office 365 の新しいデータセット](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Office 365 データセット用の新しいタブが開きます。 プロパティ ウィンドウの下部にある **[全般]** タブで、[名前] に「SourceOffice365Dataset」と入力します。

    ![一般的な Office 365 データセットを構成する](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. プロパティ ウィンドウの **[接続]** タブに移動します。 [リンクされたサービス] ボックスの横にある **[+ 新規]** をクリックします。
 
    ![Office 365 データセットの接続を構成する](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. [New Linked Service]\(新しいリンクされたサービス) ウィンドウで、名前として「Office365LinkedService」と入力し、サービス プリンシパル ID とサービス プリンシパル キーを入力したら、[保存] を選択し、リンクされたサービスをデプロイします。

    ![新しい Office 365 のリンクされたサービス](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. リンクされたサービスが作成されると、データセットの設定に戻ります。 "Table" の横にある下向き矢印を選択し、使用可能な Office 365 データセットの一覧を展開して、ドロップダウン リストから "BasicDataSet_v0.Contact_v0" を選択します。

    ![Office 365 データセット テーブルを構成する](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. プロパティ ウィンドウの **[スキーマ]** タブに移動し、**[スキーマのインポート]** を選択します。  連絡先データセットのスキーマとサンプルの値が表示されることに注目してください。

    ![Office 365 データセット スキーマを構成する](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. 次に、パイプライン -> [ソース] タブに戻り、[SourceBlobDataset] が選択されていることを確認します。
 
### <a name="configure-sink"></a>シンクの構成

1. パイプライン -> **[シンク]** タブに移動し、**[+ 新規]** を選択してシンク データセットを作成します。
 
2. [新しいデータセット] ウィンドウで、Office 365 からコピーするときには、サポートされているコピー先のみが選択されていることに注目します。 **[Azure Blob Storage]** を選択し、**[完了]** を選択します。  このチュートリアルでは、Office 365 のデータを Azure Blob Storage にコピーします。

    ![新しい BLOB データセット](./media/load-office-365-data/new-blob-dataset.png)

4. プロパティ ウィンドウの **[General]\(一般\)** タブの [名前] に「OutputBlobDataset」と入力します。

5. プロパティ ウィンドウの **[接続]** タブに移動します。 [リンクされたサービス] ボックスの横にある **[+ 新規]** を選択します。

    ![Blob データセットの接続を構成する](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. [New Linked Service]\(新しいリンクされたサービス) ウィンドウで、名前として「AzureStorageLinkedService」と入力し、認証方法のドロップダウン リストから [サービス プリンシパル] を選択します。サービス エンドポイント、テナント サービスのプリンシパル ID、およびサービス プリンシパル キーを記入したら、[保存] を選択し、リンクされたサービスをデプロイします。  Azure Blob Storage のサービス プリンシパルの認証を設定する方法については、[ここ](connector-azure-blob-storage.md#service-principal-authentication)を参照してください。

    ![BLOB の新しいリンクされたサービス](./media/load-office-365-data/new-blob-linked-service.png)

7. リンクされたサービスが作成されると、データセットの設定に戻ります。 ファイル パスの横にある **[参照]** を選択して、Office 365 データの抽出先となる出力フォルダーを選択します。  [File Format Settings]\(ファイル形式設定) で、[File Format]\(ファイル形式) の横にある **[JSON format]** \(JSON 形式) を選択し、[File Pattern]\(ファイル パターン) の横の **[Set of objects]\(オブジェクトのセット)** を選択します。

    ![BLOB データセットのパスと形式を構成する](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. パイプライン -> [シンク] タブに戻り、[OutputBlobDataset] が選択されていることを確認します。

## <a name="validate-the-pipeline"></a>パイプラインを検証する

パイプラインを検証するには、ツール バーから **[検証]** を選択します。

右上隅にある [コード] をクリックすると、パイプラインに関連付けられている JSON コードを参照することもできます。

## <a name="publish-the-pipeline"></a>パイプラインを発行する

上部ツールバーの **[すべて発行]** を選択します。 これにより、作成したエンティティ (データセットとパイプライン) が Data Factory に発行されます。

![変更を発行する](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>パイプラインを手動でトリガーする

ツール バーの **[トリガー]** を選択し、**[Trigger Now]\(今すぐトリガー\)** を選択します。 [Pipeline Run]\(パイプラインの実行\) ページで **[完了]** を選択します。 

## <a name="monitor-the-pipeline"></a>パイプラインの監視

左側の **[監視]** タブに移動します。 手動トリガーによってトリガーされたパイプラインの実行が表示されます。 **[アクション]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

![パイプラインを監視する](./media/load-office-365-data/pipeline-monitoring.png) 

パイプラインの実行に関連付けられているアクティビティの実行を表示するには、[アクション] 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 この例では、アクティビティが 1 つだけなので、一覧に表示されるエントリは 1 つのみです。 コピー操作の詳細については、[アクション] 列の **[詳細]** リンク (眼鏡アイコン) を選択します。

![アクティビティを監視する](./media/load-office-365-data/activity-monitoring.png) 

このコンテキスト (アクセスされようとしているデータ テーブル、データが読み込まれようとしている宛先アカウント、データ アクセス要求を行っているユーザー ID の組み合わせ) のデータを要求するのはこれが初めてである場合は、コピー アクティビティの状態が "**In Progress**(進行中)" として表示されます。[アクション] の下にある [詳細] リンクをクリックしたときにのみ、状態は "**RequesetingConsent**" と表示されます。  データの抽出を進めるには、データ アクセス承認者グループのメンバーが、Privileged Access Management で要求を承認する必要があります。

_同意を要求しているときの状態:_
![アクティビティ実行の詳細 - 同意の要求](./media/load-office-365-data/activity-details-request-consent.png) 

_データを抽出しているときの状態:_

![アクティビティの実行の詳細 - データの抽出](./media/load-office-365-data/activity-details-extract-data.png) 

同意が得られるとデータの抽出が続行されて、しばらくするとパイプラインの実行が完了として表示されます。

![パイプラインの監視 - 成功](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

ここで抽出先の Azure Blob Storage に移動し、Office 365 データが JSON 形式で抽出されていることを確認します。

## <a name="next-steps"></a>次の手順

次の資料に進んで、Azure SQL Data Warehouse のサポートを確認します。 

> [!div class="nextstepaction"]
>[Office 365 コネクタ](connector-office-365.md)