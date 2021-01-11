---
title: チュートリアル:Azure Active Directory に Workday 受信プロビジョニングを構成する | Microsoft Docs
description: Workday から Azure AD への受信プロビジョニングを構成する方法について説明します
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 69c3246c910a83d889151d6ad749e1be86340e8c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540968"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>チュートリアル:Azure AD ユーザー プロビジョニングに対して Workday を構成する
このチュートリアルの目的は、Workday のワーカー データを Azure Active Directory にプロビジョニングするために実行する必要がある手順を示すことです。 

>[!NOTE]
>Workday からプロビジョニングするユーザーがオンプレミスの AD アカウントを必要としないクラウド専用のユーザーである場合は、このチュートリアルを使用してください。 ユーザーがオンプレミスの AD アカウントのみを必要とする、または AD と Azure AD アカウントの両方を必要とする場合は、[Active Directory ユーザー プロビジョニングへの Workday の構成](workday-inbound-tutorial.md)に関するチュートリアルを参照してください。 

## <a name="overview"></a>概要

ユーザー アカウントをプロビジョニングするために、[Azure Active Directory ユーザー プロビジョニング サービス](../app-provisioning/user-provisioning.md)を [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) と統合します。 Azure AD のユーザー プロビジョニング サービスでサポートされている Workday ユーザー プロビジョニング ワークフローは、次の人事管理および ID ライフサイクル管理シナリオを自動化します。

* **新しい従業員の雇用** - Workday に新しい従業員が追加されると、Azure Active Directory に (必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)にも) ユーザー アカウントが自動的に作成され、メール アドレスが Workday に書き戻されます。

* **従業員の属性とプロファイルの更新** - Workday で従業員レコード (氏名、職名、管理者など) が更新されると、Azure Active Directory (および必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)) でユーザー アカウントが自動的に更新されます。

* **従業員の退職** - Workday で従業員が退職状態になると、Azure Active Directory (および必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)) でユーザー アカウントが自動的に無効になります。

* **従業員の再雇用** - Workday で従業員が再雇用されると、Azure Active Directory (および必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)) に以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この Workday から Azure Active Directory へのユーザー プロビジョニング ソリューションは、次の場合に最適です。

* Workday ユーザー プロビジョニング用に事前に構築されたクラウドベースのソリューションを求めている組織

* Workday から Azure Active Directory への直接的なユーザー プロビジョニングを必要とする組織

* Workday から取得したデータを使用してユーザーをプロビジョニングする必要がある組織

* 電子メールに Office 365 を使用している組織

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このセクションでは、クラウド専用のユーザーに向けた、エンド ツー エンドのユーザー プロビジョニング ソリューションのアーキテクチャについて説明します。 2 つの関連するフローがあります。

