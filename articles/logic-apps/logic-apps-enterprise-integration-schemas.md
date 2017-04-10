---
title: "XML 検証のスキーマ - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps と Enterprise Integration Pack でスキーマを使用して XML ドキュメントを検証する"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7884032fd4b5a72ca7e2f2dc2830b0a7c2220e94
ms.openlocfilehash: 5d8ddd4449d3b160dbca7496c801e75dcf4a44e5


---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack でスキーマを使用して XML ドキュメントを検証する

スキーマを使用すると、受信した XML ドキュメントが有効であり、予期したデータが定義済みの形式でドキュメントに含まれていることを確認できます。 また、B2B シナリオで交換されるメッセージを検証することもできます。

## <a name="add-a-schema"></a>スキーマを追加する

1. Azure Portal で **[その他のサービス]** を選択します。

    ![Azure Portal の [その他のサービス]](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. フィルター検索ボックスに「**統合**」と入力し、結果リストから **[統合アカウント]** を選択します。

    ![フィルター検索ボックス](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. スキーマの追加先となる**統合アカウント**を選択します。

    ![統合アカウントの一覧](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. **[スキーマ]** タイルを選択します。

    ![サンプルの統合アカウントの [スキーマ]](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>2 MB 未満のスキーマ ファイルを追加する

1. (前の手順の結果) 開いた **[スキーマ]** ブレードで、**[追加]** を選択します。

    ![[スキーマ] ブレードの [追加]](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. スキーマの名前を入力します。 **[スキーマ]** ボックスの横にあるフォルダー アイコンをクリックして、スキーマ ファイルをアップロードします。 アップロード プロセスが完了したら、**[OK]** を選択します。

    ![[小さいファイル] が強調表示されている [スキーマの追加] のスクリーンショット](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>2 MB 以上のスキーマ ファイルを追加する (最大 8 MB)

この手順は、BLOB コンテナーのアクセス レベル (**パブリック**または**匿名アクセスはありません**) によって異なります。

**アクセス レベルを確認するには**

1.  **Azure ストレージ エクスプローラー**を開きます。 

2.  **[BLOB コンテナー]** で、目的の BLOB コンテナーを選択します。 

3.  **[セキュリティ]**、**[アクセス レベル]** の順に選択します。

BLOB のセキュリティ レベルが **[パブリック]** の場合は、次の手順に従います。

![BLOB コンテナー、[セキュリティ]、および [パブリック] が強調表示されている Azure ストレージ エクスプローラー](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. スキーマをストレージ アカウントにアップロードし、URI をコピーします。

    ![URI が強調表示されているストレージ アカウント](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. **[スキーマの追加]** で **[大きいファイル]** を選択し、**[コンテンツ URI]** に URI を指定します。

    ![[追加] ボタンと [大きいファイル] が強調表示されている [スキーマ]](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

BLOB のセキュリティ レベルが **[匿名アクセスはありません]** の場合は、次の手順に従います。

![BLOB コンテナー、[セキュリティ]、および [匿名アクセスはありません] が強調表示されている Azure ストレージ エクスプローラー](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. スキーマをストレージ アカウントにアップロードします。

    ![ストレージ アカウント](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. スキーマの共有アクセス署名を生成します。

    ![[共有アクセス署名] タブが強調表示されているストレージ アカウント](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. **[スキーマの追加]** で **[大きいファイル]** を選択し、**[コンテンツ URI]** に共有アクセス署名 URI を指定します。

    ![[追加] ボタンと [大きいファイル] が強調表示されている [スキーマ]](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. 統合アカウントの **[スキーマ]** ブレードに、新たに追加されたスキーマが表示されます。

    ![[スキーマ] と新しいスキーマが強調表示されている統合アカウント](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>スキーマを編集する

1. **[スキーマ]** タイルを選択します。

2. **[スキーマ]** ブレードが開いたら、編集するスキーマを選択します。

3. **[スキーマ]** ブレードで **[編集]** を選択します。

    ![[スキーマ] ブレード](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. 編集するスキーマ ファイルを選択し、**[開く]** を選択します。

    ![編集するスキーマ ファイルを開く](media/logic-apps-enterprise-integration-schemas/edit-31.png)

スキーマが正常にアップロードされたことを示すメッセージが表示されます。

## <a name="delete-schemas"></a>スキーマを削除する

1. **[スキーマ]** タイルを選択します。

2. **[スキーマ]** ブレードが開いたら、削除するスキーマを選択します。

3. **[スキーマ]** ブレードで **[削除]** を選択します。

    ![[スキーマ] ブレード](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. 選択したスキーマの削除を確定するには、**[はい]** を選択します。

    ![[スキーマの削除] 確認メッセージ](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    **[スキーマ]** ブレードのスキーマの一覧が更新され、削除したスキーマが表示されなくなります。

    ![[スキーマ] が強調表示されている統合アカウント](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  




<!--HONumber=Feb17_HO1-->


