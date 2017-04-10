---
title: "Power BI Embedded の行レベルのセキュリティ"
description: "Power BI Embedded の行レベルのセキュリティの詳細"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 7936ade5-2c75-435b-8314-ea7ca815867a
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1cde5b9ee4c716af07d427d4d0eb3f0775d456ac
ms.lasthandoff: 03/14/2017


---
# <a name="row-level-security-with-power-bi-embedded"></a>Power BI Embedded の行レベルのセキュリティ

行レベルのセキュリティ (RLS) を使用すると、レポートやデータセット内の特定のデータへのユーザー アクセスを制限できるため、同じレポートを使用する複数のユーザーにそれぞれ異なるデータを表示することが可能になります。 Power BI Embedded では、RLS を使用して構成されたデータセットがサポートされるようになりました。

![](media/power-bi-embedded-rls/pbi-embedded-rls-flow-1.png)

RLS を利用するには、ユーザー、ロール、ルールの&3; つの主要概念を理解しておくことが重要です。 各概念について詳しく見てみましょう。

**ユーザー** – レポートを表示する実際のエンドユーザーです。 Power BI Embedded では、ユーザーはアプリ トークンの username プロパティによって識別されます。

**ロール** – ユーザーはロールに属しています。 ロールとはルールのコンテナーです。ロールには、"Sales Manager" や "Sales Rep" のような名前を付けることができます。 Power BI Embedded では、ユーザーはアプリ トークンの roles プロパティによって識別されます。

**ルール** – ロールにはルールが含まれます。これらのルールは、データに適用される実際のフィルターです。 これは、"Country = USA" のように単純な場合もあれば、もっと動的なものの場合もあります。

### <a name="example"></a>例

この記事の残りの部分では、RLS を作成し、埋め込みアプリケーション内でその RLS を使用する例を紹介します。 この例では、 [Retail Analysis Sample](http://go.microsoft.com/fwlink/?LinkID=780547) PBIX ファイルを使用します。

![](media/power-bi-embedded-rls/pbi-embedded-rls-scenario-2.png)

Retail Analysis Sample では、特定の小売チェーンのすべての店舗の売上が示されます。 RLS を使用しない場合、どの地区マネージャーがサインインしてレポートを表示しても、同じデータが表示されます。 上級管理職が、各地区マネージャーに対して、それぞれが管理する店舗の売上だけが表示されるようにすることを決定しました。これを実現するために RLS を使用します。

RLS は Power BI Desktop で作成されます。 データセットとレポートが開いたら、ダイアグラム ビューに切り替えてスキーマを確認できます。

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-3.png)

このスキーマでは、次の点に注意してください。

* **Total Sales** など、すべてのメジャーが **Sales** ファクト テーブルに格納されています。
* 関連するその他のディメンション テーブルとして、**Item**、**Time**、**Store**、**District** の&4; つのテーブルがあります。
* リレーションシップの線上の矢印は、テーブル間のフィルターの方向を示しています。 たとえば、**Time[Date]** にフィルターを配置すると、現在のスキーマでは **Sales** テーブルの値だけがフィルター処理されます。 リレーションシップの線上のすべての矢印が Sales テーブルを指しているため、他のテーブルはこのフィルターの影響を受けません。
* **District** テーブルは、次のように各地区の担当マネージャーを示しています。
  
  ![](media/power-bi-embedded-rls/pbi-embedded-rls-district-table-4.png)

このスキーマに基づいて、District テーブルの **District Manager** 列にフィルターを適用し、そのフィルターがレポートを表示するユーザーに一致した場合、フィルターによって **Store** テーブルと **Sales** テーブルもフィルター処理され、該当する地区マネージャーにのみデータが表示されます。

その方法は次のとおりです。

1. [モデリング] タブで **[ロールの管理]**をクリックします。  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-modeling-tab-5.png)
2. **Manager**という新しいロールを作成します。  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-6.png)
3. **District** テーブルで、**[District Manager] = USERNAME()** という DAX 式を入力します。  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-7.png)
4. ルールが機能していることを確認するには、**[モデリング]** タブで **[ロールとして表示]** をクリックし、次のように入力します。  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-view-as-roles-8.png)
   
   これで、 **Andrew Ma**としてサインインした場合と同様に、レポートにデータが表示されるようになります。

ここで設定したフィルターを適用すると、**District**、**Store**、**Sales** の各テーブルのすべてのレコードがフィルター処理されます。 ただし、**Sales** と **Time**、**Sales** と **Item**、**Item** と **Time** の各テーブル間のリレーションシップでのフィルターの方向から、Item テーブルのレコードはフィルター処理されません。

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-9.png)

これはこの要件では問題ありません。ただし、マネージャーに売上のない商品を表示しない場合は、リレーションシップで双方向のクロス フィルターを有効にし、両方向にセキュリティ フィルターを適用することができます。 これを実行するには、**Sales** と **Item** 間のリレーションシップを次のように編集します。

![](media/power-bi-embedded-rls/pbi-embedded-rls-edit-relationship-10.png)

これで、Sales テーブルから **Item** テーブルの方向にもフィルターを適用できます。

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-11.png)

> [!NOTE]
> データに DirectQuery モードを使用している場合は、次の&2; つのオプションを選択して、双方向のクロス フィルターを有効にする必要があります。

1. **[ファイル]** -> **[オプションと設定]** -> **[プレビュー機能]** -> **[Enable cross filtering in both directions for DirectQuery (DirectQuery 用に両方向のクロス フィルターを有効にする)]**。
2. **[ファイル]** -> **[オプションと設定]** -> **[DirectQuery]** -> **[DirectQuery モードで無制限のメジャーを許可する]**。

双方向のクロス フィルターの詳細については、ホワイトペーパー [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) をダウンロードしてください。

このホワイトペーパーでは、Power BI Desktop で実行する必要があるすべての作業がまとめられていますが、定義した RLS のルールを Power BI Embedded で機能させるために実行する必要がある作業がもう&1; つあります。 ユーザーはアプリケーションによって認証および承認され、アプリ トークンを使用して、そのユーザーに特定の Power BI Embedded レポートへのアクセス権が付与されます。 Power BI Embedded には、ユーザーが誰であるかに関する具体的な情報はありません。 RLS を機能させるには、アプリ トークンの一部として追加のコンテキストを渡す必要があります。

* **username** (省略可能) – RLS で使用されます。この文字列を使用して、RLS のルールを適用するときにユーザーを特定できます。 「Using Row Level Security with Power BI Embedded (Power BI Embedded での行レベルのセキュリティの使用)」をご覧ください。
* **roles** – 行レベルのセキュリティのルールを適用するときに選択したロールを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。

トークンは [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__) メソッドを使用して作成します。 username プロパティが存在する場合は、roles にも&1; つ以上の値を渡す必要があります。

たとえば、EmbedSample を変更して、 DashboardController の 55 行目を次のように更新することもできます。

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

完全なアプリ トークンは次のようになります。

![](media/power-bi-embedded-rls/pbi-embedded-rls-app-token-string-12.png)

これですべてが揃いました。ユーザーは、アプリケーションにログインしてこのレポートを表示したときに、行レベルのセキュリティでの定義に従って、表示が許可されているデータだけを表示することができます。

![](media/power-bi-embedded-rls/pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>関連項目

[Power の行レベルのセキュリティ (RLS)](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)  
[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。


