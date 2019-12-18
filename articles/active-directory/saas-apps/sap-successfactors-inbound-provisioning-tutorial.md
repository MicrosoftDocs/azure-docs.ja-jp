---
title: チュートリアル:Azure Active Directory での SuccessFactors 受信プロビジョニングの構成 | Microsoft Docs
description: SuccessFactors からの受信プロビジョニングを構成する方法について説明します
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: cc17b8158c847bff5f07d6088a99566dc499d1bf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914775"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>チュートリアル:SAP SuccessFactors から Active Directory へのユーザー プロビジョニング (プレビュー) を構成する
このチュートリアルでは、SuccessFactors Employee Central から Active Directory (AD) と Azure AD にユーザーをプロビジョニングするために必要な手順と、SuccessFactors にメール アドレスを書き戻すオプションについて説明します。 この統合は、パブリック プレビュー中であり、SuccessFactors Employee Central から [70 を超えるユーザー属性](../manage-apps/sap-successfactors-attribute-reference.md)を取得することをサポートしています。

>[!NOTE]
>SuccessFactors からプロビジョニングするユーザーに、オンプレミス AD アカウントと、場合によって Azure AD アカウントも必要な場合は、このチュートリアルを使用します。 SuccessFactors のユーザーに Azure AD アカウント (クラウドのみのユーザー) のみが必要な場合は、[SAP SuccessFactors から Azure AD へのユーザー プロビジョニングを構成する](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)方法に関するチュートリアルを参照してください。 


## <a name="overview"></a>概要

[Azure Active Directory のユーザー プロビジョニング サービス](../manage-apps/user-provisioning.md)は、ユーザーの ID ライフ サイクルを管理するために [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) と統合されています。 

Azure AD のユーザー プロビジョニング サービスでサポートされている SuccessFactors ユーザー プロビジョニング ワークフローは、次の人事管理および ID ライフサイクル管理シナリオを自動化します。

* **新しい従業員の雇用** - SuccessFactors に新しい従業員が追加されると、Active Directory、Azure Active Directory、および必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に作成され、メール アドレスが SuccessFactors に書き戻されます。

* **従業員の属性とプロファイルの更新** - SuccessFactors で従業員レコード (名前、職名、マネージャーなど) が更新されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に更新されます。

* **従業員の退職** - SuccessFactors で従業員が退職状態になると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)でユーザー アカウントが自動的に無効になります。

* **従業員の再雇用** - SuccessFactors で従業員が再雇用されると、Active Directory、Azure Active Directory、必要に応じて Office 365 や [Azure AD によってサポートされているその他の SaaS アプリケーション](../manage-apps/user-provisioning.md)に以前のアカウントが (設定に応じて) 自動的に再アクティブ化または再プロビジョニングされます。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この SuccessFactors から Active Directory へのユーザー プロビジョニング ソリューションは、次の場合に最適です。

* SuccessFactors ユーザー プロビジョニング用に事前に構築されたクラウドベースのソリューションを求めている組織

* SuccessFactors から Active Directory への直接ユーザー プロビジョニングを必要とする組織

* [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) から取得したデータを使用してユーザーをプロビジョニングする必要がある組織

* 1 つ以上の Active Directory フォレスト、ドメイン、および OU と同期するために、[SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) で検出された変更情報のみに基づいてユーザーの登録、移動、削除を行う必要がある組織

* 電子メールに Office 365 を使用している組織

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このセクションでは、一般的なハイブリッド環境に向けた、エンド ツー エンドのユーザー プロビジョニング ソリューションのアーキテクチャについて説明します。 2 つの関連するフローがあります。

