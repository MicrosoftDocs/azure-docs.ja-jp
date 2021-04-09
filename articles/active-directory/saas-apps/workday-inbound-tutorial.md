---
title: チュートリアル:Workday を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Workday に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954220"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>チュートリアル:Workday を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Workday のワーカー プロファイルをオンプレミスの Active Directory (AD) にプロビジョニングするために実行する必要がある手順を示すことです。

>[!NOTE]
>Workday からプロビジョニングするユーザーに、オンプレミス AD アカウントと Azure AD アカウントが必要な場合は、このチュートリアルを使用します。 
>* Workday のユーザーに Azure AD アカウント (クラウドのみのユーザー) のみが必要な場合は、[Workday から Azure AD へのユーザー プロビジョニングを構成する](workday-inbound-cloud-only-tutorial.md)方法に関するチュートリアルを参照してください。 
>* Azure AD から Workday へのメール アドレス、ユーザー名、電話番号などの属性の書き戻しを構成するには、[Workday の書き戻しの構成](workday-writeback-tutorial.md)に関するチュートリアルを参照してください。

## <a name="overview"></a>概要

ユーザー アカウントをプロビジョニングするために、[Azure Active Directory ユーザー プロビジョニング サービス](../app-provisioning/user-provisioning.md)を [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) と統合します。 Azure AD のユーザー プロビジョニング サービスでサポートされている Workday ユーザー プロビジョニング ワークフローは、次の人事管理および ID ライフサイクル管理シナリオを自動化します。

* **新しい従業員の雇用** - Workday に新しい従業員が追加されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)でユーザー アカウントが自動的に作成され、IT 管理の連絡情報が Workday に書き戻されます。

* **従業員の属性とプロファイルの更新** - Workday で従業員レコード (名前、職名、マネージャーなど) が更新されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)でユーザー アカウントが自動的に更新されます。

* **従業員の退職** - Workday で従業員が退職状態になると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)でユーザー アカウントが自動的に無効になります。

* **従業員の再雇用** - Workday で従業員が再雇用されると、Active Directory、Azure Active Directory、必要に応じて Microsoft 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../app-provisioning/user-provisioning.md)に以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。

### <a name="whats-new"></a>新機能
このセクションでは、最新の Workday 統合の機能強化について説明します。 更新内容、予定されている変更、アーカイブの包括的な一覧については、「[Azure Active Directory の新着情報](../fundamentals/whats-new.md)」のページを参照してください。 

* **2020 年 10 月 - Workday のオンデマンド プロビジョニングが可能に:** [オンデマンド プロビジョニング](../app-provisioning/provision-on-demand.md)を使用すると、Workday に対する特定のユーザー プロファイルのプロビジョニングをエンド ツー エンドでテストして、属性マッピングと式のロジックを検証できます。   

* **2020 年 5 月 - 電話番号を Workday に書き戻す機能:** メールとユーザー名に加えて、勤務先の電話番号と携帯電話番号を Azure AD から Workday に書き戻すことができるようになりました。 詳細については、[Writeback アプリのチュートリアル](workday-writeback-tutorial.md)を参照してください。

* **2020 年 4 月 - 最新バージョンの Workday Web Services (WWS) API のサポート:** Workday は年に 2 回、3 月と 9 月に、ビジネス目標の達成と労働力需要の変化に追い付くために役立つ機能豊富なアップデートを提供します。 Workday が提供する新機能を活用していただくため、使用する WWS API バージョンを接続 URL で直接指定できるようになりました。 Workday API バージョンを指定する方法の詳細については、[Workday 接続の構成](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)に関するセクションを参照してください。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この Workday ユーザー プロビジョニング ソリューションは、次のお客様に最適です。

* Workday ユーザー プロビジョニング用に事前に構築されたクラウドベースのソリューションを求めている組織

* Workday から Active Directory、または Azure Active Directory への直接ユーザー プロビジョニングを必要とする組織

* Workday HCM モジュールから取得したデータを使用してユーザーをプロビジョニングする必要がある組織 ([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) を参照してください)

* 1 つ以上の Active Directory フォレスト、ドメイン、および OU と同期するために、Workday HCM モジュールで検出された変更情報のみに基づいてユーザーの登録、移動、削除を行う必要がある組織 ([Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) を参照してください)

* 電子メールに Microsoft 365 を使用している組織

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このセクションでは、一般的なハイブリッド環境に向けた、エンド ツー エンドのユーザー プロビジョニング ソリューションのアーキテクチャについて説明します。 2 つの関連するフローがあります。

* **権限がある人事データのフロー – Workday からオンプレミスの Active Directory へ:** このフローでは、最初に社員イベント (新規雇用、異動、退職など) がクラウドの Workday HR テナントで発生し、イベント データはその後、Azure AD ディレクトリとプロビジョニング エージェントを通ってオンプレミスの Active Directory に移動します。 イベントによっては、AD での作成/更新/有効化/無効化の操作に至る可能性があります。
* **書き戻しフロー – オンプレミスの Active Directory から Workday へ:** Active Directory でアカウントの作成が完了すると、Azure AD Connect を介して Azure AD と同期され、メール、ユーザー名、電話番号などの情報を Workday に書き戻すことが可能になります。

