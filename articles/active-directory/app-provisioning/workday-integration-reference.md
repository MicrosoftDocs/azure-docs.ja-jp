---
title: Azure Active Directory と Workday の統合のリファレンス
description: Workday による人事主導のプロビジョニングに関する技術的な詳細
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104333"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory のプロビジョニングと Workday の統合方法

ユーザーの ID のライフ サイクルを管理するために、[Azure Active Directory ユーザー プロビジョニング サービス](../app-provisioning/user-provisioning.md)と [Workday HCM](https://www.workday.com) を統合します。 Azure Active Directory では、次の 3 つの統合があらかじめ構築されています。 

* [Workday からオンプレミスの Active Directory へのユーザー プロビジョニング](../saas-apps/workday-inbound-tutorial.md)
* [Workday から Azure Active Directory へのユーザー プロビジョニング](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

この記事では、統合のしくみと、さまざまな HR シナリオでプロビジョニング動作をカスタマイズする方法について説明します。 

## <a name="establishing-connectivity"></a>接続の確立 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Azure AD エンドポイントへの Workday API アクセスを制限する
Azure AD プロビジョニング サービスでは、基本認証を使用して Workday Web サービス API エンドポイントに接続します。  

Azure AD プロビジョニング サービスと Workday の間の接続をさらにセキュリティで保護するため、指定された統合システム ユーザーが許可された Azure AD の IP 範囲からのみ Workday API にアクセスするように、アクセスを制限できます。 Workday 管理者と協力しながら、Workday テナントで次の構成を完了してください。 

1. Azure パブリック クラウドの[最新の IP 範囲](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードします。 
1. ファイルを開き、**AzureActiveDirectory** タグを検索します 

   >[!div class="mx-imgBorder"] 
   >![Azure AD の IP 範囲](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *addressPrefixes* 要素内に列記されているすべての IP アドレス範囲をコピーし、その範囲を使用して IP アドレス リストを作成します。
1. Workday 管理ポータルにログインします。 
1. **[IP 範囲の管理]** タスクにアクセスして、Azure データセンターの新しい IP 範囲を作成します。 IP 範囲 (CIDR 表記を使用) をコンマ区切りのリストとして指定します。  
1. **[認証ポリシーの管理]** タスクにアクセスして、新しい認証ポリシーを作成します。 認証ポリシーで、認証許可リストを使用して、Azure AD の IP 範囲と、この IP 範囲からのアクセスを許可するセキュリティ グループを指定します。 変更を保存します。 
1. **[保留中のすべての認証ポリシーの変更をアクティブ化]** タスクにアクセスして、変更を確認します。

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>制約付きセキュリティ グループを使用して Workday のワーカー データへのアクセスを制限する

[Workday 統合システムユーザーを構成](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday)する既定の手順では、Workday テナントのすべてのユーザーを取得するためのアクセス権が付与されます。 統合シナリオによっては、特定の監督組織にのみ所属するユーザーが Get_Workers API 呼び出しによって返され、Workday Azure AD コネクタによって処理されるように、アクセスを制限する必要があります。 

Workday 管理者と協力して制約付きの統合システム セキュリティ グループを構成することで、この要件を満たすことができます。 これを行う方法の詳細については、[こちらの Workday コミュニティの記事](https://community.workday.com/forums/customer-questions/620393)をご覧ください (*この記事にアクセスするには、Workday コミュニティのログイン資格情報が必要です*)

このように制約付きの ISSG (統合システム セキュリティ グループ) を使用してアクセスを制限する戦略は、次のシナリオで特に役立ちます。 
* **フェーズ ロールアウトのシナリオ**:大規模な Workday テナントがあり、Workday から Azure AD への自動プロビジョニングを段階的にロールアウトする予定である。 このシナリオでは、Azure AD スコープ フィルターを使用して現在のフェーズのスコープに含まれないユーザーを除外するのではなく、スコープ内のワーカーだけが Azure AD に表示されるように制約付きの ISSG を構成することをお勧めします。
* **複数のプロビジョニング ジョブのシナリオ**:大規模な Workday テナントと、それぞれ異なる事業単位/部門/会社をサポートする複数の AD ドメインがある。 このトポロジをサポートするには、複数の Workday から Azure AD へのプロビジョニング ジョブを実行し、各ジョブで特定のワーカー セットをプロビジョニングします。 このシナリオでは、Azure AD スコープ フィルターを使用してワーカー データを除外するのではなく、関連するワーカー データだけが Azure AD に表示されるように制約付き ISSG を構成することをお勧めします。 

### <a name="workday-test-connection-query"></a>Workday テスト接続クエリ

Workday への接続をテストするため、次の *Get_Workers* Workday Web サービス要求が Azure AD によって送信されます。 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>完全同期のしくみ

Workday 主導のプロビジョニングのコンテキストにおける **完全同期** とは、Workday からすべての ID をフェッチし、各ワーカー オブジェクトに適用するプロビジョニング ルールを決定するプロセスを指します。 完全同期は、初めてプロビジョニングを有効にしたときと、Azure portal または Graph API を使用して "*プロビジョニングを再開*" したときに行われます。 

ワーカー データを取得するために、次の *Get_Workers* Workday Web サービス要求が Azure AD によって送信されます。 このクエリでは、Workday トランザクション ログから、完全同期の実行に対応する時点の有効日が指定されたすべてのワーカー エントリが検索されます。 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
*Response_Group* ノードは、Workday からフェッチするワーカー属性を指定するために使用されます。 *Response_Group* ノードの各フラグの説明については、Workday の [Get_Workers API のドキュメント](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)をご覧ください。 

*Response_Group* ノードに指定されるいくつかのフラグ値は、Workday Azure AD プロビジョニング アプリで構成された属性に基づいて計算されます。 フラグ値の設定に使用される条件については、"*サポートされているエンティティ*" に関するセクションを参照してください。 

上記のクエリに対する Workday からの *Get_Workers* 応答には、ワーカー レコードの数とページ数が含まれています。

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
結果セットの次のページを取得するには、次の *Get_Workers* クエリで、*Response_Filter* のパラメーターとしてページ番号を指定します。

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
完全同期中に、Azure AD プロビジョニング サービスによって各ページが処理され、すべての有効なワーカーが反復処理されます。Workday からインポートされたワーカー エントリごとに、
* Workday から属性値を取得するために [XPATH 式](workday-attribute-reference.md)が適用されます。
* 属性マッピングと照合ルールが適用されます。 
* ターゲット (Azure AD/AD) で実行する操作がサービスによって決定されます。 

処理が完了すると、完全同期の開始に関連付けられているタイムスタンプがウォーターマークとして保存されます。 このウォーターマークは、増分同期サイクルの開始点として使用されます。 

## <a name="how-incremental-sync-works"></a>増分同期のしくみ

完全同期の後、Azure AD プロビジョニング サービスによって `LastExecutionTimestamp` が保持され、それを使用して増分変更を取得するためのデルタ クエリが作成されます。 増分同期中は、次の種類のクエリが Azure AD から Workday に送信されます。 

* [手動更新に対するクエリ](#query-for-manual-updates)
* [有効日が指定された更新と終了に対するクエリ](#query-for-effective-dated-updates-and-terminations)
* [将来の日付の雇用に対するクエリ](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>手動更新に対するクエリ

次の *Get_Workers* 要求によって、前回の実行と今回の実行時間の間に発生した手動更新が照会されます。 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>有効日が指定された更新と終了に対するクエリ

次の *Get_Workers* 要求によって、前回の実行と今回の実行時間の間に発生した、有効日が指定された更新が照会されます。 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>将来の日付の雇用に対するクエリ

上記のクエリのいずれかで将来の日付の雇用が返された場合は、次の *Get_Workers* 要求を使用して、新しい将来の日付の雇用に関する情報が取得されます。 新しい雇用の *WID* 属性を使用して参照が実行され、有効日が雇用の日時に設定されます。 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>ワーカー データ属性の取得

*Get_Workers* API では、ワーカーに関連付けられたさまざまなデータ セットが返されます。 Workday から取得されるデータ セットは、プロビジョニング スキーマで構成された [XPATH API 式](workday-attribute-reference.md)に応じて、Azure AD プロビジョニング サービスによって決定されます。 それに応じて、*Get_Workers* 要求の *Response_Group* のフラグが設定されます。 

次の表に、特定のデータ セットを取得するために使用するマッピング構成に関するガイダンスを示します。 

| \# | Workday エンティティ                       | 既定で含まれる | 既定以外のエンティティをフェッチするためにマッピングで指定する XPATH パターン             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 個人データ                        | はい                 | wd:Worker\_Data/wd:Personal\_Data                                             |
| 2  | 雇用データ                      | はい                 | wd:Worker\_Data/wd:Employment\_Data                                           |
| 3  | 追加のジョブ データ                  | はい                 | wd:Worker\_Data/wd:Employment\_Data/wd:Worker\_Job\_Data\[@wd:Primary\_Job=0\]|
| 4  | 組織データ                    | はい                 | wd:Worker\_Data/wd:Organization\_Data                                         |
| 5  | 管理チェーン データ                | はい                 | wd:Worker\_Data/wd:Management\_Chain\_Data                                    |
| 6  | 監督組織             | はい                 | 'SUPERVISORY'                                                                 |
| 7  | [会社]                              | はい                 | 'COMPANY'                                                                     |
| 8  | 事業単位                        | いいえ                  | 'BUSINESS\_UNIT'                                                              |
| 9  | 事業単位の階層              | いいえ                  | 'BUSINESS\_UNIT\_HIERARCHY'                                                   |
| 10 | 会社の階層                    | いいえ                  | 'COMPANY\_HIERARCHY'                                                          |
| 11 | Cost Center                          | いいえ                  | 'COST\_CENTER'                                                                |
| 12 | コスト センターの階層                | いいえ                  | 'COST\_CENTER\_HIERARCHY'                                                     |
| 13 | 資金                                 | いいえ                  | 'FUND'                                                                        |
| 14 | 資金の階層                       | いいえ                  | 'FUND\_HIERARCHY'                                                             |
| 15 | 贈与                                 | いいえ                  | 'GIFT'                                                                        |
| 16 | 贈与の階層                       | いいえ                  | 'GIFT\_HIERARCHY'                                                             |
| 17 | Grant                                | いいえ                  | 'GRANT'                                                                       |
| 18 | 許可の階層                      | いいえ                  | 'GRANT\_HIERARCHY'                                                            |
| 19 | 営業所の階層              | いいえ                  | 'BUSINESS\_SITE\_HIERARCHY'                                                   |
| 20 | マトリックス組織                  | いいえ                  | 'MATRIX'                                                                      |
| 21 | 支払いグループ                            | いいえ                  | 'PAY\_GROUP'                                                                  |
| 22 | プログラム                             | いいえ                  | 'PROGRAMS'                                                                    |
| 23 | プログラムの階層                    | いいえ                  | 'PROGRAM\_HIERARCHY'                                                          |
| 24 | リージョン                               | いいえ                  | 'REGION\_HIERARCHY'                                                           |
| 25 | 場所の階層                   | いいえ                  | 'LOCATION\_HIERARCHY'                                                         |
| 26 | アカウント プロビジョニング データ            | いいえ                  | wd:Worker\_Data/wd:Account\_Provisioning\_Data                                |
| 27 | 身元調査データ                | いいえ                  | wd:Worker\_Data/wd:Background\_Check\_Data                                    |
| 28 | 特典の対象データ             | いいえ                  | wd:Worker\_Data/wd:Benefit\_Eligibility\_Data                                 |
| 29 | 手当登録データ              | いいえ                  | wd:Worker\_Data/wd:Benefit\_Enrollment\_Data                                  |
| 30 | キャリア データ                          | いいえ                  | wd:Worker\_Data/wd:Career\_Data                                               |
| 31 | 報酬データ                    | いいえ                  | wd:Worker\_Data/wd:Compensation\_Data                                         |
| 32 | 臨時職員の税務当局データ | いいえ                  | wd:Worker\_Data/wd:Contingent\_Worker\_Tax\_Authority\_Form\_Type\_Data       |
| 33 | 育成項目データ                | いいえ                  | wd:Worker\_Data/wd:Development\_Item\_Data                                    |
| 34 | 従業員契約データ              | いいえ                  | wd:Worker\_Data/wd:Employee\_Contracts\_Data                                  |
| 35 | 従業員レビュー データ                 | いいえ                  | wd:Worker\_Data/wd:Employee\_Review\_Data                                     |
| 36 | 受け取ったフィードバック データ               | いいえ                  | wd:Worker\_Data/wd:Feedback\_Received\_Data                                   |
| 37 | ワーカー目標データ                     | いいえ                  | wd:Worker\_Data/wd:Worker\_Goal\_Data                                         |
| 38 | 写真データ                           | いいえ                  | wd:Worker\_Data/wd:Photo\_Data                                                |
| 39 | 資格データ                   | いいえ                  | wd:Worker\_Data/wd:Qualification\_Data                                        |
| 40 | 関係者データ                 | いいえ                  | wd:Worker\_Data/wd:Related\_Persons\_Data                                     |
| 41 | 職務データ                            | いいえ                  | wd:Worker\_Data/wd:Role\_Data                                                 |
| 42 | スキル データ                           | いいえ                  | wd:Worker\_Data/wd:Skill\_Data                                                |
| 43 | 継承プロファイル データ              | いいえ                  | wd:Worker\_Data/wd:Succession\_Profile\_Data                                  |
| 44 | 人材評価データ               | いいえ                  | wd:Worker\_Data/wd:Talent\_Assessment\_Data                                   |
| 45 | ユーザー アカウント データ                    | いいえ                  | wd:Worker\_Data/wd:User\_Account\_Data                                        |
| 46 | ワーカー ドキュメント データ                 | いいえ                  | wd:Worker\_Data/wd:Worker\_Document\_Data                                     |

>[!NOTE]
>表に示されている各 Workday エンティティは、Workday の "**ドメイン セキュリティ ポリシー**" によって保護されています。 適切な XPATH を設定した後にエンティティに関連付けられている属性を取得できない場合は、Workday 管理者に問い合わせて、プロビジョニング アプリに関連付けられている統合システム ユーザーに適切なドメイン セキュリティ ポリシーが構成されていることを確認してください。 たとえば、*スキル データ* を取得するには、*Get* アクセスが Workday ドメイン *Worker Data: Skills and Experience* で必要です。 

次に、Workday 統合を拡張して特定の要件を満たす方法の例をいくつか示します。 

**例 1**

Workday から次のデータ セットを取得し、それらをプロビジョニング ルールで使用するとします。

* コスト センター
* コスト センターの階層
* 支払いグループ

上記のデータ セットは、既定では含まれていません。 これらのデータ セットを取得するには、次の手順に従います。
1. Azure portal にログインし、Workday から AD または Azure AD へのユーザー プロビジョニングを開きます。 
1. [プロビジョニング] ブレードで、マッピングを編集し、[詳細設定] セクションで Workday 属性の一覧を開きます。 
1. 次の属性定義を追加し、それらを "必須" としてマークします。 これらの属性は、AD または Azure AD のどの属性にもマップされません。 これらは、コスト センター、コスト センターの階層、支払いグループの各情報を取得するようにコネクタに指示するシグナルとして機能します。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名 | XPATH API 式 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER_HIERARCHY']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() |

1. *Get_Workers* 応答でコスト センターと支払いグループのデータ セットを入手できるようになったら、以下の XPATH 値を使用して、コスト センター名、コスト センター コード、支払いグループを取得できます。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名 | XPATH API 式 |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Pay Group']/wd:Organization_Name/text() |

**例 2**

あるユーザーに関連付けられている認定資格を取得するとします。 この情報は、*資格データ* セットの一部として入手できます。 このデータ セットを *Get_Workers* 応答の一部として取得するには、次の XPATH を使用します。 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**例 3**

あるワーカーに割り当てられている *プロビジョニング グループ* を取得するとします。 この情報は、*アカウント プロビジョニング データ* セットの一部として入手できます。 このデータ セットを *Get_Workers* 応答の一部として取得するには、次の XPATH を使用します。 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>さまざまな HR シナリオの処理

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>国際的なジョブの割り当てとセカンダリ ジョブの詳細の取得

既定で Workday コネクタによって取得されるのは、worker のプライマリ ジョブに関連付けられた属性です。 また、このコネクタでは、国際的なジョブの割り当てまたはセカンダリ ジョブに関連付けられた "*追加のジョブ データ*" を取得することもサポートされています。 

国際的なジョブの割り当てに関連付けられた属性を取得するには、次の手順に従ってください。 

1. Workday Web サービス API バージョン 30.0 以降を使用する Workday 接続 URL を設定します。 それに応じて、Workday プロビジョニング アプリで[適切な XPATH 値](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)を設定します。 
1. `Worker_Job_Data` ノードのセレクター `@wd:Primary_Job=0` を使用して、適切な属性を取得します。 
   * **例 1:** `SecondaryBusinessTitle` を取得するには、XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()` を使用します
   * **例 2:** `SecondaryBusinessLocation` を取得するには、XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor` を使用します

 

## <a name="next-steps"></a>次の手順

* [Workday から Active Directory へのプロビジョニングを構成する方法を確認する](../saas-apps/workday-inbound-tutorial.md)
* [Workday への書き戻しを構成する方法を確認する](../saas-apps/workday-writeback-tutorial.md)
* [受信プロビジョニングのサポートされている Workday 属性に関する詳細情報](workday-attribute-reference.md)

