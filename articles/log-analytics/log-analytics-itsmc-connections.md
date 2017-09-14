---
title: "OMS の IT Service Management Connector との ITSM 接続 | Microsoft Docs"
description: "ITSM 製品/サービスを OMS の IT Service Management Connector に接続して、ITSM 作業項目を一元的に監視、管理します。"
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e4f2e0a23aa52a0e02e7047916b77fb15107defa
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---
# <a name="connect-itsm-productsservices-with-it-service-management-connector-preview"></a>ITSM 製品/サービスを IT Service Management Connector に追加する (プレビュー)
この記事では、お使いの ITSM 製品/サービスを OMS の IT Service Management Connector に接続して、作業項目を一元的に管理する方法について説明します。 IT Service Management Connector の詳細については、[概要](log-analytics-itsmc-overview.md)に関する記事をご覧ください。

次の製品/サービスがサポートされています。

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-oms)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-oms)
- [Provance](#connect-provance-to-it-service-management-connector-in-oms)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-oms"></a>System Center Service Manager を OMS の IT Service Management Connector に接続する

次のセクションでは、System Center Service Manager 製品を OMS の IT Service Management Connector に接続する方法について詳しく説明します。

### <a name="prerequisites"></a>前提条件

以下の前提条件を満たしていることを確認してください。

- IT Service Management Connector がインストールされている。
詳細については、「[構成](log-analytics-itsmc-overview.md#configuration)」をご覧ください。
- Service Manager Web アプリケーション (Web アプリ) がデプロイされ構成されている。 Web アプリに関する情報については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
- ハイブリッド接続が作成および構成されている。 詳細については、「[ハイブリッド接続の構成](#configure-the-hybrid-connection)」をご覧ください。
- サポートされている Service Manager のバージョン (2012 R2 または 2016) を使用している。
- ユーザー ロールが[上級オペレーター](https://technet.microsoft.com/library/ff461054.aspx)である。

### <a name="connection-procedure"></a>接続手順

System Center Service Manager インスタンスを IT Service Management Connector に接続するには、次の手順に従います。

1. **[OMS]** >**[設定]** > **[接続されたソース]** に移動します。
2. **[ITSM Connector]** を選択し **[新しい接続の追加]** をクリックします。

    ![Service Manager ](./media/log-analytics-itsmc/itsmc-service-manager-connection.png)
3. 以下の表に示す情報を入力し、**[保存]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **名前**   | IT Service Management Connector に接続する System Center Service Manager インスタンスの名前を入力します。  この名前は、インスタンスの作業項目を構成したり、詳細な Log Analytics を確認したりするときに使用します。 |
| **接続の種類を選択**   | **[System Center Service Manager]** を選択します。 |
| **サーバー URL**   | Service Manager Web アプリの URL を入力します。 Service Manager Web アプリの詳細については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
| **クライアント ID**   | Web アプリを認証するために (自動スクリプトを使用して) 生成したクライアント ID を入力します。 自動スクリプトの詳細については、[こちら](log-analytics-itsmc-service-manager-script.md)をご覧ください。|
| **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
| **データ同期スコープ**   | IT Service Management Connector を介して同期する Service Manager 作業項目を選択します。  これらの作業項目は、Log Analytics にインポートされます。 **オプション:** インシデント、変更要求。|
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、OMS は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

正常に接続すると、同期します。

- Service Manager で選択した作業項目は、OMS の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- OMS からこの Service Manager インスタンスに、OMS アラートまたはログ検索からのインシデントを作成できます。

詳細については、「[Create ITSM work items for OMS alerts (OMS アラートの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)」と「[Create ITSM work items from OMS logs (OMS ログからの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)」をご覧ください。

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web アプリ サービスを作成しデプロイする

オンプレミスの Service Manager を OMS の IT Service Management Connector に接続するために、Microsoft は GitHub 上に Service Manager Web アプリを作成しました。

お使いの Service Manager 用に ITSM Web アプリを設定するには、次の手順に従います。

- **Web アプリのデプロイ** – Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。 Microsoft が提供する[自動スクリプト](log-analytics-itsmc-service-manager-script.md)を使用して、Web アプリをデプロイできます。
- **ハイブリッド接続の構成** - 手動で[ハイブリッド接続を構成](#configure-the-hybrid-connection)します。

#### <a name="deploy-the-web-app"></a>Web アプリのデプロイ
[自動スクリプト](log-analytics-itsmc-service-manager-script.md)を使用して、Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。

次の必要な情報を提供することにより、スクリプトを実行します。

- Azure サブスクリプションの詳細
- リソース グループ名
- 場所
- Service Manager サーバーの詳細 (サーバー名、ドメイン、ユーザー名とパスワード)
- Web アプリのサイト名のプレフィックス
- ServiceBus 名前空間。

スクリプトによって、指定した名前で (一意の名前にするために文字列をいくつか追加して) Web アプリが作成されます。 また、スクリプトによって、**Web アプリの URL**、**クライアント ID**、**クライアント シークレット**が生成されます。

これらの値を保存してください。IT Service Management Connector への接続を作成するときに必要になります。

**Web アプリのインストールを確認する**

1. **[Azure Portal]** > **[リソース]** に移動します。
2. Web アプリを選択し、**[設定]** > **[アプリケーションの設定]** の順にクリックします。
3. スクリプトを使用してアプリをデプロイする際に入力した Service Manager インスタンスに関する情報を確認します。

### <a name="configure-the-hybrid-connection"></a>ハイブリッド接続の構成

Service Manager インスタンスを OMS の IT Service Management Connector に接続するハイブリッド接続を構成するには、次の手順に従います。

1. **[Azure リソース]** の下にある Service Manager Web アプリを見つけます。
2. **[設定]** > **[ネットワーク]** の順にクリックします。
3. **[ハイブリッド接続]** の下にある **[ハイブリッド接続エンドポイントの構成]** をクリックします。

    ![ハイブリッド接続のネットワーク](./media/log-analytics-itsmc/itsmc-hybrid-connection-networking-and-end-points.png)
4. **[ハイブリッド接続]** ブレードで、**[ハイブリッド接続の追加]** をクリックします。

    ![ハイブリッド接続の追加](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-add.png)

5. **[ハイブリッド接続の追加]** ブレードで、**[ハイブリッド接続の新規作成]** をクリックします。

    ![新しいハイブリッド接続](./media/log-analytics-itsmc/itsmc-create-new-hybrid-connection.png)

6. 次の値を入力します。

    - **[エンドポイント名]**: 新しいハイブリッド接続の名前を指定します。
    -  **[エンドポイント ホスト]**: Service Manager 管理サーバーの FQDN です。
    - **[エンドポイント ポート]**: 「5724」を入力します。
    - **[サービス バス名前空間]**: 既存の Service Bus 名前空間を使用するか、新規に作成します。
    - **[場所]**: 場所を選択します。
    -  **[名前]**: Service Bus を作成する場合は名前を指定します。

    ![ハイブリッド接続の値](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-values.png)
6. **[OK]** をクリックし、**[ハイブリッド接続の作成]** ブレードを閉じ、ハイブリッド接続の作成を開始します。

    ハイブリッド接続が作成されると、ブレードに表示されます。

7. ハイブリッド接続を作成したら、その接続を選択し、**[選択したハイブリッド接続の追加]** をクリックします。

    ![新しいハイブリッド接続](./media/log-analytics-itsmc/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>リスナーのセットアップの構成

ハイブリッド接続のリスナーのセットアップを構成するには、次の手順に従います。

1. **[ハイブリッド接続]** ブレードで、**[接続マネージャーのダウンロード]** をクリックし、System Center Service Manager インスタンスが実行中のコンピューターに接続マネージャーをインストールします。

    インストールが完了すると、**[開始]** メニューにある **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** オプションが使用可能になります。

2. **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** をクリックすると、Azure の資格情報の入力を求められます。

3. Azure の資格情報でログインし、ハイブリッド接続を作成したサブスクリプションを選択します。

4. [ **Save**] をクリックします。

ハイブリッド接続が正常に接続されています。

![正常なハイブリッド接続](./media/log-analytics-itsmc/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> ハイブリッド接続を作成したら、デプロイした Service Manager Web アプリにアクセスして接続を確認し、テストします。 OMS の IT Service Management Connector に接続する前に、ハイブリッド接続が正常であることを確認してください。

次の図は、正常な接続の詳細を示しています。

![ハイブリッド接続のテスト](./media/log-analytics-itsmc/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-oms"></a>ServiceNow を OMS の IT Service Management Connector に接続する

次のセクションでは、ServiceNow 製品を OMS の IT Service Management Connector に接続する方法について詳しく説明します。

### <a name="prerequisites"></a>前提条件

以下の前提条件を満たしていることを確認してください。

- IT Service Management Connector がインストールされている。 詳細については、「[構成](log-analytics-itsmc-overview.md#configuration)」をご覧ください。
- ServiceNow のバージョンがサポートされている ( Fuji、Geneva、Helsinki)。

ServiceNow 管理者は、ServiceNow インスタンスで次のことを行う必要があります。
- ServiceNow 製品のクライアント ID とクライアント シークレットを生成します。 クライアント ID とシークレットを生成する方法については、「[OAuth Setup (OAuth のセットアップ)](http://wiki.servicenow.com/index.php?title=OAuth_Setup)」をご覧ください。
- Microsoft OMS 統合のユーザー アプリ (ServiceNow アプリ) をインストールします。 [詳細情報](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0 )
- インストールしたユーザー アプリの統合ユーザー ロールを作成します。 統合ユーザー ロールを作成する方法については、[こちら](#create-integration-user-role-in-servicenow-app)をご覧ください。


### <a name="connection-procedure"></a>**接続手順**

ServiceNow 接続を作成するには、次の手順に従います。

1. **[OMS]** > **[設定]** > **[接続されたソース]** に移動します。
2. **[ITSM Connector]** を選択し **[新しい接続の追加]** をクリックします。

    ![ServiceNow の接続](./media/log-analytics-itsmc/itsmc-servicenow-connection.png)

3. 以下の表に示す情報を入力し、**[保存]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **名前**   | IT Service Management Connector に接続する ServiceNow インスタンスの名前を入力します。  この名前は、ITSM の作業項目を構成したり、詳細な Log Analytics を確認したりするときに、あとで OMS 上で使用します。 |
| **接続の種類を選択**   | **[ServiceNow]** を選択します。 |
| **ユーザー名**   | IT Service Management Connector への接続をサポートする ServiceNow アプリ内で作成した統合ユーザー名を入力します。 詳細については、「[ServiceNow アプリのユーザー ロールの作成](#create-integration-user-role-in-servicenow-app)」をご覧ください。|
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注**: ユーザー名とパスワードは、認証トークンを生成するためにのみ使用されます。OMS サービス内には格納されません。  |
| **サーバー URL**   | IT Service Management Connector に接続する ServiceNow インスタンスの URL を入力します。 |
| **クライアント ID**   | 先ほど生成した、OAuth2 認証に使用するクライアント ID を入力します。  クライアント ID とシークレットを生成する方法の詳細については、「[OAuth Setup (OAuth のセットアップ)](http://wiki.servicenow.com/index.php?title=OAuth_Setup)」をご覧ください。 |
| **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
| **データ同期スコープ**   | IT Service Management Connector を介して OMS に同期する ServiceNow 作業項目を選択します。  選択した値は、Log Analytics にインポートされます。   **オプション:** インシデント、変更要求。|
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、OMS は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |


正常に接続すると、同期します。

- ServiceNow 接続で選択した作業項目は、OMS の Log Analytics にインポートされます。  IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。
- この ServiceNow インスタンスに、OMS アラートまたはログ検索からのインシデント、アラート、イベントを作成できます。  


詳細については、「[Create ITSM work items for OMS alerts (OMS アラートの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)」と「[Create ITSM work items from OMS logs (OMS ログからの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)」をご覧ください。

### <a name="create-integration-user-role-in-servicenow-app"></a>ServiceNow アプリで統合ユーザー ロールを作成する

次の手順に従います。

1.  [ServiceNow ストア](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.0)にアクセスし、**ServiceNow と Microsoft OMS 統合のユーザー アプリ**を ServiceNow インスタンスにインストールします。
2.  インストール後に、ServiceNow インスタンスの左側のナビゲーション バーで、Microsoft OMS インテグレーターを検索し選択します。  
3.  **[Installation Checklist] \(インストールのチェックリスト)** をクリックします。

    ユーザー ロールがまだ作成されていない場合は、**[完了していません]** と表示されます。

4.  **[Create integration user]\(統合ユーザーの作成\)** の横にあるテキスト ボックスに、OMS の IT Service Management Connector に接続できるユーザーのユーザー名を入力します。
5.  このユーザーのパスワードを入力し、**[OK]** をクリックします。  

>[!NOTE]

> これらの資格情報は、OMS で ServiceNow 接続を作成するために使用します。

新規に作成したユーザーには既定のロールが割り当てられ、表示されます。

既定のロール:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

ユーザーが正常に作成されると、**[Check Installation Checklist] \(インストールのチェックリストの確認)** のステータスが [完了] に移行し、そのアプリ用に作成したユーザー ロールの詳細が一覧表示されます。

> [!NOTE]

> OMS から ServiceNow 内に**アラート**と**イベント**を作成することをユーザーに許可するには:

> - ServiceNow インスタンスにイベント管理モジュールがインストールされていることを確認します。

> - 統合ユーザーに次のロールを追加します。
>      - evt_mgmt_integration
>      - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-oms"></a>Provance を OMS の IT Service Management Connector に接続する

次のセクションでは、Provance 製品を OMS の IT Service Management Connector に接続する方法について詳しく説明します。

### <a name="prerequisites"></a>前提条件

以下の前提条件を満たしていることを確認してください。

- IT Service Management Connector がインストールされている。 詳細については、「[構成](log-analytics-itsmc-overview.md#configuration)」をご覧ください。
- Provance アプリが Azure AD で登録されており、クライアント ID が使用可能である。 詳細については、[Active Directory 認証の構成方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)に関する記事をご覧ください。
- ユーザー ロールが管理者である。

### <a name="connection-procedure"></a>接続手順

Provance 接続を作成するには、次の手順に従います。

1. **[OMS]** > **[設定]** > **[接続されたソース]** に移動します。
2. **[ITSM Connector]** を選択し **[新しい接続の追加]** をクリックします。  

    ![Provance 接続](./media/log-analytics-itsmc/itsmc-provance-connection.png)
3. 以下の表に示す情報を入力し、**[保存]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **名前**   | IT Service Management Connector に接続する Provance インスタンスの名前を入力します。  この名前は、ITSM の作業項目を構成したり、詳細な Log Analytics を確認したりするときに、あとで OMS 上で使用します。 |
| **接続の種類を選択**   | **[Provance]** を選択します。 |
| **ユーザー名**   | IT Service Management Connector に接続できるユーザー名を入力します。    |
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注**: ユーザー名とパスワードは、認証トークンを生成するためにのみ使用されます。OMS サービス内には格納されません。|
| **サーバー URL**   | IT Service Management Connector に接続する Provance インスタンスの URL を入力します。 |
| **クライアント ID**   | Provance インスタンスで生成した、この接続を認証するためのクライアント ID を入力します。  クライアント ID の詳細については、[Active Directory 認証の構成方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)に関する記事をご覧ください。 |
| **データ同期スコープ**   | IT Service Management Connector を介して OMS に同期する Provance 作業項目を選択します。  これらの作業項目は、Log Analytics にインポートされます。   **オプション:** インシデント、変更要求。|
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、OMS は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。|

正常に接続すると、同期します。

- Provance 接続で選択した作業項目は、OMS の **Log Analytics** にインポートされます。  IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。
- この Provance インスタンスに、OMS アラートまたはログ検索からのインシデントとイベントを作成できます。

詳細については、「[Create ITSM work items for OMS alerts (OMS アラートの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)」と「[Create ITSM work items from OMS logs (OMS ログからの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)」をご覧ください。

## <a name="connect-cherwell-to-it-service-management-connector-in-oms"></a>Cherwell を OMS の IT Service Management Connector に接続する

次のセクションでは、Cherwell 製品を OMS の IT Service Management Connector に接続する方法について詳しく説明します。

### <a name="prerequisites"></a>前提条件

以下の前提条件を満たしていることを確認してください。

- IT Service Management Connector がインストールされている。 詳細については、「[構成](log-analytics-itsmc-overview.md#configuration)」をご覧ください。
- クライアント ID が生成されている。 詳細については、「[Cherwell のクライアント ID を生成する](#generate-client-id-for-cherwell)」をご覧ください。
- ユーザー ロールが管理者である。

### <a name="connection-procedure"></a>接続手順

Cherwell 接続を作成するには、次の手順に従います。

1. **[OMS]** >  **[設定]** > **[接続されたソース]** に移動します。
2. **[ITSM Connector]** を選択し **[新しい接続の追加]** をクリックします。  

    ![Cherwell ユーザー ID](./media/log-analytics-itsmc/itsmc-cherwell-connection.png)

3. 以下の表に示す情報を入力し、**[保存]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **名前**   | IT Service Management Connector に接続する Cherwell インスタンスの名前を入力します。  この名前は、ITSM の作業項目を構成したり、詳細な Log Analytics を確認したりするときに、あとで OMS 上で使用します。 |
| **接続の種類を選択**   | **[Cherwell]** を選択します。 |
| **ユーザー名**   | IT Service Management Connector に接続できる Cherwell ユーザー名を入力します。 |
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注**: ユーザー名とパスワードは、認証トークンを生成するためにのみ使用されます。OMS サービス内には格納されません。|
| **サーバー URL**   | IT Service Management Connector に接続する Cherwell インスタンスの URL を入力します。 |
| **クライアント ID**   | Cherwell インスタンスで生成した、この接続を認証するためのクライアント ID を入力します。   |
| **データ同期スコープ**   | IT Service Management Connector を介して同期する Cherwell 作業項目を選択します。  これらの作業項目は、Log Analytics にインポートされます。   **オプション:** インシデント、変更要求。 |
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、OMS は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

正常に接続すると、同期します。

- Cherwell 接続で選択した作業項目は、OMS の Log Analytics にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。
- OMS からこの Cherwell インスタンス内のインシデントとイベントを作成できます。 詳細については、「Create ITSM work items for OMS alerts (OMS アラートの ITSM 作業項目の作成)」と「Create ITSM work items from OMS logs (OMS ログからの ITSM 作業項目の作成)」をご覧ください。

詳細については、「[Create ITSM work items for OMS alerts (OMS アラートの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)」と「[Create ITSM work items from OMS logs (OMS ログからの ITSM 作業項目の作成)](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)」をご覧ください。

### <a name="generate-client-id-for-cherwell"></a>Cherwell のクライアント ID を生成する

Cherwell のクライアント ID とキーを生成するには、次の手順に従います。

1. Cherwell インスタンスに管理者としてログインします。
2. **[セキュリティ]** > **[Edit REST API client settings] \(REST API クライアント設定の編集)** の順にクリックします。
3. **[Create new client] \(クライアントの新規作成)** > **[クライアント シークレット]** の順に選択します。

    ![Cherwell ユーザー ID](./media/log-analytics-itsmc/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>次のステップ
 - [OMS アラートの ITSM 作業項目を作成する](log-analytics-itsmc-overview.md#create-itsm-work-items-for-oms-alerts)

 - [OMS ログから ITSM 作業項目を作成する](log-analytics-itsmc-overview.md#create-itsm-work-items-from-oms-logs)

- [接続に関する Log Analytics を表示する](log-analytics-itsmc-overview.md#using-the-solution)

