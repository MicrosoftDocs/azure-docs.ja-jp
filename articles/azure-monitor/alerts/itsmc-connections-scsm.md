---
title: SCSM を IT Service Management Connector に接続する
description: この記事では、SCSM を Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3f80c42be217d062510c687075cf46b4e7539419
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045345"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>System Center Service Manager を IT Service Management Connector に接続する

この記事では、System Center Service Manager インスタンスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。

以降のセクションでは、System Center Service Manager 製品を Azure の ITSMC に接続する方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認してください。

- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](./itsmc-definition.md)。
- Service Manager Web アプリケーション (Web アプリ) がデプロイされ構成されている。 Web アプリに関する情報については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
- ハイブリッド接続が作成および構成されている。 詳細情報: [ハイブリッド接続の構成](#configure-the-hybrid-connection)。
- サポートされている Service Manager のバージョン: 2012 R2 または 2016。
- ユーザー ロール: [上級オペレーター](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10))。
- 現在、Azure Monitor から送信されたアラートによって、System Center Service Manager インシデントが作成されます。

> [!NOTE]
> - ITSM Connector は、クラウドベースの ServiceNow インスタンスにのみ接続できます。 オンプレミスの ServiceNow インスタンスは現在サポートされていません。
> - アクションの一部としてカスタム [テンプレート](./itsmc-definition.md#define-a-template)を使用するには、SCSM テンプレートのパラメーター "ProjectionType" を "IncidentManagement!System.WorkItem.Incident.ProjectionType" にマップする必要があります

## <a name="connection-procedure"></a>接続手順

System Center Service Manager インスタンスを ITSMC に接続するには、次の手順を使用します。

1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2. **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。

    ![新しい接続](media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する System Center Service Manager インスタンスの名前を入力します。  この名前は、インスタンスの作業項目を構成したり、詳細なログ分析を確認したりするときに使用します。 |
| **パートナーの種類**   | **[System Center Service Manager]** を選択します。 |
| **[サーバー URL]**   | Service Manager Web アプリの URL を入力します。 Service Manager Web アプリの詳細については、[こちら](#create-and-deploy-service-manager-web-app-service)をご覧ください。
| **クライアント ID**   | Web アプリを認証するために (自動スクリプトを使用して) 生成したクライアント ID を入力します。 自動スクリプトの詳細については、[こちら](./itsmc-service-manager-script.md)をご覧ください。|
| **クライアント シークレット**   | この ID 用に生成したクライアント シークレットを入力します。   |
| **データの同期**   | ITSMC 経由で同期する Service Manager 作業項目を選択します。  これらの作業項目は、Log Analytics にインポートされます。 **オプション:** インシデント、変更要求。|
| **データ同期スコープ** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、Log Analytics は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

![Service Manager の接続](media/itsmc-connections-scsm/service-manager-connection.png)

**正常に接続され、同期された場合**:

- Service Manager で選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この Service Manager インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。

詳細情報:[Azure アラートから ITSM 作業項目を作成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web アプリ サービスを作成しデプロイする

オンプレミスの Service Manager をAzure の ITSMC に接続するために、Microsoft は GitHub 上に Service Manager Web アプリを作成しました。

お使いの Service Manager 用に ITSM Web アプリを設定するには、次の手順に従います。

- **Web アプリのデプロイ** – Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。 Microsoft が提供する[自動スクリプト](./itsmc-service-manager-script.md)を使用して、Web アプリをデプロイできます。
- **ハイブリッド接続の構成** - 手動で [ハイブリッド接続を構成](#configure-the-hybrid-connection)します。

### <a name="deploy-the-web-app"></a>Web アプリのデプロイ
[自動スクリプト](./itsmc-service-manager-script.md)を使用して、Web アプリをデプロイしてプロパティを設定し、Azure AD で認証します。

次の必要な情報を提供することにより、スクリプトを実行します。

- Azure サブスクリプションの詳細
- リソース グループ名
- 場所
- Service Manager サーバーの詳細 (サーバー名、ドメイン、ユーザー名とパスワード)
- Web アプリのサイト名のプレフィックス
- ServiceBus 名前空間。

スクリプトによって、指定した名前で (一意の名前にするために文字列をいくつか追加して) Web アプリが作成されます。 また、スクリプトによって、**Web アプリの URL**、**クライアント ID**、**クライアント シークレット** が生成されます。

これらの値を保存します。これらは、ITSMC との接続を作成するときに使用します。

**Web アプリのインストールを確認する**

1. **[Azure Portal]**  >  **[リソース]** に移動します。
2. Web アプリを選択し、 **[設定]**  >  **[アプリケーションの設定]** の順にクリックします。
3. スクリプトを使用してアプリをデプロイする際に入力した Service Manager インスタンスに関する情報を確認します。

## <a name="configure-the-hybrid-connection"></a>ハイブリッド接続の構成

Service Manager インスタンスを Azure の ITSMC に接続するハイブリッド接続を構成するには、次の手順を使用します。

1. **[Azure リソース]** の下にある Service Manager Web アプリを見つけます。
2. **[設定]**  >  **[ネットワーク]** の順にクリックします。
3. **[ハイブリッド接続]** の下にある **[ハイブリッド接続エンドポイントの構成]** をクリックします。

    ![ハイブリッド接続のネットワーク](media/itsmc-connections-scsm/itsmc-hybrid-connection-networking-and-end-points.png)
4. **[ハイブリッド接続]** ブレードで、 **[ハイブリッド接続の追加]** をクリックします。

    ![ハイブリッド接続の追加](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-add.png)

5. **[ハイブリッド接続の追加]** ブレードで、 **[ハイブリッド接続の新規作成]** をクリックします。

    ![新しいハイブリッド接続](media/itsmc-connections-scsm/itsmc-create-new-hybrid-connection.png)

6. 次の値を入力します。

   - **エンドポイント名**: 新しいハイブリッド接続の名前を指定します。
   - **エンドポイント ホスト**: Service Manager 管理サーバーの FQDN。
   - **エンドポイント ポート**: 「5724」と入力します。
   - **Servicebus 名前空間**: 既存の Servicebus 名前空間を使用するか、または新規に作成します。
   - **[場所]** : 場所を選択します。
   - **Name**:Servicebus を作成する場合は、その名前を指定します。

     ![ハイブリッド接続の値](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-values.png)
6. **[OK]** をクリックし、 **[ハイブリッド接続の作成]** ブレードを閉じ、ハイブリッド接続の作成を開始します。

    ハイブリッド接続が作成されると、ブレードに表示されます。

7. ハイブリッド接続を作成したら、その接続を選択し、 **[選択したハイブリッド接続の追加]** をクリックします。

    ![新しいハイブリッド接続](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>リスナーのセットアップの構成

ハイブリッド接続のリスナーのセットアップを構成するには、次の手順に従います。

1. **[ハイブリッド接続]** ブレードで、 **[接続マネージャーのダウンロード]** をクリックし、System Center Service Manager インスタンスが実行中のコンピューターに接続マネージャーをインストールします。

    インストールが完了すると、 **[開始]** メニューにある **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** オプションが使用可能になります。

2. **[Hybrid Connection Manager UI] \(ハイブリッド接続マネージャー UI)** をクリックすると、Azure の資格情報の入力を求められます。

3. Azure の資格情報でログインし、ハイブリッド接続を作成したサブスクリプションを選択します。

4. **[保存]** をクリックします。

ハイブリッド接続が正常に接続されています。

![正常なハイブリッド接続](media/itsmc-connections-scsm/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> ハイブリッド接続を作成したら、デプロイした Service Manager Web アプリにアクセスして接続を確認し、テストします。 Azure の ITSMC に接続しようとする前に、その接続が正常であることを確認してください。

次のサンプル画面は、正常な接続の詳細を示しています。

![ハイブリッド接続のテスト](media/itsmc-connections-scsm/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>次のステップ

* [ITSM Connector の概要](itsmc-overview.md)
* [Azure アラートから ITSM 作業項目を作成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)