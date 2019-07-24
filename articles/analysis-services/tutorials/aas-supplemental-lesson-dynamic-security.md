---
title: 'Azure Analysis Services チュートリアルの補足のレッスン: 動的なセキュリティ | Microsoft Docs'
description: この Azure Analysis Services チュートリアルでは、行フィルターを使用して、動的なセキュリティの使用方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1908d655064a4a320191695c048271246951c29c
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187486"
---
# <a name="supplemental-lesson---dynamic-security"></a>補足のレッスン - 動的なセキュリティ

この補足のレッスンでは、動的なセキュリティを実装する追加のロールを作成します。 動的なセキュリティは、現在ログオンしているユーザーのユーザー名またはログイン ID に基づく行レベルのセキュリティを提供します。 
  
動的なセキュリティを実装するには、モデルに接続してモデル オブジェクトやデータを参照できるユーザーのユーザー名が含まれているテーブルをモデルに追加します。 このチュートリアルを使用して作成するモデルは、Adventure Works のコンテキスト内にありますが、このレッスンを完了するには、自分のドメインのユーザーが含まれているテーブルを追加する必要があります。 追加するユーザー名のパスワードは必要ありません。 自分のドメインの少人数のサンプル ユーザーを使用して EmployeeSecurity テーブルを作成するには、貼り付け機能を使用して、Excel のスプレッドシートから従業員データを貼り付けます。 実際のシナリオでは、ユーザー名が含まれたテーブルは通常、データ ソースとしての実際のデータベースのテーブルです (たとえば、実際の DimEmployee テーブルなど)。  
  
