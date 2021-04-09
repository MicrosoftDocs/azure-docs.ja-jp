---
title: チュートリアル:Azure Active Directory で SAP SuccessFactors の書き戻しを構成する | Microsoft Docs
description: Azure AD から SAP SuccessFactors への属性の書き戻しを構成する方法について説明します
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526977"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>チュートリアル:Azure AD から SAP SuccessFactors への属性の書き戻しを構成する
このチュートリアルの目的は、Azure AD から SAP SuccessFactors Employee Central に属性を書き戻すための手順を説明することです。 

## <a name="overview"></a>概要

Azure Active Directory から SAP SuccessFactors Employee Central に特定の属性が書き込まれるように、SAP SuccessFactors Writeback アプリを構成することができます。 SuccessFactors 書き戻しプロビジョニング アプリでは、次の Employee Central 属性に対する値の割り当てがサポートされています。

* 勤務先の電子メール
* ユーザー名
* 勤務先の電話番号 (国番号、市外局番、番号、内線番号を含む)
* 勤務先電話番号のプライマリ フラグ
* 携帯電話番号 (国番号、市外局番、番号、内線番号を含む)
* 携帯電話のプライマリ フラグ 
* ユーザーの custom01 - custom15 属性
* loginMethod 属性

> [!NOTE]
> このアプリには、SuccessFactors 受信ユーザー プロビジョニング統合アプリに対する依存関係はありません。 [SuccessFactors からオンプレミス AD](sap-successfactors-inbound-provisioning-tutorial.md) プロビジョニング アプリまたは [SuccessFactors から Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) プロビジョニング アプリとは関係なく、構成することができます。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この SuccessFactors Writeback ユーザー プロビジョニング ソリューションは、次の場合に最適です：

* IT によって管理される信頼できる属性 (メール アドレス、電話番号、ユーザー名など) を SuccessFactors Employee Central に書き戻す必要のある、Microsoft 365 を使用している組織。

## <a name="configuring-successfactors-for-the-integration"></a>統合のための SuccessFactors の構成

すべての SuccessFactors プロビジョニング コネクタでは、Employee Central OData API を呼び出すための適切なアクセス許可を持つ SuccessFactors アカウントの資格情報が必要です。 このセクションでは、SuccessFactors でサービス アカウントを作成し、適切なアクセス許可を付与する手順を説明します。 

