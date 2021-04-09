---
title: チュートリアル - 既存の同期済み AD フォレストに対して Azure AD Connect クラウド同期のパイロットを実施する
description: Azure Active Directory (Azure AD) Connect 同期を使用して既に同期されているテスト用 Active Directory フォレストに対して、クラウド同期のパイロットを実施する方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50eac71203a94ffb5c7dddc8995b56980c3f8815
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798716"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>既存の同期済み AD フォレストに対してクラウド同期のパイロットを実施する 

このチュートリアルでは、Azure Active Directory (Azure AD) Connect 同期を使用して既に同期されているテスト用 Active Directory フォレストに対して、クラウド同期のパイロットを実施する手順について説明します。

![作成](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>考慮事項
このチュートリアルを試す前に、次の点を考慮してください。
1. クラウド同期の基礎を理解しておきます。 
2. Azure AD Connect 同期バージョン 1.4.32.0 以降が実行されていることと、同期ルールが説明どおりに構成されていることを確認します。 パイロットを実施する際に、Azure AD Connect 同期のスコープからテスト OU またはグループを削除します。 スコープからオブジェクトを移動すると、それらのオブジェクトが Azure AD から削除されます。 ユーザー オブジェクトの場合、Azure AD 内のオブジェクトは論理的に削除されるので、復元できます。 グループ オブジェクトの場合は、Azure AD 内のオブジェクトが物理的に削除されるので、復元することはできません。 パイロット実施中の削除を防ぐ新しいリンク タイプが Azure AD Connect 同期に導入されました。 
3. クラウド同期でオブジェクトの完全一致が実行されるように、パイロット スコープ内のオブジェクトに ms-ds-consistencyGUID が設定されていることを確認します。 

   > [!NOTE]
   > Azure AD Connect 同期では、グループ オブジェクトの *ms-ds-consistencyGUID* が既定では設定されません。

4. これは高度なシナリオです。 このチュートリアルに記載の手順に正確に従うようにします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するために必要な前提条件を次に示します。
- Azure AD Connect 同期バージョン 1.4.32.0 以降がインストールされたテスト環境
- 同期のスコープに含まれ、かつパイロットを使用できる OU またはグループ。 少数のオブジェクトから始めることをお勧めします。
- プロビジョニング エージェントのホストとなる Windows Server 2012 R2 以降を実行するサーバー。  Azure AD Connect サーバーと同じサーバーは使用できません。
- Azure AD Connect 同期のソース アンカーが *objectGuid* または *ms-ds-consistencyGUID* であること。

## <a name="update-azure-ad-connect"></a>Azure AD Connect を更新する

[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) バージョン 1.4.32.0 以上を使用している必要があります。 Azure AD Connect 同期を更新するには、「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](../hybrid/how-to-upgrade-previous-version.md)」の手順を完了してください。  

## <a name="stop-the-scheduler"></a>スケジューラの停止
Azure AD Connect 同期は、オンプレミス ディレクトリで発生した変更を、スケジューラを使用して同期します。 カスタム ルールを変更したり追加したりするには、その作業を行っている間に同期が実行されないようスケジューラを無効にする必要があります。  次の手順に従います。

