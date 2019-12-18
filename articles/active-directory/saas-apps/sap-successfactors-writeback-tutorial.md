---
title: チュートリアル:Azure Active Directory Domain Services で SuccessFactors 書き戻しを構成する | Microsoft Docs
description: Azure Active Directory から SuccessFactors への属性の書き戻しを構成する方法について学びます
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: c780ee973c1dabb15c37b2519eb8253d2371080a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932132"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>チュートリアル:Azure AD から SAP SuccessFactors への属性の書き戻しを構成 する (プレビュー)
このチュートリアルの目的は Azure Active Directory から SuccessFactors Employee Central に属性を書き戻すために必要な手順を説明することです。 書き戻しに対して、現在、サポートされている唯一の属性は email 属性です。 

## <a name="overview"></a>概要

[SuccessFactors からオンプレミス AD](sap-successfactors-inbound-provisioning-tutorial.md) プロビジョニングアプリまたは[SuccessFactorsからMicrosoft Azure Active Directory](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)プロビジョニングアプリのいずれかを使用してインバウンド プロビジョニング統合をセットアップした後、オプションで SuccessFactors 書き戻しアプリを構成して、電子メールアドレスをSuccessFactorsに書き戻すことができます。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>このユーザー プロビジョニング ソリューションが最適な場合

この SuccessFactors Writeback ユーザー プロビジョニング ソリューションは、次の場合に最適です：

* IT が管理する信頼できる属性（メールアドレスなど）を SuccessFactors に書き戻す必用のある、 Office 365 を使用している組織

## <a name="configuring-successfactors-for-the-integration"></a>統合 SuccessFactors の構成

すべての SuccessFactors プロビジョニング コネクタの一般的な要件は、SuccessFactors OData API を呼び出すための適切なアクセス許可を持つ SuccessFactors アカウントの資格情報が必要なことです。 このセクションでは、SuccessFactors でサービス アカウントを作成し、適切なアクセス許可を付与する手順を説明します。 

