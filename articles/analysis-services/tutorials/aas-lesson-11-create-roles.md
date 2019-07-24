---
title: 'Azure Analysis Services チュートリアルのレッスン 11: ロールを作成する | Microsoft Docs'
description: この Azure Analysis Services チュートリアル プロジェクトでロールを作成する方法を説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5b89051cab7e89f79a2b62a392173e6dc234e48d
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189747"
---
# <a name="create-roles"></a>ロールを作成する

このレッスンでは､ロールを作成します｡ ロールは､ロールのメンバーであるユーザーにのみアクセス権を制限することによって、モデル データベース オブジェクトとデータをセキュリティ保護します｡ 各ロールには､1 つの許可 (なし､読み取り､読み取りと処理､処理､管理者のいずれか) のみ定義します｡ ロールは､モデルのオーサリング中に Role Manager を使用して定義できます｡ モデルをデプロイした後は､SQL Server Management Studio (SSMS) を使用してロールを管理できます｡ 詳細は、[ロール](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular) を参照してください。
  
> [!NOTE]  
> このチュートリアルでは､ロールの作成は必須のレッスンではありません｡ 既定では､現在のログインに使用したアカウントがモデルの管理者権限を保有します｡ ただし､組織の他のユーザーがレポート用クライアントを使って参照できるようにするには､読み取り許可を持つロールを少なくとも 1 つ作成し､そのユーザーをメンバーとして追加する必要があります｡  
  
3 つのロールを作成します｡  
  
-   **Sales Manager** – このロールには､すべてのモデル オブジェクトとデータに対する読み取り権限を割り当てるユーザーを登録できます｡  
  
-   **Sales Analyst US** – このロールには､米国内の販売関係のデータの参照のみ許可するユーザーを登録できます｡ このロールに対しては DAX 式を使用して､メンバーを米国のみのデータ参照に制限する *Row Filter* を定義します｡  
  
-   **Administrator** – このロールには､管理者権限を持たせるユーザーを登録できます｡管理者権限では､モデル データベースに対する管理作業を行うために無制限のアクセスと権限が許可されます｡  
  
社内の Windows ユーザー アカウントとグループ アカウントは一意であるため､特定のアカウントをメンバーに追加できます｡ ただし､このチュートリアルでは､メンバーを空白のままにしておくこともできます｡ 各ロールの効果は､後で｢レッスン 12: Excel で分析する｣でテストします｡  
  
このレッスンの推定所要時間: **15 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは、表形式モデルのチュートリアルの一部であり、チュートリアルでの順番に従って実行する必要があります。 このレッスンのタスクを実行する前に、前のレッスン「[レッスン 10: パーティションを作成する](../tutorials/aas-lesson-10-create-partitions.md)」を完了する必要があります。  
  
## <a name="create-roles"></a>ロールを作成する  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Sales Manager ユーザー ロールを作成する  
  
1.  Tabular Model Explorer で **Roles** > **Roles** を右クリックします｡  
  
2.  [ロール マネージャー] で **[新規]** をクリックします。  
  
3.  新しいロールをクリックし、**Name** 列で、ロール名を **Sales Manger** に変更します。  
  
4.  **[権限]** 列で、ドロップダウン リストをクリックし、**[読み取り]** 権限を選択します。 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  省略可能:**[メンバー]** タブをクリックし、**[追加]** をクリックします。 **Select Users また Groups** ダイアログ ボックスでこのロールに含める社内 Windows ユーザーまたはグループを入力します｡  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Sales Analyst US ユーザー ロールを作成する  
  
1.  [ロール マネージャー] で **[新規]** をクリックします。    
  
2.  ロール名を **Sales Analyst US** に変更します｡  
  
3.  このロールに**読み取り**権限を付与します｡  
  
4.  [Row Filters] タブをクリックし､**DimGeography** テーブルのみについて､DAX Filter 列に次の式を入力します｡  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Row Filter 式の結果は ブール値 (真/偽) をとる必要があります｡ この式を使用して､Country Region Code 値が｢US｣の行のみユーザーに見えるように指定します｡  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  省略可能:**[メンバー]** タブをクリックし、**[追加]** をクリックします。 **Select Users また Groups** ダイアログ ボックスでこのロールに含める社内 Windows ユーザーまたはグループを入力します｡  
  
#### <a name="to-create-an-administrator-user-role"></a>Administrator ユーザー ロールを作成する  
  
1.  **[新規]** をクリックします。  
  
2.  ロール名を**管理者**に変更します。  
  
3.  このロールに**管理者**権限を付与します｡  
  
4.  省略可能:**[メンバー]** タブをクリックし、**[追加]** をクリックします。 **Select Users また Groups** ダイアログ ボックスでこのロールに含める社内 Windows ユーザーまたはグループを入力します｡ 
  
  
## <a name="whats-next"></a>次の手順
[レッスン 12: Excel で分析する](../tutorials/aas-lesson-12-analyze-in-excel.md)。

  
  