1.  Azure AD Connect 同期が実行されているサーバー上で、管理特権を使用して PowerShell を開きます。
2.  `Stop-ADSyncSyncCycle` を実行します。  Enter キーを押します。
3.  `Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。

>[!NOTE] 
>Azure AD Connect 同期用に独自のカスタム スケジューラを実行している場合は、スケジューラを無効にしてください。 

## <a name="create-custom-user-inbound-rule"></a>カスタム ユーザーの受信規則を作成する

 1. 以下に示すように、デスクトップのアプリケーション メニューから同期エディターを起動します。</br>
 ![同期規則エディター メニュー](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. [方向] のドロップダウン リストから **[受信]** を選択し、 **[新しいルールの追加]** をクリックします。
 ![[同期ルールの表示と管理] ウィンドウのスクリーンショット。[受信] と [新しいルールの追加] ボタンが選択されています。](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. **[説明]** ページで次のように入力し、 **[次へ]** をクリックします。

    **[名前]:** 規則にわかりやすい名前を付けます<br>
    **説明:** わかりやすい説明を追加します<br>
    **[Connected System]\(接続先システム\):** カスタム同期規則の作成対象となる AD コネクタを選択します<br>
    **[Connected System Object Type]\(接続先システム オブジェクトの種類\):** User<br>
    **[Metaverse Object Type]\(メタバース オブジェクトの種類\):** Person<br>
    **[リンクの種類]:** Join<br>
    **[優先順位]:** システム内で一意になる値を指定します<br>
    **[タグ]:** 空のままにします<br>
    ![[受信方向の同期規則の作成] の [説明] ページのスクリーンショット。値が選択されています。](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. **[Scoping filter]\(スコープ フィルター\)** ページで、パイロットのベースとなる OU またはセキュリティ グループを入力します。  OU でフィルター処理するには、識別名の OU 部分を追加します。 この規則は、その OU に属しているすべてのユーザーに適用されます。  したがって、DN の末尾が "OU=CPUsers,DC=contoso,DC=com" である場合は、次のフィルターを追加することになります。  続けて、 **[次へ]** をクリックします。 

    |ルール|属性|演算子|値|
    |-----|----|----|-----|
    |スコープ OU|DN|ENDSWITH|OU の識別名。|
    |スコープ グループ||ISMEMBEROF|セキュリティ グループの識別名。|

    ![[受信方向の同期規則の作成] の [Scoping filter]\(スコープ フィルター\) のスクリーンショット。スコープ フィルターの値が入力されています。](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. **[Join rules]\(結合規則\)** ページで、 **[次へ]** をクリックします。
 6. **[変換]** ページで、cloudNoFlow 属性に Constant transformation: flow True を追加します。 **[追加]** をクリックします。
 ![[受信方向の同期規則の作成] の [変換] のスクリーンショット。"Constant transformation" フローが追加されています。](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

オブジェクトの種類すべて (ユーザー、グループ、連絡先) に対して同じ手順を実行する必要があります。 構成済みの AD コネクタごと、または AD フォレストごとに手順を繰り返します。 

## <a name="create-custom-user-outbound-rule"></a>カスタム ユーザーの送信規則を作成する

 1. [方向] のドロップダウン リストから **[送信]** を選択し、 **[ルールの追加]** をクリックします。
 ![[送信] 方向を選択している画面のスクリーンショット。[新しいルールの追加] ボタンが強調表示されています。](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. **[説明]** ページで次のように入力し、 **[次へ]** をクリックします。

    **[名前]:** 規則にわかりやすい名前を付けます<br>
    **説明:** わかりやすい説明を追加します<br>
    **[Connected System]\(接続先システム\):** カスタム同期規則の作成対象となる Azure AD コネクタを選択します<br>
    **[Connected System Object Type]\(接続先システム オブジェクトの種類\):** User<br>
    **[Metaverse Object Type]\(メタバース オブジェクトの種類\):** Person<br>
    **[リンクの種類]:** JoinNoFlow<br>
    **[優先順位]:** システム内で一意になる値を指定します<br>
    **[タグ]:** 空のままにします<br>
    
    ![[説明] ページのスクリーンショット。プロパティが入力されています。](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. **[Scoping filter]\(スコープ フィルター\)** ページで、**cloudNoFlow** = **True** を選択します。 続けて、 **[次へ]** をクリックします。
 ![カスタム規則](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. **[Join rules]\(結合規則\)** ページで、 **[次へ]** をクリックします。
 5. **[変換]** ページで **[追加]** をクリックします。

オブジェクトの種類すべて (ユーザー、グループ、連絡先) に対して同じ手順を実行する必要があります。

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect プロビジョニング エージェントをインストールする
1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。  [AD と Azure の基本的な環境](tutorial-basic-ad-azure.md)に関するチュートリアルを使用している場合、これは CP1 になります。
2. [こちら](how-to-install.md#install-the-agent)に記載されている手順を使用して、Azure AD Connect クラウド プロビジョニング エージェントをダウンロードします。
3. Azure AD Connect クラウド同期 (AADConnectProvisioningAgent.Installer) を実行します。
3. スプラッシュ スクリーンでライセンス条項に **同意** し、 **[インストール]** をクリックします。</br>
![[Microsoft Azure A D Connect Provisioning Agent] スプラッシュ スクリーンのスクリーンショット。](media/how-to-install/install-1.png)</br>

4. この操作が完了すると、構成ウィザードが起動します。  Azure AD 全体管理者アカウントでサインインします。
5. **[Connect Active Directory]\(Active Directory の接続\)** 画面で **[ディレクトリの追加]** をクリックし、Active Directory 管理者アカウントを使用してサインインします。  この操作によってオンプレミス ディレクトリが追加されます。  **[次へ]** をクリックします。</br>
![[Active Directory の接続] 画面のスクリーンショット。ディレクトリの値が入力されています。](media/how-to-install/install-3a.png)</br>

6. **[構成が完了しました]** 画面で、 **[Confirm]\(確認\)** をクリックします。  この操作によって、エージェントが登録されて再起動されます。</br>
![[構成が完了しました] 画面のスクリーンショット。[確認] ボタンが選択されています。](media/how-to-install/install-4a.png)</br>

7. この操作が完了すると、"**Your agent configuration was successfully verified. (エージェントの構成が正常に検証されました。)** " という通知が表示されます。  **[終了]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install-5.png)</br>
8. まだ最初のスプラッシュ スクリーンが表示されている場合は、 **[閉じる]** をクリックします。

## <a name="verify-agent-installation"></a>エエージェントのインストールを確認する
エージェントの確認は、Azure portal のほか、エージェントが実行されているローカル サーバーで行います。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認
エージェントが Azure で表示されていることを確認するには、次の手順を実行します。

1. Azure portal にサインインします。
2. 左側の **[Azure Active Directory]** を選択して **[Azure AD Connect]** をクリックし、中央の **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  **[Azure AD Connect cloud sync]\(Azure AD Connect クラウド同期\)** 画面で、 **[すべてのエージェントの確認]** をクリックします。
![Azure AD のプロビジョニング](media/how-to-install/install-7.png)</br>
 
4. **[On-premises provisioning agents]\(オンプレミス プロビジョニング エージェント\)** 画面に、インストールしたエージェントが表示されます。  該当するエージェントが存在し、 **[無効]** としてマークされていることを確認します。  既定では、エージェントが無効になっています。![プロビジョニング エージェント](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>ローカル サーバーの場合
エージェントが実行されていることを確認するには、次の手順に従います。

1.  管理者アカウントでサーバーにログオンします。
2.  **[サービス]** を開きます。これには、そこに直接移動するか、スタート ボタンをクリックし、[ファイル名を指定して実行] で「Services.msc」と入力します。
3.  **[サービス]** に **[Microsoft Azure AD Connect Agent Updater]** と **[Microsoft Azure AD Connect Provisioning Agent]** が存在することと、その状態が **[実行中]** になっていることを確認します。
![サービス](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期を構成する
プロビジョニングを構成するには、次の手順に従います。

 1. Azure AD ポータルにサインインします。
 2. **[Azure Active Directory]** をクリックします。
 3. **[Azure AD Connect]** をクリックします。
 4. **[Manage cloud sync]\(クラウド同期の管理\)** を選択します。
 ![[Manage cloud sync]\(クラウド同期の管理\) リンクを示すスクリーンショット。](media/how-to-configure/manage-1.png)</br>
 5.  **[新しい構成]** をクリックします。
 ![[新しい構成] リンクが強調表示された [Azure AD Connect cloud sync]\(Azure AD Connect クラウド同期\) 画面のスクリーンショット。](media/tutorial-single-forest/configure-1.png)</br>
 6.  構成画面で、**通知用メール アドレス** を入力し、セレクターを **[有効]** に移動して、 **[保存]** をクリックします。
 ![メール アドレスの入力を終え、[有効にする] を選択した状態の [構成] 画面のスクリーンショット。](media/tutorial-single-forest/configure-2.png)</br>
 7. **[構成]** で **[すべてのユーザー]** を選択して、構成規則のスコープを変更します。
 ![[ユーザーのスコープを指定する] の横にある [すべてのユーザー] が強調表示された [構成] 画面のスクリーンショット。](media/how-to-configure/scope-2.png)</br>
 8. 右側で、先ほど作成した特定の OU "OU=CPUsers,DC=contoso,DC=com" が含まれるようにスコープを変更します。
 ![[ユーザーのスコープを指定する] 画面のスクリーンショット (作成した OU にスコープを変更したところ)。](media/tutorial-existing-forest/scope-2.png)</br>
 9.  **[完了]** 、 **[保存]** の順にクリックします。
 10. この時点でスコープは、1 つの組織単位に設定されている必要があります。 
 ![[ユーザーのスコープを指定する] の横にある [1 個の組織単位] が強調表示された [構成] 画面のスクリーンショット。](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>クラウド同期によってユーザーがプロビジョニングされていることを確認する
オンプレミスのディレクトリに存在していたユーザーが同期され、現在は Azure AD テナントに存在することを確認します。  これが完了するまでに数時間かかる場合があることに注意してください。  クラウド同期によってユーザーがプロビジョニングされていることを確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動し、Azure サブスクリプションがあるアカウントを使ってサインインします。
2. 左側の **[Azure Active Directory]** を選択します。
3. **[Azure AD Connect]** をクリックします。
4. **[Manage cloud sync]\(クラウド同期の管理\)** をクリックします。
5. **[ログ]** ボタンをクリックします。
6. ユーザー名を検索して、そのユーザーがクラウド同期によってプロビジョニングされていることを確認します。

さらに、ユーザーとグループが Azure AD に存在することを確認できます。

## <a name="start-the-scheduler"></a>スケジューラの開始
Azure AD Connect 同期は、オンプレミス ディレクトリで発生した変更を、スケジューラを使用して同期します。 規則の編集が完了したので、スケジューラを再起動してください。  次の手順に従います。

1.  Azure AD Connect 同期が実行されているサーバー上で、管理特権を使用して PowerShell を開きます。
2.  `Set-ADSyncScheduler -SyncCycleEnabled $true` を実行します。
3.  `Start-ADSyncSyncCycle` を実行します。  Enter キーを押します。  

>[!NOTE] 
>Azure AD Connect 同期用に独自のカスタム スケジューラを実行している場合は、スケジューラを有効にしてください。 

スケジューラが有効になると、参照属性 (例: manager) が更新されていない限り、Azure AD Connect によって、メタバースで `cloudNoFlow=true` が設定されたオブジェクトに対する変更のエクスポートが停止されます 。 オブジェクトに参照属性の更新がある場合、Azure AD Connect では `cloudNoFlow` シグナルを無視し、オブジェクトのすべての更新をエクスポートします。

## <a name="something-went-wrong"></a>問題が発生した場合
パイロットが正しく機能しない場合は、次の手順に従って Azure AD Connect 同期の設定に戻ることができます。
1.  Azure portal でプロビジョニング構成を無効にします。 
2.  同期規則エディター ツールを使用してクラウド プロビジョニング用に作成されたカスタム同期規則をすべて無効にします。 無効にすると、すべてのコネクタで完全同期が実行されます。



## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)

