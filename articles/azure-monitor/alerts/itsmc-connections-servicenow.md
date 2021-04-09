---
title: ServiceNow を IT Service Management Connector に接続する
description: ServiceNow を Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 51d16ae53e34dacfc41e3d31b01115f9c55ca9f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039548"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow を IT Service Management Connector に接続する

この記事では、ServiceNow インスタンスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成する方法について説明します。これにより、IT Service Management (ITSM) 作業項目を一元的に管理できます。

## <a name="prerequisites"></a>前提条件
接続に関して次の前提条件が満たされていることを確認します。

### <a name="itsmc-installation"></a>ITSMC のインストール

ITSMC のインストールの詳細については、[IT Service Management Connector ソリューションの追加](./itsmc-definition.md#add-it-service-management-connector)に関する記事を参照してください。

> [!NOTE]
> ITSMC では、ServiceNow からの公式の SaaS (サービスとしてのソフトウェア) オファリングのみがサポートされています。 ServiceNow のプライベート デプロイはサポートされていません。

### <a name="oauth-setup"></a>OAuth のセットアップ

ServiceNow でサポートされているバージョンとしては、Paris、Orlando、New York、Madrid、London、Kingston、Jakarta、Istanbul、Helsinki、および Geneva があります。

ServiceNow 管理者は、ServiceNow インスタンスのクライアント ID とクライアント シークレットを生成する必要があります。 必要に応じて、次の情報を参照してください。

- [Paris の OAuth の設定](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Orlando の OAuth の設定](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [New York の OAuth の設定](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Madrid の OAuth の設定](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [London の OAuth の設定](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Kingston の OAuth の設定](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Jakarta の OAuth の設定](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Istanbul の OAuth の設定](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Helsinki の OAuth の設定](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Geneva の OAuth の設定](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

OAuth の設定の一環として、次のことをお勧めします。

1. [クライアントがインスタンスにアクセスするためのエンドポイントを作成します](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html)。

1. 更新トークンの有効期間を更新します。

   1. **[ServiceNow]** ペインで「**System OAuth**」を検索し、 **[Application Registry]\(アプリケーション登録\)** を選択します。 
   1. 定義された OAuth の名前を選択し、 **[Refresh Token Lifespan]\(更新トークンの有効期限\)** を **7,776,000 秒** (90 日間) に変更します。 
   1. **[更新]** を選択します。 

1. 接続が維持されるように内部プロシージャを確立します。 想定されている更新トークンの有効期限切れの数日前に、次の操作を実行します。

   1. [ITSM コネクタ構成の手動での同期プロセスを完了します](./itsmc-resync-servicenow.md)。

   1. 古い更新トークンを失効させます。 セキュリティ上の理由から、古いキーを保持しないことをお勧めします。 
   
      1. **[ServiceNow]** ペインで「**System OAuth**」を検索し、 **[Manage Tokens]\(トークンの管理\)** を選択します。 
      
      1. OAuth 名と有効期限の日付にしたがって、一覧から古いトークンを選択します。

         ![OAuth のトークン一覧を示すスクリーンショット。](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. **[Revoke Access]\(アクセスの取り消し\)**  >  **[Revoke]\(取り消し\)** を選択します。

## <a name="install-the-user-app-and-create-the-user-role"></a>ユーザー アプリをインストールし、ユーザー ロールを作成する

次の手順を使用して、ServiceNow ユーザー アプリをインストールし、それに対する統合ユーザー ロールを作成します。 これらの資格情報は、Azure で ServiceNow 接続を作成するために使用します。

> [!NOTE]
> ITSMC では、ServiceNow ストアからダウンロードされる Microsoft Log Analytics 統合用の公式ユーザー アプリのみがサポートされます。 ITSMC では、ServiceNow 側でのコード インジェストや公式の ServiceNow ソリューションに含まれていないアプリケーションはサポートされていません。 

1. [ServiceNow ストア](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)にアクセスし、**ServiceNow と Microsoft OMS 統合用のユーザー アプリ** を ServiceNow インスタンスにインストールします。
   
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS) から Azure Monitor への現在進行中の移行の一環として、OMS は Log Analytics という名称になりました。     
2. インストール後に、ServiceNow インスタンスの左側のナビゲーション バーに移動し、**Microsoft OMS インテグレーター** を検索して選択します。  
3. **[Installation Checklist] \(インストールのチェックリスト\)** を選択します。

   ユーザー ロールがまだ作成されていないため、状態は **[Not complete]\(完了していません\)** と表示されます。

4. **[Create integration user]\(統合ユーザーの作成\)** の横にあるテキスト ボックスに、Azure の ITSMC に接続できるユーザーの名前を入力します。
5. このユーザーのパスワードを入力し、 **[OK]** を選択します。  

新しく作成されたユーザーが、既定のロールが割り当てられて表示されます。

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

ユーザーが正常に作成されると、 **[Check Installation Checklist] \(インストールのチェックリストの確認)** の状態が **[Completed]\(完了\)** に移行し、そのアプリ用に作成したユーザー ロールの詳細が一覧表示されます。

> [!NOTE]
> ITSM では、ServiceNow インスタンスに他のモジュールをインストールせずに、インシデントを ServiceNow に送信できます。 ServiceNow インスタンスで EventManagement モジュールを使用しているときに、コネクタを使用して ServiceNow でイベントまたはアラートを作成する場合は、次のロールを統合ユーザーに追加します。
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>接続の作成
ServiceNow 接続を作成するには、次の手順に従います。

> [!NOTE]
> 現在、Azure Monitor から送信されるアラートによって、ServiceNow でイベント、インシデント、またはアラートのいずれかの要素が作成されます。 

1. Azure portal で、 **[すべてのリソース]** に移動し、「**ServiceDesk(YourWorkspaceName)** 」を探します

2. **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![データ ソースの選択を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)

3. 右側のペインの上部で、 **[追加]** を選択します。

4. 以下の表に示す情報を入力し、 **[OK]** を選択します。

   | **フィールド** | **説明** |
   | --- | --- |
   | **Connection Name**   | ITSMC に接続する ServiceNow インスタンスの名前を入力します。 この名前は、後で ITSM 作業項目を構成したり、詳細な分析を表示したりするときに Log Analytics で使用します。 |
   | **パートナーの種類**   | **[ServiceNow]** を選択します。 |
   | **サーバー URL**   | ITSMC に接続する ServiceNow インスタンスの URL を入力します。 この URL は、 *.servicenow.com* というサフィックスが付いたサポートされている SaaS バージョンを指している必要があります (たとえば、`https://XXXXX.service-now.com/`)。|
   | **ユーザー名**   | ITSMC への接続をサポートするために ServiceNow アプリで作成した統合ユーザー名を入力します。|
   | **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注**:このユーザー名とパスワードは、認証トークンのみを生成するために使用されます。 ITSMC サービス内のどこにも格納されません。  |
   | **クライアント ID**   | 先ほど生成した、OAuth2 認証に使用するクライアント ID を入力します。 クライアント ID とシークレットの生成の詳細については、[OAuth の設定](https://old.wiki/index.php/OAuth_Setup)に関する記事を参照してください。 |
   | **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
   | **データ同期スコープ (日数)** | 過去何日分のデータを同期するのかについて、日数を入力します。 上限は 120 日間です。 |
   | **同期する作業項目**   | ITSMC 経由で Azure Log Analytics に同期する ServiceNow 作業項目を選択します。 選択した値は、Log Analytics にインポートされます。 オプションは、インシデントと変更要求です。|
   | **Create New Configuration Item in ITSM Product \(ITSM 製品内に新しい構成アイテムを作成する\)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、ITSMC によって、サポートされている ITSM システムに構成アイテム (存在しない場合) が作成されます。 既定では無効になっています。 |

![ServiceNow 接続を追加するためのボックスとオプションのスクリーンショット。](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

正常に接続され、同期された場合:

- ServiceNow インスタンスで選択した作業項目が Log Analytics にインポートされます。 **IT Service Management Connector** タイルで、これらの作業項目の概要を確認できます。

- この ServiceNow インスタンスでは、Log Analytics のアラートやログ レコード、または Azure アラートからインシデントを作成できます。

> [!NOTE]
> ServiceNow には、1 時間あたりの要求数のレート制限があります。 この制限を構成するには、ServiceNow インスタンスで **インバウンド REST API レート制限** を定義します。

## <a name="next-steps"></a>次のステップ

* [ITSM Connector の概要](itsmc-overview.md)
* [Azure アラートから ITSM 作業項目を作成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
