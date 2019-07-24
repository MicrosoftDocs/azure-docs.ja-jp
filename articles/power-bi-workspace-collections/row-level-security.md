---
title: Power BI ワークスペース コレクションでの行レベルのセキュリティ
description: Power BI ワークスペース コレクションでの行レベルのセキュリティの詳細
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: ae9819cd5b27d794f26227f342cb0fdf1b0ab644
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518390"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションでの行レベルのセキュリティ

行レベルのセキュリティ (RLS) を使用すると、レポートやデータセット内の特定のデータへのユーザー アクセスを制限できるため、同じレポートを使用する複数のユーザーにそれぞれ異なるデータを表示することが可能になります。 Power BI ワークスペース コレクションでは、RLS で構成されたデータセットがサポートされます。

![Power BI ワークスペース コレクションでの行レベルのセキュリティのフロー](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

RLS を利用するには、ユーザー、ロール、ルールの 3 つの主要概念を理解しておくことが重要です。 各概念について詳しく見てみましょう。

**ユーザー** – レポートを表示する実際のエンドユーザーです。 Power BI ワークスペース コレクションでは、ユーザーがアプリケーション トークンのユーザー名プロパティによって識別されます。

**ロール** – ユーザーはロールに属しています。 ロールとはルールのコンテナーです。ロールには、"Sales Manager" や "Sales Rep" のような名前を付けることができます。 Power BI ワークスペース コレクションでは、ユーザーがアプリケーション トークンのロール プロパティによって識別されます。

**ルール** – ロールにはルールが含まれます。これらのルールは、データに適用される実際のフィルターです。 これは、"Country = USA" のように単純な場合もあれば、もっと動的なものの場合もあります。

### <a name="example"></a>例

この記事の残りの部分では、RLS を作成し、埋め込みアプリケーション内でその RLS を使用する例を紹介します。 この例では、 [Retail Analysis Sample](https://go.microsoft.com/fwlink/?LinkID=780547) PBIX ファイルを使用します。

![セールス レポートの例](media/row-level-security/scenario-2.png)

Retail Analysis Sample では、特定の小売チェーンのすべての店舗の売上が示されます。 RLS を使用しない場合、どの地区マネージャーがサインインしてレポートを表示しても、同じデータが表示されます。 上級管理職が、各地区マネージャーに対して、それぞれが管理する店舗の売上だけが表示されるようにすることを決定しました。これを実現するために RLS を使用します。

RLS は Power BI Desktop で作成されます。 データセットとレポートが開いたら、ダイアグラム ビューに切り替えてスキーマを確認できます。

![Power BI Desktop のモデル ダイアグラム](media/row-level-security/diagram-view-3.png)

このスキーマでは、次の点に注意してください。

* **Total Sales** など、すべてのメジャーが **Sales** ファクト テーブルに格納されています。
* 関連するその他のディメンション テーブルとして、**Item**、**Time**、**Store**、**District** の 4 つのテーブルがあります。
* リレーションシップの線上の矢印は、テーブル間のフィルターの方向を示しています。 たとえば、**Time[Date]** にフィルターを配置すると、現在のスキーマでは **Sales** テーブルの値だけがフィルター処理されます。 リレーションシップの線上のすべての矢印が Sales テーブルを指しているため、他のテーブルはこのフィルターの影響を受けません。
* **District** テーブルは、次のように各地区の担当マネージャーを示しています。
  
  ![District テーブルの行](media/row-level-security/district-table-4.png)

このスキーマに基づいて、District テーブルの **District Manager** 列にフィルターを適用し、そのフィルターがレポートを表示するユーザーに一致した場合、フィルターによって **Store** テーブルと **Sales** テーブルもフィルター処理され、該当する地区マネージャーにのみデータが表示されます。

その方法は次のとおりです。

1. [モデリング] タブで **[ロールの管理]** をクリックします。  
   ![[モデリング] リボンの [ロールの管理] ボタン](media/row-level-security/modeling-tab-5.png)
2. **Manager**という新しいロールを作成します。  
   ![Power BI Desktop でのロールの作成](media/row-level-security/manager-role-6.png)
3. **District** テーブルで、**[District Manager] = USERNAME()** という DAX 式を入力します。  
   ![ロールのテーブルの DAX フィルター式](media/row-level-security/manager-role-7.png)
4. ルールが機能していることを確認するには、**[モデリング]** タブで **[ロールとして表示]** をクリックし、次のように入力します。  
   ![[ロールとして表示]](media/row-level-security/view-as-roles-8.png)

   これで、**Andrew Ma** としてサインインした場合と同様に、レポートにデータが表示されるようになります。

ここで設定したフィルターを適用すると、**District**、**Store**、**Sales** の各テーブルのすべてのレコードがフィルター処理されます。 ただし、**Sales** と **Time**、**Sales** と **Item**、**Item** と **Time** の各テーブル間のリレーションシップでのフィルターの方向から、Item テーブルのレコードはフィルター処理されません。

![リレーションシップが強調表示されたダイアグラム ビュー](media/row-level-security/diagram-view-9.png)

これはこの要件では問題ありません。ただし、マネージャーに売上のない商品を表示しない場合は、リレーションシップで双方向のクロス フィルターを有効にし、両方向にセキュリティ フィルターを適用することができます。 これを実行するには、**Sales** と **Item** 間のリレーションシップを次のように編集します。

![リレーションシップのクロス フィルターの方向](media/row-level-security/edit-relationship-10.png)

これで、Sales テーブルから **Item** テーブルの方向にもフィルターを適用できます。

![ダイアグラム ビューでのリレーションシップの [フィルターの方向] アイコン](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> データに DirectQuery モードを使用している場合は、次の 2 つのオプションを選択して、双方向のクロス フィルターを有効にする必要があります。

1. **[ファイル]** -> **[オプションと設定]** -> **[プレビュー機能]** -> **[Enable cross filtering in both directions for DirectQuery (DirectQuery 用に両方向のクロス フィルターを有効にする)]**。
2. **[ファイル]** -> **[オプションと設定]** -> **[DirectQuery]** -> **[DirectQuery モードで無制限のメジャーを許可する]**。

双方向のクロス フィルターの詳細については、ホワイトペーパー [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) をダウンロードしてください。

このホワイトペーパーでは、Power BI Desktop で実行する必要があるすべての作業がまとめられていますが、定義した RLS のルールを Power BI Embedded で機能させるために実行する必要がある作業がもう 1 つあります。 ユーザーはアプリケーションによって認証および承認され、アプリ トークンを使用して、そのユーザーに特定の Power BI Embedded レポートへのアクセス権が付与されます。 Power BI Embedded には、ユーザーが誰であるかに関する具体的な情報はありません。 RLS を機能させるには、アプリケーション トークンの一部として追加のコンテキストを渡す必要があります。

* **username** (省略可能) – RLS で使用されます。この文字列を使用して、RLS のルールを適用するときにユーザーを特定できます。 「Using Row Level Security with Power BI Embedded (Power BI Embedded での行レベルのセキュリティの使用)」をご覧ください。
* **roles** – 行レベルのセキュリティのルールを適用するときに選択したロールを含む文字列。 複数のロールを渡す場合は、文字列配列として渡す必要があります。

トークンは [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) メソッドを使用して作成します。 username プロパティが存在する場合は、roles にも 1 つ以上の値を渡す必要があります。

たとえば、EmbedSample を変更して、 DashboardController の 55 行目を次のように更新することもできます。

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

完全なアプリケーション トークンは、次のようになります。

![JSON Web トークンの例](media/row-level-security/app-token-string-12.png)

これですべてが揃いました。ユーザーは、アプリケーションにログインしてこのレポートを表示したときに、行レベルのセキュリティでの定義に従って、表示が許可されているデータを表示することができます。

![アプリケーションに表示されるレポート](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>関連項目

[Power の行レベルのセキュリティ (RLS)](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。