* **権限がある HR データのフロー – Workday から Azure Active Directory へ:** このフローでは、最初に雇用者イベント (新規雇用、異動、退職など) が Workday で発生し、イベント データはその後、Azure Active Directory に送られます。 イベントによっては、Azure AD での作成/更新/有効化/無効化の操作に至る可能性があります。
* **書き戻しフロー – オンプレミスの Active Directory から Workday へ:** Active Directory でアカウントの作成が完了すると、Azure AD Connect を介して Azure AD と同期され、メール、ユーザー名、電話番号などの情報を Workday に書き戻すことが可能になります。

  ![概要](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>エンド ツー エンドのユーザー データ フロー

1. HR チームは、Workday Employee Central で雇用者トランザクション (現職者/異動者/退職者または新規雇用/異動/退職) を実行します。
2. Azure AD プロビジョニング サービスで、Workday EC からのスケジュールされた ID の同期が実行され、オンプレミスの Active Directory との同期のために処理する必要がある変更が識別されます。
3. Azure AD プロビジョニング サービスは、変更を特定し、Azure AD のユーザーに対して作成/更新/有効化/無効化の操作を呼び出します。
4. [Workday Writeback](workday-writeback-tutorial.md) アプリが構成されている場合は、メール、ユーザー名、電話番号などの属性が Azure AD から取得されます。 
5. Azure AD プロビジョニング サービスによって、メール、ユーザー名、および電話番号が Workday に設定されます。

## <a name="planning-your-deployment"></a>デプロイの計画

Workday から Azure AD へのクラウド HR 駆動型のユーザー プロビジョニングを構成するには、次のようなさまざまな側面をカバーする大がかりな計画が必要です。

* 一致する ID の決定 
* 属性マッピング
* 属性の変換 
* スコープ フィルター

これらのトピックに関する包括的なガイドラインについては、[クラウド人事デプロイ計画](../app-provisioning/plan-cloud-hr-provision.md)に関するページを参照してください。 

## <a name="configure-integration-system-user-in-workday"></a>Workday の統合システム ユーザーの構成

Workday 統合システム ユーザー アカウントと雇用者データを取得するためのアクセス許可の作成については、「[統合システム ユーザーの構成](workday-inbound-tutorial.md#configure-integration-system-user-in-workday)」セクションを参照してください。 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Workday から Azure AD へのユーザー プロビジョニングを構成する

以下のセクションでは、クラウドのみのデプロイで Workday から Azure AD へのユーザー プロビジョニングを構成する手順について説明します。

* [Azure AD プロビジョニング コネクタ アプリケーションの追加と Workday への接続の作成](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday と Azure AD の属性マッピングを構成する](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>パート 1: Azure AD プロビジョニング コネクタ アプリケーションの追加と Workday への接続の作成

**Workday を構成して、クラウドのみのユーザーを Azure Active Directory にプロビジョニングするには、**

1. <https://portal.azure.com> にアクセスします。

2. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **[Workday to Azure AD user provisioning]\(Workday から Azure AD へのユーザー プロビジョニング\)** を検索し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します。

7. **[プロビジョニング** **モード]** を **[自動]** に変更します。

8. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **Workday ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 次のように表示されます: username@contoso4

   * **Workday パスワード** - Workday 統合システム アカウントのパスワードを入力します

   * **Workday Web Services API URL** - テナントの Workday Web サービス エンドポイントへの URL を入力します。 URL によって、コネクタで使用される Workday Web Services API のバージョンが決まります。 
   
     | URL 形式 | 使用される WWS API のバージョン | XPATH の変更が必要 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | いいえ |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | いいえ |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | はい |

      > [!NOTE]
     > URL にバージョン情報が指定されていない場合、アプリでは Workday Web Services (WWS) v21.1 が使用され、アプリに付属している既定の XPATH API 式の変更は必要ありません。 特定の WWS API バージョンを使用するには、URL の中にバージョン番号を指定します。 <br>
     > 例: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> WWS API v30.0 以降を使用する場合は、プロビジョニング ジョブを有効にする前に、「[構成の管理](workday-inbound-tutorial.md#managing-your-configuration)」セクションおよび [Workday 属性のリファレンス](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)を参照して、 **[属性マッピング] -> [詳細オプション] -> [Edit attribute list for Workday]\(Workday の属性リストの編集\)** の下にある **XPATH API 式**を更新します。  

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。

   * **[接続のテスト]** ボタンをクリックします。

   * 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、Workday URL と資格情報が Workday で有効であることを再度確認します。

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>パート 2: Workday と Azure AD の属性マッピングを構成する

このセクションでは、クラウドのみのユーザー データが Workday から Azure Active Directory に移動する方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、 **[Synchronize Workers to Azure AD (Workers を Azure AD に同期する)]** をクリックします。

2. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、Azure AD へのプロビジョニングの対象にする Workday のユーザー セットを選択できます。 既定のスコープは、"Workday のすべてのユーザー" です。 フィルターの例:

   * 例:1000000 から 2000000 までの Worker ID を持つユーザーにスコープを設定

      * 属性:WorkerID

      * 演算子:REGEX Match

      * 値:(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 例:正規従業員ではなく、臨時社員のみ

      * 属性:ContingentID

      * 演算子:IS NOT NULL

3. **[対象オブジェクトのアクション]** フィールドでは、Azure AD で実行されるアクションをグローバルにフィルター処理できます。 **[作成]** と **[更新]** が最も一般的です。

4. **[属性マッピング]** セクションでは、個別の Workday 属性を Active Directory の属性にマッピングする方法を定義できます。

5. 既存の属性マッピングをクリックして更新するか、または画面の下部にある **[新しいマッピングの追加]** をクリックして、新しいマッピングを追加します。 個々の属性マッピングは、次のプロパティをサポートしています。

   * **マッピングの種類**

      * **ダイレクト** - 変更なしで Workday 属性の値を AD 属性に書き込みます

      * **定数** - 静的な定数文字列の値を AD 属性に書き込みます

      * **式** – 1 つ以上の Workday 属性に基づいて、AD 属性にカスタム値を書き込むことができます。 [詳細については、式に関するこの記事を参照してください](../app-provisioning/functions-for-customizing-application-data.md)。

   * **ソース属性** - Workday のユーザー属性。 探している属性が存在しない場合は、「[Workday のユーザー属性リストをカスタマイズする](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)」を参照してください。

   * **既定値** – 省略可能。 ソース属性に空の値がある場合、マッピングではこの値が代わりに書き込まれます。
            最も一般的な構成では、これを空白のままにします。

   * **ターゲット属性** – Azure AD のユーザーの属性です。

   * **この属性を使用してオブジェクトを照合する** - この属性を使用して、Workday と Azure AD 間でユーザーを一意に識別するかどうかを示します。 この値は、通常、Workday の Worker ID フィールドで設定され、一般的に Azure AD の従業員 ID 属性 (新規) または拡張属性にマッピングされます。

   * **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

   * **このマッピングを適用する**

     * **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します

     * **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します

6. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。


## <a name="enable-and-launch-user-provisioning"></a>ユーザー プロビジョニングの有効化と起動

Workday プロビジョニング アプリの構成が完了したら、Azure portal でプロビジョニング サービスを有効にすることができます。

> [!TIP]
> 既定では、プロビジョニング サービスを有効にすると、スコープ内のすべてのユーザーに対してプロビジョニング操作が開始されます。 マッピングのエラーまたは Workday データの問題がある場合、プロビジョニング ジョブが失敗し、検疫状態になる可能性があります。 これを避けるために、ベスト プラクティスとして、すべてのユーザーの完全同期を開始する前に、 **[ソース オブジェクト スコープ]** フィルターを構成して少数のテスト ユーザーで属性マッピングをテストすることをお勧めします。 マッピングが機能し、目的の結果が得られていることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **[プロビジョニング]** タブで、 **[プロビジョニングの状態]** を **[ON]** に設定します。

2. **[保存]** をクリックします。

3. この操作により初期同期が開始されます。これに要する時間は Workday テナントのユーザー数に応じて変わります。 進行状況バーをチェックして、同期サイクルの進行状況を追跡できます。 

4. 好きなときに、Azure Portal の **[監査ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 監査ログには、Workday から読み込まれたユーザーや、その後 Azure Active Directory に追加または更新されたユーザーなど、プロビジョニング サービスによって実行された個々の同期イベントがすべて表示されます。 

5. 最初の同期が完了すると、次に示すように、 **[プロビジョニング]** タブに監査概要レポートが書き込まれます。

   > [!div class="mx-imgBorder"]
   > ![プロビジョニングの進行状況バー](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>次のステップ

* [受信プロビジョニングのサポートされている Workday 属性に関する詳細情報](../app-provisioning/workday-attribute-reference.md)
* [Workday Writeback の構成方法を確認する](workday-writeback-tutorial.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday と Azure Active Directory の間でシングル サインオンを構成する方法](workday-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)
* [プロビジョニング構成をエクスポートおよびインポートする方法を学習する](../app-provisioning/export-import-provisioning-configuration.md)


