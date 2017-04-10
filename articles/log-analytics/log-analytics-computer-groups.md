---
title: "Log Analytics のログ検索におけるコンピューター グループ | Microsoft Docs"
description: "Log Analytics では、コンピューター グループを使用して、ログ検索の範囲を特定のコンピューターの集合に限定することができます。  この記事では、コンピューター グループを作成する各種の方法とそれらをログ検索で使用する方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6c0affd0f5ea600f979cfcc87e2435658c8dab14


---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics のログ検索におけるコンピューター グループ
Log Analytics では、コンピューター グループを使用して、[ログ検索](log-analytics-log-searches.md)の範囲を特定のコンピューターの集合に限定することができます。  それぞれのグループには、自分で定義したクエリを使用するか、さまざまなソースからグループをインポートすることでコンピューターを追加します。  そのグループをログの検索に含めると、対応するグループ内のコンピューターと一致するレコードに検索結果が限定されます。

## <a name="creating-a-computer-group"></a>コンピューター グループの作成
Log Analytics のコンピューター グループは、以下の表に示した方法のいずれかで作成できます。  それぞれの方法について、以降のセクションで詳しく説明します。 

| メソッド | Description |
|:--- |:--- |
| ログ検索 |一連のコンピューターを返すログ検索を作成し、その結果をコンピューター グループとして保存します。 |
| Log Search API |ログ検索の結果に基づいてプログラムからコンピューター グループを作成するには、Log Search API を使用します。 |
| Active Directory |Active Directory ドメインに属しているエージェント コンピューターのグループ メンバーシップを自動的にスキャンし、セキュリティ グループごとのグループを Log Analytics に作成します。 |
| WSUS |WSUS のサーバーまたはクライアントを自動的にスキャンして WSUS の対象グループを取得し、それぞれのグループを Log Analytics に作成します。 |

### <a name="log-search"></a>ログ検索
ログ検索から作成されたコンピューター グループには、定義した検索クエリによって返されたすべてのコンピューターが含まれています。  このクエリは、コンピューター グループを使用するたびに実行されます。そうすることで、グループの作成以降に行われた変更が反映されます。

ログ検索からコンピューター グループを作成するには、次の手順に従います。

1. 一連のコンピューターを返す[ログ検索を作成](log-analytics-log-searches.md)します。  この検索は、クエリの中で **Distinct Computer** や **measure count() by Computer** を使用することによって重複を除外した一連のコンピューターを返す必要があります。  
2. 画面上部にある **[保存]** ボタンをクリックします。
3. **[このクエリを次のコンピューター グループとして保存します]** で **[はい]** を選択します。
4. グループの**名前**と**カテゴリ**を入力します。  同じ名前とカテゴリの検索が既に存在する場合、既存の検索を上書きするように求められます。  カテゴリが違えば同じ名前の検索が複数あってもかまいません。 

コンピューター グループとして保存できる検索の例を次に示します。

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>Log Search API
Log Search API を使って作成されたコンピューター グループは、[ログ検索] を使って作成された検索と同じです。

Log Search API を使ったコンピューター グループ作成の詳細については、[「Log Analytics のログ検索 REST API」の「コンピューター グループ」](log-analytics-log-search-api.md#computer-groups)を参照してください。

### <a name="active-directory"></a>Active Directory
Active Directory グループのメンバーシップをインポートするように Log Analytics を構成した場合、OMS エージェントがインストールされている、ドメインに参加しているすべてのコンピューターのグループ メンバーシップが分析されます。  Log Analytics には、Active Directory 内のセキュリティ グループごとにコンピューター グループが作成され、それぞれのコンピューターは、属しているセキュリティ グループに対応したコンピューター グループに追加されます。  このメンバーシップは絶えず 4 時間おきに更新されます。  

Active Directory のセキュリティ グループをインポートするために必要な Log Analytics の構成は、Log Analytics の **[設定]** の **[コンピューター グループ]** メニューから行います。  **[Automation]** を選択し、**[コンピューターから Active Directory のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![Active Directory からのコンピューター グループ](media/log-analytics-computer-groups/configure-activedirectory.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

### <a name="windows-server-update-service"></a>Windows Server Update Service
WSUS グループのメンバーシップをインポートするように Log Analytics を構成した場合、OMS エージェントがインストールされているすべてのコンピューターについて、WSUS の対象グループのメンバーシップが分析されます。  クライアント側のターゲット指定方式を使用している場合、OMS に接続されていて、かつ WSUS の対象グループに属しているすべてのコンピューターのグループ メンバーシップが Log Analytics にインポートされます。 サーバー側のターゲット指定方式を使用している場合、グループ メンバーシップ情報を OMS にインポートするためには、WSUS サーバーに OMS エージェントがインストールされている必要があります。  このメンバーシップは絶えず 4 時間おきに更新されます。 

Active Directory のセキュリティ グループをインポートするために必要な Log Analytics の構成は、Log Analytics の **[設定]** の **[コンピューター グループ]** メニューから行います。  **[Active Directory]** を選択し、**[コンピューターから Active Directory のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![Active Directory からのコンピューター グループ](media/log-analytics-computer-groups/configure-wsus.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

## <a name="managing-computer-groups"></a>コンピューター グループの管理
ログ検索または Log Search API から作成されたコンピューター グループは、Log Analytics の **[設定]** の **[コンピューター グループ]** メニューから表示できます。  **[削除]** 列の **[x]** をクリックすると、コンピューター グループが削除されます。  グループの **[メンバーの表示]** アイコンをクリックすると、グループのログ検索が実行されて、そのメンバーが返されます。 

![保存されているコンピューター グループ](media/log-analytics-computer-groups/configure-saved.png)

グループを編集するには、同じ**カテゴリ**と**名前**で新しいグループを作成し、元のグループを上書きします。

## <a name="using-a-computer-group-in-a-log-search"></a>ログ検索におけるコンピューター グループの使用
ログ検索の中で特定のコンピューター グループを参照するには、次の構文を使用します。  **Category** の指定は、同じ名前でカテゴリが異なるコンピューター グループが複数存在する場合にのみ必須となります。それ以外の場合は省略できます。 

    $ComputerGroups[Category: Name]

検索を実行するとまず、その対象となるコンピューター グループのメンバーが解決されます。  そのグループがログ検索から得られたものである場合は、その検索を実行してグループのメンバーを取得したうえで、最上位のログ検索が実行されます。

一般にログ検索では、コンピューター グループが **IN** 句と組み合わせて使用されます。その例を次に示します。

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>コンピューター グループのレコード
Active Directory または WSUS から作成されたコンピューター グループでは、そのメンバーシップごとのレコードが OMS リポジトリに作成されます。  これらは **ComputerGroup** タイプのレコードとして、次の表に示すプロパティを持ちます。  ログ検索に基づくコンピューター グループにはレコードが作成されません。

| プロパティ | 説明 |
|:--- |:--- |
| 型 |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| コンピューター |メンバー コンピューターの名前。 |
| グループ |グループの名前。 |
| GroupFullName |ソースとソース名を含んだグループの完全パス。 |
| GroupSource |グループの収集元となったソース。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |グループの収集元となったソースの名前。  Active Directory の場合はドメイン名になります。 |
| ManagementGroupName |SCOM エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |
| TimeGenerated |コンピューター グループが作成または更新された日時。 |

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  




<!--HONumber=Nov16_HO3-->