* **権限がある人事データのフロー - SuccessFactors からオンプレミスの Active Directory へ:** このフローでは、最初に社員イベント (新規雇用、異動、退職など) がクラウドの SuccessFactors Employee Central で発生し、イベント データはその後、Azure AD ディレクトリとプロビジョニング エージェントを通ってオンプレミスの Active Directory に移動します。 イベントによっては、AD での作成/更新/有効化/無効化の操作に至る可能性があります。
* **メール書き戻しのフロー - オンプレミスの Active Directory から SuccessFactors へ:** Active Directory でアカウントの作成が完了すると、Azure AD Connect を介して Azure AD Connect 同期と同期され、メール属性を SuccessFactors に書き戻すことができます。

  ![概要](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>エンド ツー エンドのユーザー データ フロー

1. 人事チームは、SuccessFactors Employee Central で社員のトランザクション (参加者/異動者/休暇者または新規雇用/移動/退職) を実行します
2. Azure AD プロビジョニング サービスは、SuccessFactors EC からの、スケジュールされた ID の同期を実行し、オンプレミスの Active Directory との同期のために処理する必要がある変更を識別します。
3. Azure AD プロビジョニング サービスは、AD アカウントの作成/更新/有効化/無効化の操作を含む要求ペイロードを使用して、オンプレミスの Azure AD Connect プロビジョニング エージェントを呼び出します。
4. Azure AD Connect プロビジョニング エージェントは、サービス アカウントを使用して AD アカウントのデータを追加/更新します。
5. Azure AD Connect 同期エンジンによってデルタ同期が実行され、AD 内の更新がプルされます。
6. Active Directory の更新は、Azure Active Directory と同期されます。
7. [SuccessFactors Writeback アプリ](sap-successfactors-writeback-tutorial.md)が構成されている場合、使用された一致する属性に基づいて、メール属性を SuccessFactors に書き戻します。

## <a name="planning-your-deployment"></a>デプロイの計画

SuccessFactors から AD へのクラウド人事駆動型のユーザー プロビジョニングを構成するには、次のようなさまざまな側面をカバーするかなりの計画が必要です。
* Azure AD Connect プロビジョニング エージェントの設定 
* AD ユーザー プロビジョニング アプリにデプロイする SuccessFactors の数
* 一致する ID、属性マッピング、変換、およびスコープ フィルター

これらのトピックに関する包括的なガイドラインについては、[クラウド人事デプロイ計画](../manage-apps/plan-cloud-hr-provision.md)に関するページを参照してください。 

## <a name="configuring-successfactors-for-the-integration"></a>統合のための SuccessFactors の構成

すべての SuccessFactors プロビジョニング コネクタの一般的な要件は、SuccessFactors OData API を呼び出すための適切なアクセス許可を持つ SuccessFactors アカウントの資格情報が必要なことです。 このセクションでは、SuccessFactors でサービス アカウントを作成し、適切なアクセス許可を付与する手順を説明します。 

* [SuccessFactors で API ユーザー アカウントを作成または識別する](#createidentify-api-user-account-in-successfactors)
* [API アクセス許可ロールを作成する](#create-an-api-permissions-role)
* [API ユーザーのアクセス許可グループを作成する](#create-a-permission-group-for-the-api-user)
* [アクセス許可グループへのアクセス許可ロールの付与をする](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors で API ユーザー アカウントを作成または識別する
SuccessFactors 管理チームまたは実装パートナーと協力して、OData API の呼び出しに使用される SuccessFactors のユーザー アカウントを作成または識別します。 Azure AD でプロビジョニング アプリを構成する場合、このアカウントのユーザー名とパスワードの資格情報が必要になります。 

### <a name="create-an-api-permissions-role"></a>API アクセス許可ロールを作成する

* Admin Center にアクセスできるユーザーアカウントで SAP SuccessFactors にログインします。
* "*Manage Permission Roles*" を検索し、検索結果から **[Manage Permission Roles]\(アクセス許可ロールの管理\)** を選択します。
  ![アクセス許可ロールを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* [Permission Role List]\(アクセス許可ロール一覧\) で、 **[Create New]\(新規作成\)** をクリックします。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可ロールを新規作成する](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 新しいアクセス許可ロールの **[Role Name]\(ロール名\)** と **[Description]\(説明\)** を追加します。 名前と説明では、このロールが API 使用アクセス許可されていることを示す必要があります。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可のロールの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* [Permission settings]\(アクセス許可の設定\) で、 **[Permission]\(アクセス許可\)** をクリックし、アクセス許可一覧を下にスクロールして **[Manage Integration Tools]\(統合ツールの管理\)** をクリックします。 **[Allow Admin to Access to OData API through Basic Authentication]\(管理者が Websites Basic 認証を介して OData API にアクセスできるようにする\)** チェックボックスを有効にします。
  > [!div class="mx-imgBorder"]
  > ![統合ツールを管理する](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 同じボックス内を下にスクロールし、 **[Employee Central API]** を選択します。 次に示すように、ODATA API を使用して読み取り、ODATA API を使用して編集するためのアクセス許可を追加します。 SuccessFactors への書き戻しシナリオに同じアカウントを使用する場合は、編集オプションを選択してください。 
  > [!div class="mx-imgBorder"]
  > ![読み取りと書き込みのアクセス許可](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

  >[!NOTE]
  >このプロビジョニング アプリによって取得される属性の詳細な一覧については、[SuccessFactors 属性のリファレンス](../manage-apps/sap-successfactors-attribute-reference.md)に関するページを参照してください。

* **[Done]\(終了)** をクリックします。 **[変更を保存]** をクリックします。

### <a name="create-a-permission-group-for-the-api-user"></a>API ユーザーのアクセス許可グループを作成する

* SuccessFactors Admin Center で、"*Manage Permission Groups*" を検索し、検索結果から **[Manage Permission Groups]\(アクセス許可グループの管理\)** を選択します。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* [Manage Permission Groups]\(アクセス許可グループの管理\) ウィンドウで、 **[Create New]\(新規作成\)** をクリックします。
  > [!div class="mx-imgBorder"]
  > ![新しいグループを追加する](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 新しいグループのグループ名を追加します。 グループ名は、グループが API ユーザー用であることを示す必要があります。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループ名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* グループにメンバーを追加します。 たとえば、[People Pool]\(ユーザー プール\) ドロップダウン メニューから **[Username]\(ユーザー名\)** を選択し、統合に使用する API アカウントのユーザー名を入力します。 
  > [!div class="mx-imgBorder"]
  > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png)\(グループ メンバーの追加\)
* **[Done]\(完了\)** をクリックして、アクセス許可グループの作成を完了します。

### <a name="grant-permission-role-to-the-permission-group"></a>許可グループに許可ロールを付与する

* SuccessFactors Admin Center で、"*Manage Permission Roles*" を検索し、検索結果から **[Manage Permission Roles]\(アクセス許可ロールの管理\)** を選択します。
* **[Permission Role List]\(アクセス許可ロール一覧\)** から、API 使用アクセス許可用に作成したロールを選択します。
* **[Grant this role to]\(このロールに付与するアクセス許可\)** で、 **[Add]\(追加\)** ボタンをクリックします。
* ドロップダウン メニューから **[Permission Group]\(アクセス許可グループ\)** を選択し、 **[Select]\(選択\)** をクリックして [Groups]\(グループ\) ウィンドウを開き、先ほど作成したグループを検索して選択します。 
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループを追加する](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* アクセス許可グループに対するアクセス許可ロールの付与を確認します。 
  > [!div class="mx-imgBorder"]
  > ![アクセス許可ロールおよびグループの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **[変更を保存]** をクリックします。

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>SuccessFactors から Active Directory へのユーザー プロビジョニングの構成

このセクションでは、SuccessFactors から、統合の範囲内にある各 Active Directory ドメインへのユーザー アカウントのプロビジョニングの手順について説明します。

* [プロビジョニング コネクタ アプリを追加し、プロビジョニング エージェントをダウンロードする](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [オンプレミス プロビジョニング エージェントのインストールと構成](#part-2-install-and-configure-on-premises-provisioning-agents)
* [SuccessFactors と Active Directory の接続の構成](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [属性マッピングの構成](#part-4-configure-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>パート 1:プロビジョニング コネクタ アプリを追加し、プロビジョニング エージェントをダウンロードする

**SuccessFactors から Active Directory へのプロビジョニングを構成するには:**

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します

2. 左のナビゲーション バーで、 **[Azure Active Directory]** を選択します

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **[SuccessFactors to Active Directory User Provisioning]\(SuccessFactors から Active Directory へのユーザー プロビジョニング\)** を探し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します

7. **[プロビジョニング** **モード]** を **[自動]** に設定します

8. 表示された情報バナーをクリックして、プロビジョニング エージェントをダウンロードします。 
   > [!div class="mx-imgBorder"]
   > ![エージェントのダウンロード](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "[エージェントのダウンロード] 画面")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>パート 2:オンプレミス プロビジョニング エージェントのインストールと構成

オンプレミスの Active Directory にプロビジョニングするには、.NET 4.7.1 以降の Framework と目的の Active Directory ドメインへのネットワーク アクセスを備えたサーバーに、プロビジョニング エージェントがインストールされている必要があります。

> [!TIP]
> [ここ](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)に示した手順に従って、サーバーの .NET Framework のバージョンをチェックできます。
> サーバーに .NET 4.7.1 以降がインストールされていない場合は、[ここ](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)からダウンロードできます。  

ダウンロードしたエージェント インストーラーをサーバー ホストに転送し、以下の手順に従ってエージェントの構成を完了します。

1. 新しいエージェントをインストールする Windows Server にサインインします。

1. プロビジョニング エージェントのインストーラーを起動し、条件に同意して **[インストール]** ボタンをクリックします。

   ![インストール画面](./media/workday-inbound-tutorial/pa_install_screen_1.png "インストール画面")
   
1. インストールが完了したらウィザードが起動され、 **[Connect Azure AD]\(Azure AD の接続)** 画面が表示されます。 **[認証]** ボタンをクリックして、お使いの Azure AD インスタンスに接続します。

   ![Azure AD の接続](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD の接続")
   
1. グローバル管理者の資格情報を使用して、Azure AD インスタンスに対して認証します。

   ![管理者認証](./media/workday-inbound-tutorial/pa_install_screen_3.png "管理者認証")

   > [!NOTE]
   > Azure AD 管理者の資格情報は、Azure AD テナントへの接続にのみ使用されます。 エージェントでは、資格情報がサーバー上のローカルに保存されません。

1. Azure AD で認証が成功すると、 **[Connect Active Directory]\(Active Directory の接続)** 画面が表示されます。 この手順では、お使いの AD ドメイン名を入力し、 **[ディレクトリの追加]** ボタンをクリックしてください。

   ![ディレクトリの追加](./media/workday-inbound-tutorial/pa_install_screen_4.png "ディレクトリの追加")
  
1. ここで、AD ドメインに接続するために必要な資格情報の入力を求められます。 同じ画面で、 **[Select domain controller priority]\(ドメイン コント ローラーの優先度を選択する)** を使用して、エージェントがプロビジョニング要求の送信に使用する必要があるドメイン コントローラーを指定できます。

   ![ドメイン資格情報](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. ドメインの構成後、インストーラーによって、構成されたドメインの一覧が表示されます。 この画面では、手順 5 と 6 を繰り返してより多くのドメインを追加するか、 **[次へ]** をクリックしてエージェントの登録に進むことができます。

   ![構成されたドメイン](./media/workday-inbound-tutorial/pa_install_screen_6.png "構成されたドメイン")

   > [!NOTE]
   > 複数の AD ドメイン (例: na.contoso.com、emea.contoso.com) がある場合は、各ドメインを個々に一覧に追加してください。
   > 親ドメイン (contoso.com など) の追加だけでは十分ではありません。 各子ドメインをエージェントに登録する必要があります。
   
1. 構成の詳細を確認し、 **[Confirm]\(確認)** をクリックしてエージェントを登録します。
  
   ![画面の確認](./media/workday-inbound-tutorial/pa_install_screen_7.png "画面の確認")
   
1. 構成ウィザードに、エージェント登録の進行状況が表示されます。
  
   ![エージェントの登録](./media/workday-inbound-tutorial/pa_install_screen_8.png "エージェントの登録")
   
1. エージェントの登録が成功したら、 **[終了]** をクリックしてウィザードを終了できます。
  
   ![終了画面](./media/workday-inbound-tutorial/pa_install_screen_9.png "終了画面")
   
1. [サービス] スナップインを開き、"Microsoft Azure AD Connect Provisioning Agent" という名前のサービスを探して、エージェントのインストールを確認し、実行中であることを確認します
  
   ![サービス](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>パート 3:プロビジョニング アプリで、SuccessFactors と Active Directory の接続を構成します。
この手順では、Azure portal で SuccessFactors と Active Directory の接続を確立します。 

1. Azure portal で、[パート 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) で作成した SuccessFactors から Active Directory へのユーザー プロビジョニング アプリに戻ります。

1. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理ユーザー名** - SuccessFactors API ユーザー アカウントのユーザー名に、会社 ID を追加して入力します。 形式は **username\@companyID** です

   * **管理パスワード** - SuccessFactors API ユーザー アカウントのパスワードを入力します。 

   * **テナント URL** - SuccessFactors OData API サービス エンドポイントの名前を入力します。 http または https なしでサーバーのホスト名のみを入力してください。 この値は、 **<api-server-name>.successfactors.com** のようになります。

   * **Active Directory フォレスト** - エージェントに登録されている Active Directory ドメインの "名前"。 ドロップダウンを使用して、プロビジョニングのターゲット ドメインを選択します。 通常、この値は *contoso.com* のような文字列です。

   * **Active Directory コンテナー -** エージェントが既定でユーザー アカウントを作成する必要のあるコンテナー DN を入力します。
        例:*OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > この設定が適用されるのは、属性のマッピングで *parentDistinguishedName* 属性が構成されていない場合のユーザー アカウント作成のみです。 この設定は、ユーザーの検索や更新の操作には使用されません。 ドメインのサブツリー全体が、検索操作の範囲内になります。

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗する場合は、エージェントのセットアップで構成された SuccessFactors 資格情報と AD 資格情報が有効であることを再確認します。
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 資格情報が正常に保存されると、 **[マッピング]** セクションに既定のマッピング **[Synchronize SuccessFactors Users to On Premises Active Directory]\(SuccessFactors のユーザーをオンプレミスの Active Directory に同期する\)** が表示されます

### <a name="part-4-configure-attribute-mappings"></a>パート 4:属性マッピングの構成

このセクションでは、ユーザーデータが SuccessFactors から Active Directory Domain Services に流れる方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、 **[Synchronize SuccessFactors Users to On Premises Active Directory]\(SuccessFactors のユーザーをオンプレミスの Active Directory に同期する\)** をクリックします。

1. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、AD へのプロビジョニングの対象にする SuccessFactors のユーザー セットを選択できます。 既定のスコープは、"SuccessFactors のすべてのユーザー" です。 フィルターの例:

   * 例:1000000 から 2000000 (2000000 を除く) までの personIdExternal を持つユーザーにスコープを設定

      * 属性: personIdExternal

      * 演算子:REGEX Match

      * 値:(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 例:臨時社員ではなく、従業員のみ

      * 属性:EmployeeID

      * 演算子:IS NOT NULL

   > [!TIP]
   > 初めてプロビジョニング アプリを構成するときは、属性マッピングと式をテストして検証し、目的の結果が得られていることを確認する必要があります。 Microsoft は、SuccessFactors の少数のテスト ユーザーを使用してマッピングをテストするために **[ソース オブジェクト スコープ]** の下のスコープ フィルターを使用することをお勧めします。 マッピングが機能していることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

   > [!CAUTION] 
   > プロビジョニング エンジンの既定の動作では、スコープ外に出るユーザーが無効化または削除されます。 これはご使用の SuccessFactors と AD の統合には望ましくない場合があります。 この既定の動作をオーバーライドするには、「[スコープ外に出るユーザー アカウントの削除をスキップする](../manage-apps/skip-out-of-scope-deletions.md)」の記事を参照してください。
  
1. **[対象オブジェクトのアクション]** フィールドでは、Active Directory 上で実行されるアクションをグローバルにフィルター処理できます。 **作成**と**更新**が最も一般的です。

1. **[属性マッピング]** セクションでは、個別の SuccessFactors 属性を Active Directory の属性にマッピングする方法を定義できます。

  >[!NOTE]
  >アプリケーションでサポートされている SuccessFactors 属性の完全な一覧については、[SuccessFactors 属性のリファレンス](../manage-apps/sap-successfactors-attribute-reference.md)に関するページを参照してください。


1. 既存の属性マッピングをクリックして更新するか、または画面の下部にある **[新しいマッピングの追加]** をクリックして、新しいマッピングを追加します。 個々の属性マッピングは、次のプロパティをサポートしています。

      * **マッピングの種類**

         * **ダイレクト** - 変更なしで SuccessFactors 属性の値を AD 属性に書き込みます

         * **定数** - 静的な定数文字列の値を AD 属性に書き込みます

         * **式** - 1 つ以上の SuccessFactors 属性に基づいて、AD 属性にカスタム値を書き込むことができます。 [詳細については、式に関するこの記事を参照してください](../manage-apps/functions-for-customizing-application-data.md)。

      * **ソース属性** - SuccessFactors のユーザー属性

      * **既定値** – 省略可能。 ソース属性に空の値がある場合、マッピングではこの値が代わりに書き込まれます。
            最も一般的な構成では、これを空白のままにします。

      * **ターゲット属性** - Active Directory のユーザー属性。

      * **この属性を使用してオブジェクトを照合する** - このマッピングを使用して、SuccessFactors と Active Directory 間でユーザーを一意に識別するかどうかを示します。 この値は、通常、SuccessFactors の Worker ID フィールドで設定され、一般的に Active Directory の従業員 ID 属性のいずれかにマッピングされます。

      * **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。

      * **このマッピングを適用する**

         * **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します

         * **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します

1. マッピングを保存するには、[属性マッピング] セクションの上部にある **[保存]** をクリックします。

属性マッピングの構成が完了したら、[ユーザー プロビジョニング サービスを有効にして起動](#enable-and-launch-user-provisioning)できるようになります。

## <a name="enable-and-launch-user-provisioning"></a>ユーザー プロビジョニングの有効化と起動

SuccessFactors プロビジョニング アプリの構成が完了すると、Azure portal でプロビジョニング サービスを有効にできます。

> [!TIP]
> 既定では、プロビジョニング サービスを有効にすると、スコープ内のすべてのユーザーに対してプロビジョニング操作が開始されます。 マッピングのエラーまたは Workday データの問題がある場合、プロビジョニング ジョブが失敗し、検疫状態になる可能性があります。 これを避けるために、ベスト プラクティスとして、すべてのユーザーの完全同期を開始する前に、 **[ソース オブジェクト スコープ]** フィルターを構成して少数のテスト ユーザーで属性マッピングをテストすることをお勧めします。 マッピングが機能し、目的の結果が得られていることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **[プロビジョニング]** タブで、 **[プロビジョニングの状態]** を **[ON]** に設定します。

2. **[Save]** をクリックします。

3. この操作により初期同期が開始されます。所要時間は SuccessFactors テナントのユーザー数に応じて変わります。 進行状況バーをチェックして、同期サイクルの進行状況を追跡できます。 

4. 好きなときに、Azure Portal の **[監査ログ]** タブをチェックして、プロビジョニング サービスで実行されたアクションを確認します。 監査ログには、Workday から読み込まれたユーザーや、その後 Active Directory に追加または更新されたユーザーなど、プロビジョニング サービスによって実行された個々の同期イベントがすべて表示されます。 

5. 最初の同期が完了すると、次に示すように、 **[プロビジョニング]** タブに監査概要レポートが書き込まれます。

   > [!div class="mx-imgBorder"]
   > ![プロビジョニングの進行状況バー](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>次の手順

* [受信プロビジョニングのサポートされている SuccessFactors 属性に関する詳細情報](../manage-apps/sap-successfactors-attribute-reference.md)
* [SuccessFactors へのメール書き戻しを構成する方法](sap-successfactors-writeback-tutorial.md)
* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
* [SuccessFactors と Azure Active Directory Domain Services の間でシングル サインオンを構成する方法を学習する](successfactors-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)
* [プロビジョニング構成をエクスポートおよびインポートする方法を学習する](../manage-apps/export-import-provisioning-configuration.md)
