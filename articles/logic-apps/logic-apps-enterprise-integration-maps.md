---
title: "XSLT マップを使用した XML 変換 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps と Enterprise Integration Pack を使用して XML データを変換する XSLT マップを追加する"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 7729890157900d0211b3a7ec05096ca315018875
ms.openlocfilehash: 23d79fb83c1c4b103407c001dcafb8b1f3cfa5a7


---
# <a name="add-maps-for-xml-data-transform"></a>XML データ変換のためのマップを追加する

エンタープライズ統合では、マップを使用して XML データを別の形式に変換します。 マップとは、ドキュメント内のどのデータを別の形式に変換するかを定義する XML ドキュメントです。 

## <a name="why-use-maps"></a>マップを使用する理由

たとえば、B2B の注文または請求書を顧客から定期的に受け取るとします。顧客が使う日付の形式は YYYMMDD です。 しかし自分の組織では、MMDDYYY の形式で日付を保存しています。 マップを使用することで、注文または請求書の詳細を顧客活動データベースに保存する前に、日付の形式を YYYMMDD から MMDDYYY に*変換*できます。

## <a name="how-do-i-create-a-map"></a>マップを作成する方法

Visual Studio 2015 の [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") を使用して、Biztalk の統合プロジェクトを作成します。 次に統合マップ ファイルを作成し、2 つの XML スキーマ ファイルの間でアイテムを視覚的にマップします。 このプロジェクトをビルドすると、XSLT ドキュメントが生成されます。

## <a name="how-do-i-add-a-map"></a>マップを追加する方法

1. Azure Portal で **[参照]** を選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. フィルター検索ボックスに「**統合**」と入力し、結果リストから **[統合アカウント]** を選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. マップの追加先となる統合アカウントを選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. **[マップ]** タイルを選択します。

    ![](./media/logic-apps-enterprise-integration-maps/map-1.png)

5. [マップ] ブレードが開いたら、**[追加]** を選択します。

    ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  

6. マップの**名前**を入力します。 **[マップ]** テキスト ボックスの右側にあるフォルダー アイコンをクリックして、マップ ファイルをアップロードします。 アップロード プロセスが完了したら、**[OK]** を選択します。

    ![](./media/logic-apps-enterprise-integration-maps/map-3.png)

7. マップが統合アカウントに追加されると、マップ ファイルが追加されたことを示すメッセージが画面に表示されます。 このメッセージが表示されたら、**[マップ]** タイルを選択すると、新しく追加したマップが表示されます。

    ![](./media/logic-apps-enterprise-integration-maps/map-4.png)

## <a name="how-do-i-edit-a-map"></a>マップを編集する方法

必要な変更を施した新しいマップ ファイルをアップロードする必要があります。 最初に編集するマップをダウンロードします。

既存のマップに替わる新しいマップをアップロードするには、次の手順に従います。

1. **[マップ]** タイルを選択します。

2. [マップ] ブレードが開いたら、編集するマップを選択します。

3. **[マップ]** ブレードで **[更新]** を選択します。

    ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)

4. アップロードするマップ ファイルをファイル ピッカーで選択し、**[開く]** を選択します。

    ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)

## <a name="how-to-delete-a-map"></a>マップを削除する方法

1. **[マップ]** タイルを選択します。

2. [マップ] ブレードが開いたら、削除するマップを選択します。

3. **[削除]** を選択します。

    ![](./media/logic-apps-enterprise-integration-maps/delete.png)

4. マップを削除することを確認します。

    ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  
* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  
* [変換の詳細](logic-apps-enterprise-integration-transform.md "エンタープライズ統合変換についての詳細")  




<!--HONumber=Feb17_HO1-->