* [SuccessFactors の API ユーザー アカウントの作成/識別](#createidentify-api-user-account-in-successfactors)
* [API アクセス許可ロールを作成する](#create-an-api-permissions-role)
* [API ユーザーのアクセス許可グループを作成する](#create-a-permission-group-for-the-api-user)
* [アクセス許可グループへのアクセス許可ロールの付与をする](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors の API ユーザー アカウントの作成/識別
SuccessFactors 管理チームまたは実装パートナーと協力して、OData API の呼び出しに使用される SuccessFactors のユーザー アカウントを作成または識別します。 Azure AD でプロビジョニング アプリを構成する場合、このアカウントのユーザー名とパスワードの資格情報が必要になります。 

### <a name="create-an-api-permissions-role"></a>API アクセス許可ロールを作成する

* Admin Center にアクセスできるユーザーアカウントで SAP SuccessFactors にログインします。
* 「*アクセス許可ロール*」を検索し、検索結果から **[アクセス許可ロールの管理]** を選択します。
  ![アクセス許可ロールを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* [アクセス許可 ] 一覧で、 **[新規作成]** をクリックします。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可ロールを新規作成する](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 新しいアクセス許可ロールの**ロール名**および**説明**を追加します。 名前と説明では、このロールが API 使用アクセス許可されていることを示す必要があります。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可のロールの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* [アクセス許可の設定] で、 **[アクセス許可]** をクリックし、アクセス許可の一覧を下にスクロールして **[統合ツールの管理]** をクリックします。 **[管理者が Websites Basic 認証を介して OData API にアクセスできるようにする]** チェックボックスを有効にします。
  > [!div class="mx-imgBorder"]
  > ![統合ツールを管理する](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 同じボックス内を下方向にスクロールし、 **[Employee Central API]** を選択します。 次に示すように、ODATA API を使用して読み取り、ODATA API を使用して編集するためのアクセス許可を追加します。 SuccessFactors への書き戻しシナリオに同じアカウントを使用する場合は、編集オプションを選択してください。 
  > [!div class="mx-imgBorder"]
  > ![アクセス許可を読み取り/書き込みする](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* **[Done]\(終了)** をクリックします。 **[変更を保存]** をクリックします。

### <a name="create-a-permission-group-for-the-api-user"></a>API ユーザーのアクセス許可グループを作成する

* SuccessFactors 管理センターで、「*アクセス許可グループの管理*」を検索し、検索結果から **[アクセス許可グループ]** を選択します。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループを管理する](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* [アクセス許可グループの管理] ウィンドウで、 **[新規作成]** をクリックします。
  > [!div class="mx-imgBorder"]
  > ![新しいグループを追加する](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 新しいグループのグループ名を追加します。 グループ名は、グループが API ユーザー用であることを示す必要があります。
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループ名](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* グループにメンバーを追加します。 たとえば、[People Pool] ドロップダウン メニューから **[Username]** を選択し、統合に使用する API アカウントのユーザー名を入力します。 
  > [!div class="mx-imgBorder"]
  > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png)\(グループ メンバーの追加\)
* **[Done]** をクリックして、アクセス許可グループの作成を完了します。

### <a name="grant-permission-role-to-the-permission-group"></a>アクセス許可グループへのアクセス許可ロールの付与をする

* SuccessFactors 管理センターで、「*アクセス許可ロールの管理*」を検索し、検索結果から **[アクセス許可ロールの管理]** を選択します。
* **[アクセス許可ロール]** の一覧で、API 使用アクセス許可用に作成したロールを選択します。
* **[Grant this role to]** で、 **[追加]** ボタンをクリックします。
* ドロップダウン メニューから **[アクセス許可グループ...]** を選択し、 **[選択]** をクリックして [グループ] ウィンドウを開き、上記で作成したグループを検索して選択します。 
  > [!div class="mx-imgBorder"]
  > ![アクセス許可グループの追加](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* アクセス許可グループに対するアクセス許可ロールの付与を確認します。 
  > [!div class="mx-imgBorder"]
  > ![アクセス許可ロールおよびグループの詳細](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **[変更を保存]** をクリックします。

## <a name="configuring-successfactors-writeback"></a>SAP SuccessFactors 書き戻し

このセクションでは、次のステップについて説明します 

* [プロビジョニング コネクタ アプリを追加して、SuccessFactors への接続の構成をする](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [属性マッピングの構成](#part-2-configure-attribute-mappings)
* [ユーザー プロビジョニングの有効化と起動](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>パート 1:プロビジョニング コネクタ アプリを追加して、SuccessFactors への接続の構成をする

**SAP SuccessFactors 書き戻し：**

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します

2. 左のナビゲーション バーで、 **[Azure Active Directory]** を選択します

3. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順に選択します。

4. **[アプリケーションの追加]** を選択し、 **[すべて]** のカテゴリを選択します。

5. **SuccessFactors Writeback** を検索し、ギャラリーからそのアプリを追加します。

6. アプリが追加され、アプリの詳細画面が表示されたら、 **[プロビジョニング]** を選択します

7. **[プロビジョニング** **モード]** を **[自動]** に設定します

8. 以下のように **[管理者の資格情報]** セクションを完了します。

   * **管理ユーザー名** – SuccessFactors API ユーザー アカウントのユーザー名に、会社 ID を追加して入力します。 次の形式で指定します： **username\@companyID**

   * **管理パスワード –** SuccessFactors API ユーザー アカウントのパスワードを入力します。 

   * **テナント URL –** SuccessFactors OData API サービス エンドポイントの名前を入力します。 http または https なしでサーバーのホスト名のみを入力してください。 この値は次のようになります: **api-server-name.successfactors.com**。

   * **メール通知** - メール アドレスを入力し、[send email if failure occurs]\(失敗した場合にメールを送信する\) チェックボックスをオンにします。
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * **[接続のテスト]** ボタンをクリックします。 接続テストが成功した場合、上部の **[保存]** ボタンをクリックします。 失敗した場合は、SuccessFactors 資格情報および URL が有効か再度確認します。
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 資格情報が正常に保存されると、**マッピング** セクションにはデフォルトのマッピングが次の表示をする： **Azure Active Directory Domain Services ユーザーを SuccessFactors に同期する**

### <a name="part-2-configure-attribute-mappings"></a>パート 2:属性マッピングの構成

このセクションでは、ユーザーデータが SuccessFactors から Active Directory Domain Services に流れる方法を構成します。

1. **[マッピング]** の [プロビジョニング] タブで、**Azure Active Directory Domain Services ユーザーを SuccessFactors に同期する** をクリックします。

1. **[ソース オブジェクト スコープ]** フィールドでは、属性ベースのフィルター セットを定義して、書き戻しの対象となる Azure Active Directory Domain Services のユーザー セットを選択できます。 既定のスコープは、"Azure AD のすべてのユーザー" です。 
   > [!TIP]
   > 初めてプロビジョニング アプリを構成するときは、属性マッピングと式をテストして検証し、目的の結果が得られていることを確認する必要があります。 Microsoft は、Azure Active Directory の少数のテスト ユーザーを使用してマッピングをテストする場合には、 **[ソース オブジェクト スコープ]** の下のスコープ フィルターを使用するようお勧めします。 マッピングが機能していることを確認したら、フィルターを削除するか、徐々に拡張してより多くのユーザーを含めることができます。

1. **ターゲットオブジェクトアクション** フィールドでは、**更新** 操作のみがサポートされます。

1. **[属性マッピング]** セクションでは、SuccessFactors ユーザープロファイルを Azure Active Directory ユーザーにリンクするために使用される一致 ID と、電子メールの送信元として機能する Azure Active Directory の属性を変更できます。 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors 書き戻しでは、email 属性のみがサポートされています。 新しい属性を追加する場合に、 **[新しいマッピングの追加]** を使用しないでください。 

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

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
* [SuccessFactors と Azure Active Directory Domain Services の間でシングル サインオンを構成する方法を学習する](successfactors-tutorial.md)
* [他の SaaS アプリケーションを Azure Active Directory と統合する方法](tutorial-list.md)
* [プロビジョニング構成をエクスポートおよびインポートする方法を学習する](../manage-apps/export-import-provisioning-configuration.md)

