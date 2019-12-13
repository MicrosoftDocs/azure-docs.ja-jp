---
title: Azure Log Analytics の IT Service Management Connector とのサポートされている接続 | Microsoft Docs
description: この記事では、ITSM 製品/サービスを Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: JYOTHIRMAISURI
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: d800f20826723d3a626d9a0f5f83664927c1185c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927595"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 製品/サービスを IT Service Management Connector に追加する
この記事では、ITSM 製品/サービスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。 ITSMC の詳細については、[概要](../../azure-monitor/platform/itsmc-overview.md)に関する記事をご覧ください。

次の ITSM 製品/サービスがサポートされています。 ITSMC に製品を接続する方法に関する詳細な情報を表示する製品を選択してください。

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM Connector は、クラウドベースの ServiceNow インスタンスにのみ接続できます。 オンプレミスの ServiceNow インスタンスは現在サポートされていません。

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>System Center Service Manager を Azure の IT Service Management Connector に接続する

以降のセクションでは、System Center Service Manager 製品を Azure の ITSMC に接続する方法について詳しく説明します。

### <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認してください。

- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- Service Manager Web アプリケーション (Web アプリ) がデプロイされ構成されている。 Web アプリに関する情報については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
- ハイブリッド接続が作成および構成されている。 詳細情報: [ハイブリッド接続の構成](#configure-the-hybrid-connection)。
- サポートされている Service Manager のバージョン: 2012 R2 または 2016。
- ユーザー ロール: [上級オペレーター](https://technet.microsoft.com/library/ff461054.aspx)。

### <a name="connection-procedure"></a>接続手順

System Center Service Manager インスタンスを ITSMC に接続するには、次の手順を使用します。

1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2.  **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。

    ![新しい接続](media/itsmc-connections/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。

> [!NOTE]
> 
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する System Center Service Manager インスタンスの名前を入力します。  この名前は、インスタンスの作業項目を構成したり、詳細なログ分析を確認したりするときに使用します。 |
| **パートナーの種類**   | **[System Center Service Manager]** を選択します。 |
| **サーバー URL**   | Service Manager Web アプリの URL を入力します。 Service Manager Web アプリの詳細については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
| **クライアント ID**   | Web アプリを認証するために (自動スクリプトを使用して) 生成したクライアント ID を入力します。 自動スクリプトの詳細については、[こちら](../../azure-monitor/platform/itsmc-service-manager-script.md)をご覧ください。|
| **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
| **データの同期**   | ITSMC 経由で同期する Service Manager 作業項目を選択します。  これらの作業項目は、Log Analytics にインポートされます。 **オプション:** インシデント、変更要求。|
| **データ同期スコープ** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、Log Analytics は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

![Service Manager の接続](media/itsmc-connections/service-manager-connection.png)

**正常に接続され、同期された場合**:

- Service Manager で選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この Service Manager インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。


詳細情報:[Azure アラートから ITSM 作業項目を作成する](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web アプリ サービスを作成しデプロイする

オンプレミスの Service Manager をAzure の ITSMC に接続するために、Microsoft は GitHub 上に Service Manager Web アプリを作成しました。

お使いの Service Manager 用に ITSM Web アプリを設定するには、次の手順に従います。

- **Web アプリのデプロイ** – Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。 Microsoft が提供する[自動スクリプト](../../azure-monitor/platform/itsmc-service-manager-script.md)を使用して、Web アプリをデプロイできます。
- **ハイブリッド接続の構成** - 手動で[ハイブリッド接続を構成](#configure-the-hybrid-connection)します。

#### <a name="deploy-the-web-app"></a>Web アプリのデプロイ
[自動スクリプト](../../azure-monitor/platform/itsmc-service-manager-script.md)を使用して、Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。

次の必要な情報を提供することにより、スクリプトを実行します。

- Azure サブスクリプションの詳細
- リソース グループ名
- Location
- Service Manager サーバーの詳細 (サーバー名、ドメイン、ユーザー名とパスワード)
- Web アプリのサイト名のプレフィックス
- ServiceBus 名前空間。

スクリプトによって、指定した名前で (一意の名前にするために文字列をいくつか追加して) Web アプリが作成されます。 また、スクリプトによって、**Web アプリの URL**、**クライアント ID**、**クライアント シークレット**が生成されます。

これらの値を保存します。これらは、ITSMC との接続を作成するときに使用します。

**Web アプリのインストールを確認する**

1. **[Azure Portal]**  >  **[リソース]** に移動します。
2. Web アプリを選択し、 **[設定]**  >  **[アプリケーションの設定]** の順にクリックします。
3. スクリプトを使用してアプリをデプロイする際に入力した Service Manager インスタンスに関する情報を確認します。

### <a name="configure-the-hybrid-connection"></a>ハイブリッド接続の構成

Service Manager インスタンスを Azure の ITSMC に接続するハイブリッド接続を構成するには、次の手順を使用します。

1. **[Azure リソース]** の下にある Service Manager Web アプリを見つけます。
2. **[設定]**  >  **[ネットワーク]** の順にクリックします。
3. **[ハイブリッド接続]** の下にある **[ハイブリッド接続エンドポイントの構成]** をクリックします。

    ![ハイブリッド接続のネットワーク](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. **[ハイブリッド接続]** ブレードで、 **[ハイブリッド接続の追加]** をクリックします。

    ![ハイブリッド接続の追加](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. **[ハイブリッド接続の追加]** ブレードで、 **[ハイブリッド接続の新規作成]** をクリックします。

    ![新しいハイブリッド接続](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. 次の値を入力します。

   - **エンドポイント名**: 新しいハイブリッド接続の名前を指定します。
   - **エンドポイント ホスト**: Service Manager 管理サーバーの FQDN。
   - **エンドポイント ポート**: 「5724」と入力します。
   - **Servicebus 名前空間**: 既存の Servicebus 名前空間を使用するか、または新規に作成します。
   - **[場所]** : 場所を選択します。
   - **名前**: Servicebus を作成する場合は、その名前を指定します。

     ![ハイブリッド接続の値](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. **[OK]** をクリックし、 **[ハイブリッド接続の作成]** ブレードを閉じ、ハイブリッド接続の作成を開始します。

    ハイブリッド接続が作成されると、ブレードに表示されます。

7. ハイブリッド接続を作成したら、その接続を選択し、 **[選択したハイブリッド接続の追加]** をクリックします。

    ![新しいハイブリッド接続](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>リスナーのセットアップの構成

ハイブリッド接続のリスナーのセットアップを構成するには、次の手順に従います。

1. **[ハイブリッド接続]** ブレードで、 **[接続マネージャーのダウンロード]** をクリックし、System Center Service Manager インスタンスが実行中のコンピューターに接続マネージャーをインストールします。

    インストールが完了すると、 **[開始]** メニューにある **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** オプションが使用可能になります。

2. **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** をクリックすると、Azure の資格情報の入力を求められます。

3. Azure の資格情報でログインし、ハイブリッド接続を作成したサブスクリプションを選択します。

4. **[Save]** をクリックします。

ハイブリッド接続が正常に接続されています。

![正常なハイブリッド接続](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> ハイブリッド接続を作成したら、デプロイした Service Manager Web アプリにアクセスして接続を確認し、テストします。 Azure の ITSMC に接続しようとする前に、その接続が正常であることを確認してください。

次のサンプル画面は、正常な接続の詳細を示しています。

![ハイブリッド接続のテスト](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow を Azure の IT Service Management Connector に接続する

以降のセクションでは、ServiceNow 製品を Azure の ITSMC に接続する方法について詳細に説明します。

### <a name="prerequisites"></a>前提条件
次の前提条件が満たされていることを確認してください。
- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- ServiceNow でサポートされているバージョン: Madrid、London、Kingston、Jakarta、Istanbul、Helsinki、Geneva。

**ServiceNow 管理者は、ServiceNow インスタンスで次のことを行う必要があります。**
- ServiceNow 製品のクライアント ID とクライアント シークレットを生成します。 クライアント ID とシークレットを生成する方法については、必要に応じて以下の情報をご覧ください。

    - [New York の OAuth の設定](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Madrid の OAuth の設定](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [London の OAuth の設定](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Kingston の OAuth の設定](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Jakarta の OAuth の設定](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Istanbul の OAuth の設定](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Helsinki の OAuth の設定](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Geneva の OAuth の設定](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Microsoft Log Analytics 統合のユーザー アプリ (ServiceNow アプリ) をインストールします。 [詳細情報](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )。
- インストールしたユーザー アプリの統合ユーザー ロールを作成します。 統合ユーザー ロールを作成する方法については、[こちら](#create-integration-user-role-in-servicenow-app)をご覧ください。

### <a name="connection-procedure"></a>**接続手順**
ServiceNow 接続を作成するには、次の手順に従います。


1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2.  **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。
    ![新しい接続](media/itsmc-connections/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。


> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する ServiceNow インスタンスの名前を入力します。  この名前は、ITSM の作業項目を構成したり、詳細なログ分析を確認したりするときに、後で Log Analytics 上で使用します。 |
| **パートナーの種類**   | **[ServiceNow]** を選択します。 |
| **ユーザー名**   | ITSMC への接続をサポートするために ServiceNow アプリで作成した統合ユーザー名を入力します。 詳細情報: [ServiceNow アプリのユーザー ロールの作成](#create-integration-user-role-in-servicenow-app)に関するページ。|
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **メモ**:ユーザー名とパスワードは、認証トークンを生成するためにのみ使用され、ITSMC サービス内のどこにも格納されません。  |
| **サーバー URL**   | ITSMC に接続する ServiceNow インスタンスの URL を入力します。 |
| **クライアント ID**   | 先ほど生成した、OAuth2 認証に使用するクライアント ID を入力します。  クライアント ID とシークレットの生成の詳細については、 [OAuth のセットアップ](https://wiki.servicenow.com/index.php?title=OAuth_Setup)に関するページを参照してください。 |
| **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
| **データ同期スコープ**   | ITSMC 経由で Azure Log Analytics に同期する ServiceNow 作業項目を選択します。  選択した値は、ログ分析にインポートされます。   **オプション:** インシデント、変更要求。|
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、ITSMC は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

![ServiceNow の接続](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**正常に接続され、同期された場合**:

- ServiceNow インスタンスで選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この ServiceNow インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。

詳細情報:[Azure アラートから ITSM 作業項目を作成する](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="create-integration-user-role-in-servicenow-app"></a>ServiceNow アプリで統合ユーザー ロールを作成する

次の手順に従います。

1. [ServiceNow ストア](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)にアクセスし、**ServiceNow と Microsoft OMS 統合のユーザー アプリ**を ServiceNow インスタンスにインストールします。
   
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS) から Azure Monitor への現在進行中の移行の一環として、OMS は Log Analytics という名称になります。     
2. インストール後に、ServiceNow インスタンスの左側のナビゲーション バーで、Microsoft OMS インテグレーターを検索し選択します。  
3. **[Installation Checklist] \(インストールのチェックリスト)** をクリックします。

   ユーザー ロールがまだ作成されていない場合は、 **[完了していません]** と表示されます。

4. **[Create integration user]\(統合ユーザーの作成\)** の横にあるテキスト ボックスに、Azure の ITSMC に接続できるユーザーのユーザー名を入力します。
5. このユーザーのパスワードを入力し、 **[OK]** をクリックします。  

> [!NOTE]
> 
> これらの資格情報は、Azure で ServiceNow 接続を作成するために使用します。

新規に作成したユーザーには既定のロールが割り当てられ、表示されます。

**既定のロール**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

ユーザーが正常に作成されると、 **[Check Installation Checklist] \(インストールのチェックリストの確認)** のステータスが [完了] に移行し、そのアプリ用に作成したユーザー ロールの詳細が一覧表示されます。

> [!NOTE]
> 
> ITSM Connector では、ServiceNow インスタンスに他のモジュールをインストールせずに、インシデントを ServiceNow に送信することができます。 ServiceNow インスタンスで EventManagement モジュールを使用しているときに、コネクタを使用して ServiceNow でイベントまたはアラートを作成する場合は、次のロールを統合ユーザーに追加します。
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Provance を Azure の IT Service Management Connector に接続する

以降のセクションでは、Provance 製品を Azure の ITSMC に接続する方法について詳細に説明します。


### <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認してください。


- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- Provance アプリが Azure AD で登録されており、クライアント ID が使用可能である。 詳細については、[Active Directory 認証の構成方法](../../app-service/configure-authentication-provider-aad.md)に関する記事をご覧ください。

- ユーザー ロール: 管理者。

### <a name="connection-procedure"></a>接続手順

Provance 接続を作成するには、次の手順に従います。

1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2.  **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。
    ![新しい接続](media/itsmc-connections/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。

> [!NOTE]
> 
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する Provance インスタンスの名前を入力します。  この名前は、後で ITSM の作業項目を構成したり、詳細なログ分析を確認したりするときに使用します。 |
| **パートナーの種類**   | **[Provance]** を選択します。 |
| **ユーザー名**   | ITSMC に接続できるユーザー名を入力します。    |
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注:** ユーザー名とパスワードは、認証トークンを生成するためにのみ使用され、ITSMC サービス内のどこにも格納されません。|
| **サーバー URL**   | ITSMC に接続する Provance インスタンスの URL を入力します。 |
| **クライアント ID**   | Provance インスタンスで生成した、この接続を認証するためのクライアント ID を入力します。  クライアント ID の詳細については、[Active Directory 認証の構成方法](../../app-service/configure-authentication-provider-aad.md)に関する記事をご覧ください。 |
| **データ同期スコープ**   | ITSMC 経由で Azure Log Analytics に同期する Provance 作業項目を選択します。  これらの作業項目は、ログ分析にインポートされます。   **オプション:**  インシデント、変更要求。|
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、ITSMC は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。|

![Provance 接続](media/itsmc-connections/itsm-connections-provance-latest.png)

**正常に接続され、同期された場合**:

- この Provance インスタンスで選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この Provance インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。

詳細情報:[Azure アラートから ITSM 作業項目を作成する](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cherwell を Azure の IT Service Management Connector に接続する

以降のセクションでは、Cherwell 製品を Azure の ITSMC に接続する方法について詳細に説明します。

### <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認してください。

- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)。
- クライアント ID が生成されている。 詳細情報: [Cherwell のクライアント ID を生成する](#generate-client-id-for-cherwell)。
- ユーザー ロール: 管理者。

### <a name="connection-procedure"></a>接続手順

Provance 接続を作成するには、次の手順に従います。

1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2.  **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。
    ![新しい接続](media/itsmc-connections/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。

> [!NOTE]
> 
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する Cherwell インスタンスの名前を入力します。  この名前は、後で ITSM の作業項目を構成したり、詳細なログ分析を確認したりするときに使用します。 |
| **パートナーの種類**   | **[Cherwell]** を選択します。 |
| **ユーザー名**   | ITSMC に接続できる Cherwell ユーザー名を入力します。 |
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注:** ユーザー名とパスワードは、認証トークンを生成するためにのみ使用され、ITSMC サービス内のどこにも格納されません。|
| **サーバー URL**   | ITSMC に接続する Cherwell インスタンスの URL を入力します。 |
| **クライアント ID**   | Cherwell インスタンスで生成した、この接続を認証するためのクライアント ID を入力します。   |
| **データ同期スコープ**   | ITSMC 経由で同期する Cherwell 作業項目を選択します。  これらの作業項目は、ログ分析にインポートされます。   **オプション:** インシデント、変更要求。 |
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、ITSMC は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |


![Provance 接続](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**正常に接続され、同期された場合**:

- この Cherwell インスタンスで選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この Cherwell インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。

詳細情報:[Azure アラートから ITSM 作業項目を作成する](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)。

### <a name="generate-client-id-for-cherwell"></a>Cherwell のクライアント ID を生成する

Cherwell のクライアント ID とキーを生成するには、次の手順に従います。

1. Cherwell インスタンスに管理者としてログインします。
2. **[セキュリティ]**  >  **[Edit REST API client settings] \(REST API クライアント設定の編集)** の順にクリックします。
3. **[Create new client] \(クライアントの新規作成)**  >  **[クライアント シークレット]** の順に選択します。

    ![Cherwell ユーザー ID](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>次の手順
 - [Azure アラートから ITSM 作業項目を作成する](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