動的なセキュリティを実装するには、[USERNAME 関数 (DAX)](https://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) および [LOOKUPVALUE 関数 (DAX)](https://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab) という 2 つの DAX 関数を使用します。 行フィルター式に適用されるこれらの関数は、新しいロールで定義されています。 LOOKUPVALUE 関数を使用した場合、この数式は EmployeeSecurity テーブルの値を指定します。 次に、その値を USERNAME 関数に渡し、ログオンしたユーザーのユーザー名がこのロールに属していることを指定します。 ユーザーは、ロールの行フィルターによって指定されたデータのみを参照できます。 このシナリオでは、販売担当者がインターネット上で自分の販売区域の売上データのみを参照できるように指定します。  
  
この Adventure Works 表形式モデルのシナリオに固有の作業で、実際のシナリオに必ずしも適用されないものは、そのように記載されています。 各作業には、作業の目的を説明する追加情報が含まれています。  
  
このレッスンの推定所要時間: **30 分**  
  
## <a name="prerequisites"></a>前提条件  
この補足のレッスンのトピックは表形式モデルのチュートリアルの一部であり、順番に従って実行する必要があります。 この補足のレッスンの作業を実行する前に、前のレッスンをすべて完了している必要があります。  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>AW Internet Sales Tabular Model プロジェクトへの DimSalesTerritory テーブルの追加  
この Adventure Works のシナリオの動的セキュリティを実装するには、2 つの追加テーブルをモデルに追加する必要があります。 最初に追加するテーブルは、同じ AdventureWorksDW データベースの (販売区域としての) DimSalesTerritory です。 後から、ログオンしているユーザーが参照できる特定のデータを定義する行フィルターを SalesTerritory テーブルに適用します。  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>DimSalesTerritory テーブルを追加するには  
  
1.  表形式モデル エクスプローラーで **[データ ソース]** を展開し、接続を右クリックして **[新しいテーブルのインポート]** をクリックします。  

    [権限借用の資格情報] ダイアログ ボックスが表示された場合は、「レッスン 2: データの追加」で使用した権限借用の資格情報を入力します。
  
2.  ナビゲーターで、**DimSalesTerritory** テーブルを選択し、**[OK]** をクリックします。    
  
3.  クエリ エディターで、**DimSalesTerritory** クエリをクリックして、**SalesTerritoryAlternateKey** 列を削除します。  
  
7.  **[インポート]** をクリックします。  
  
    新しいテーブルがモデル ワークスペースに追加されます。 ソースの DimSalesTerritory テーブルのオブジェクトおよびデータが AW Internet Sales Tabular Model にインポートされます。  
  
9. テーブルのインポートが正常に完了したら、**[閉じる]** をクリックします。  

## <a name="add-a-table-with-user-name-data"></a>ユーザー名データを含むテーブルの追加  
AdventureWorksDW サンプル データベース内の DimEmployee テーブルには、AdventureWorks ドメインのユーザーが含まれています。 ご使用の環境にこれらのユーザー名は存在しないので、 組織内の実際のユーザー (少なくとも 3 名) を使った小さいサンプルを含むテーブルをモデル内に作成する必要があります。 その後、これらのユーザーを新しいロールにメンバーとして追加します。 サンプルのユーザー名のパスワードは不要ですが、自分のドメインの実際の Windows ユーザー名が必要になります。  
  
#### <a name="to-add-an-employeesecurity-table"></a>EmployeeSecurity テーブルを追加するには  
  
1.  Microsoft Excel を開き、ワークシートを作成します。  
  
2.  ヘッダー行を含む次のテーブルをコピーし、ワークシートに貼り付けます。  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  名、姓、および domain\username を組織の 3 人のユーザーの名前とログイン ID に置き換えます。 最初の 2 行 (EmployeeId 1 の行) には同じユーザーを入力します。これは、このユーザーが複数の販売区域に属していることを示します。 EmployeeId フィールドと SalesTerritoryId フィールドはそのままにします。  
  
4.  ワークシートを **SampleEmployee** として保存します。  
  
5.  ワークシートで、従業員データを含むすべてのセルをヘッダーと共に選択し、選択したデータを右クリックし、**[コピー]** をクリックします。  
  
6.  SSDT で、**[編集]** メニューをクリックし、**[貼り付け]** をクリックします。  
  
    [貼り付け] がグレー表示されている場合は、モデル デザイナー ウィンドウの任意のテーブルの任意の列をクリックし、もう一度操作を行います。  
  
7.  **[貼り付けプレビュー]** ダイアログ ボックスの **[テーブル名]** に、「**EmployeeSecurity**」と入力します。  
  
8.  **[貼り付けるデータ]** で、SampleEmployee ワークシートのすべてのユーザー データとヘッダーが含まれていることを確認します。  
  
9. **[先頭の行を列見出しとして使用する]** がオンであることを確認し、**[OK]** をクリックします。  
  
    SampleEmployee ワークシートからコピーした従業員データを含む、EmployeeSecurity という名前の新しいテーブルが作成されます。  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>FactInternetSales、DimGeography、および DimSalesTerritory テーブル間のリレーションシップの作成  
FactInternetSales、DimGeography、および DimSalesTerritory テーブルはすべて、共通の列である SalesTerritoryId を含みます。 DimSalesTerritory テーブルの SalesTerritoryId 列には、販売区域ごとに異なる ID の値が含まれています。  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>FactInternetSales、DimGeography、および DimSalesTerritory テーブル間のリレーションシップを作成するには  
  
1.  ダイアグラム ビューの **DimGeography** テーブルで、**SalesTerritoryId** 列をクリックし、そのままカーソルを **DimSalesTerritory** テーブル内の **SalesTerritoryId** 列にドラッグして離します。  
  
2.  **FactInternetSales** テーブルで、**SalesTerritoryId** 列をクリックし、そのままカーソルを **DimSalesTerritory** テーブル内の **SalesTerritoryId** 列にドラッグして離します。  
  
    このリレーションシップの Active プロパティが False (つまり非アクティブ) になっていることを確認します。 FactInternetSales テーブルには、既に別のアクティブなリレーションシップがあります。  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>クライアント アプリケーションで EmployeeSecurity テーブルを非表示にする  
この作業では、EmployeeSecurity テーブルを非表示にして、クライアント アプリケーションのフィールド リストに表示されないようにします。 テーブルを非表示にしても、セキュリティで保護されるわけではないことに注意してください。 方法を知っている場合、ユーザーは引き続き EmployeeSecurity テーブル データのクエリを実行できます。 EmployeeSecurity テーブル データをセキュリティで保護し、ユーザーがデータに対してクエリを実行できないようにするには、後の作業で取り上げられるフィルターを適用します。  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>クライアント アプリケーションで EmployeeSecurity テーブルを非表示にするには  
  
-   モデル デザイナーのダイアグラム ビューで、**Employee** テーブルの見出しを右クリックし、**[クライアント ツールに非表示]** をクリックします。  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Sales Employees by Territory ユーザー ロールの作成  
この作業では、ユーザー ロールを作成します。 このロールには、DimSalesTerritory テーブル内のどの行がユーザーに表示されるかを定義する行フィルターが含まれます。 このフィルターは、一対多のリレーションシップの方向で、DimSalesTerritory に関連する他のすべてのテーブルに適用されます。 また、ロールのメンバーであるユーザーならだれでもクエリを実行できる状況を避けるために、EmployeeSecurity テーブル全体を保護するフィルターも適用します。  
  
> [!NOTE]  
> このレッスンで作成する Sales Employees by Territory ロールによって、メンバーが参照 (またはクエリを実行) できるデータは、自分が属する販売区域の売上データのみに制限されます。 Sales Employees by Territory ロールにメンバーとして追加したユーザーが、「[レッスン 11: ロールの作成](../tutorials/aas-lesson-11-create-roles.md)」で作成したロールのメンバーでもある場合、権限は組み合わせとなります。 あるユーザーが複数のロールのメンバーである場合、各ロールに対して定義された権限と行フィルターは累積されます。 つまり、ロールを組み合わせることでユーザーの権限は引き上げられます。  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Sales Employees by Territory ユーザー ロールを作成するには  
  
1.  SSDT で、**[モデル]** メニューをクリックし、**[ロール]** をクリックします。  
  
2.  **[ロール マネージャー]** で、**[新規]** をクリックします。  
  
    "なし" 権限を設定された新しいロールがリストに追加されます。  
  
3.  新しいロールをクリックし、**[名前]** 列で、ロールの名前を **Sales Employees by Territory** に変更します。  
  
4.  **[権限]** 列で、ドロップダウン リストをクリックし、**[読み取り]** 権限を選択します。  
  
5.  **[メンバー]** タブをクリックし、**[追加]** をクリックします。  
  
6.  **[ユーザーまたはグループの選択]** ダイアログ ボックスの **[選択するオブジェクト名を入力してください]** に、EmployeeSecurity テーブルの作成で使用した最初のサンプル ユーザー名を入力します。 **[名前の確認]** をクリックしてユーザー名が有効であることを確認し、**[OK]** をクリックします。  
  
    この手順を繰り返して、EmployeeSecurity テーブルの作成で使用した他のサンプル ユーザー名を追加します。  
  
7.  **[行フィルター]** タブをクリックします。  
  
8.  **EmployeeSecurity** テーブルについて、**[DAX フィルター]** 列に次の式を入力します。  
  
    ```
      =FALSE()  
    ```
  
    この式は、すべての列が false ブール条件に解決されることを指定します。 Sales Employees by Territory ユーザー ロールのメンバーは、EmployeeSecurity テーブルの列を照会できません。  
  
9. **DimSalesTerritory** テーブルには、次の数式を入力します。  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    この数式では、LOOKUPVALUE 関数によって、EmployeeSecurity[LoginId] と現在ログオンしている Windows ユーザー名が同じで、EmployeeSecurity[SalesTerritoryId] と DimSalesTerritory[SalesTerritoryId] が同じであるすべての列の DimEmployeeSecurity[SalesTerritoryId] の値が返されます。  
  
    LOOKUPVALUE によって返された一連の 販売区域の ID を使用して、DimSalesTerritory テーブルに表示される行が制限されます。 行の SalesTerritoryID が LOOKUPVALUE 関数で返された一連の ID の中にある行のみが表示されます。  
  
10. [ロール マネージャー] で、**[OK]** をクリックします。  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Sales Employees by Territory ユーザー ロールのテスト  
この作業では、SSDT の [Excel で分析] 機能を使用して、Sales Employees by Territory ユーザー ロールの有効性をテストします。 EmployeeSecurity テーブルにロールのメンバー追として加したいずれかのユーザー名を指定します。 このユーザー名は、Excel とモデルの間に作成された接続で有効なユーザー名として使用されます。  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Sales Employees by Territory ユーザー ロールをテストするには  
  
1.  SSDT で、**[モデル]** メニューをクリックし、**[Excel で分析]** をクリックします。  
  
2.  **[Excel で分析]** ダイアログ ボックスの **[モデルへの接続に使用するユーザー名またはロールを指定します]** で、**[その他の Windows ユーザー]** をクリックし、**[参照]** をクリックします。  
  
3.  **[ユーザーまたはグループの選択]** ダイアログ ボックスの **[選択するオブジェクト名を入力します]** に、EmployeeSecurity テーブルに追加したいずれかのユーザー名を入力し、**[名前の確認]** をクリックします。  
  
4.  **[OK]** をクリックして **[ユーザーまたはグループの選択]** ダイアログ ボックスを閉じ、**[OK]** をクリックして **[Excel で分析]** ダイアログ ボックスを閉じます。  
  
    Excel で新しいブックが開きます。 ピボットテーブルが自動的に作成されます。 [ピボットテーブルのフィールド] リストには、新しいモデルで使用できるほとんどのデータ フィールドが表示されます。  
  
    EmployeeSecurity テーブルが [ピボットテーブルのフィールド] リストに表示されていないことを確認します。 このテーブルは、前の作業でクライアント ツールから非表示にしました。  
  
5.  **[フィールド]** リストの **∑ Internet Sales** (メジャー) で、**InternetTotalSales** メジャーを選択します。 メジャーは "**値**" フィールドに入力されます。  
  
6.  **DimSalesTerritory** テーブルの **SalesTerritoryId** 列を選択します。 列は "**行ラベル**" フィールドに入力されます。  
  
    使用した有効なユーザー名が属している 1 つのリージョンのインターネット販売成績のみが表示されていることに注意してください。 たとえば DimGeography テーブルの City など、別の列を "行ラベル" フィールドとして選択すると、有効なユーザーが属する販売区域の都市のみが表示されます。  
  
    このユーザーは、自分が属する区域以外の区域のインターネット販売データを参照したり、クエリを実行することはできません。 このように制限されるのは、Sales Employees by Territory ユーザー ロールで DimSalesTerritory テーブルに定義された行フィルターによって、他の販売区域に関連するすべてのデータが保護されているためです。  
  
## <a name="see-also"></a>関連項目  
[USERNAME 関数 (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE 関数 (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA 関数 (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  