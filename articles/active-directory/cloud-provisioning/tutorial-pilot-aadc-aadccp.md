---
title: チュートリアル - 既存の同期済み AD フォレストに対して Azure AD Connect クラウド プロビジョニングのパイロットを実施する
description: チュートリアル。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 812f9bc71cde26b6f32a1259984bb0859ba49d54
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868764"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>既存の同期済み AD フォレストに対してクラウド プロビジョニングのパイロットを実施する 

このチュートリアルでは、既に Azure Active Directory (Azure AD) Connect 同期を使用して同期されたテスト用 Active Directory フォレストに対してクラウド プロビジョニングのパイロットを実施する手順について説明します。

![作成](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>考慮事項
このチュートリアルを試す前に、次の点を考慮してください。
1. クラウド プロビジョニングの基礎を理解しておくようにします。 
2. Azure AD Connect 同期バージョン 1.4.32.0 以降が実行されていることと、同期ルールが説明どおりに構成されていることを確認します。 パイロットを実施する際に、Azure AD Connect 同期のスコープからテスト OU またはグループを削除します。 スコープからオブジェクトを移動すると、それらのオブジェクトが Azure AD から削除されます。 ユーザー オブジェクトの場合、Azure AD 内のオブジェクトは論理的に削除されるので、復元できます。 グループ オブジェクトの場合は、Azure AD 内のオブジェクトが物理的に削除されるので、復元することはできません。 パイロット実施中の削除を防ぐ新しいリンク タイプが Azure AD Connect 同期に導入されました。 
3. クラウド プロビジョニングでオブジェクトの完全一致が実行されるよう、パイロット スコープ内のオブジェクトに ms-ds-consistencyGUID が事前設定されていることを確認します。 

   > [!NOTE]
   > Azure AD Connect 同期では、グループ オブジェクトの *ms-ds-consistencyGUID* が既定では設定されません。 [こちらのブログ記事](https://blogs.technet.microsoft.com/markrenoden/2017/10/13/choosing-a-sourceanchor-for-groups-in-multi-forest-sync-with-aad-connect/)に記載の手順に従って、グループ オブジェクトの *ms-ds-consistencyGUID* を設定してください。

4. これは高度なシナリオです。 このチュートリアルに記載の手順に正確に従うようにします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するために必要な前提条件を次に示します。
- Azure AD Connect 同期バージョン 1.4.32.0 以降がインストールされたテスト環境
- 同期のスコープに含まれ、かつパイロットを使用できる OU またはグループ。 少数のオブジェクトから始めることをお勧めします。
- プロビジョニング エージェントのホストとなる Windows Server 2012 R2 以降を実行するサーバー。  Azure AD Connect サーバーと同じサーバーは使用できません。
- AAD Connect 同期のソース アンカーが *objectGuid* または *ms-ds-consistencyGUID* であること。

## <a name="update-azure-ad-connect"></a>Azure AD Connect を更新する

[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) バージョン 1.4.32.0 以上を使用している必要があります。 Azure AD Connect 同期を更新するには、「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](../hybrid/how-to-upgrade-previous-version.md)」の手順を完了してください。  

## <a name="stop-the-scheduler"></a>スケジューラの停止
Azure AD Connect 同期は、オンプレミス ディレクトリで発生した変更を、スケジューラを使用して同期します。 カスタム ルールを変更したり追加したりするには、その作業を行っている間に同期が実行されないようスケジューラを無効にする必要があります。  次の手順に従います。

1.  Azure AD Connect 同期が実行されているサーバー上で、管理特権を使用して PowerShell を開きます。
2.  `Stop-ADSyncSyncCycle` を実行します。  Enter キーを押します。
3.  `Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。

>[!NOTE] 
>AAD Connect 同期用に独自のカスタム スケジューラを実行している場合は、スケジューラを無効にしてください。 

## <a name="create-custom-user-inbound-rule"></a>カスタム ユーザーの受信規則を作成する

 1. 以下に示すように、デスクトップのアプリケーション メニューから同期エディターを起動します。</br>
 ![同期規則エディター メニュー](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. [方向] のドロップダウン リストから **[受信]** を選択し、 **[新しいルールの追加]** をクリックします。
 ![カスタム規則](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. **[説明]** ページで次のように入力し、 **[次へ]** をクリックします。

    **[名前]:** 規則にわかりやすい名前を付けます<br>
    **説明:** わかりやすい説明を追加します<br> 
     **[Connected System]\(接続先システム\):** カスタム同期規則の作成対象となる AD コネクタを選択します<br>
    **[Connected System Object Type]\(接続先システム オブジェクトの種類\):** User<br>
    **[Metaverse Object Type]\(メタバース オブジェクトの種類\):** Person<br>
    **[リンクの種類]:** 結合<br>
    **[優先順位]:** システム内で一意になる値を指定します<br>
    **[タグ]:** 空のままにします<br>
    ![カスタム規則](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. **[Scoping filter]\(スコープ フィルター\)** ページで、パイロットのベースとなる OU またはセキュリティ グループを入力します。  OU でフィルター処理するには、識別名の OU 部分を追加します。 この規則は、その OU に属しているすべてのユーザーに適用されます。  したがって、DN の末尾が "OU=CPUsers,DC=contoso,DC=com" である場合は、次のフィルターを追加することになります。  その後、 **[次へ]** をクリックします。 

    |ルール|Attribute|演算子|値|
    |-----|----|----|-----|
    |スコープ OU|DN|ENDSWITH|OU の識別名。|
    |スコープ グループ||ISMEMBEROF|セキュリティ グループの識別名。|

    ![カスタム規則](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. **[Join rules]\(結合規則\)** ページで、 **[次へ]** をクリックします。
 6. **[変換]** ページで、cloudNoFlow 属性に Constant transformation: flow True を追加します。 **[追加]** をクリックします。
 ![カスタム規則](media/how-to-cloud-custom-user-rule/user4.png)</br>

オブジェクトの種類すべて (ユーザー、グループ、連絡先) に対して同じ手順を実行する必要があります。 構成済みの AD コネクタごと、または AD フォレストごとに手順を繰り返します。 

## <a name="create-custom-user-outbound-rule"></a>カスタム ユーザーの送信規則を作成する

 1. [方向] のドロップダウン リストから **[送信]** を選択し、 **[ルールの追加]** をクリックします。
 ![カスタム規則](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. **[説明]** ページで次のように入力し、 **[次へ]** をクリックします。

    **[名前]:** 規則にわかりやすい名前を付けます<br>
    **説明:** わかりやすい説明を追加します<br> 
     **[Connected System]\(接続先システム\):** カスタム同期規則の作成対象となる AAD コネクタを選択します<br>
    **[Connected System Object Type]\(接続先システム オブジェクトの種類\):** User<br>
    **[Metaverse Object Type]\(メタバース オブジェクトの種類\):** Person<br>
    **[リンクの種類]:** JoinNoFlow<br>
    **[優先順位]:** システム内で一意になる値を指定します<br>
    **[タグ]:** 空のままにします<br>
    
    ![カスタム規則](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. **[Scoping filter]\(スコープ フィルター\)** ページで、**cloudNoFlow** = **True** を選択します。 その後、 **[次へ]** をクリックします。
 ![カスタム規則](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. **[Join rules]\(結合規則\)** ページで、 **[次へ]** をクリックします。
 5. **[変換]** ページで **[追加]** をクリックします。

オブジェクトの種類すべて (ユーザー、グループ、連絡先) に対して同じ手順を実行する必要があります。

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect プロビジョニング エージェントをインストールする
1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。  [AD と Azure の基本的な環境](tutorial-basic-ad-azure.md)に関するチュートリアルを使用している場合、これは CP1 になります。
2. Azure AD Connect クラウド プロビジョニング エージェントを[こちら](https://go.microsoft.com/fwlink/?linkid=2109037)からダウンロードします。
3. Azure AD Connect クラウドプロビジョニング (AADConnectProvisioningAgent.Installer) を実行します。
3. スプラッシュ スクリーンでライセンス条項に**同意**し、 **[インストール]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install1.png)</br>

4. この操作が完了すると、構成ウィザードが起動します。  Azure AD 全体管理者アカウントでサインインします。
5. **[Connect Active Directory]\(Active Directory の接続\)** 画面で **[ディレクトリの追加]** をクリックし、Active Directory 管理者アカウントを使用してサインインします。  この操作によってオンプレミス ディレクトリが追加されます。  **[次へ]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install3.png)</br>

6. **[構成が完了しました]** 画面で、 **[Confirm]\(確認\)** をクリックします。  この操作によって、エージェントが登録されて再起動されます。</br>
![[ようこそ] 画面](media/how-to-install/install4.png)</br>

7. この操作が完了すると、"**Your agent configuration was successfully verified. (エージェントの構成が正常に検証されました。)** " という通知が表示されます。  **[終了]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install5.png)</br>
8. まだ最初のスプラッシュ スクリーンが表示されている場合は、 **[閉じる]** をクリックします。1. 使用するサーバーにエンタープライズ管理者のアクセス許可でサインインします。
2. Azure AD Connect クラウド プロビジョニング エージェントを[こちら](https://go.microsoft.com/fwlink/?linkid=2109037)からダウンロードします。
3. Azure AD Connect クラウドプロビジョニング (AADConnectProvisioningAgent.Installer) を実行します。
3. スプラッシュ スクリーンでライセンス条項に**同意**し、 **[インストール]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install1.png)</br>

4. この操作が完了すると、構成ウィザードが起動します。  Azure AD 全体管理者アカウントでサインインします。
5. **[Connect Active Directory]\(Active Directory の接続\)** 画面で **[ディレクトリの追加]** をクリックし、Active Directory 管理者アカウントを使用してサインインします。  この操作によってオンプレミス ディレクトリが追加されます。  **[次へ]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install3.png)</br>

6. **[構成が完了しました]** 画面で、 **[Confirm]\(確認\)** をクリックします。  この操作によって、エージェントが登録されて再起動されます。</br>
![[ようこそ] 画面](media/how-to-install/install4.png)</br>

7. この操作が完了すると、"**Your agent configuration was successfully verified. (エージェントの構成が正常に検証されました。)** " という通知が表示されます。  **[終了]** をクリックします。</br>
![[ようこそ] 画面](media/how-to-install/install5.png)</br>
8. まだ最初のスプラッシュ スクリーンが表示されている場合は、 **[閉じる]** をクリックします。

## <a name="verify-agent-installation"></a>エエージェントのインストールを確認する
エージェントの確認は、Azure portal のほか、エージェントが実行されているローカル サーバーで行います。

### <a name="azure-portal-agent-verification"></a>Azure portal でのエージェントの確認
エージェントが Azure で表示されていることを確認するには、次の手順を実行します。

1. Azure ポータルにサインインします。
2. 左側の **[Azure Active Directory]** を選択して **[Azure AD Connect]** をクリックし、中央の **[プロビジョニングの管理 (プレビュー)]** を選択します。</br>
![Azure Portal](media/how-to-install/install6.png)</br>

3.  **[Azure AD のプロビジョニング (プレビュー)]** 画面で **[すべてのエージェントの確認]** をクリックします。
![Azure AD のプロビジョニング](media/how-to-install/install7.png)</br>
 
4. **[On-premises provisioning agents]\(オンプレミス プロビジョニング エージェント\)** 画面に、インストールしたエージェントが表示されます。  該当するエージェントが存在し、 **[無効]** としてマークされていることを確認します。  既定では、エージェントが無効になっています。![プロビジョニング エージェント](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>ローカル サーバーの場合
エージェントが実行されていることを確認するには、次の手順に従います。

1.  管理者アカウントでサーバーにログオンします。
2.  **[サービス]** を開きます。これには、[サービス] に直接移動するか、スタート ボタンをクリックし、[ファイル名を指定して実行] で「Services.msc」と入力してください。
3.  **[サービス]** に **[Microsoft Azure AD Connect Agent Updater]** と **[Microsoft Azure AD Connect Provisioning Agent]** が存在することと、その状態が **[実行中]** になっていることを確認します。
![サービス](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect クラウド プロビジョニングを構成する
プロビジョニングを構成するには、次の手順に従います。

 1. Azure AD ポータルにサインインします。
 2. **[Azure Active Directory]** をクリックします。
 3. **[Azure AD Connect]** をクリックします。
 4. **[プロビジョニングの管理 (プレビュー)]** を選択します。
 ![](media/how-to-configure/manage1.png)</br>
 5.  **[新しい構成]** をクリックします。
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  構成画面で、**通知用メール アドレス**を入力し、セレクターを **[有効]** に移動して、 **[保存]** をクリックします。
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. **[構成]** で **[すべてのユーザー]** を選択して、構成規則のスコープを変更します。
 ![](media/how-to-configure/scope2.png)</br>
 8. 右側で、先ほど作成した特定の OU "OU=CPUsers,DC=contoso,DC=com" が含まれるようにスコープを変更します。
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  **[完了]** 、 **[保存]** の順にクリックします。
 10. この時点でスコープは、1 つの組織単位に設定されている必要があります。 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>クラウド プロビジョニングによってユーザーがプロビジョニングされていることを確認する
オンプレミスのディレクトリに存在していたユーザーが同期され、現在は Azure AD テナントに存在することを確認します。  これが完了するまでに数時間かかる場合があることに注意してください。  クラウド プロビジョニングによってユーザーがプロビジョニングされていることを確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動し、Azure サブスクリプションがあるアカウントを使ってサインインします。
2. 左側の **[Azure Active Directory]** を選択します。
3. **[Azure AD Connect]** をクリックします。
4. **[プロビジョニングの管理 (プレビュー)]** をクリックします。
5. **[ログ]** ボタンをクリックします。
6. ユーザー名を検索して、そのユーザーがクラウド プロビジョニングによってプロビジョニングされていることを確認します。

さらに、ユーザーとグループが Azure AD に存在することを確認できます。

## <a name="start-the-scheduler"></a>スケジューラの開始
Azure AD Connect 同期は、オンプレミス ディレクトリで発生した変更を、スケジューラを使用して同期します。 規則の編集が完了したので、スケジューラを再起動してください。  次の手順に従います。

1.  Azure AD Connect 同期が実行されているサーバー上で、管理特権を使用して PowerShell を開きます。
2.  `Set-ADSyncScheduler -SyncCycleEnabled $true` を実行します。
3.  `Start-ADSyncSyncCycle` を実行します。  Enter キーを押します。  

>[!NOTE] 
>AAD Connect 同期用に独自のカスタム スケジューラを実行している場合は、スケジューラを有効にしてください。 

## <a name="something-went-wrong"></a>問題が発生した場合
パイロットが正しく機能しない場合は、次の手順に従って Azure AD Connect 同期の設定に戻ることができます。
1.  Azure portal でプロビジョニング構成を無効にします。 
2.  同期規則エディター ツールを使用してクラウド プロビジョニング用に作成されたカスタム同期規則をすべて無効にします。 無効にすると、すべてのコネクタで完全同期が実行されます。

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Azure AD Connect 同期でパイロット OU を除外するよう設定する
パイロット OU のユーザーがクラウド プロビジョニングによって正しく管理されていることを確認したら、先ほど作成したパイロット OU を除外するように Azure AD Connect を再構成することができます。  以後これらのユーザーの同期は、クラウド プロビジョニング エージェントによって処理されます。  Azure AD Connect のスコープを設定するには、次の手順を使用します。

 1. Azure AD Connect が実行されているサーバー上で、Azure AD Connect アイコンをダブルクリックします。
 2. **[構成]** をクリックします
 3. **[同期オプションのカスタマイズ]** を選択し、[次へ] をクリックします。
 4. Azure AD にサインインし、 **[次へ]** をクリックします。
 5. **[ディレクトリの接続]** 画面で、 **[次へ]** をクリックします。
 6. **[ドメインと OU のフィルタリング]** 画面で、 **[選択したドメインと OU の同期]** を選択します。
 7. 実際のドメインを展開し、**CPUsers** OU の**選択を解除**します。  **[次へ]** をクリックします。
![scope](media/tutorial-existing-forest/scope1.png)</br>
 9. **[オプション機能]** 画面で、 **[次へ]** をクリックします。
 10. **[構成の準備完了]** 画面で、 **[構成]** をクリックします。
 11. 完了したら **[終了]** をクリックします。 

## <a name="next-steps"></a>次の手順 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

