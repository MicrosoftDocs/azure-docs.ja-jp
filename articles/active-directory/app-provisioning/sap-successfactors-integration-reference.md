---
title: Azure Active Directory と SAP SuccessFactors の統合のリファレンス
description: SAP SuccessFactors に関する技術的な詳細 - HR 主導のプロビジョニング
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256223"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory のプロビジョニングと SAP SuccessFactors の統合方法 

ユーザーの ID のライフ サイクルを管理するために、[Azure Active Directory ユーザー プロビジョニング サービス](../app-provisioning/user-provisioning.md)と [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) を統合します。 Azure Active Directory では、次の 3 つの統合があらかじめ構築されています。 

* [オンプレミスの Active Directory のユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Azure Active Directory のユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors の書き戻し](../saas-apps/sap-successfactors-writeback-tutorial.md)

この記事では、統合のしくみと、さまざまな HR シナリオでプロビジョニング動作をカスタマイズする方法について説明します。 

## <a name="establishing-connectivity"></a>接続の確立 
Azure AD プロビジョニング サービスでは、基本認証を使用して、Employee Central OData API エンドポイントへの接続が行われます。 SuccessFactors プロビジョニング アプリを設定するときに、 *[管理者資格情報]* セクションの *[テナント URL]* パラメーターを使用して、[API データ センターの URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682) を構成します。 

Azure AD プロビジョニング サービスと SuccessFactors の間の接続をさらにセキュリティで保護するには、次の手順のようにして、SuccessFactors の IP 許可リストに Azure AD の IP 範囲を追加できます。

