---
title: Azure Active Directory Connect Health の操作
description: この記事では、Azure AD Connect Health のデプロイ後に実行できる追加操作について説明しています。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e41a00c1489d5b7b4822fec8bb4da35e917826
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686463"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health の操作
このトピックでは、Azure Active Directory (Azure AD) Connect Health を使用して実行できるさまざまな操作について説明します。

## <a name="enable-email-notifications"></a>電子メール通知を有効にする
ID インフラストラクチャの状態に問題があることをアラートが示した場合に、電子メール通知を送信するように Azure AD Connect Health サービスを構成することができます。 この通知は、アラートが生成されたときと、解決されたときに送信されます。

![Azure AD Connect Health の電子メール通知の設定のスクリーンショット](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> 既定では、電子メール通知は有効です。
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health の電子メール通知を有効にするには
1. 電子メール通知を受信するサービスの **[アラート]** ブレードを開きます。
2. 操作バーの **[通知設定]** をクリックします。
3. 電子メール通知スイッチで、 **[オン]** を選択します。
4. すべてのグローバル管理者が電子メール通知を受信するようにする場合は、チェック ボックスをオンにします。
5. 他の電子メール アドレスで電子メール通知を受信する必要がある場合は、 **[追加の電子メール受信者]** ボックスで指定します。 このリストから電子メール アドレスを削除するには、対象のエントリを右クリックして **[削除]** を選択します。
6. 変更を確定するには、 **[保存]** をクリックします。 保存した後にのみ変更は有効になります。

>[!NOTE] 
> バックエンド サービスで同期要求を処理する際に問題が発生した場合は、このサービスからご利用のテナントの管理者の連絡先の電子メール アドレスに、エラーの詳細が記載された通知電子メールが送信されます。 特定のケースではこれらのメッセージの量が非常に多くなるというフィードバックをお客様からいただいたので、これらのメッセージの送信方法を変更中です。 
>
> 同期エラーが発生するたびに毎回メッセージを送信するのではなく、バックエンド サービスから返されたすべてのエラーのダイジェストを毎日送信することになります。 これにより、お客様はより効率的な方法でこれらのエラーを処理し、重複するエラー メッセージの数を減らすことができます。

## <a name="delete-a-server-or-service-instance"></a>サーバーまたはサービス インスタンスを削除する

>[!NOTE] 
> 削除の手順を実行するには、Azure AD Premium ライセンスが必要です。

特定のサーバーを監視対象から除外しなければならない場合があります。 Azure AD Connect Health サービスから特定のサーバーを削除する場合に必要な知識をここで説明します。

サーバーを削除する際は次の点に注意してください。

* 削除したサーバーからはデータが一切収集されなくなります。 そのサーバーは監視サービスから除外されます。 削除されたサーバーについての新しいアラートや監視データ、使用状況分析データは表示できません。
* このアクションによって、Health エージェントがサーバーからアンインストールされることはありません。 Health エージェントをアンインストールせずにこの手順を実行した場合、そのサーバー上の Health エージェントに関連したエラーが表示されます。
* このサーバーから既に収集されたデータは削除されません。 収集済みのデータは、Azure のデータ リテンション期間ポリシーに従って削除されます。
* このアクションを実行した後、同じサーバーの監視を再開する場合、このサーバー上の Health エージェントをアンインストールしてから再インストールしてください。

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health サービスからサーバーを削除する

>[!NOTE] 
> 削除の手順を実行するには、Azure AD Premium ライセンスが必要です。

Active Directory フェデレーション サービス (AD FS) および Azure AD Connect (Sync) の Azure AD Connect Health の場合は、以下の手順に従います。

1. **[サーバーの一覧]** ブレードから、削除するサーバー名を選択して **[サーバー]** ブレードを開きます。
2. **[サーバー]** ブレードで、操作バーの **[削除]** をクリックします。
![Azure AD Connect Health でのサーバーの削除のスクリーンショット](./media/how-to-connect-health-operations/DeleteServer2.png)
3. 確認ボックスにサーバー名を入力して確定します。
4. **[削除]** をクリックします。

Azure Active Directory Domain Services の Azure AD Connect Health の場合は、以下の手順に従います。

1. **[ドメイン コントローラー]** ダッシュボードを開きます。
2. 削除するドメイン コントローラーを選択します。
3. 操作バーの **[選択した項目を削除]** をクリックします。
4. サーバーの削除アクションを確定します。
5. **[削除]** をクリックします。

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Azure AD Connect Health サービスからのサービス インスタンスの削除
特定のサービス インスタンスを削除しなければならない場合があります。 Azure AD Connect Health サービスから特定のサービス インスタンスを削除する場合に必要な知識をここで説明します。

サービス インスタンスを削除する際は次の点に注意してください。

* このアクションによって、現在のサービス インスタンスが監視サービスから削除されます。
* このサービス インスタンスの一部として監視されていたいずれのサーバーからも、Health エージェントがアンインストールされることや削除されることはありません。 Health エージェントをアンインストールせずにこの手順を実行した場合、そのサーバー上の Health エージェントに関連したエラーが表示されます。
* このサービス インスタンスからのデータはすべて、Azure のデータ リテンション期間ポリシーに従って削除されます。
* このアクションを実行した後、サービスの監視を再開する場合、すべてのサーバー上の Health エージェントをアンインストールしてから再インストールしてください。 このアクションを実行した後、同じサーバーの監視を再開する場合、そのサーバー上の Health エージェントをアンインストールしてから再インストールし、登録してください。

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health サービスからサービス インスタンスを削除するには
1. **[サービスの一覧]** ブレードから、削除するサービスの ID (ファーム名) を選択して、 **[サービス]** ブレードを開きます。 
2. **[サービス]** ブレードで、操作バーの **[削除]** をクリックします。 
![Azure AD Connect Health でのサービスの削除のスクリーンショット](./media/how-to-connect-health-operations/DeleteServer.png)
3. 確認ボックスにサービス名 (例: sts.contoso.com) を入力して確定します。
4. **[削除]** をクリックします。
   <br><br>

[//]: # (RBAC セクションの開始)
## <a name="manage-access-with-azure-rbac"></a>Azure RBAC を使用したアクセスの管理
Azure AD Connect Health の [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) は、グローバル管理者以外のユーザーおよびグループにアクセスを提供します。 Azure RBAC では、目的のユーザーおよびグループにロールを割り当て、ディレクトリ内のグローバル管理者を制限するメカニズムが用意されています。

### <a name="roles"></a>ロール
Azure AD Connect Health では、次の組み込みのロールがサポートされています。

| Role | アクセス許可 |
| --- | --- |
| 所有者 |所有者は、"*アクセスの管理*" (例: ユーザーやグループへのロールの割り当て)、ポータルからの "*すべての情報の表示*" (例: アラートの表示)、Azure AD Connect Health 内の "*設定の変更*" (例: 電子メール通知) を実行できます。 <br>このロールは、既定で Azure AD のグローバル管理者に割り当てられ、これを変更することはできません。 |
| Contributor |共同作成者は、ポータルからの "*すべての情報の表示*" (例: アラートの表示) と Azure AD Connect Health 内の "*設定の変更*" (例: 電子メール通知) を実行できます。 |
| Reader |閲覧者は、ポータルから Azure AD Connect Health 内の "*すべての情報の表示*" (例: アラートの表示) を実行できます。 |

上記以外のすべてのロール ("ユーザー アクセス管理者" や "DevTest Labs ユーザー" など) は、ポータル エクスペリエンスで利用できる場合でも、Azure AD Connect Health 内のアクセスに影響することはありません。

### <a name="access-scope"></a>アクセス スコープ
Azure AD Connect Health では、2 つのレベルのアクセス管理をサポートします。

* **すべてのサービス インスタンス**: これはほとんどの場合の推奨パスです。 Azure AD Connect Health によって監視されるすべてのロールの種類のすべてのサービス インスタンス (たとえば、AD FS ファーム) のアクセスを制御します。
* **サービス インスタンス**: 場合によっては、ロールの種類またはサービス インスタンスに基づいてアクセスを分離する必要があります。 この場合は、サービス インスタンス レベルでアクセスを管理できます。  

アクセス許可は、エンド ユーザーがディレクトリ レベルまたはサービス インスタンス レベルのいずれかでアクセス権がある場合に与えられます。

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>ユーザーまたはグループに Azure AD Connect Health へのアクセスを許可する
次の手順では、アクセスを許可する方法を説明します。
#### <a name="step-1-select-the-appropriate-access-scope"></a>手順 1: 適切なアクセス スコープを選択する
Azure AD Connect Health 内で "*すべてのサービス インスタンス*" レベルのユーザーにアクセスを許可するには、Azure AD Connect Health でメイン ブレードを開きます。<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>手順 2: ユーザーおよびグループを追加し、ロールを割り当てる
1. **[構成]** セクションで、 **[ユーザー]** をクリックします。<br>
   ![Azure AD Connect Health リソースのサイドバーのスクリーンショット](./media/how-to-connect-health-operations/startRBAC.png)
2. **[追加]** を選択します。
3. **[役割の選択]** ウィンドウで、ロール (たとえば、 **[所有者]** ) を選択します。<br>
   ![Azure AD Connect Health と Azure RBAC の構成メニューのスクリーンショット](./media/how-to-connect-health-operations/RBAC_add.png)
4. 対象となるユーザー/グループの名前または識別子を入力します。 1 つまたは複数のユーザー/グループを同時に選択できます。 **[選択]** をクリックします。
   ![Azure AD Connect Health と Azure のロール一覧のスクリーンショット](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. **[OK]** を選択します。<br>
6. ロールの割り当てが完了した後に、ユーザーとグループが一覧に表示されます。<br>
   ![新しいユーザーが強調表示されている、Azure AD Connect Health と Azure RBAC のスクリーンショット](./media/how-to-connect-health-operations/RBAC_user_list.png)

表示されているユーザーとグループは、割り当てられたロールに基づいてアクセスが許可されました。

> [!NOTE]
> * グローバル管理者は常にすべての操作にフル アクセスできますが、グローバル管理者のアカウントは上記のリストには表示されません。
> * "ユーザーの招待" 機能は、Azure AD Connect Health ではサポートされません。
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>手順 3: ブレードの場所をユーザーまたはグループと共有する
1. アクセス許可が割り当てられた後、ユーザーは[ここ](https://aka.ms/aadconnecthealth)に移動することで Azure AD Connect Health にアクセスできます。
2. ブレードで、ブレードやブレードのさまざまな部分をダッシュボードにピン留めできます。 **[ダッシュボードにピン留めする]** アイコンをクリックするだけです。<br>
   ![ピン アイコンが強調表示されている、Azure AD Connect Health と Azure RBAC のピン ブレードのスクリーンショット](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> 閲覧者ロールを割り当てられたユーザーは、Azure Marketplace から Azure AD Connect Health の拡張機能を取得することはできません。 このユーザーは、そのために必要な "作成" 操作を実行することができません。 ただし、このユーザーは、上記のリンクに移動してブレードにアクセスできます。 引き続き使用するために、ブレードをダッシュボードにピン留めできます。
>
>

### <a name="remove-users-or-groups"></a>ユーザーまたはグループを削除する
Azure AD Connect Health と Azure RBAC に追加されたユーザーまたはグループを削除することができます。 ユーザーまたはグループを右クリックして **[削除]** を選択するだけです。<br>
![[削除] が強調表示されている、Azure AD Connect Health と Azure RBAC のスクリーンショット](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC セクションの終了)

## <a name="next-steps"></a>次のステップ
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
* [AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](how-to-connect-health-adds.md)
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health のバージョン履歴](reference-connect-health-version-history.md)
