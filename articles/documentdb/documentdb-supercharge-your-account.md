---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>DocumentDB S1、S2 または S3 アカウントを移行します。
次の手順に従って、Standard 価格レベルに移行し、Azure DocumentDB アカウントのスループットを向上させ、活用します。 追加コストをほとんどかけずに、既存の S1 アカウントのスループットを 250 [RU/秒](documentdb-request-units.md) から 400 RU/秒以上に向上させることができます。 すべての Standard アカウントは、アプリケーションのニーズに合うようにスループットを向上させる機能のメリットを受けることができます。 定義済みのスループット オプションから選択する必要はなくなります。アプリケーションで必要なスループットを達成する必要があるときに、いつでも拡張できます。 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Azure ポータルでユーザー定義のパフォーマンスを変更する
1. ブラウザーで [**Azure Portal**](https://portal.azure.com) に移動します。 
2. 左側のメニューで **[NoSQL (DocumentDB)]** をクリックします。または、**[その他のサービス]** をクリックし、**[データベース]** までスクロールしてから **[NoSQL (DocumentDB)]** を選択します。   
3. **[NoSQL (DocumentDB)]** ブレードで、変更するアカウントを選択します。
4. 新しいブレードの **[コレクション]** の下のメニューで **[スケール]** をクリックします。 

      ![Screen shot of the DocumentDB Settings and Choose your pricing tier blades](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. 上のスクリーンショットで次の操作を行います。 

 - 新しいブレードで、ドロップダウン メニューを使用して、S1、S2 または S3 価格レベルのコレクションを選択します。 
 - **[価格レベル] の[S1]**、**[S2]** または **[S3]** をクリックします。
 - **[価格レベルの選択]** ブレードで **[Standard]** をクリックし、**[選択]** をクリックして変更を保存します。
   
6. **[スケール]** ブレードに戻ると、**[スループット (RU/s)]** ボックスには既定値の 400 が表示され、**[価格レベル]** が **[Standard]** に変更されています。  スループットは、アプリケーションで必要などのような値にも変更できます。 [DocumentDB Capacity Planner](https://www.documentdb.com/capacityplanner) を使用して、アプリケーションで必要な[要求単位](documentdb-request-units.md)/秒 (RU/s) を判別することをお勧めします。 ページの下部にある **[毎月の請求の概算]** 領域が自動的に更新され、月額料金の見積もりが表示されます。 **[保存]** をクリックして変更を保存します。 
      
    ![Screen shot of the Settings blade showing where to change the throughput value](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. コレクションが新しい要件を満たすように拡張され、成功メッセージが表示されます。  
   
    ![Screen shot of the Database blade with modified collection](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

ユーザー定義のスループットと事前定義されたスループットに関連した変更の詳細については、ブログ記事「 [DocumentDB: Everything you need to know about using the new pricing options (DocumentDB: 新しい価格オプションの使用に関して知っておく必要があるすべてのこと)](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

DocumentDB でデータをパーティション分割してグローバルなスケーリングを実現する方法の詳細については、「[Azure DocumentDB でのパーティション分割とスケーリング](documentdb-partition-data.md)」を参照してください。



<!--HONumber=Feb17_HO2-->