1. Azure パブリック クラウドの[最新の IP 範囲](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードします 
1. ファイルを開き、**AzureActiveDirectory** タグを検索します 

   >[!div class="mx-imgBorder"] 
   >![Azure AD の IP 範囲](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *addressPrefixes* 要素内に列記されているすべての IP アドレス範囲をコピーし、その範囲を使用して IP アドレス制限リストを作成します。
1. CIDR の値を IP 範囲に変換します。  
1. SuccessFactors 管理ポータルにログインして、IP 範囲を許可リストに追加します。 SAP [サポート ノート 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200) を参照してください。 このツールで [IP 範囲を入力](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html)できるようになります。 

## <a name="supported-entities"></a>サポートされているエンティティ
SuccessFactors のすべてのユーザーについて、Azure AD プロビジョニング サービスにより次のエンティティが取得されます。 各エンティティは、OData API の *$expand* クエリ パラメーターを使用して展開されます。 下の「*取得規則*」列を参照してください。 一部のエンティティは既定で展開されますが、一部のエンティティはマッピングに特定の属性が存在する場合にのみ展開されます。 

| \# | SuccessFactors エンティティ                  | OData ノード     | 取得規則 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | "*ルート ノード*"                  | Always (常に)           |
| 2  | PerPersonal                            | personalInfoNav              | Always (常に)           |
| 3  | PerPhone                               | phoneNav                     | Always (常に)           |
| 4  | PerEmail                               | emailNav                     | Always (常に)           |
| 5  | EmpEmployment                          | employmentNav                | Always (常に)           |
| 6  | User                                   | employmentNav/userNav        | Always (常に)           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Always (常に)           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Always (常に)           |
| 9  | ユーザーのマネージャー                         | employmentNav/userNav/manager/empInfo | 常時  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | `company` または `companyId` 属性がマップされている場合のみ |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | `department` または `departmentId` 属性がマップされている場合のみ |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | `businessUnit` または `businessUnitId` 属性がマップされている場合のみ |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | `costCenter` または `costCenterId` 属性がマップされている場合のみ |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | `division` または `divisionId` 属性がマップされている場合のみ |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | `jobCode` または `jobCodeId` 属性がマップされている場合のみ |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | `payGrade` 属性がマップされている場合のみ |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | `location` 属性がマップされている場合のみ |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | マッピングに次の属性のいずれかが含まれている場合: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | `eventReason` 属性がマップされている場合のみ |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | `assignmentType` がマップされている場合のみ |
| 21 | EmploymentType 候補リスト                | employmentNav/jobInfoNav/employmentTypeNav | `employmentType` がマップされている場合のみ |
| 22 | EmployeeClass 候補リスト                 | employmentNav/jobInfoNav/employeeClassNav | `employeeClass` がマップされている場合のみ |
| 23 | EmplStatus 候補リスト                    | employmentNav/jobInfoNav/emplStatusNav | `emplStatus` がマップされている場合のみ |
| 24 | AssignmentType 候補リスト                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | `assignmentType` がマップされている場合のみ |

## <a name="how-full-sync-works"></a>完全同期のしくみ
属性マッピングに基づいて、完全同期では、Azure AD プロビジョニング サービスにより、次の "GET" OData API クエリが送信されて、すべてのアクティブ ユーザーの有効なデータがフェッチされます。 

> [!div class="mx-tdCol2BreakAll"]
>| パラメーター | 説明 |
>| ----------|-------------|
>| OData API ホスト | "*テナント URL*" に https を追加します。 例: `https://api4.successfactors.com` |
>| OData API エンドポイント | `/odata/v2/PerPerson` |
>| OData $format クエリ パラメーター | `json` |
>| OData $filter クエリ パラメーター | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand クエリ パラメーター | このパラメーター値は、マップされた属性によって異なります。 例: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize クエリ パラメーター | `100` |

> [!NOTE]
> 最初の初期完全同期の間に、Azure AD プロビジョニング サービスでは、アクティブでないワーカー データまたは終了したワーカー データは取得されません。

SuccessFactors のユーザーごとに、プロビジョニング サービスにより、マッピングで定義されている一致する属性を使用して、ターゲット (Azure AD とオンプレミスの Active Directory) でアカウントが検索されます。 たとえば、*personIdExternal* が *employeeId* にマップされ、一致する属性として設定されている場合は、プロビジョニング サービスによって *personIdExternal* 値を使用して、*employeeId* フィルターでユーザーが検索されます。 ユーザーの一致が見つかった場合は、ターゲット属性が更新されます。 一致するものが見つからない場合は、ターゲットに新しいエントリが作成されます。 

特定の `personIdExternal`に対する OData API エンドポイントによって返されたデータを検証するには、次の API クエリの `SuccessFactorsAPIEndpoint` を自分の API データ センター サーバーの URL で更新し、[Postman](https://www.postman.com/downloads/) などのツールを使用してクエリを呼び出します。 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>増分同期のしくみ

完全同期の後、Azure AD プロビジョニング サービスにより `LastExecutionTimestamp` が維持され、それを使用して増分変更を取得するためのデルタ クエリが作成されます。 各 SuccessFactors エンティティに含まれるタイムスタンプ属性 (`lastModifiedDateTime`、`startDate`、`endDate`、`latestTerminationDate` など) が評価され、変更が `LastExecutionTimestamp` と `CurrentExecutionTime` の間にあるかどうかが確認されます。 そうである場合、エントリの変更は有効であると見なされ、同期のために処理されます。 

## <a name="reading-attribute-data"></a>属性データの読み取り

Azure AD プロビジョニング サービスにより SuccessFactors のクエリが行われて、JSON の結果セットが取得されます。 JSON の結果セットには、Employee Central に格納されている属性の数が含まれています。 既定では、プロビジョニング スキーマは、これらの属性のサブセットのみを取得するように構成されています。 

追加の属性を取得するには、次の手順のようにします。
    
1. **[エンタープライズ アプリケーション]**  ->  **[SuccessFactors アプリ]**  ->  **[プロビジョニング]**  ->  **[Edit Provisioning]\(プロビジョニングの編集\)**  -> "**属性マッピング ページ**" を参照します。
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[SuccessFactors の属性リストを編集します]** をクリックします。 

   > [!NOTE] 
   > **[SuccessFactors の属性リストを編集します]** オプションが Azure portal に表示されない場合は、URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* を使用してページにアクセスします。 

1. このビューの **[API 式]** 列には、コネクタによって使用される JSONPath 式が表示されます。

   >[!div class="mx-imgBorder"] 
   >![API 式](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 既存の JSONPath 値を編集するか、有効な JSONPath 式を使用して新しい属性をスキーマに追加することができます。 

次のセクションでは、JSONPath 値の編集に関する一般的なシナリオの一覧を示します。 

## <a name="handling-different-hr-scenarios"></a>さまざまな HR シナリオの処理

JSONPath は、XML 用の XPath に似た JSON 用のクエリ言語です。 XPath と同様に、JSONPath を使用すると、JSON ペイロードのデータの抽出とフィルター処理を行うことができます。

JSONPath 変換を使用すると、Azure AD プロビジョニング アプリの動作をカスタマイズして、カスタム属性を取得し、再雇用、作業者変換、グローバル割り当てなどのシナリオを処理することができます。 

このセクションでは、次の HR シナリオ用にプロビジョニング アプリをカスタマイズする方法について説明します。 
* [追加の属性の取得](#retrieving-additional-attributes)
* [カスタム属性の取得](#retrieving-custom-attributes)
* [作業者変換シナリオの処理](#handling-worker-conversion-scenario)
* [再雇用シナリオの処理](#handling-rehire-scenario)
* [グローバル割り当てシナリオの処理](#handling-global-assignment-scenario)
* [同時実行ジョブのシナリオの処理](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>追加の属性の取得

既定の Azure AD SuccessFactors プロビジョニング アプリ スキーマには、[90 以上の定義済み属性](sap-successfactors-attribute-reference.md)が付属しています。 さらにプロビジョニング スキーマに SuccessFactors 属性を追加するには、次の手順のようにします。 

1. 次の OData クエリを使用して、Employee Central から有効なテスト ユーザーのデータを取得します。 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 属性に関連付けられている Employee Central エンティティを確認します
   * 属性が *EmpEmployment* エンティティの一部である場合は、*employmentNav* ノードの下で属性を探します。 
   * 属性が *User* エンティティの一部である場合は、*employmentNav/userNav* ノードの下で属性を探します。
   * 属性が *EmpJob* エンティティの一部である場合は、*employmentNav/jobInfoNav* ノードの下で属性を探します。 
1. 属性に関連付けられた JSON パスを作成し、この新しい属性を SuccessFactors 属性のリストに追加します。 
   * 例 1:*employmentNav* エンティティの一部である *okToRehire* 属性を追加し、JSONPath `$.employmentNav.results[0].okToRehire` を使用するとします
   * 例 2:*userNav* エンティティの一部である *timeZone* 属性を追加し、JSONPath `$.employmentNav.results[0].userNav.timeZone` を使用するとします
   * 例 3: *jobInfoNav* エンティティの一部である *flsaStatus* 属性を追加し、JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus` を使用するとします
1. スキーマを保存します。 
1. プロビジョニングを再開します。

### <a name="retrieving-custom-attributes"></a>カスタム属性の取得

Azure AD SuccessFactors プロビジョニング アプリでは、次のカスタム属性が既定で事前に定義されています。 
* User (userNav) エンティティからの *custom01 - custom15*
* *empNavCustomString1 - empNavCustomString15* と呼ばれる、EmpEmployment (employmentNav) エンティティからの *customString1 - customString15*
* *empJobNavCustomString1 - empNavJobCustomString15* と呼ばれる、EmpJobInfo (jobInfoNav) エンティティからの *customString1 - customString15*

たとえば、Employee Central のインスタンスで、*EmpJobInfo* の *customString35* 属性に、場所の説明が格納されているとします。 この値を Active Directory の *physicalDeliveryOfficeName* 属性にフローさせるものとします。 このシナリオの属性マッピングを構成するには、次の手順のようにします。 

1. SuccessFactors の属性リストを編集して、*empJobNavCustomString35* という名前の新しい属性を追加します。
1. この属性の JSONPath API 式を次のように設定します: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Azure portal でマッピングの変更を保存し、再度読み込みます。  
1. 属性マッピング ブレードで、*empJobNavCustomString35* を *physicalDeliveryOfficeName* にマップします。
1. マッピングを保存します。

このシナリオの拡張: 
* *custom35* 属性を *User* エンティティからマップする場合は、JSONPath `$.employmentNav.results[0].userNav.custom35` を使用します
* *customString35* 属性を *EmpEmployment* エンティティからマップする場合は、JSONPath `$.employmentNav.results[0].customString35` を使用します

### <a name="handling-worker-conversion-scenario"></a>作業者変換シナリオの処理

作業者の変換とは、既存のフルタイム従業員を契約者または請負業者にフルタイムで変換するプロセスです。 このシナリオでは、Employee Central により新しい *EmpEmployment* エンティティが、同じ *Person* エンティティに対する新しい *User* エンティティと共に追加されます。 前の *EmpEmployment* エンティティの下に入れ子にされていた *User* エンティティは、null に設定されます。 変換が発生したときに新しい雇用データが表示されるようにこのシナリオを処理するには、以下の手順のようにして、プロビジョニング アプリのスキーマを一括更新できます。  

1. SuccessFactors プロビジョニング アプリの属性マッピング ブレードを開きます。 
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[Review your schema here]\(ここでスキーマを確認する\)** リンクをクリックして、スキーマ エディターを開きます。 

   >![スキーマ エディターを開く [Review your schema here]\(ここでスキーマを確認する\) リンクを示すスクリーンショット。](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. **[ダウンロード]** リンクをクリックして、編集前にスキーマのコピーを保存します。 

   >![スキーマ エディターでスキーマのコピーを保存するために選択する [ダウンロード] を示すスクリーンショット。](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. スキーマ エディターで Ctrl + H キーを押して、検索して置換コントロールを開きます。
1. 検索テキスト ボックスに値 `$.employmentNav.results[0]` をコピーして貼り付けます
1. 置換テキスト ボックスに値 `$.employmentNav.results[?(@.userNav != null)]` をコピーして貼り付けます。 `!=` 演算子の前後の空白文字に注意してください。これは、JSONPath 式を正常に処理するために重要です。 
   >![変換の検索と置換](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. [すべて置換] をクリックしてスキーマを更新します。 
1. スキーマを保存します。 
1. 上のプロセスでは、すべての JSONPath 式が次のように更新されます。 
   * 古い JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新しい JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. プロビジョニングを再開します。 

### <a name="handling-rehire-scenario"></a>再雇用シナリオの処理

通常、再雇用を処理するには、次の 2 つのオプションがあります。 
* オプション 1: Employee Central で新しい個人プロファイルを作成する
* オプション 2:Employee Central で既存の個人プロファイルを再利用する

HR プロセスでオプション 1 を使用する場合は、プロビジョニング スキーマを変更する必要はありません。 HR プロセスでオプション 2 を使用する場合は、Employee Central により新しい *EmpEmployment* エンティティが、同じ *Person* エンティティに対する新しい *User* エンティティと共に追加されます。 変換シナリオとは異なり、以前の *EmpEmployment* エンティティの *User* エンティティは null に設定されません。 

最新の雇用データが再雇用プロファイルに表示されるように、この再雇用シナリオ (オプション 2) を処理するには、以下の手順のようにして、プロビジョニング アプリのスキーマを一括更新できます。  

1. SuccessFactors プロビジョニング アプリの属性マッピング ブレードを開きます。 
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[Review your schema here]\(ここでスキーマを確認する\)** リンクをクリックして、スキーマ エディターを開きます。   
1. **[ダウンロード]** リンクをクリックして、編集前にスキーマのコピーを保存します。   
1. スキーマ エディターで Ctrl + H キーを押して、検索して置換コントロールを開きます。
1. 検索テキスト ボックスに値 `$.employmentNav.results[0]` をコピーして貼り付けます
1. 置換テキスト ボックスに値 `$.employmentNav.results[-1:]` をコピーして貼り付けます。 この JSONPath 式では、最新の *EmpEmployment* レコードが返されます。   
1. [すべて置換] をクリックしてスキーマを更新します。 
1. スキーマを保存します。 
1. 上のプロセスでは、すべての JSONPath 式が次のように更新されます。 
   * 古い JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新しい JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. プロビジョニングを再開します。 

このスキーマ変更では、作業者変換シナリオもサポートされています。 

### <a name="handling-global-assignment-scenario"></a>グローバル割り当てシナリオの処理

Employee Central のユーザーがグローバル割り当てに対して処理されると、SuccessFactors により、新しい *EmpEmployment* エンティティが追加されて、*assignmentClass* が "GA" に設定されます。 また、新しい *User* エンティティも作成されます。 したがって、ユーザーは次のようになります。
* *assignmentClass* が "ST" に設定されたホーム割り当てに対応する 1 つの *EmpEmployment* + *User* エンティティ 
* *assignmentClass* が "GA" に設定されたグローバル割り当てに対応する別の *EmpEmployment* + *User* エンティティ

標準割り当てとグローバル割り当てのユーザー プロファイルに属する属性を取得するには、次の手順のようにします。 

1. SuccessFactors プロビジョニング アプリの属性マッピング ブレードを開きます。 
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[Review your schema here]\(ここでスキーマを確認する\)** リンクをクリックして、スキーマ エディターを開きます。   
1. **[ダウンロード]** リンクをクリックして、編集前にスキーマのコピーを保存します。   
1. スキーマ エディターで Ctrl + H キーを押して、検索して置換コントロールを開きます。
1. 検索テキスト ボックスに値 `$.employmentNav.results[0]` をコピーして貼り付けます
1. 置換テキスト ボックスに値 `$.employmentNav.results[?(@.assignmentClass == 'ST')]` をコピーして貼り付けます。 
1. [すべて置換] をクリックしてスキーマを更新します。 
1. スキーマを保存します。 
1. 上のプロセスでは、すべての JSONPath 式が次のように更新されます。 
   * 古い JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新しい JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. アプリの属性マッピング ブレードを再度読み込みます。 
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[SuccessFactors の属性リストを編集します]** をクリックします。
1. 新しい属性を追加して、グローバル割り当てデータをフェッチします。 たとえば、グローバル割り当てプロファイルに関連付けられている部署名を取得する場合は、JSONPath 式を `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` に設定して属性 *globalAssignmentDepartment* を追加します。 
1. 両方の部署の値を Active Directory 属性にフローさせるか、または式マッピングを使用して値を選択的にフローさせることができます。 例: 次の式では、AD の *department* 属性の値が、*globalAssignmentDepartment* が存在する場合はそれに設定され、存在しない場合は標準割り当てに関連付けられている *department* に設定されます。 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. マッピングを保存します。 
1. プロビジョニングを再開します。 

### <a name="handling-concurrent-jobs-scenario"></a>同時実行ジョブのシナリオの処理

Employee Central のユーザーに複数の同時実行ジョブがある場合は、2 つの *EmpEmployment* エンティティと *User* エンティティで *assignmentClass* が "ST" に設定されています。 両方のジョブに属している属性をフェッチするには、次の手順のようにします。 

1. SuccessFactors プロビジョニング アプリの属性マッピング ブレードを開きます。 
1. 下にスクロールし、 **[詳細オプションの表示]** をクリックします。
1. **[SuccessFactors の属性リストを編集します]** をクリックします。
1. たとえば、ジョブ 1 とジョブ 2 に関連付けられている部署を取得するとします。 定義済みの属性 *department* では、最初のジョブの部署の値が既にフェッチされています。 *secondJobDepartment* という名前の新しい属性を定義し、JSONPath 式を `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized` に設定します
1. 両方の部署の値を Active Directory 属性にフローさせるか、または式マッピングを使用して値を選択的にフローさせることができます。 
1. マッピングを保存します。 
1. プロビジョニングを再開します。 

## <a name="writeback-scenarios"></a>書き戻しのシナリオ

このセクションでは、さまざまな書き戻しのシナリオについて説明します。 SuccessFactors でのメールと電話番号の設定方法に基づいた構成方法をお勧めします。

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>電話とメールの書き戻しに対してサポートされるシナリオ 

| \# | シナリオの要件 | メールのプライマリ <br> フラグの値 | 勤務先の電話番号 <br> プライマリ フラグの値 | 携帯電話 <br> プライマリ フラグの値 | 勤務先の電話番号 <br> mapping | 携帯電話 <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * プライマリとして勤務先メールだけを設定する。 <br> * 電話番号は設定しない。 | true | true | false | \[設定しない\] | \[設定しない\] | 
| 2 | * SuccessFactors では、勤務先メールと勤務先電話がプライマリである <br> * 常に、Azure AD 電話番号を勤務先電話に送信し、モバイルを携帯電話に送信する。 | true | true | false | telephoneNumber | mobile | 
| 3 | * SuccessFactors では、勤務先メールと携帯電話がプライマリである <br> * 常に、Azure AD 電話番号を勤務先電話に送信し、モバイルを携帯電話に送信する | TRUE | false | true |  telephoneNumber | mobile | 
| 4 | * SuccessFactors では、勤務先メールがプライマリである <br> * Azure AD では、勤務先電話番号があるかどうかを調べ、ある場合はモバイル番号もあるかどうかを調べて、モバイル番号が存在しない場合にのみ、勤務先電話番号をプライマリとしてマークする。 | true | 式マッピングを使用する: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 式マッピングを使用する: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * SuccessFactors では、勤務先メールと勤務先電話がプライマリである。 <br> * Azure AD では、モバイルが使用可能な場合は、それを勤務先電話として設定し、それ以外の場合は telephoneNumber を使用する。 | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[設定しない\] | 

* 書き戻し属性マッピングに電話番号のマッピングがない場合は、メールのみが書き戻しに含まれます。
* Employee Central での新規雇用オンボードの間に、勤務先のメールと電話番号を使用できない場合があります。 オンボードの間に勤務先メールと勤務先電話をプライマリとして設定する必要がある場合は、新規雇用の作成の間に勤務先の電話とメールにダミーの値を設定できます。これは、最終的に書き戻しアプリによって更新されます。
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>電話とメールの書き戻しに対してサポートされないシナリオ

* Employee Central では、オンボードの間に、個人のメールと電話がプライマリとして設定されます。 書き戻しアプリでは、この設定を切り替えることができず、勤務先メールと勤務先電話をプライマリとして設定することはできません。
* Employee Central では、勤務先電話がプライマリとして設定されます。 書き戻しアプリではこれを変更できず、携帯電話をプライマリとして設定することはできません。
* 書き戻しアプリでは、現在のプライマリ フラグの設定を読み取ることはできず、書き込み操作に同じ値を使用することはできません。 属性マッピングで構成されているフラグの値が常に使用されます。 

## <a name="next-steps"></a>次のステップ

* [SuccessFactors から Active Directory へのプロビジョニングを構成する方法を確認する](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors への書き戻しを構成する方法を確認する](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [受信プロビジョニングのサポートされている SuccessFactors 属性に関する詳細情報](sap-successfactors-attribute-reference.md)