* [SuccessFactors で API ユーザー アカウントを作成または識別する](#createidentify-api-user-account-in-successfactors)
* [API アクセス許可ロールを作成する](#create-an-api-permissions-role)
* [API ユーザーのアクセス許可グループを作成する](#create-a-permission-group-for-the-api-user)
* [アクセス許可グループへのアクセス許可ロールの付与をする](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors で API ユーザー アカウントを作成または識別する
SuccessFactors 管理チームまたは実装パートナーと協力して、OData API の呼び出しに使用される SuccessFactors のユーザー アカウントを作成または識別します。 Azure AD でプロビジョニング アプリを構成する場合、このアカウントのユーザー名とパスワードの資格情報が必要になります。 

### <a name="create-an-api-permissions-role"></a>API アクセス許可ロールを作成する

1. Admin Center にアクセスできるユーザーアカウントで SAP SuccessFactors にログインします。
1. "*Manage Permission Roles*" を検索し、検索結果から **[Manage Permission Roles]\(アクセス許可ロールの管理\)** を選択します。

   ![アクセス許可ロールを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. [Permission Role List]\(アクセス許可ロール一覧\) で、 **[Create New]\(新規作成\)** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![アクセス許可ロールを新規作成する](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 新しいアクセス許可ロールの **[Role Name]\(ロール名\)** と **[Description]\(説明\)** を追加します。 名前と説明では、このロールが API 使用アクセス許可されていることを示す必要があります。

   > [!div class="mx-imgBorder"]
   > ![アクセス許可のロールの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. [Permission settings]\(アクセス許可の設定\) で、 **[Permission]\(アクセス許可\)** をクリックし、アクセス許可一覧を下にスクロールして **[Manage Integration Tools]\(統合ツールの管理\)** をクリックします。 **[Allow Admin to Access to OData API through Basic Authentication]\(管理者が Websites Basic 認証を介して OData API にアクセスできるようにする\)** チェックボックスを有効にします。

   > [!div class="mx-imgBorder"]
   > ![統合ツールを管理する](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 同じボックス内を下にスクロールし、 **[Employee Central API]** を選択します。 次に示すように、ODATA API を使用して読み取り、ODATA API を使用して編集するためのアクセス許可を追加します。 SuccessFactors への書き戻しシナリオに同じアカウントを使用する場合は、編集オプションを選択します。 

   > [!div class="mx-imgBorder"]
   > ![読み取りと書き込みのアクセス許可](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. **[Done]\(終了)** をクリックします。 **[変更を保存]** をクリックします。

### <a name="create-a-permission-group-for-the-api-user"></a>API ユーザーのアクセス許可グループを作成する

1. SuccessFactors Admin Center で、"*Manage Permission Groups*" を検索し、検索結果から **[Manage Permission Groups]\(アクセス許可グループの管理\)** を選択します。

   > [!div class="mx-imgBorder"]
   > ![アクセス許可グループを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. [Manage Permission Groups]\(アクセス許可グループの管理\) ウィンドウで、 **[Create New]\(新規作成\)** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![新しいグループを追加する](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 新しいグループのグループ名を追加します。 グループ名は、グループが API ユーザー用であることを示す必要があります。

   > [!div class="mx-imgBorder"]
   > ![アクセス許可グループ名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. グループにメンバーを追加します。 たとえば、[People Pool]\(ユーザー プール\) ドロップダウン メニューから **[Username]\(ユーザー名\)** を選択し、統合に使用する API アカウントのユーザー名を入力します。 

   > [!div class="mx-imgBorder"]
   > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png)\(グループ メンバーの追加\)

1. **[Done]\(完了\)** をクリックして、アクセス許可グループの作成を完了します。

### <a name="grant-permission-role-to-the-permission-group"></a>許可グループに許可ロールを付与する

1. SuccessFactors Admin Center で、"*Manage Permission Roles*" を検索し、検索結果から **[Manage Permission Roles]\(アクセス許可ロールの管理\)** を選択します。
1. **[Permission Role List]\(アクセス許可ロール一覧\)** から、API 使用アクセス許可用に作成したロールを選択します。
1. **[Grant this role to]\(このロールに付与するアクセス許可\)** で、 **[Add]\(追加\)** ボタンをクリックします。
1. ドロップダウン メニューから **[Permission Group]\(アクセス許可グループ\)** を選択し、 **[Select]\(選択\)** をクリックして [Groups]\(グループ\) ウィンドウを開き、先ほど作成したグループを検索して選択します。 

   > [!div class="mx-imgBorder"]
   > ![アクセス許可グループを追加する](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. アクセス許可グループに対するアクセス許可ロールの付与を確認します。 
   > [!div class="mx-imgBorder"]
   > ![アクセス許可ロールおよびグループの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. **[変更を保存]** をクリックします。

## <a name="preparing-for-successfactors-writeback"></a>SuccessFactors Writeback の準備

SuccessFactors Writeback プロビジョニング アプリでは、Employee Central でメール アドレスと電話番号を設定するために、特定の "*コード*" の値が使用されます。 これらの "*コード*" の値は、属性マッピング テーブルの定数値として設定され、SuccessFactors のインスタンスごとに異なります。 このセクションでは、これらの "*コード*" 値をキャプチャする手順について説明します。

   > [!NOTE]
   > このセクションの手順を完了するには、SuccessFactors 管理者に協力を要請してください。 

### <a name="identify-email-and-phone-number-picklist-names"></a>メール アドレスと電話番号の候補リストの名前を識別する 

SAP SuccessFactors では、"*候補リスト*" は、ユーザーが選択できるオプションの構成可能なセットです。 さまざまな種類のメール アドレスと電話番号 (例: 勤務先、個人、その他) は、候補リストを使用して表されます。 この手順では、メール アドレスと電話番号の値を格納するために、SuccessFactors テナントで構成されている候補リストを識別します。 
 
1. SuccessFactors Admin Center で、"*Manage business configuration*" (勤務先の構成の管理) を検索します。 

   > [!div class="mx-imgBorder"]
   > ![Manage business configuration (勤務先の構成の管理)](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. **[HRIS Elements]\(HRIS 要素\)** で **[emailInfo]** を選択し、 **[email-type]** フィールドの *[Details]\(詳細\)* をクリックします。

   > [!div class="mx-imgBorder"]
   > ![メール アドレス情報の取得](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. **[email-type]** の詳細ページで、このフィールドに関連付けられている候補リストの名前を書き留めます。 既定では、**ecEmailType** です。 ただし、テナントによって異なる場合があります。 

   > [!div class="mx-imgBorder"]
   > ![メール アドレスの候補リストの識別](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. **[HRIS Elements]\(HRIS 要素\)** で **[phoneInfo]** を選択し、 **[phone-type]** フィールドの *[Details]\(詳細\)* をクリックします。

   > [!div class="mx-imgBorder"]
   > ![電話情報の取得](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. **[phone-type]** の詳細ページで、このフィールドに関連付けられている候補リストの名前を書き留めます。 既定では、**ecPhoneType** です。 ただし、テナントによって異なる場合があります。 

   > [!div class="mx-imgBorder"]
   > ![電話の候補リストを識別する](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>emailType の定数値を取得する

1. SuccessFactors Admin Center で *Picklist Center* を検索して開きます。 
1. 前のセクションで取得したメール アドレスの候補リストの名前 (例: ecEmailType) を使用して、メール アドレスの候補リストを見つけます。 

   > [!div class="mx-imgBorder"]
   > ![メール アドレスの種類の候補リストの検索](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. アクティブなメール アドレスの候補リストを開きます。 

   > [!div class="mx-imgBorder"]
   > ![アクティブなメール アドレスの種類の候補リストを開く](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. メール アドレスの種類の候補リスト ページで、メール アドレスの種類として *[Business]\(勤務先\)* を選択します。

   > [!div class="mx-imgBorder"]
   > ![勤務先のメール アドレスの種類の選択](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. *[Business]\(勤務先\)* メール アドレスに関連付けられている **[Option ID]\(オプション ID\)** をメモしておきます。 これは、属性マッピング テーブルで *emailType* と共に使用するコードです。

   > [!div class="mx-imgBorder"]
   > ![メール アドレスの種類のコードを取得する](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > 値をコピーするときは、コンマ文字を削除してください。 たとえば、 **[Option ID]\(オプション ID\)** 値が *8,448* の場合は、Azure AD の *[emailType]* を (コンマ文字を含まない) 定数 *8448* に設定します。 

### <a name="retrieve-constant-value-for-phonetype"></a>phoneType の定数値を取得する

1. SuccessFactors Admin Center で *Picklist Center* を検索して開きます。 
1. 前のセクションで取得した電話の候補リストの名前を使用して、電話の候補リストを見つけます。 

   > [!div class="mx-imgBorder"]
   > ![電話の種類の候補リストの検索](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. アクティブな電話の候補リストを開きます。 

   > [!div class="mx-imgBorder"]
   > ![アクティブな電話の種類の候補リストを開く](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. 電話の種類の候補リストのページで、 **[Picklist Values]\(候補リストの値\)** に表示されているさまざまな電話の種類を確認します。

   > [!div class="mx-imgBorder"]
   > ![電話の種類を確認する](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. *[Business]\(勤務先\)* 電話に関連付けられている **[Option ID]\(オプション ID\)** をメモしておきます。 これは、属性マッピング テーブルで *businessPhoneType* と共に使用するコードです。

   > [!div class="mx-imgBorder"]
   > ![勤務先の電話コードを取得する](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. *[Cell]\(携帯\)* 電話に関連付けられている **[Option ID]\(オプション ID\)** をメモしておきます。 これは、属性マッピング テーブルで *cellPhoneType* と共に使用するコードです。

   > [!div class="mx-imgBorder"]
   > ![携帯電話コードを取得する](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > 値をコピーするときは、コンマ文字を削除してください。 たとえば、 **[Option ID]\(オプション ID\)** 値が *10,606* の場合は、Azure AD の *[cellPhoneType]* を (コンマ文字を含まない) 定数 *10606* に設定します。 


## <a name="configuring-successfactors-writeback-app"></a>SuccessFactors Writeback アプリの構成

このセクションでは、次のステップについて説明します 

* [プロビジョニング コネクタ アプリを追加して、SuccessFactors への接続の構成をする](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [属性マッピングの構成](#part-2-configure-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>パート 1: プロビジョニング コネクタ アプリを追加して、SuccessFactors への接続の構成をする

**SAP SuccessFactors 書き戻し：**

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します

2. 左のナビゲーション バーで、 **[Azure Active Directory]** を選択します

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **SuccessFactors Writeback** を検索し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します

7. **[プロビジョニング** **モード]** を **[自動]** に変更します

8. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理ユーザー名** - SuccessFactors API ユーザー アカウントのユーザー名に、会社 ID を追加して入力します。 形式は **username\@companyID** です

   * **管理パスワード** - SuccessFactors API ユーザー アカウントのパスワードを入力します。 

   * **テナント URL** - SuccessFactors OData API サービス エンドポイントの名前を入力します。 http または https なしでサーバーのホスト名のみを入力してください。 この値は次のようになります: `api4.successfactors.com`。

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。
    > [!NOTE]
    > Azure AD プロビジョニング サービスは、プロビジョニング ジョブが[検査](../app-provisioning/application-provisioning-quarantine-status.md)状態になった場合にメール通知を送信します。

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、SuccessFactors 資格情報および URL が有効か再度確認します。
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 資格情報が正常に保存されると、 **[マッピング]** セクションに既定のマッピングが表示されます。 属性マッピングが表示されない場合は、ページを更新します。  

### <a name="part-2-configure-attribute-mappings"></a>パート 2: 属性マッピングの構成

このセクションでは、ユーザーデータが SuccessFactors から Active Directory Domain Services に流れる方法を構成します。

1. [プロビジョニング] タブの **[マッピング]** で、 **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** をクリックします。

1. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、書き戻しの対象となる Azure AD のユーザー セットを選択できます。 既定のスコープは、"Azure AD のすべてのユーザー" です。 
   > [!TIP]
   > 初めてプロビジョニング アプリを構成するときは、属性マッピングと式をテストして検証し、目的の結果が得られていることを確認する必要があります。 Microsoft は、Azure Active Directory の少数のテスト ユーザーを使用してマッピングをテストする場合には、 **[ソース オブジェクト スコープ]** の下のスコープ フィルターを使用するようお勧めします。 マッピングが機能していることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **ターゲットオブジェクトアクション** フィールドでは、**更新** 操作のみがサポートされます。

1. **[属性マッピング]** セクションのマッピング テーブルでは、次の Azure Active Directory 属性を SuccessFactors にマップできます。 次の表では、書き戻し属性をマップする方法に関するガイダンスを示します。 

   | \# | Azure AD の属性 | SuccessFactors 属性 | 解説 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 既定では、この属性は一致する識別子です。 employeeId の代わりに、SuccessFactors の personIdExternal に等しい値を格納できる他の Azure AD 属性を使用できます。    |
   | 2 | mail | email | メール属性ソースをマップします。 テストのため、userPrincipalName をメールにマップできます。 |
   | 3 | 8448 | emailType | この定数値は、勤務先のメールに関連付けられている SuccessFactors ID の値です。 SuccessFactors の環境に合わせてこの値を更新します。 この値を設定する手順については、「[emailType の定数値を取得する](#retrieve-constant-value-for-emailtype)」セクションを参照してください。 |
   | 4 | true | emailIsPrimary | SuccessFactors のプライマリとして勤務先のメールを設定するには、この属性を使用します。 勤務先のメールがプライマリでない場合は、このフラグを false に設定します。 |
   | 5 | userPrincipalName | [custom01 – custom15] | **[新しいマッピングの追加]** を使用すると、必要に応じて、SuccessFactors の User オブジェクトで使用可能なカスタム属性に、userPrincipalName または任意の Azure AD 属性を書き込むことができます。  |
   | 6 | On Prem SamAccountName | username | **[新しいマッピングの追加]** を使用すると、必要に応じてオンプレミスの samAccountName を SuccessFactors の username 属性にマップできます。 [Azure AD Connect 同期: ディレクトリ拡張機能](../hybrid/how-to-connect-sync-feature-directory-extensions.md)を使用して、samAccountName を Azure AD に同期します。 これは、ソース ドロップダウンに *extension_yourTenantGUID_samAccountName* として表示されます。 |
   | 7 | SSO | loginMethod | SuccessFactors テナントが[部分的な SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766) に対してセットアップされている場合、[新しいマッピングの追加] を使用すると、必要に応じて loginMethod を "SSO" または "PWD" の定数値に設定できます。 |
   | 8 | telephoneNumber | businessPhoneNumber | *telephoneNumber* を Azure AD から SuccessFactors の勤務先または職場の電話番号に転送するには、このマッピングを使用します。 |
   | 9 | 10605 | businessPhoneType | この定数値は、勤務先の電話に関連付けられている SuccessFactors ID の値です。 SuccessFactors の環境に合わせてこの値を更新します。 この値を設定する手順については、「[phoneType の定数値を取得する](#retrieve-constant-value-for-phonetype)」セクションを参照してください。 |
   | 10 | true | businessPhoneIsPrimary | 勤務先電話番号に対してプライマリ フラグを設定するには、この属性を使用します。 有効な値は true または false です。 |
   | 11 | mobile | cellPhoneNumber | *telephoneNumber* を Azure AD から SuccessFactors の勤務先または職場の電話番号に転送するには、このマッピングを使用します。 |
   | 12 | 10606 | cellPhoneType | この定数値は、携帯電話に関連付けられている SuccessFactors ID の値です。 SuccessFactors の環境に合わせてこの値を更新します。 この値を設定する手順については、「[phoneType の定数値を取得する](#retrieve-constant-value-for-phonetype)」セクションを参照してください。 |
   | 13 | false | cellPhoneIsPrimary | 携帯電話番号に対してプライマリ フラグを設定するには、この属性を使用します。 有効な値は true または false です。 |
 
1. 属性マッピングを検証して確認します。 
 
    >[!div class="mx-imgBorder"]
    >![書き戻し属性マッピング](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. **[保存]** をクリックして、マッピングを保存します。 次に、SuccessFactors のインスタンスで phoneType コードを使用するように、JSON Path API 式を更新します。 
1. **[詳細オプションの表示]** 　を選択します。 

    >[!div class="mx-imgBorder"]
    >![詳細オプションを表示する](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. **[SuccessFactors の属性リストを編集します]** をクリックします。 

   > [!NOTE] 
   > **[SuccessFactors の属性リストを編集します]** オプションが Azure portal に表示されない場合は、URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* を使用してページにアクセスします。 

1. このビューの **[API 式]** 列には、コネクタによって使用される JSON パス式が表示されます。 
1. 環境に対応する ID 値 (*businessPhoneType* と *cellPhoneType*) を使用するように、勤務先電話と携帯電話の JSON パス式を更新します。 

    >[!div class="mx-imgBorder"]
    >![電話の JSON パスの変更](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. **[保存]** をクリックして、マッピングを保存します。

## <a name="enable-and-launch-user-provisioning"></a>ユーザー プロビジョニングの有効化と起動

SuccessFactors プロビジョニング アプリの構成が完了すると、Azure portal でプロビジョニング サービスを有効にできます。

> [!TIP]
> 既定では、プロビジョニング サービスを有効にすると、スコープ内のすべてのユーザーに対してプロビジョニング操作が開始されます。 マッピングのエラーまたはデータの問題がある場合、プロビジョニング ジョブが失敗し、検疫状態になる可能性があります。 これを避けるために、ベスト プラクティスとして、すべてのユーザーの完全同期を開始する前に、 **[ソース オブジェクト スコープ]** フィルターを構成して少数のテスト ユーザーで属性マッピングをテストすることをお勧めします。 マッピングが機能し、目的の結果が得られていることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **[プロビジョニング]** タブで、 **[プロビジョニングの状態]** を **[ON]** に設定します。

1. **[スコープ]** を選択します。 以下のオプションのいずれかを選択できます。 
   * **すべてのユーザーとグループを同期する**:すべてのユーザーのマップされた属性を **[マッピング]** の **[ソース オブジェクト スコープ]** に定義されているスコープ規則に従って Azure AD から SuccessFactors に書き戻す予定の場合、このオプションを使用します。 
   * **割り当てられたユーザーとグループのみを同期する**: **[アプリケーション]** の **[管理]** の **[ユーザーとグループ]** メニュー オプションでこのアプリケーションに割り当てたユーザーのみのマップされた属性を書き戻す予定の場合、このオプションを選択します。 これらのユーザーは、 **[マッピング]** の **[ソース オブジェクト スコープ]** で定義されているスコープ規則にも制約されます。

   > [!div class="mx-imgBorder"]
   > ![書き戻しのスコープを選択する](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > SuccessFactors Writeback プロビジョニング アプリでは "グループ割り当て" がサポートされていません。 "ユーザー割り当て" のみがサポートされています。 

1. **[保存]** をクリックします。

1. この操作により初期同期が開始されます。所要時間は Azure AD テナントのユーザー数と操作に定義されているスコープに応じて変わります。 進行状況バーをチェックして、同期サイクルの進行状況を追跡できます。 

1. 好きなときに、Azure Portal の **[プロビジョニング ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 プロビジョニング ログには、プロビジョニング サービスによって実行される個々の同期イベントがすべて一覧表示されます。 

1. 最初の同期が完了すると、次に示すように、 **[プロビジョニング]** タブに監査概要レポートが書き込まれます。

   > [!div class="mx-imgBorder"]
   > ![プロビジョニングの進行状況バー](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>サポートされるシナリオ、既知の問題、制限事項

SAP SuccessFactors 統合リファレンス ガイドの[書き戻しシナリオに関するセクション](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios)を参照してください。 

## <a name="next-steps"></a>次のステップ

* [Azure AD と SAP SuccessFactors の統合の詳細リファレンス](../app-provisioning/sap-successfactors-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
* [SuccessFactors と Azure Active Directory Domain Services の間でシングル サインオンを構成する方法を学習する](successfactors-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)
* [プロビジョニング構成をエクスポートおよびインポートする方法を学習する](../app-provisioning/export-import-provisioning-configuration.md)