![概要](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>エンド ツー エンドのユーザー データ フロー

1. 人事チームは、Workday HCM で社員のトランザクション (参加者/異動者/休暇者または新規雇用/移動/退職) を実行します
2. Azure AD プロビジョニング サービスは、Workday HR からの、スケジュールされた ID の同期を実行し、オンプレミスの Active Directory との同期のために処理する必要がある変更を識別します。
3. Azure AD プロビジョニング サービスは、AD アカウントの作成/更新/有効化/無効化の操作を含む要求ペイロードを使用して、オンプレミスの Azure AD Connect プロビジョニング エージェントを呼び出します。
4. Azure AD Connect プロビジョニング エージェントは、サービス アカウントを使用して AD アカウントのデータを追加/更新します。
5. Azure AD Connect (AD の同期エンジン) は、デルタ同期を実行して AD 内の更新をプルします。
6. Active Directory の更新は、Azure Active Directory と同期されます。
7. [Workday Writeback](workday-writeback-tutorial.md) アプリが構成されている場合は、メール、ユーザー名、電話番号などの属性が Workday に書き戻されます。

## <a name="planning-your-deployment"></a>デプロイの計画

Workday から Active Directory へのユーザー プロビジョニングを構成するには、次のようなさまざまな側面をカバーする膨大な計画が必要です。
* Azure AD Connect プロビジョニング エージェントの設定 
* デプロイする Workday から AD へのユーザー プロビジョニング アプリの数
* 一致する正しい識別子、属性マッピング、変換、およびスコープ フィルターの選択

包括的なガイドラインと推奨されるベスト プラクティスについては、[クラウド人事デプロイ計画](../app-provisioning/plan-cloud-hr-provision.md)に関する記事を参照してください。 

## <a name="configure-integration-system-user-in-workday"></a>Workday の統合システム ユーザーの構成

すべての Workday プロビジョニング コネクタに共通する要件は、Workday Human Resources API に接続するために、Workday 統合システム ユーザーの資格情報を必要とすることです。 このセクションでは、Workday で統合システムのユーザーを作成する方法について説明します。次のセクションがあります。

* [統合システム ユーザーの作成](#creating-an-integration-system-user)
* [統合セキュリティ グループの作成](#creating-an-integration-security-group)
* [ドメイン セキュリティ ポリシーのアクセス許可の構成](#configuring-domain-security-policy-permissions)
* [ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成](#configuring-business-process-security-policy-permissions)
* [セキュリティ ポリシーの変更のアクティブ化](#activating-security-policy-changes)

> [!NOTE]
> この手順を省略し、代わりに Workday グローバル管理者アカウントをシステム統合アカウントとして使用することもできます。 これはデモでは問題なく動作するかもしれませんが、本番環境での展開にはお勧めできません。

### <a name="creating-an-integration-system-user"></a>統合システム ユーザーの作成

**統合システム ユーザーを作成するには、次の手順に従います。**

1. 管理者アカウントを使用して Workday テナントにサインインします。 **Workday アプリケーション** で、検索ボックスに「ユーザーの作成」と入力し、 **[Create Integration System User (統合システム ユーザーの作成)]** をクリックします。

   >[!div class="mx-imgBorder"] 
   >![ユーザーの作成](./media/workday-inbound-tutorial/wd_isu_01.png "ユーザーの作成")
2. 新しい統合システム ユーザーのユーザー名とパスワードを指定して、**統合システム ユーザーの作成** を完了します。  

   * このユーザーはプログラムを使用してログオンするため、 **[次回のサインイン時に新しいパスワードを要求する]** オプションはオフのままにしておきます。
   * **[セッション タイムアウト (分)]** は既定値の 0 のままにしておきます。これにより、ユーザーのセッションが有効期限前にタイムアウトするのを防ぎます。
   * オプション **[Do Not Allow UI Sessions]\(UI セッションを許可しない)** を選択します。これは、統合システムのパスワードを持つユーザーが Workday にログインできないようにする追加のセキュリティ層を提供するためです。

   > [!div class="mx-imgBorder"]
   > ![[Create Integration System User (統合システム ユーザーの作成)]](./media/workday-inbound-tutorial/wd_isu_02.png "[Create Integration System User (統合システム ユーザーの作成)]")

### <a name="creating-an-integration-security-group"></a>統合セキュリティ グループの作成

この手順では、Workday 内に、制約のない、または制約付きの統合システム セキュリティ グループを作成し、このグループに、前の手順で作成した統合システム ユーザーを割り当てます。

**セキュリティ グループを作成するには、次の手順に従います。**

1. 検索ボックスに「セキュリティ グループの作成」と入力し、 **[Create Security Group (セキュリティ グループの作成)]** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![検索ボックスに "create security group" (セキュリティ グループの作成) と入力し、検索結果に [Create Security Group - Task]\(セキュリティ グループの作成 -タスク\) と表示されたことを示すスクリーンショット。](./media/workday-inbound-tutorial/wd_isu_03.png)
2. **[セキュリティ グループの作成]** タスクを完了します。 

   * Workday のセキュリティ グループには次の 2 種類があります。
     * **制約なし:** セキュリティ グループのすべてのメンバーは、セキュリティ グループによって保護されているすべてのデータ インスタンスにアクセスできます。
     * **制約付き:** すべてのセキュリティ グループ メンバーは、セキュリティ グループがアクセスできるデータ インスタンス (行) のサブセットへのコンテキスト アクセス権を持っています。
   * 統合に適したセキュリティ グループの種類を選択するには、Workday 統合パートナーに確認してください。
   * グループの種類がわかったら、 **[Type of Tenanted Security Group]\(テナント セキュリティ グループの種類\)** ドロップダウンから **[Integration System Security Group (Unconstrained)]\(統合システム セキュリティ グループ (制約なし)\)** または **[Integration System Security Group (Constrained)]\(統合システム セキュリティ グループ (制約あり)\)** を選択します。

     > [!div class="mx-imgBorder"]
     >![CreateSecurity グループ](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity グループ")

3. セキュリティ グループの作成が成功した後、セキュリティ グループにメンバーを割り当てることができるページが表示されます。 前の手順で作成した新しい統合システム ユーザーをこのセキュリティ グループに追加します。 "*制約付き*" セキュリティ グループを使用している場合は、適切な組織の範囲も選択する必要があります。

   >[!div class="mx-imgBorder"]
   >![セキュリティ グループの編集](./media/workday-inbound-tutorial/wd_isu_05.png "[Edit Security Group (セキュリティ グループの編集)]")

### <a name="configuring-domain-security-policy-permissions"></a>ドメイン セキュリティ ポリシーのアクセス許可の構成

この手順では、セキュリティ グループに、社員データについての "ドメイン セキュリティ" ポリシーのアクセス許可を付与します。

**ドメイン セキュリティ ポリシーのアクセス許可を構成するには:**

1. 検索ボックスに「**Security Group Membership and Access**」と入力してレポート リンクをクリックします。
   >[!div class="mx-imgBorder"]
   >![Security Group Membership を検索する](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. 前の手順で作成したセキュリティ グループを検索して選択します。 
   >[!div class="mx-imgBorder"]
   >![セキュリティ グループの選択](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. グループ名の横にある省略記号 (...) をクリックし、メニューから **[Security Group]\(セキュリティ グループ\) > [Maintain Domain Permissions for Security Group]\(セキュリティ グループのドメイン アクセス許可の管理\)** を選択します。
   >[!div class="mx-imgBorder"]
   >![ドメイン アクセス許可の管理を選択する](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. **[Integration Permissions]\(統合アクセス許可\)** の **[Domain Security Policies permitting Put access]\(Put アクセスを許可するドメイン セキュリティ ポリシー\)** リストに、次のドメインを追加します。
   * *External Account Provisioning*
   * *Worker Data:Public Worker Reports* 
   * *Person Data:Work Contact Information* (連絡先データを Azure AD から Workday に書き戻す予定がある場合は必須)
   * *Workday Accounts* (ユーザー名と UPN を Azure AD から Workday に書き戻す予定がある場合は必須)

1. **[Integration Permissions]\(統合アクセス許可\)** の **[Domain Security Policies permitting Get access]\(Get アクセスを許可するドメイン セキュリティ ポリシー\)** リストに、次のドメインを追加します。
   * *Worker Data:Workers*
   * *Worker Data:All Positions*
   * *Worker Data:Current Staffing Information*
   * *Worker Data:Business Title on Worker Profile*
   * *Worker Data:Qualified Workers* (省略可 - プロビジョニング用に従業員の適性データを取得する場合は追加してください)
   * *Worker Data:Skills and Experience* (省略可 - プロビジョニング用に従業員のスキル データを取得する場合は追加してください)

1. 以上の手順が完了すると、次のようなアクセス許可画面が表示されます。
   >[!div class="mx-imgBorder"]
   >![すべてのドメイン セキュリティのアクセス許可](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. 次の画面で **[OK]** と **[Done]\(完了\)** をクリックして構成を完了します。 

### <a name="configuring-business-process-security-policy-permissions"></a>ビジネス プロセス セキュリティ ポリシーのアクセス許可の構成

この手順では、セキュリティ グループに、社員データについての "ビジネス プロセス セキュリティ" ポリシーのアクセス許可を付与します。 

> [!NOTE]
> この手順は、Workday Writeback アプリのコネクタを設定するためにのみ必要です。

**ビジネス プロセス セキュリティ ポリシーのアクセス許可を構成するには:**

1. 検索ボックスに「**Business Process Policy**」と入力し、 **[Edit Business Process Security Policy]\(ビジネス プロセス セキュリティ ポリシーの編集)** タスクのリンクをクリックします。  

   >[!div class="mx-imgBorder"]
   >![検索ボックスに "Business Process Policy" と表示され、[Edit Business Process Security Policy - Task]\(ビジネス プロセス セキュリティ ポリシーの編集 - タスク\) が選択されたスクリーンショット。](./media/workday-inbound-tutorial/wd_isu_12.png "ビジネス プロセスのセキュリティ ポリシー")  

2. **[Business Process Type]\(ビジネス プロセスの種類)** テキストボックスで、 *[Contact]* を検索し、 **[Work Contact Change]** ビジネス プロセスを選択して **[OK]** をクリックします。

   >[!div class="mx-imgBorder"]
   >![[Edit Business Process Security Policy]\(ビジネス プロセス セキュリティ ポリシーの編集\) ページと、[Business Process Type]\(ビジネス プロセスの種類\) メニューで [Work Contact Change] が選択されたことを示すスクリーンショット。](./media/workday-inbound-tutorial/wd_isu_13.png "ビジネス プロセスのセキュリティ ポリシー")  

3. **[Edit Business Process Security Policy]\(ビジネス プロセス セキュリティ ポリシーの編集)** ページで、 **[Change Work Contact Information (Web Service)]\(勤務先の連絡先情報の変更 (Web サービス))** セクションまで下へスクロールします。


4. 新しい統合システム セキュリティ グループを選択し、Web サービス要求を開始できるセキュリティ グループの一覧に追加します。 

   >[!div class="mx-imgBorder"]
   >![ビジネス プロセスのセキュリティ ポリシー](./media/workday-inbound-tutorial/wd_isu_15.png "ビジネス プロセスのセキュリティ ポリシー")  

5. **[Done]\(終了)** をクリックします。 

### <a name="activating-security-policy-changes"></a>セキュリティ ポリシーの変更のアクティブ化

**セキュリティ ポリシーの変更をアクティブにするには、次の手順に従います。**

1. 検索ボックスに「アクティブ化」と入力し、 **[保留中のセキュリティ ポリシーの変更をアクティブ化]** リンクをクリックします。
   >[!div class="mx-imgBorder"]
   >![アクティブ化](./media/workday-inbound-tutorial/wd_isu_16.png "アクティブ化")

1. 監査用のコメントを入力し、 **[OK]** をクリックして、[Activate Pending Security Policy Changes (保留中のセキュリティ ポリシーの変更のアクティブ化)] 作業を開始します。
1. **[確認]** チェック ボックスをオンにして、 **[OK]** をクリックし、次の画面で作業を完了します。

   >[!div class="mx-imgBorder"]
   >![保留中のセキュリティのアクティブ化](./media/workday-inbound-tutorial/wd_isu_18.png "保留中のセキュリティのアクティブ化")  

## <a name="provisioning-agent-installation-prerequisites"></a>プロビジョニング エージェントのインストールの前提条件

[プロビジョニング エージェントのインストールの前提条件](../cloud-sync/how-to-prerequisites.md)を確認してから、次のセクションに進んでください。 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Workday から Active Directory へのユーザー プロビジョニングの構成

このセクションでは、Workday から、統合の範囲内にある各 Active Directory ドメインへのユーザー アカウントのプロビジョニングの手順について説明します。

* [プロビジョニング コネクタ アプリを追加し、プロビジョニング エージェントをダウンロードする](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [オンプレミス プロビジョニング エージェントのインストールと構成](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Workday と Active Directory への接続を構成する](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [属性マッピングの構成](#part-4-configure-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>パート 1: プロビジョニング コネクタ アプリを追加し、プロビジョニング エージェントをダウンロードする

**Workday を Active Directory プロビジョニングに構成するには、**

1. <https://portal.azure.com> にアクセスします。

2. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **[Workday to Active Directory User Provisioning]\(Workday から Active Directory へのユーザー プロビジョニング\)** を探し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します。

7. **[プロビジョニング** **モード]** を **[自動]** に変更します。

8. 表示された情報バナーをクリックして、プロビジョニング エージェントをダウンロードします。 

   >[!div class="mx-imgBorder"]
   >![エージェントのダウンロード](./media/workday-inbound-tutorial/pa-download-agent.png "[エージェントのダウンロード] 画面")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>パート 2: オンプレミス プロビジョニング エージェントのインストールと構成

オンプレミスの Active Directory にプロビジョニングするには、目的の Active Directory ドメインへのネットワーク アクセスを備えたドメイン参加済みのサーバーに、プロビジョニング エージェントがインストールされている必要があります。

ダウンロードしたエージェント インストーラーをサーバー ホストに転送し、[**エージェントのインストール** に関するセクション](../cloud-sync/how-to-install.md)の手順に従ってエージェントの構成を完了します。

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>パート 3: プロビジョニング アプリで Workday と Active Directory への接続を構成する
この手順では、Azure portal で Workday および Active Directory との接続を確立します。 

1. Azure portal で、[パート 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) で作成した Active Directory User Provisioning アプリに戻ります。

1. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **Workday ユーザー名** – Workday 統合システム アカウントのユーザー名にテナント ドメイン名を追加して入力します。 このようになります。**username\@tenant_name**

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
     > <br> WWS API v30.0 以降を使用する場合は、プロビジョニング ジョブを有効にする前に、「[構成の管理](#managing-your-configuration)」セクションおよび [Workday 属性のリファレンス](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)を参照して、 **[属性マッピング] -> [詳細オプション] -> [Edit attribute list for Workday]\(Workday の属性リストの編集\)** の下にある **[XPATH API 式]** を更新してください。  

   * **Active Directory フォレスト** - エージェントに登録されている Active Directory ドメインの "名前"。 ドロップダウンを使用して、プロビジョニングのターゲット ドメインを選択します。 通常、この値は *contoso.com* のような文字列です。

   * **Active Directory コンテナー -** エージェントが既定でユーザー アカウントを作成する必要のあるコンテナー DN を入力します。
        例:*OU=Standard Users,OU=Users,DC=contoso,DC=test*

     > [!NOTE]
     > この設定が適用されるのは、属性のマッピングで *parentDistinguishedName* 属性が構成されていない場合のユーザー アカウント作成のみです。 この設定は、ユーザーの検索や更新の操作には使用されません。 ドメインのサブツリー全体が、検索操作の範囲内になります。

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。

     > [!NOTE]
     > Azure AD プロビジョニング サービスは、プロビジョニング ジョブが[検査](../app-provisioning/application-provisioning-quarantine-status.md)状態になった場合にメール通知を送信します。

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗する場合は、エージェントのセットアップで構成された Workday 資格情報と AD 資格情報が有効であることを再確認します。

     >[!div class="mx-imgBorder"]
     >![資格情報を入力した [プロビジョニング] ページを示すスクリーンショット。](./media/workday-inbound-tutorial/wd_1.png)

   * 資格情報が正常に保存されると、 **[マッピング]** セクションに既定のマッピング **[Synchronize Workday Workers to On Premises Active Directory]\(Workday の社員をオンプレミスの Active Directory に同期する\)** が表示されます

### <a name="part-4-configure-attribute-mappings"></a>パート 4:属性マッピングの構成

このセクションでは、ユーザー データが Workday から Active Directory に移動する方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、 **[Synchronize Workday Workers to On Premises Active Directory]\(Workday の社員をオンプレミスの Active Directory に同期する\)** をクリックします。

1. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、AD へのプロビジョニングの対象にする Workday のユーザー セットを選択できます。 既定のスコープは、"Workday のすべてのユーザー" です。 フィルターの例:

   * 例:1000000 から 2000000 (2000000 を除く) までの Worker ID を持つユーザーにスコープを設定

      * 属性:WorkerID

      * 演算子:REGEX Match

      * 値:(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 例:臨時社員ではなく、従業員のみ

      * 属性:EmployeeID

      * 演算子:IS NOT NULL

   > [!TIP]
   > 初めてプロビジョニング アプリを構成するときは、属性マッピングと式をテストして検証し、目的の結果が得られていることを確認する必要があります。 Microsoft は、Workday の少数のテスト ユーザーを使用してマッピングをテストするために、[オンデマンド プロビジョニング](../app-provisioning/provision-on-demand.md)と **[ソース オブジェクト スコープ]** の [スコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を使用することをお勧めします。 マッピングが機能していることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

   > [!CAUTION] 
   > プロビジョニング エンジンの既定の動作では、スコープ外に出るユーザーが無効化または削除されます。 これはご使用の Workday と AD の統合には望ましくない場合があります。 この既定の動作をオーバーライドするには、「[スコープ外に出るユーザー アカウントの削除をスキップする](../app-provisioning/skip-out-of-scope-deletions.md)」の記事を参照してください。

1. **[対象オブジェクトのアクション]** フィールドでは、Active Directory 上で実行されるアクションをグローバルにフィルター処理できます。 **作成** と **更新** が最も一般的です。

1. **[属性マッピング]** セクションでは、個別の Workday 属性を Active Directory の属性にマッピングする方法を定義できます。

1. 既存の属性マッピングをクリックして更新するか、または画面の下部にある **[新しいマッピングの追加]** をクリックして、新しいマッピングを追加します。 個々の属性マッピングは、次のプロパティをサポートしています。

      * **マッピングの種類**

         * **ダイレクト** - 変更なしで Workday 属性の値を AD 属性に書き込みます

         * **定数** - 静的な定数文字列の値を AD 属性に書き込みます

         * **式** – 1 つ以上の Workday 属性に基づいて、AD 属性にカスタム値を書き込むことができます。 [詳細については、式に関するこの記事を参照してください](../app-provisioning/functions-for-customizing-application-data.md)。

      * **ソース属性** - Workday のユーザー属性。 探している属性が存在しない場合は、「[Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)」を参照してください。

      * **既定値** – 省略可能。 ソース属性に空の値がある場合、マッピングではこの値が代わりに書き込まれます。
            最も一般的な構成では、これを空白のままにします。

      * **ターゲット属性** - Active Directory のユーザー属性。

      * **この属性を使用してオブジェクトを照合する** –このマッピングを使用して、Workday と Active Directory 間でユーザーを一意に識別するかどうかを示します。 この値は、通常、Workday の Worker ID フィールドで設定され、一般的に Active Directory の従業員 ID 属性のいずれかにマッピングされます。

      * **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

      * **このマッピングを適用する**

         * **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します

         * **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します

1. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。
   >[!div class="mx-imgBorder"]
   >![[保存] アクションが選択された [属性マッピング] ページを示すスクリーンショット。](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Workday と Active Directory との間の属性マッピングの例と、一般的に使用される式を次に示します

* *parentDistinguishedName* 属性にマップされる式を使用すると、1 つ以上の Workday ソース属性に基づいて異なる OU にユーザーをプロビジョニングできます。 この例では、住所の市区町村に基づいて、異なる OU にユーザーを配置しています。

* Active Directory の *userPrincipalName* 属性は、ターゲット AD ドメインに生成された値が存在するかどうかを確認し、一意である場合にのみ設定する重複排除関数 [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) を使用して生成されます。  

* [式の記述に関するドキュメントについては、こちらを参照してください](../app-provisioning/functions-for-customizing-application-data.md)。 このセクションでは、特殊文字を削除する方法の例についても紹介しています。

| WORKDAY 属性 | ACTIVE DIRECTORY 属性 |  ID 一致の有無 | 作成/更新 |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **はい** | 作成時のみ書き込まれる |
| **PreferredNameData**    |  cn    |   |   作成時のみ書き込まれる |
| **SelectUniqueValue( Join("\@", Join(".",  \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".",  Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | 作成時のみ書き込まれる 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         作成時のみ書き込まれる |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | 作成時 + 更新時 |
| **FirstName**   | givenName       |     |    作成時 + 更新時 |
| **LastName**   |   sn   |     |  作成時 + 更新時 |
| **PreferredNameData**  |  displayName |     |   作成時 + 更新時 |
| **Company**         | company   |     |  作成時 + 更新時 |
| **SupervisoryOrganization**  | department  |     |  作成時 + 更新時 |
| **ManagerReference**   | manager  |     |  作成時 + 更新時 |
| **BusinessTitle**   |  title     |     |  作成時 + 更新時 | 
| **AddressLineData**    |  streetAddress  |     |   作成時 + 更新時 |
| **Municipality**   |   l   |     | 作成時 + 更新時 |
| **CountryReferenceTwoLetter**      |   co |     |   作成時 + 更新時 |
| **CountryReferenceTwoLetter**    |  c  |     |         作成時 + 更新時 |
| **CountryRegionReference** |  st     |     | 作成時 + 更新時 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  作成時 + 更新時 |
| **PostalCode**  |   postalCode  |     | 作成時 + 更新時 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 作成時 + 更新時 |
| **Fax**      | facsimileTelephoneNumber     |     |    作成時 + 更新時 |
| **Mobile**  |    mobile       |     |       作成時 + 更新時 |
| **LocalReference** |  preferredLanguage  |     |  作成時 + 更新時 |                                               
| **Switch(\[Municipality\], "OU=Default Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  作成時 + 更新時 |

属性マッピングの構成が完了したら、[オンデマンド プロビジョニング](../app-provisioning/provision-on-demand.md)を使用して単一ユーザーのプロビジョニングをテストし、[ユーザー プロビジョニング サービスを有効にして起動](#enable-and-launch-user-provisioning)できるようになります。

## <a name="enable-and-launch-user-provisioning"></a>ユーザー プロビジョニングの有効化と起動

Workday プロビジョニング アプリの構成が完了し、[オンデマンド プロビジョニング](../app-provisioning/provision-on-demand.md)で単一ユーザーのプロビジョニングを検証したら、Azure portal でプロビジョニング サービスを有効にすることができます。

> [!TIP]
> 既定では、プロビジョニング サービスを有効にすると、スコープ内のすべてのユーザーに対してプロビジョニング操作が開始されます。 マッピングのエラーまたは Workday データの問題がある場合、プロビジョニング ジョブが失敗し、検疫状態になる可能性があります。 これを避けるために、ベスト プラクティスとして、すべてのユーザーの完全同期を開始する前に、 **[ソース オブジェクト スコープ]** フィルターを構成し、[オンデマンド プロビジョニング](../app-provisioning/provision-on-demand.md)を使用して少数のテスト ユーザーを対象に属性マッピングをテストすることをお勧めします。 マッピングが機能し、目的の結果が得られていることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **[プロビジョニング]** ブレードに移動し、 **[プロビジョニングの開始]** をクリックします。

1. この操作により初期同期が開始されます。これに要する時間は Workday テナントのユーザー数に応じて変わります。 進行状況バーをチェックして、同期サイクルの進行状況を追跡できます。 

1. 好きなときに、Azure Portal の **[監査ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 監査ログには、Workday から読み込まれたユーザーや、その後 Active Directory に追加または更新されたユーザーなど、プロビジョニング サービスによって実行された個々の同期イベントがすべて表示されます。 監査ログを確認してプロビジョニング エラーを修正する方法の手順については、トラブルシューティングに関するセクションを参照してください。

1. 最初の同期が完了すると、次に示すように、 **[プロビジョニング]** タブに監査概要レポートが書き込まれます。
   > [!div class="mx-imgBorder"]
   > ![プロビジョニングの進行状況バー](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

* **ソリューションの機能に関する質問**
  * [Workday の新規採用者を処理するときに、ソリューションは Active Directory の新規ユーザー アカウントのパスワードをどのように設定しますか。](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [ソリューションは、プロビジョニング操作の完了後にメール通知を送信することをサポートしていますか。](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [ソリューションでは、Azure AD クラウドまたはプロビジョニング エージェント レイヤーに Workday ユーザー プロファイルがキャッシュされますか。](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [ソリューションは、オンプレミスの AD グループをユーザーに割り当てることをサポートしていますか。](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Workday の従業員プロファイルのクエリと更新にこのソリューションが使用する Workday API はどれですか。](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [2 つの Azure AD テナントを持つ Workday HCM テナントは構成できますか。](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Workday と Azure AD の統合に関連した改善を提案したり、新しい機能を依頼したりするにはどうすればよいですか。](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **プロビジョニング エージェントに関する質問**
  * [プロビジョニング エージェントの一般提供 (GA) バージョンは何ですか。](#what-is-the-ga-version-of-the-provisioning-agent)
  * [プロビジョニング エージェントのバージョンを確認する方法を教えてください。](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Microsoft からプロビジョニング エージェントの更新プログラムは自動的にプッシュされますか。](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Azure AD Connect を実行しているのと同じサーバーにプロビジョニング エージェントをインストールできますか。](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [送信 HTTP 通信にプロキシ サーバーを使用するようにプロビジョニング エージェントを構成するにはどうすればよいですか。](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [プロビジョニング エージェントが Azure AD テナントと通信できること、ファイアウォールがエージェントに必要なポートをブロックしていないことを確認するにはどうすればよいですか。](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [プロビジョニング エージェントに関連付けられているドメインの登録を解除するにはどうすればよいですか。](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [プロビジョニング エージェントをアンインストールするにはどうすればよいですか。](#how-do-i-uninstall-the-provisioning-agent)

* **Workday から AD への属性マッピングと構成に関する質問**
  * [Workday プロビジョニング属性マッピングとスキーマの作業用コピーをバックアップまたはエクスポートするにはどうすればよいですか。](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Workday と Active Directory にカスタム属性があります。カスタム属性と連携するようにソリューションを構成するにはどうすればよいですか。](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [ユーザーの写真を Workday から Active Directory にプロビジョニングできますか。](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [パブリック使用に関するユーザーの同意に基づいて Workday の携帯電話番号を同期するにはどうすればよいですか。](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [ユーザーの部署/国/市区町村の属性に基づいて AD の表示名の書式を設定し、地域の差異を処理するにはどうすればよいですか。](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [SelectUniqueValue を使用して samAccountName 属性の一意の値を生成する方法を教えてください。](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [分音記号を使用する文字を削除し、通常の英語のアルファベットに変換するにはどうすればよいですか。](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>ソリューションの機能に関する質問

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Workday の新規採用者を処理するときに、ソリューションは Active Directory の新規ユーザー アカウントのパスワードをどのように設定しますか。

オンプレミス プロビジョニング エージェントで、新しい AD アカウントの作成要求を受け取ると、AD サーバーによって定義されたパスワードの複雑さの要件を満たすように設計された複雑なランダム パスワードが自動的に生成され、それがユーザー オブジェクトに設定されます。 このパスワードはどこにも記録されていません。

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>ソリューションは、プロビジョニング操作の完了後にメール通知を送信することをサポートしていますか。

いいえ。プロビジョニング操作の完了後にメール通知を送信することは、現在のリリースではサポートされていません。

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>ソリューションでは、Azure AD クラウドまたはプロビジョニング エージェント レイヤーに Workday ユーザープロファイルがキャッシュされますか。

いいえ。このソリューションではユーザー プロファイルのキャッシュが保持されません。 Azure AD プロビジョニング サービスは単なるデータ プロセッサとして機能し、Workday からデータを読み取り、ターゲット Active Directory または Azure AD に書き込みます。 ユーザーのプライバシーとデータ保持の詳細については、「[個人データの管理](#managing-personal-data)」セクションを参照してください。

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>ソリューションは、オンプレミスの AD グループをユーザーに割り当てることをサポートしていますか。

現在、この機能はサポートされていません。 推奨される回避策は、Microsoft Graph API エンドポイントに[監査ログ データ](/graph/api/resources/azure-ad-auditlog-overview)のクエリを実行し、それを使用してグループの割り当てなどのシナリオをトリガーする PowerShell スクリプトをデプロイすることです。 この PowerShell スクリプトは、タスク スケジューラにアタッチして、プロビジョニング エージェントを実行している同じボックスにデプロイできます。  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Workday の従業員プロファイルのクエリと更新にこのソリューションが使用する Workday API はどれですか。

現在、このソリューションは次の Workday API を使用しています。

* **[管理者の資格情報]** セクションで使用される **Workday Web Services API URL** 形式は、Get_Workers で使用される API バージョンを決定します
  * URL の形式が https://\#\#\#\#\.workday\.com/ccx/service/tenantName の場合は、API v21.1 が使用されます。 
  * URL の形式が https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources の場合は、API v21.1 が使用されます 
  * URL の形式が https://\#\#\#\#\.workday\.com/ccx/service/tenantName/Human\_Resources/v\#\#\.\# の場合は、指定した API のバージョンが使用されます。 (例: v34.0 が指定されている場合は、これが使用されます。)  

* Workday メール書き戻し機能は Change_Work_Contact_Information (v30.0) を使用します 
* Workday ユーザー名書き戻し機能は Update_Workday_Account (v31.2) を使用します 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>2 つの Azure AD テナントを持つ Workday HCM テナントは構成できますか。

はい。この構成はサポートされています。 このシナリオを構成する手順の概要を次に示します。

* プロビジョニング エージェント 1 をデプロイし、Azure AD テナント 1 に登録します。
* プロビジョニング エージェント 2 をデプロイし、Azure AD テナント 2 に登録します。
* 各プロビジョニング エージェントが管理する "子ドメイン" に基づいて、各エージェントとドメインを構成します。 1 つのエージェントで複数のドメインを処理できます。
* Azure portal で、各テナントの Workday to AD User Provisioning アプリを設定し、それぞれのドメインと共に構成します。

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Workday と Azure AD の統合に関連した改善を提案したり、新しい機能を依頼したりするにはどうすればよいですか。

今後のリリースや機能強化の方向性を決める上でお客様のフィードバックはとても貴重です。 どのようなフィードバックでもお待ちしております。[Azure AD のフィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)にアイデアや改善の提案をご投稿ください。 Workday の統合に固有のフィードバックについては、カテゴリ *[SaaS アプリケーション]* を選択し、キーワード「*Workday*」を使用して検索すると、Workday に関連する既存のフィードバックが見つかります。

> [!div class="mx-imgBorder"]
> ![UserVoice SaaS アプリ](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![UserVoice Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

新しいアイデアを提案するときは、他のユーザーが既に同様の機能を提案しているかどうかを確認してください。 その場合は、機能や機能強化のリクエストに賛成を投票することができます。 また、特定のユース ケースについてコメントを残してアイデアを支持していることを示し、その機能がお客様にとってどのように価値があるかを示すこともできます。

### <a name="provisioning-agent-questions"></a>プロビジョニング エージェントに関する質問

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>プロビジョニング エージェントの一般提供 (GA) バージョンは何ですか。

プロビジョニング エージェントの最新の GA バージョンについては、「[Azure AD Connect プロビジョニング エージェント:バージョンのリリース履歴](../app-provisioning/provisioning-agent-release-version-history.md)」を参照してください。  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>プロビジョニング エージェントのバージョンを確認する方法を教えてください。

* プロビジョニング エージェントがインストールされている Windows サーバーにサインインします。
* **[コントロール パネル]**  ->  **[プログラムのアンインストールまたは変更]** メニューに移動します
* エントリ **Microsoft Azure AD Connect プロビジョニング エージェント** に対応するバージョンを探します

  >[!div class="mx-imgBorder"]
  >![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft からプロビジョニング エージェントの更新プログラムは自動的にプッシュされますか。

はい。Windows サービス **Microsoft Azure AD Connect Agent Updater** が稼働している場合、Windows ではプロビジョニング エージェントが自動的に更新されます。

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Azure AD Connect を実行しているのと同じサーバーにプロビジョニング エージェントをインストールできますか。

はい、Azure AD Connect を実行しているのと同じサーバーにプロビジョニング エージェントをインストールすることができます。

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>構成時に、プロビジョニング エージェントでは Azure AD 管理者の資格情報が求められます。 エージェントでは、資格情報がサーバー上のローカルに保存されますか。

構成時に、プロビジョニング エージェントでは、Azure AD テナントに接続するときにのみ、Azure AD 管理者の資格情報が求められます。 エージェントでは、資格情報がサーバー上のローカルに保存されません。 ただし、ローカルの Windows パスワード コンテナーにある *オンプレミスの Active Directory ドメイン* への接続に使用される資格情報は保持されます。

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>送信 HTTP 通信にプロキシ サーバーを使用するようにプロビジョニング エージェントを構成するにはどうすればよいですか。

プロビジョニング エージェントは送信プロキシの使用をサポートしています。 構成するには、エージェントの構成ファイル **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** を編集します。ファイルの末尾近くにある `</configuration>` の終了タグの直前に次の行を追加します。
変数 [proxy-server] と [proxy-port] をお客様のプロキシ サーバー名とポート値に置き換えてください。

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>プロビジョニング エージェントが Azure AD テナントと通信できること、ファイアウォールがエージェントに必要なポートをブロックしていないことを確認するにはどうすればよいですか。

[必要なポート](../manage-apps/application-proxy-add-on-premises-application.md#open-ports)がすべて開いているかどうかを確認することもできます。

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>1 つのプロビジョニング エージェントを複数の AD ドメインをプロビジョニングするように構成できますか。

はい。1 つのプロビジョニング エージェントは、そのエージェントから各ドメイン コントローラーへの通信経路がある限り、複数の AD ドメインを処理するように構成できます。 高可用性を確保し、フェールオーバーをサポートするために、同じ AD ドメインのセットにサービスを提供する 3 つのプロビジョニング エージェントのグループを設定することをお勧めします。

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>プロビジョニング エージェントに関連付けられているドメインの登録を解除するにはどうすればよいですか。

* Azure portal から、Azure AD テナントの "*テナント ID*" を取得します。
* プロビジョニング エージェントを実行している Windows サーバーにサインインします。
* Windows 管理者として PowerShell を開きます。
* 登録スクリプトを含むディレクトリに移動し、\[tenant ID\] パラメーターをお客様のテナント ID の値に置き換えて、次のコマンドを実行します。

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* 表示されるエージェントの一覧から *resourceName* がお客様の AD ドメイン名と同じリソースから `id` フィールドの値をコピーします。
* このコマンドに ID 値を貼り付けて、PowerShell でコマンドを実行します。

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Agent configuration (エージェントの構成) ウィザードを再実行します。
* 以前にこのドメインに割り当てられていた他のエージェントがある場合は、すべて再構成する必要があります。

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>プロビジョニング エージェントをアンインストールするにはどうすればよいですか。

* プロビジョニング エージェントがインストールされている Windows サーバーにサインインします。
* **[コントロール パネル]**  ->  **[プログラムのアンインストールまたは変更]** メニューに移動します
* 次のプログラムをアンインストールします。
  * Microsoft Azure AD Connect プロビジョニング エージェント
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect Provisioning Agent Package

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Workday から AD への属性マッピングと構成に関する質問

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Workday プロビジョニング属性マッピングとスキーマの作業用コピーをバックアップまたはエクスポートするにはどうすればよいですか。

Microsoft Graph API を使用して、Workday のユーザー プロビジョニング構成をエクスポートできます。 詳細な手順については、[Workday のユーザー プロビジョニング属性マッピングの構成のエクスポートとインポート](#exporting-and-importing-your-configuration)に関するセクションを参照してください。

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Workday と Active Directory にカスタム属性があります。 カスタム属性と連携するようにソリューションを構成するにはどうすればよいですか。

このソリューションは、カスタムの Workday 属性と Active Directory 属性をサポートしています。 カスタム属性をマッピング スキーマに追加するには、 **[属性マッピング]** ブレードを開き、下にスクロールして **[詳細オプションの表示]** を展開します。 

![属性リストの編集](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

カスタムの Workday 属性を追加するには、オプション *[Edit attribute list for Workday]\(Workday の属性リストの編集\)* オプションを選択し、カスタムの AD 属性を追加するには、 *[Edit attribute list for On Premises Active Directory]\(オンプレミスの Active Directory の属性リストの編集\)* オプションを選択します。

関連項目:

* [Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Workday の変更に基づいて AD 内の属性のみを更新し、新しい AD アカウントを作成しないようにソリューションを構成するにはどうすればよいですか。

この構成は、次のように **[属性マッピング]** ブレードの **[対象オブジェクトのアクション]** を設定することで実現できます。

![更新アクション](./media/workday-inbound-tutorial/wd_target_update_only.png)

Workday から AD 方向の更新操作のみを実行するには、[更新] チェックボックスをオンにします。 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>ユーザーの写真を Workday から Active Directory にプロビジョニングできますか。

現在、このソリューションでは Active Directory 内の *thumbnailPhoto* や *jpegPhoto* などのバイナリ属性を設定することはサポートされていません。

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>パブリック使用に関するユーザーの同意に基づいて Workday の携帯電話番号を同期するにはどうすればよいですか。

* Workday Provisioning アプリの [プロビジョニング] ブレードに移動します。
* [属性マッピング] をクリックします 
* **[マッピング]** で **[Synchronize Workday Workers to On Premises Active Directory]\(Workday の従業員をオンプレミスの Active Directory と同期\)** または **[Synchronize Workday Workers to Azure AD]\(Workday の従業員を Azure AD と同期\)** を選択します。
* [属性マッピング] ページで、下にスクロールして [詳細オプションの表示] チェックボックスをオンにします。  **[Edit attribute list for Workday]\(Workday の属性リストの編集\)** をクリックします
* 開いたブレードで、 **[API 式]** を編集するために "Mobile" 属性を探してその行をクリックします ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* **[API 式]** を次の新しい式に置き換えます。これで、Workday で [Public Usage Flag]\(パブリック使用フラグ\) が "True" に設定されている場合にのみ、勤務先の携帯電話番号が取得されます。

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* 属性リストを保存します。
* 属性マッピングを保存します。
* 現在の状態を消去して、完全な同期を再開します。

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>ユーザーの部署/国/市区町村の属性に基づいて AD の表示名の書式を設定し、地域の差異を処理するにはどうすればよいですか。

ユーザーの部署と国/地域に関する情報も提供されるように AD で *displayName* 属性を構成することが一般的な要件です。 たとえば、John Smith が米国のマーケティング部署に勤務している場合、彼の *displayName* を *Smith, John (Marketing-US)* と表示することができます。

会社、部署、市区町村、国/地域などの属性を含むように *CN* または *displayName* を構成するためのこのような要件を処理する方法は次のとおりです。

* 各 Workday 属性は、基になる XPATH API 式を使用して取得されます。これは、 **[属性マッピング] -> [Advanced Section]\(詳細セクション\) -> [Edit attribute list for Workday]\(Workday の属性リストの編集\)** で構成できます。 Workday の *PreferredFirstName*、*PreferredLastName*、*Company*、および *SupervisoryOrganization* 属性の既定の XPATH API 式は次のとおりです。

     | Workday 属性 | API XPATH 式 |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | [会社] | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |

   上記の API 式がお客様の Workday テナント構成で有効であることをお客様の Workday チームに確認してください。 必要に応じて、「[Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)」セクションの説明に従って編集できます。

* 同様に、Workday 内に存在する国/地域情報を取得するには、*wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference* という XPATH を使用します

     Workday 属性リスト セクションで使用できる国/地域関連の属性が 5 つあります。

     | Workday 属性 | API XPATH 式 |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  上の API 式がお客様の Workday テナント構成で有効であることをお客様の Workday チームに確認してください。 必要に応じて、「[Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)」セクションの説明に従って編集できます。

* 正しい属性マッピング式を構築するには、どの Workday 属性が "正式に" ユーザーの姓、名、国/地域、部署を表すかを特定します。 たとえば、属性がそれぞれ *PreferredFirstName*、*PreferredLastName*、*CountryReferenceTwoLetter*、*SupervisoryOrganization* であるとします。 これを使用して、*Smith, John (Marketing-US)* のような表示名を取得するには、次のように AD の *displayName* 属性の式を構築します。

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    正しい式が完成したら、[属性マッピング] テーブルを編集して、次のように *displayName* 属性マッピングを変更します。 ![DisplayName のマッピング](./media/workday-inbound-tutorial/wd_displayname_map.png)

* 上の例を拡張して、Workday に由来する市区町村名を短縮値に変換し、それを使用して *Smith、John (CHI)* や *Doe, Jane (NYC)* などの表示名を作成するとします。この結果は、決定変数として Workday の *Municipality* 属性を含む Switch 式を使用して実現できます。

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    関連項目:
  * [Switch 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Join 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Append 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>SelectUniqueValue を使用して samAccountName 属性の一意の値を生成する方法を教えてください。

Workday の *FirstName* 属性と *LastName* 属性の組み合わせを使用して、*samAccountName* 属性に固有の値を生成するとします。 以下の式を基にして始めることができます。

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

上の式は次のように機能します。ユーザーが John Smith の場合は、まず JSmith の生成が試行され、JSmith が既に存在する場合は JoSmith が生成され、それが存在する場合は JohSmith が生成されます。 また、この式では、*samAccountName* に関連付けられている長さの制限と特殊文字の制限を満たす値が確実に生成されます。

関連項目:

* [Mid 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Replace 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue 関数の構文](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>分音記号を使用する文字を削除し、通常の英語のアルファベットに変換するにはどうすればよいですか。

ユーザーのメール アドレスまたは CN 値を作成するときに、ユーザーの姓と名の特殊文字を削除するには、関数 [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) を使用します。

## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

このセクションでは、Azure AD 監査ログと Windows Server イベント ビューアー ログを使用して、Workday 統合に関するプロビジョニングの問題を解決する方法について、具体的なガイダンスを提供します。 一般的なトラブルシューティングの手順と概念 (「[チュートリアル:自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」で説明されています) に基づいて構築されています

このセクションでは、トラブルシューティングの次の側面について説明しています。

* [イベント ビューアー ログを出力するようにプロビジョニング エージェントを構成する](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [エージェントのトラブルシューティングのための Windows イベント ビューアーの設定](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [サービスのトラブルシューティングのための Azure portal 監査ログの設定](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [AD ユーザー アカウントの作成操作のログの概要](#understanding-logs-for-ad-user-account-create-operations)
* [マネージャーの更新操作のログの概要](#understanding-logs-for-manager-update-operations)
* [よく発生するエラーの解決](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>イベント ビューアー ログを出力するようにプロビジョニング エージェントを構成する
1. プロビジョニング エージェントがデプロイされている Windows Server マシンにサインインします。
1. **Microsoft Azure AD Connect プロビジョニング エージェント** サービスを停止します。
1. 元の構成ファイル *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* のコピーを作成します。
1. 既存の `<system.diagnostics>` セクションを以下の内容に置き換えます。 
   * リスナー構成 **etw** が EventViewer ログにメッセージを出力します。
   * リスナー構成 **textWriterListener** が *ProvAgentTrace.log* ファイルにトレース メッセージを送信します。 高度なトラブルシューティングを行う場合のみ、textWriterListener に関連した行をコメント解除してください。 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. **Microsoft Azure AD Connect プロビジョニング エージェント** サービスを開始します。

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>エージェントのトラブルシューティングのための Windows イベント ビューアーの設定

1. プロビジョニング エージェントがデプロイされている Windows Server マシンにサインインします。
1. **Windows Server イベント ビューアー** デスクトップ アプリを開きます。
1. **[Windows ログ] > [アプリケーション]** の順に選択します。
1. **[現在のログをフィルター]** オプションを使用して、ソース **Azure AD Connect Provisioning Agent** 以下に記録されているすべてのイベントを表示し、次のようにフィルター "-5" を指定してイベント ID "5" のイベントを除外します。
   > [!NOTE]
   > イベント ID 5 でキャプチャされるのは Azure AD クラウド サービスに対するエージェントのブートストラップ メッセージであるため、ログ ファイルを分析する間はフィルターで除外します。 

   ![Windows イベント ビューアー](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. **[OK]** をクリックし、結果ビューを **[日付と時刻]** 列で並べ替えます。

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>サービスのトラブルシューティングのための Azure portal 監査ログの設定

1. [Azure portal](https://portal.azure.com) を起動し、Workday プロビジョニング アプリケーションの **[監査ログ]** セクションに移動します。
1. [監査ログ] ページの **[列]** ボタンを使用して、ビューに次の列 ([日付]、[アクティビティ]、[状態]、[状態の理由]) のみを表示します。 この構成にすることで、トラブルシューティングに関連するデータのみに確実に集中できます。

   ![監査ログの列](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. ビューをフィルター処理するには、 **[ターゲット]** および **[日付範囲]** クエリ パラメーターを使用します。 
   * **[ターゲット]** クエリ パラメーターを Workday worker オブジェクトの "Worker ID" または "Employee ID" に設定します。
   * **[日付範囲]** を、プロビジョニングに関するエラーまたは問題について調査する適切な期間に設定します。

   ![監査ログのフィルター](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>AD ユーザー アカウントの作成操作のログの概要

Workday の新規採用者 (たとえば、従業員 ID が *21023*) が検出されると、Azure AD プロビジョニング サービスではその従業員の新しい AD ユーザー アカウントの作成が試行され、そのプロセスで次のように 4 つの監査ログ レコードが作成されます。

  [![監査ログ作成操作](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

監査ログ レコードをクリックすると、 **[アクティビティの詳細]** ページが開きます。 **[アクティビティの詳細]** ページにログ レコードの種類ごとに表示される内容は次のとおりです。

* **Workday インポート** レコード:このログ レコードには、Workday から取り込んだ従業員情報が表示されます。 ログ レコードの "*追加の詳細*" セクションの情報を使用して、Workday からのデータの取り込みに関する問題を解決します。 レコードの例を、各フィールドの解釈方法についてのポインターと共に次に示します。

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **AD インポート** レコード:このログ レコードには、AD から取り込んだアカウントの情報が表示されます。 最初のユーザー作成時には AD アカウントがないため、 *[Activity Status Reason]\(アクティビティの状態の理由\)* には Active Directory にその [Matching ID ]\(照合 ID\) 属性値を持つアカウントが見つからなかったことが表示されます。 ログ レコードの "*追加の詳細*" セクションの情報を使用して、Workday からのデータの取り込みに関する問題を解決します。 レコードの例を、各フィールドの解釈方法についてのポインターと共に次に示します。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  この AD インポート操作に対応するプロビジョニング エージェントのログ レコードを見つけるには、Windows イベント ビューアーのログを開き、 **[検索]** メニュー オプションを使用して、[Matching ID ]\(照合 ID\)/[結合プロパティ] 属性値 (この場合 *21023*) を含むログ エントリを検索します。

  ![Find](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  "*イベント ID = 9*" のエントリを探します。これにより、AD アカウントを取得するためにエージェントで使用できる LDAP 検索フィルターが提供されます。 これが一意のユーザー エントリを取得するための適切な検索フィルターであるかどうかを確認できます。

  ![LDAP 検索](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  その直後に続く "*イベント ID = 2*" のレコードでは、返される結果がある場合は検索操作の結果がキャプチャされます。

  ![LDAP の結果](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* **同期規則アクション** レコード:このログ レコードには、受信した Workday イベントを処理するために実行されるプロビジョニング アクションと共に、属性マッピング規則の結果と構成済みのスコープ フィルターが表示されます。 ログ レコードの "*追加の詳細*" セクションの情報を使用して、同期アクションに関する問題を解決します。 レコードの例を、各フィールドの解釈方法についてのポインターと共に次に示します。

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  属性マッピング式に問題がある場合、または受信 Workday データに問題がある場合 (たとえば、必須の属性値が空または null)、この段階で、失敗の詳細を示す ErrorCode を使用して失敗を確認します。

* **AD エクスポート** レコード:このログ レコードには、AD アカウントの作成操作の結果と共に、プロセスで設定された属性値が表示されます。 ログ レコードの "*追加の詳細*" セクションの情報を使用して、アカウントの作成操作に関する問題を解決します。 レコードの例を、各フィールドの解釈方法についてのポインターと共に次に示します。 [追加の詳細] セクションで、"EventName" は "EntryExportAdd" に設定され、"JoiningProperty" は [Matching ID]\(照合 ID\) 属性の値に設定され、"SourceAnchor" はそのレコードに関連付けられた WorkdayID (WID) に設定され、"TargetAnchor" は新しく作成されたユーザーの AD の "ObjectGuid" 属性の値に設定されます。 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  この AD エクスポート操作に対応するプロビジョニング エージェントのログ レコードを見つけるには、Windows イベント ビューアーのログを開き、 **[検索]** メニュー オプションを使用して、[Matching ID ]\(照合 ID\)/[結合プロパティ] 属性値 (この場合 *21023*) を含むログ エントリを検索します。  

  "*イベント ID = 2*" のエクスポート操作のタイムスタンプに対応する HTTP POST レコードを探します。 このレコードには、プロビジョニング サービスからプロビジョニング エージェントに送信された属性値が含まれます。

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="'Provisioning Agent' ログで 'HTTP POST' レコードを示すスクリーンショット。" lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  上記のイベントの直後に、AD アカウント作成操作の応答をキャプチャする別のイベントがあるはずです。 このイベントから、AD で作成された新しい objectGuid が返され、プロビジョニング サービスの TargetAnchor 属性として設定されます。

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="AD で作成された objectGuid が強調表示されている 'Provisioning Agent' ログを示すスクリーンショット。" lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>マネージャーの更新操作のログの概要

manager 属性は AD の参照属性です。 プロビジョニング サービスでは、ユーザー作成操作の一環として manager 属性は設定されません。 そうではなく、manager 属性は、ユーザーの AD アカウントが作成された後、"*更新*" 操作の一環として設定されます。 上の例を拡張して、従業員 ID "21451" の新規採用者が Workday でアクティブ化された、新規採用者のマネージャー (*21023*) が既に AD アカウントを持っているとします。 このシナリオでは、ユーザー 21451 の監査ログを検索すると、5 つのエントリが表示されます。

  [![マネージャーの更新](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

最初の 4 つのレコードは、ユーザー作成操作の一部として調べたものと似ています。 5 つ目のレコードは、manager 属性の更新に関連付けられたエクスポートです。 ログ レコードには、AD アカウント マネージャーの更新操作の結果が表示されます。これは、マネージャーの *objectGuid* 属性を使用して実行されます。

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>よく発生するエラーの解決

このセクションでは、Workday ユーザーのプロビジョニングでよく見られるエラーとその解決方法について説明します。 エラーは次のように分類されます。

* [プロビジョニング エージェントのエラー](#provisioning-agent-errors)
* [接続エラー](#connectivity-errors)
* [AD ユーザー アカウントの作成エラー](#ad-user-account-creation-errors)
* [AD ユーザー アカウントの更新エラー](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>プロビジョニング エージェントのエラー

|#|エラーのシナリオ |考えられる原因|推奨される解決方法|
|--|---|---|---|
|1.| 次のエラー メッセージを伴うプロビジョニング エージェントのインストール時のエラー:"*Service 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) failed to start.Verify that you have sufficient privileges to start the system. (サービス 'Microsoft Azure AD Connect Provisioning Agent' (AADConnectProvisioningAgent) を開始できませんでした。システムを起動するために十分な特権を持っていることを確認してください。)* " | 通常、このエラーはプロビジョニング エージェントをドメイン コントローラーにインストールしようとして、グループ ポリシーによってサービスが開始されない場合に発生します。  また、以前のバージョンのエージェントが実行されていて、新規インストールを開始する前にアンインストールが完了していない場合にも表示されます。| DC 以外のサーバーにプロビジョニング エージェントをインストールします。 新しいエージェントをインストールする前に、必ず以前のバージョンのエージェントをアンインストールします。|
|2.| Windows サービス 'Microsoft Azure AD Connect Provisioning Agent' が "*開始中*" の状態で、"*実行中*" の状態に切り替わりません。 | インストールの一環で、エージェント ウィザードによってサーバー上にローカル アカウント (**NT サービス\\AADConnectProvisioningAgent**) が作成されます。これは、サービスの開始に使用されるログオン アカウントです。 Windows サーバー上のセキュリティ ポリシーにより、ローカル アカウントでサービスを実行できない場合は、このエラーが発生します。 | "*サービス*" コンソールを開きます。 Windows サービスの 'Microsoft Azure AD Connect Provisioning Agent' を右クリックし、ログオン タブでサービスを実行するドメイン管理者のアカウントを指定します。 サービスを再起動します。 |
|3.| "*Active Directory の接続*" 手順で AD ドメインを使用してプロビジョニング エージェントを構成するときに、ウィザードによる AD スキーマの読み込み時間が長くかかり、最終的にタイムアウトします。 | 通常、このエラーは、ファイアウォールの問題のためにウィザードから AD ドメイン コントローラー サーバーに接続できない場合に表示されます。 | "*Active Directory の接続*" ウィザード画面で、AD ドメインの資格情報を入力するときに、"*ドメイン コントローラーの優先順位の選択*" というオプションがあります。 このオプションは、エージェント サーバーと同じサイト内にあるドメイン コントローラーを選択し、通信をブロックするファイアウォール規則がないようにするために使用します。 |

#### <a name="connectivity-errors"></a>接続エラー

プロビジョニング サービスが Workday または Active Directory に接続できない場合は、プロビジョニングが検疫状態になる可能性があります。 次の表を使用して、接続の問題を解決します。

|#|エラーのシナリオ |考えられる原因|推奨される解決方法|
|--|---|---|---|
|1.| **[テスト接続]** をクリックすると、次のエラー メッセージが表示されます。"*There was an error connecting to Active Directory.Please ensure that the on-premises Provisioning Agent is running and it is configured with the correct Active Directory domain. (Active Directory への接続でエラーが発生しました。オンプレミスのプロビジョニング エージェントが実行されていて、適切な Active Directory ドメインが構成されていることを確認してください。)* " | 通常、このエラーは、プロビジョニング エージェントが実行されていない場合、または Azure AD とプロビジョニング エージェント間の通信をブロックするファイアウォールがある場合に発生します。 また、ドメインがエージェント ウィザードで構成されていない場合にもこのエラーが表示されることがあります。 | Windows サーバーで "*サービス*" コンソールを開き、エージェントが実行されていることを確認してます。 プロビジョニング エージェント ウィザードを開き、正しいドメインがエージェントに登録されていることを確認します。  |
|2.| プロビジョニング ジョブが週末 (金曜から土曜) にかけて検疫状態になり、同期にエラーがあるというメール通知を受け取ります。 | このエラーの一般的な原因の 1 つは、スケジュールされている Workday のダウンタイムです。 Workday の実装テナントを使用している場合は、Workday が週末にかけて (通常は金曜日の夜から土曜日の朝まで) 実装テナントのダウンタイムがスケジュールされており、その期間中は Workday に接続できないため、Workday プロビジョニング アプリは検疫状態になる可能性があります。 Workday 実装テナントがオンラインに戻ると、通常の状態に戻ります。 ごくまれに、テナントの更新により統合システム ユーザーのパスワードが変更された場合、またはアカウントがロックまたは期限切れの状態にある場合にも、このエラーが表示されることがあります。 | Workday 管理者または統合パートナーに連絡して、ダウンタイム期間中に Workday がアラート メッセージを無視するようにダウンタイムをスケジュールし、Workday インスタンスがオンラインに戻ったら可用性を確認します。  |

#### <a name="ad-user-account-creation-errors"></a>AD ユーザー アカウントの作成エラー

|#|エラーのシナリオ |考えられる原因|推奨される解決方法|
|--|---|---|---|
|1.| 次のメッセージを伴う監査ログのエクスポート操作の失敗。"*Error:OperationsError-SvcErr:An operation error occurred.No superior reference has been configured for the directory service.The directory service is therefore unable to issue referrals to objects outside this forest. (エラー: OperationsError-SvcErr: 操作エラーが発生ししました。このディレクトリ サービスの上位参照は構成されていません。そのため、ディレクトリ サービスはこのフォレストの外側にあるオブジェクトへの参照を発行できません。)* " | 通常、このエラーは、"*Active Directory コンテナー*" OU が正しく設定されていない場合、または *parentDistinguishedName* に使用される式のマッピングに問題がある場合に発生します。 | "*Active Directory コンテナー*" OU パラメーターに入力ミスがないか確認します。 属性マッピングで *parentDistinguishedName* を使用している場合は、常に AD ドメイン内の既知のコンテナーに評価されるようにしてください。 生成された値を確認するには、監査ログの *Export* イベントを確認します。 |
|2.| 次のエラー コードを伴う監査ログのエクスポート操作の失敗。"*SystemForCrossDomainIdentityManagementBadResponse* and message *Error:ConstraintViolation-AtrErr:A value in the request is invalid.A value for the attribute was not in the acceptable range of values. \nError Details:CONSTRAINT_ATT_TYPE - company (SystemForCrossDomainIdentityManagementBadResponse エラー: ConstraintViolation-AtrErr: 要求の値は無効です。属性の値は値の許容範囲に含まれていません。\nエラーの詳細: CONSTRAINT_ATT_TYPE - company)* " | このエラーは *company* 属性に固有のものですが、*CN* のような他の属性についてもこのエラーが発生する可能性があります。 このエラーは、AD で適用されたスキーマ制約が原因で発生します。 既定では、AD の *company* や *CN* のような属性の上限は 64 文字です。 Workday に由来する値が 64 文字を超える場合は、このエラー メッセージが表示されます。 | 監査ログで *Export* イベントを確認して、エラー メッセージで報告されている属性の値を確認します。 [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) 関数を使用して Workday に由来する値を切り捨てるか、同様の長さの制約がない AD 属性にマッピングを変更することを検討してください。  |

#### <a name="ad-user-account-update-errors"></a>AD ユーザー アカウントの更新エラー

AD ユーザー アカウントの更新プロセス中に、プロビジョニング サービスでは Workday と AD の両方から情報が読み取られ、属性マッピング規則を実行して、変更を反映する必要があるかどうかを判断します。 それに応じて更新イベントがトリガーされます。 このような手順のいずれかで障害が発生した場合は、監査ログに記録されます。 次の表を使用して、一般的な更新エラーを解決します。

|#|エラーのシナリオ |考えられる原因|推奨される解決方法|
|--|---|---|---|
|1.| 同期ログのメッセージ "*EventName = EntrySynchronizationError" と "ErrorCode = EndpointUnavailable*" で監査ログ アクションが失敗しました。 | このエラーは、オンプレミスのプロビジョニング エージェントで処理エラーが発生したために、プロビジョニング サービスで Active Directory からユーザー プロファイル データを取得できない場合に発生します。 | プロビジョニング エージェントのイベント ビューアー ログで、読み取り操作に関する問題を示すエラー イベントを確認します (イベント ID 2 でフィルター)。 |
|2.| AD の特定のユーザーについて、AD の manager 属性が更新されません。 | 最も可能性が高いこのエラーの原因は、スコープ規則を使用していて、ユーザーのマネージャーがスコープに含まれていない場合です。 マネージャーの照合 ID 属性 (例: EmployeeID) がターゲット AD ドメインに見つからないか、正しい値に設定されていない場合も、この問題が発生する可能性があります。 | スコープ フィルターを確認し、スコープ内にマネージャー ユーザーを追加します。 AD でマネージャーのプロファイルを調べ、照合 ID 属性の値があることを確認します。 |

## <a name="managing-your-configuration"></a>構成の確認

このセクションでは、Workday 主導のユーザー プロビジョニング構成をさらに拡張、カスタマイズ、および管理する方法について説明します。 次のトピックについて説明します。

* [Workday のユーザー属性リストをカスタマイズする](#customizing-the-list-of-workday-user-attributes)  
* [構成のエクスポートとインポート](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Workday のユーザー属性リストをカスタマイズする

Active Directory と Azure AD の Workday プロビジョニング アプリにはどちらも、Workday のユーザー属性の選択元となる既定のリストが含まれています。 ただしこれらのリストは、必要な情報をすべてカバーしているわけではありません。 Workday は、想定されるさまざまなユーザー属性を数多くサポートしていますが、それらのユーザー属性には、標準の属性と特定の Workday テナントに固有の属性とがあります。

Azure AD プロビジョニング サービスは、このリスト (Workday 属性) をカスタマイズできるようになっており、Human Resources API の [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 操作に公開されている属性であれば、どのような属性でも含めることができます。

この変更のためには、使用する属性の XPath 式を [Workday Studio](https://community.workday.com/studio-download) を使って抽出し、Azure portal の高度な属性エディターを使ってそれらをプロビジョニング構成に追加する必要があります。

**Workday のユーザー属性に使用する XPath 式を取得するには**

1. [Workday Studio](https://community.workday.com/studio-download) をダウンロードしてインストールします。 インストーラーにアクセスするには、Workday コミュニティ アカウントが必要です。

2. [Workday Web Services ディレクトリ](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)から使用する予定の、WWS API バージョンに固有の Workday **Human_Resources** WSDL ファイルをダウンロードします

3. Workday Studio を起動します。

4. コマンド バーから **[Workday] > [Test Web Service in Tester]\(Tester で Web サービスをテストする\)** オプションを選択します。

5. **[External]\(外部\)** を選択し、手順 2. でダウンロードした Human_Resources WSDL ファイルを選択します。

    ![Workday Studio で開いている "Human_Resources" ファイルを示すスクリーンショット。](./media/workday-inbound-tutorial/wdstudio1.png)

6. **[Location]\(場所\)** フィールドを `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` に設定します。ただし、"IMPL-CC" の部分は実際のインスタンス タイプに、"TENANT" の部分は実際のテナント名に置き換えてください。

7. **[Operation]\(操作\)** を **[Get_Workers]** に設定します。

8.    [Request/Response]\(要求/応答\) ウィンドウの下に小さく表示された **[configure]\(構成\)** リンクをクリックして、Workday の資格情報を設定します。 **[Authentication]\(認証\)** チェック ボックスをオンにし、Workday 統合システム アカウントのユーザー名とパスワードを入力します。 ユーザー名は必ず name\@tenant の形式で指定し、 **[WS-Security UsernameToken]** オプションはオンのままにしてください。
   ![[Username]\(ユーザー名\) と [Password]\(パスワード\) が入力され、[WS-Security Username Token] が選択された [Security]\(セキュリティ\) タブを示すスクリーンショット。](./media/workday-inbound-tutorial/wdstudio2.png)

9. **[OK]** を選択します。

10. **[Request]\(要求\)** ウィンドウに以下の XML を貼り付けます。 **Employee_ID** には、ご使用の Workday テナントに存在する実際のユーザーの従業員 ID を設定してください。 **wd: version** には、使用する予定の WWS のバージョンを設定してください。 抽出対象となる属性が設定されているユーザーを選択します。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. **[Send Request]\(要求を送信する\)** (緑色の矢印) をクリックしてコマンドを実行します。 成功した場合、その応答が **[Response]\(応答\)** ウィンドウに表示されます。 エラーではなく、入力したユーザー ID のデータが応答に含まれていることを確認します。

12. 成功した場合は、 **[Response]\(応答\)** ウィンドウから XML をコピーし、XML ファイルとして保存します。

13. Workday Studio のコマンド バーで **[File]\(ファイル\) > [Open File...]\(ファイルを開く...\)** を選択し、保存した XML ファイルを開きます。 この操作で、Workday Studio の XML エディターにファイルが開きます。

    !["Workday Studio X M L エディタ" で X M L ファイルが開いたスクリーンショット。](./media/workday-inbound-tutorial/wdstudio3.png)

14. ファイル ツリーで、 **[/env:Envelope] > [env:Body] > [wd:Get_Workers_Response] > [wd:Response_Data] > [wd:Worker]** の順に移動して、該当するユーザーのデータを特定します。

15. **[wd:Worker]** から、追加する属性を探して選択します。

16. 選択した属性の XPath 式を **[Document Path]\(ドキュメント パス\)** フィールドからコピーします。

17. コピーした式から **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** プレフィックスを削除します。

18. コピーした式の最後の項目がノード (例: "/wd:Birth_Date") である場合、式の末尾に **/text()** を付加します。 最後の項目が属性 (例: "/@wd: type") である場合、この手順は不要です。

19. 最終的には、`wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` のようになっている必要があります。 この値は Azure portal にコピーすることになります。

**プロビジョニング構成に Workday のカスタム ユーザー属性を追加するには**

1. このチュートリアルで前述した説明に従い、[Azure Portal](https://portal.azure.com) を起動して、対象の Workday プロビジョニング アプリケーションの [プロビジョニング] セクションに移動します。

2. **[プロビジョニングの状態]** を **[オフ]** に設定し、 **[保存]** を選択します。 この手順によって、準備が整った段階でのみ、変更を反映することができます。

3. **[マッピング]** で **[Synchronize Workday Workers to On Premises Active Directory]\(Workday の従業員をオンプレミスの Active Directory と同期\)** または **[Synchronize Workday Workers to Azure AD]\(Workday の従業員を Azure AD と同期\)** を選択します。

4. 次の画面の一番下までスクロールして **[詳細オプションを表示する]** を選択します。

5. **[Edit attribute list for Workday]\(Workday の属性リストの編集\)** を選択します。

    ![[Edit attribute list for Workday]\(Workday の属性リストの編集\) アクションが強調表示されている [Workday to Azure A D User Provisioning - Provisioning]\(Workday から Azure AD へのユーザー プロビジョニング - プロビジョニング\) ページを示すスクリーンショット。](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. 属性リストの一番下にある入力フィールドまでスクロールします。

7. **[名前]** に、属性の表示名を入力します。

8. **[Type]\(型\)** には、対象となる属性にふさわしい型を選択します (通常は **String** が一般的)。

9. **[API Expression]\(API 式\)** には、Workday Studio からコピーした XPath 式を入力します。 例: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. **[属性の追加]** を選択します。

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. 上の **[保存]** を選択し、ダイアログで **[はい]** を選択します。 [属性マッピング] 画面をまだ開いている場合は閉じてください。

12. メインの **[プロビジョニング]** タブに戻り、 **[Synchronize Workday Workers to On Premises Active Directory]\(Workday の従業員をオンプレミスの Active Directory と同期\)** または **[Synchronize Workers to Azure AD]\(従業員を Azure AD と同期\)** を再び選択します。

13. **[新しいマッピングの追加]** を選択します。

14. **[ソース属性]** リストに新しい属性が表示されます。

15. 必要に応じて新しい属性のマッピングを追加します。

16. 完了したら、忘れずに **[プロビジョニングの状態]** を **[オン]** に設定してから保存してください。

### <a name="exporting-and-importing-your-configuration"></a>構成のエクスポートとインポート

[プロビジョニング構成のエクスポートとインポート](../app-provisioning/export-import-provisioning-configuration.md)に関する記事を参照してください。

## <a name="managing-personal-data"></a>個人データの管理

Active Directory 用の Workday プロビジョニング ソリューションでは、オンプレミスの Windows サーバー上にプロビジョニング エージェントをインストールする必要があります。このエージェントでは、Workday から AD への属性マッピングに応じて個人データを含む可能性があるログが Windows イベント ログに作成されます。 ユーザーのプライバシー義務を遵守するために、イベント ログを消去する Windows のスケジュールされたタスクを設定することで、48 時間を超えてイベント ログにデータが保持されないようにすることができます。

Azure AD プロビジョニング サービスは、GDPR 分類の **データ プロセッサ** カテゴリに分類されます。 このサービスは、データ プロセッサ パイプラインとして、重要なパートナーやエンド コンシューマーにデータ処理サービスを提供するものです。 Azure AD プロビジョニング サービスは、ユーザー データを生成するものではなく、どのような個人データが収集されるかや、それらのデータの用途について、独立した制御を持つものでもありません。 Azure AD プロビジョニング サービスでのデータの取得、集計、分析、およびレポートは、既存のエンタープライズ データに基づいて行われます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

データ保持に関しては、Azure AD プロビジョニング サービスでは 30 日を超えてレポートの生成、分析の実行、または分析情報の提供を行いません。 そのため Azure AD プロビジョニング サービスでは、いかなるデータも 30 日間を超えて格納、処理、保持されることはありません。 この設計は、GDPR の規制、Microsoft のプライバシー コンプライアンス規則、および Azure AD のデータ リテンション ポリシーに準拠したものです。

## <a name="next-steps"></a>次のステップ

* [Azure AD と Workday の統合シナリオと Web サービス呼び出しについて](../app-provisioning/workday-integration-reference.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
* [Workday と Azure Active Directory の間でシングル サインオンを構成する方法](workday-tutorial.md)
* [Workday Writeback の構成方法を確認する](workday-writeback-tutorial.md)
* [Microsoft Graph API を使用してプロビジョニングの構成を管理する方法](/graph/api/resources/synchronization-overview)