---
title: Azure AD 属性マッピングのカスタマイズ | Microsoft Docs
description: Azure Active Directory における SaaS アプリの属性マッピングとは何かと、この属性マッピングをビジネス ニーズに合わせて変更する方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 04/03/2019
ms.author: kenwith
ms.openlocfilehash: 5040fca85857cd131731d67c543c08fb1114ccee
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235226"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ

Microsoft Azure AD では、Salesforce、G Suite、およびその他のサードパーティの SaaS アプリケーションへのユーザー プロビジョニングのサポートを提供します。 サード パーティの SaaS アプリケーションでユーザー プロビジョニングを有効にした場合、Azure portal では属性マッピングによってその属性値を管理します。

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連の属性と属性マッピングが存在します。 アプリによっては、ユーザーに加えてグループなど他の種類のオブジェクトを管理するものもあります。

既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。 そのため、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。

## <a name="editing-user-attribute-mappings"></a>ユーザー属性マッピングの編集

次の手順に従って、ユーザー プロビジョニングの**マッピング**機能にアクセスします。

1. [Azure Active Directory ポータル](https://aad.portal.azure.com)にサインインします。
1. 左側のウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 ギャラリーから追加されたアプリを含む、構成済みのすべてのアプリの一覧が表示されます。
1. 任意のアプリを選択し、そのアプリの管理ウィンドウを読み込み、そこでレポートを表示したり、アプリの設定を管理したりできます。
1. 選択したアプリのユーザー アカウントのプロビジョニング設定を管理するには、 **[プロビジョニング]** を選択します。
1. **[マッピング]** を展開し、Azure AD とターゲット アプリケーションの間でフローするユーザー属性を表示および編集します。 ターゲット アプリケーションが対応していれば、必要に応じてグループのプロビジョニングとユーザー アカウントをこのセクションで構成することができます。

   ![[マッピング] を使用して、ユーザー属性を表示および編集する](./media/customize-application-attributes/21.png)

1. **[マッピング]** 構成を選択して、関連する **[属性マッピング]** 画面を開きます。 SaaS アプリケーションが正常に機能するために、いくつかの属性マッピングが必要です。 必須の属性の場合は、 **[削除]** 機能を使用できません。

   ![[属性マッピング] を使用して、アプリの属性マッピングを構成する](./media/customize-application-attributes/22.png)

   このスクリーンショットで、Salesforce の管理オブジェクトの **Username** 属性には、リンクされている Azure Active Directory オブジェクトの **userPrincipalName** 値が設定されています。

1. 既存の **[属性マッピング]** を選択し、 **[属性の編集]** 画面を開きます。 ここで、Azure AD とターゲット アプリケーションの間でフローするユーザー属性を表示および編集できます。

   ![[属性の編集] を使用して、ユーザー属性を編集する](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>属性マッピングの種類の概要

属性マッピングでは、サード パーティの SaaS アプリケーションに属性を設定する方法を管理します。
次の 4 つの異なるマッピングの種類がサポートされます。

- **直接** - ターゲットの属性に、Azure AD でリンクされているオブジェクトの属性値を設定します。
- **定数** – ターゲットの属性に、指定した特定の文字列を設定します。
- **式** - ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。
  詳細については、「[Azure Active Directory における属性マッピングの式の書き方](../app-provisioning/functions-for-customizing-application-data.md)」を参照してください。
- **なし** - ターゲットの属性を変更しません。 ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。

これら 4 つの基本的な種類とともに、カスタム属性マッピングではオプションの**既定**値の割り当てという概念をサポートします。 既定値の割り当てでは、Azure AD にもターゲット オブジェクトにも値がない場合にも、ターゲットの属性には必ず値が設定されます。 最も一般的な構成では、これを空白のままにします。

### <a name="understanding-attribute-mapping-properties"></a>属性マッピングのプロパティの概要

前のセクションでは、属性マッピングの種類プロパティについて紹介しました。
属性マッピングは、このプロパティと共に次の属性もサポートしています。

- **ソース属性** - ソース システムのユーザー属性 (例:Azure Active Directory)。
- **対象の属性** - ターゲット システムのユーザー属性 (例:できます。
- **null の場合の既定値 (オプション)** - ソース属性が null の場合にターゲット システムに渡される値。 この値は、ユーザーを作成するときにのみプロビジョニングされます。 既存のユーザーを更新したときは、"null の場合の既定値" はプロビジョニングされません。 たとえば、ターゲット システムのすべての既存ユーザーを特定の役職でプロビジョニングする必要がある場合は (ソース システムでは null の場合)、次の[式](../app-provisioning/functions-for-customizing-application-data.md)を使用できます。Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]) "DefaultValue" は、ソース システムで null の場合にプロビジョニングする値に置き換えてください。 
- **この属性を使用してオブジェクトを照合する** - このマッピングを使用して、ソースとターゲットのシステム間でユーザーを一意に識別するかどうかを示します。 通常、これは Azure AD の userPrincipalName または mail 属性で設定され、一般的にターゲット アプリケーションのユーザー名フィールドにマップされます。
- **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。 一致する属性はいくつでも設定できますが、一致する属性として使用する属性が本当に一意であり、一致する属性に設定する必要があるかどうかを検討してください。 通常、お客様の構成には 1 つまたは 2 つの一致する属性があります。 
- **このマッピングを適用する**
  - **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します。
  - **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します。

## <a name="matching-users-in-the-source-and-target--systems"></a>ソース システムとターゲット システムで一致するユーザー
Azure AD プロビジョニング サービスは、"未開発" シナリオ(ユーザーがターゲット システムに存在しない) シナリオと "再開発" シナリオ (ユーザーが既にターゲット システムに存在する) のどちらでもデプロイできます。 両方のシナリオをサポートするために、プロビジョニング サービスでは、一致する属性の概念を使用します。 一致する属性を利用すると、ソースのユーザーを一意に識別してターゲットのユーザーとの一致を判定する方法を任意に決定できます。 デプロイの計画の一環として、ソース システムとターゲット システムのユーザーを一意に識別するために使用できる属性を特定します。 注意する点:

- **一致する属性は一意であることが望ましい:** 顧客は多くの場合、userPrincipalName、mail、オブジェクト ID などの属性を一致する属性として使用します。
- **複数の属性を一致する属性として使用できる:** ユーザーを一致するときに評価される複数の属性と、それらの属性が評価される順序 (UI では一致の優先順位として定義されます) を定義することができます。 たとえば、一致する属性として 3 つの属性を定義し、最初の 2 つの属性を評価した後にユーザーが一意に一致する場合、サービスは 3 番目の属性を評価しません。 サービスは、指定された順序で一致する属性を評価し、一致が見つかったら評価を停止します。  
- **ソースとターゲットの値が厳密に一致している必要はない:** ターゲットの値は、ソースの値の単純な関数である可能性があります。 したがって、ソースに emailAddress 属性、ターゲットに userPrincipalName がある場合に、emailAddress 属性の関数で一部の文字を何らかの定数値に置き換えることによって一致を判定できる場合があります。  
- **属性の組み合わせに基づいた一致はサポートされていない:** ほとんどのアプリケーションでは、2 つのプロパティに基づくクエリはサポートされていません。 したがって、属性の組み合わせに基づいて一致させることはできません。 プロパティを 1 つずつ順番に評価することは可能です。
- **少なくとも 1 つの一致する属性の値をすべてのユーザーが持っていることが必要:** 1 つの一致する属性を定義する場合、ソース システムですべてのユーザーがその属性の値を持っている必要があります。 たとえば、userPrincipalName を一致する属性として定義する場合、すべてのユーザーが userPrincipalName を持っている必要があります。 複数の一致する属性 (例: extensionAttribute1 と mail) を定義する場合、すべてのユーザーが同じ一致する属性を持っている必要はありません。 あるユーザーが extensionAttribute1 を持っていて mail は持っていない一方、別のユーザーが mail を持っていて extensionAttribute1 は持っていなくても問題ありません。 
- **ターゲット アプリケーションは一致する属性のフィルター処理をサポートする必要がある:** アプリケーション開発者が、ユーザーまたはグループ API で属性のサブセットに対するフィルター処理を許可します。 ギャラリー内のアプリケーションの場合、既定の属性マッピングは、ターゲット アプリケーションの API でフィルター処理がサポートされている属性に対するものであることが保証されています。 ターゲット アプリケーションの既定の一致する属性を変更するときは、サードパーティの API ドキュメントを参照して、その属性がフィルター処理可能であることを確認してください。  

## <a name="editing-group-attribute-mappings"></a>グループ属性マッピングの編集

ServiceNow、Box、G Suite などいくつかのアプリケーションでは、グループ オブジェクトとユーザー オブジェクトをプロビジョニングする機能がサポートされています。 グループ オブジェクトには、グループ メンバーと共に表示名や電子メール別名などのグループ プロパティを含めることができます。

![ServiceNow のプロビジョニング済みグループ オブジェクトとユーザー オブジェクトが表示されている例](./media/customize-application-attributes/24.png)

グループのプロビジョニングは必要に応じて有効と無効を切り替えることができます。これには、 **[属性マッピング]** 画面の **[マッピング]** の下でグループ マッピングを選択し、必要なオプションに **[有効]** を設定します。

グループ オブジェクトの一部としてプロビジョニングされる属性は、前に説明したユーザー オブジェクトの場合と同じ方法でカスタマイズできます。 

> [!TIP]
> グループ オブジェクト (プロパティとメンバー) のプロビジョニングは、アプリケーションへの[グループの割り当て](../manage-apps/assign-user-or-group-access-portal.md)とは異なる概念です。 グループをアプリケーションに割り当てることができますが、プロビジョニングできるのはグループに含まれるユーザー オブジェクトのみです。 グループ オブジェクト全体のプロビジョニングは、割り当てでグループを使用するためには必要ありません。

## <a name="editing-the-list-of-supported-attributes"></a>サポートされている属性一覧の編集

特定のアプリケーションでサポートされるユーザー属性は事前に構成されています。 ほとんどのアプリケーションのユーザー管理 API ではスキーマ検出がサポートされていません。 そのため、Azure AD プロビジョニング サービスは、アプリケーションを呼び出して、サポートされる属性の一覧を動的に生成することができません。

ただし、一部のアプリケーションではカスタム属性がサポートされ、Azure AD プロビジョニング サービスではカスタム属性の読み取りと書き込みができます。 それらの定義を Azure portal に入力するには、 **[属性マッピング]** 画面の一番下にある **[詳細オプションの表示]** チェック ボックスを選択し、アプリの **[Edit attribute list for]** (属性リストの編集) を選択します。

属性一覧のカスタマイズをサポートするアプリケーションとシステムには次のものが含まれます。

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory ([Azure AD Graph API の既定の属性](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity)とカスタム ディレクトリ拡張機能がサポートされる)
- [SCIM 2.0](https://tools.ietf.org/html/rfc7643) をサポートするアプリ ([コア スキーマ](https://tools.ietf.org/html/rfc7643)に定義された属性を追加する必要がある)

> [!NOTE]
> サポートされている属性一覧の編集は、アプリケーションとシステムのスキーマをカスタマイズし、カスタム属性がどのように定義されたかを直接知っている管理者のみにお勧めします。 場合によっては、アプリケーションやシステムで提供される API および開発者ツールに慣れている必要があります。

サポートされている属性の一覧を編集するときは、次のプロパティが表示されます。

- **[名前]** - ターゲット オブジェクトのスキーマで定義されている属性のシステム名。
- **[型]** - ターゲット オブジェクトのスキーマで定義されている、属性が格納するデータの型。次のいずれかの型になります。
  - *Binary* - 属性にはバイナリ データが含まれます。
  - *Boolean* - 属性には True または False 値が含まれます。
  - *DateTime* - 属性には日付文字列が含まれます。
  - *Integer* - 属性には整数が含まれます。
  - *Reference* - 属性には、ターゲット アプリケーションの別のテーブルに格納されている値を参照するための ID が含まれます。
  - *String*  - 属性にはテキスト文字列が含まれます。
- **[Primary Key?]\(主キー\)** - ターゲット オブジェクトのスキーマで属性が主キー フィールドとして定義されているかどうか。
- **必須** - ターゲット アプリケーションまたはシステムに属性を移入する必要があるかどうか。
- **[Multi-value?]\(複数値\)** - 属性が複数値をサポートするかどうか。
- **[Exact case?]\(大文字小文字の区別\)** - 大文字と小文字を区別して属性値を評価するかどうか。
- **[API 式]** - 特定のプロビジョニング コネクタ (Workday など) のドキュメントで指示されていない限り、使用しないでください。
- **[Referenced Object Attribute]** (参照オブジェクト属性) - これが Reference 型属性の場合は、このメニューを使用して、この属性に関連付けられている値を含むターゲット アプリケーションのテーブルと属性を選択できます。 たとえば、"Department" という名前の属性があるとき、そこに格納されている値が別の "Departments" テーブルのオブジェクトを参照する場合は、"Departments.Name" を選択します。 特定のアプリケーションでサポートされる参照テーブルとプライマリ ID フィールドは事前に構成されます。現在、Azure portal を使用して編集することはできませんが、[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes) を使用すると編集できます。

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>SCIM 準拠アプリケーションへカスタム拡張属性をプロビジョニングする
SCIM RFC ではコア ユーザーとグループ スキーマが定義されているだけでなく、スキーマの拡張をアプリケーションのニーズに合わせて使用することもできます。 カスタム属性を SCIM アプリケーションに追加するには、次の手順に従います。
   1. [Azure Active Directory ポータル](https://aad.portal.azure.com)にサインインし、 **[Enterprise Applications]\(エンタープライズ アプリケーション\)** を選択して、アプリケーションを選択し、 **[Provisioning]\(プロビジョニング\)** を選択します。
   2. **マッピング**で、カスタム属性を追加するオブジェクト (ユーザーまたはグループ) を選択します。
   3. ページの下部にある **[Show advanced options]\(詳細オプションの表示\)** を選択します。
   4. **[Edit attribute list for AppName]\(AppName の属性リストの編集\)** を選択します。
   5. 属性の一覧の下部にあるフィールドに、カスタム属性に関する情報を入力します。 **[属性の追加]** を選択します。

SCIM アプリケーションの場合は、次の例に示すパターンに従って属性名を指定する必要があります。 "CustomExtensionName" と "CustomAttribute" は、アプリケーションの要件に応じてカスタマイズできます。次に例を示します。  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

これらの手順は、SCIM 対応アプリケーションにのみ適用されます。 ServiceNow や Salesforce などのアプリケーションは、SCIM を使用する Azure AD と統合されていないため、カスタム属性を追加するときにこの特定の名前空間は必要ありません。

カスタム属性を参照属性または複数値の属性にすることはできません。 現在、カスタムの複数値の拡張属性は、ギャラリー内のアプリケーションに対してのみサポートされています。  
 
**拡張属性を持つユーザーの表記を次に示します。**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>SCIM アプリへのロールのプロビジョニング
ユーザーのロールをアプリケーションにプロビジョニングするには、次の手順を使用します。 下記の説明は、カスタム SCIM アプリケーションに固有のものであることに注意してください。 Salesforce や ServiceNow などのギャラリー アプリケーションの場合、事前定義済みのロール マッピングを使用します。 下記の項目は、アプリケーションで想定されている形式に AppRoleAssignments 属性を変換する方法を説明しています。

- Azure AD の appRoleAssignment をアプリケーションのロールにマップするには、[式](../app-provisioning/functions-for-customizing-application-data.md)を使用して属性を変換する必要があります。 ロールの詳細を解析するための式を使用せずに、appRoleAssignment 属性をロール属性に**直接マップしないでください**。 

- **SingleAppRoleAssignment** 
  - **使用する場合:** ユーザーの 1 つのロールをプロビジョニングする、またプライマリ ロールを指定するには、SingleAppRoleAssignment 式を使用します。 
  - **構成方法:** 上記の手順を使用して、属性マッピング ページに移動し、SingleAppRoleAssignment 式を使用してロール属性にマップします。 roles[primary eq "True"].display、roles[primary eq "True].type、roles[primary eq "True"].value の 3 つのロール属性から選択します。 ロール属性の一部または全部をマッピングに含めることを選択できます。 複数を含める場合は、新しいマッピングを追加し、それをターゲット属性として含めるだけです。  
  
  ![SingleAppRoleAssignment を追加する](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **考慮事項**
    - 複数のロールが 1 人のユーザーに割り当てられていないことを確認してください。 どのロールがプロビジョニングされるかを保証することはできません。
    
  - **出力例** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **使用する場合:** 1 人のユーザーに複数のロールをプロビジョニングするには、AppRoleAssignmentsComplex 式を使用します。 
  - **構成方法:** ロールの新しい属性を含めるには、上記のサポートされている属性の一覧を編集します。 
  
    ![ロールを追加する](./media/customize-application-attributes/add-roles.png)<br>

    その後、次の画像に示すように、AppRoleAssignmentsComplex 式を使用してカスタムのロール属性にマップします。

    ![AppRoleAssignmentsComplex を追加する](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **考慮事項**
    - すべてのロールは primary = false としてプロビジョニングされます。
    - POST にはロールの種類が含まれます。 PATCH 要求には種類は含まれません。 POST 要求と PATCH 要求の両方で種類を送信できるよう、作業を進めています。
    
  - **出力例** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>複数の値を持つ属性のプロビジョニング
phoneNumbers や emails のように、一部の属性は複数の値を持ち、異なる種類の電話番号やメール アドレスを指定することが必要な場合もあります。 複数の値を持つ属性には、次の式を使用します。 この式では、属性の種類を指定し、値に対応する Azure AD のユーザー属性にその種類をマップすることができます。 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>既定の属性と属性マッピングの復元

最初からやり直すために、既存のマッピングを既定の状態にリセットする必要がある場合は、 **[既定のマッピングを復元する]** チェック ボックスをオンにして構成を保存します。 そうすると、アプリケーションがアプリケーション ギャラリーから Azure AD テナントに追加された直後のようにすべてのマッピングとスコープ フィルターが設定されます。

このオプションを選択すると、プロビジョニング サービスを実行している際に、すべてのユーザーの再同期が実際に強制されます。

> [!IMPORTANT]
> このオプションを使用する前に、 **[サービス提供の状況]** を **[オフ]** に設定することを強くお勧めします。

## <a name="what-you-should-know"></a>知っておくべきこと

- Microsoft Azure AD では、同期プロセスの効率的な実装を提供します。 初期化された環境では、更新が必要なオブジェクトのみが同期サイクル中に処理されます。
- 属性マッピングの更新は、同期サイクルのパフォーマンスに影響を与えます。 属性マッピングの構成を更新するには、すべての管理オブジェクトを再評価する必要があります。
- 推奨されるベスト プラクティスとして、属性マッピングに対する連続的な変更の回数は最小限に抑えてください。
- 写真を同期する形式は指定できないので、プロビジョニングする写真属性のアプリへの追加は、現在サポートされていません。 [ユーザーの声](https://feedback.azure.com/forums/169401-azure-active-directory)に関するページで機能の要求をお送りください
- IsSoftDeleted 属性は、多くの場合、アプリケーションの既定のマッピングに含まれます。 IsSoftDeleted は 4 つのシナリオ (アプリケーションから割り当てられていないためユーザーがスコープ外になっている、スコープ フィルターを満たしていないためユーザーがスコープ外になっている、Azure AD でユーザーが論理的に削除されている、AccountEnabled プロパティがユーザーに対して false に設定されている) のいずれかで true にすることができます。 IsSoftDeleted 属性を属性マッピングから削除することはお勧めしません。
- Azure AD プロビジョニング サービスでは、null 値のプロビジョニングがサポートされていません。
- これらの主キー (通常は "ID") は、属性マッピングのターゲット属性として含めないでください。 
- 通常、role 属性は、直接マッピングではなく、式を使用してマッピングする必要があります。 ロールのマッピングの詳細については、上記のセクションを参照してください。 

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)
- [属性マッピングの式の書き方](../app-provisioning/functions-for-customizing-application-data.md)
- [ユーザーのプロビジョニング用のフィルターのスコープ](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](use-scim-to-provision-users-and-groups.md)
- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)