---
title: Azure Lab Services を使用してクラスルーム ラボを設定する | Microsoft Docs
description: このチュートリアルでは、Azure Lab Services を使用し、クラスの学生が使用する仮想マシンでクラスルーム ラボを設定します。
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787420"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>チュートリアル:クラスルーム ラボを設定する 
このチュートリアルでは、クラスルームで学生が使用する仮想マシンで、クラスルーム ラボを設定します。  

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * クラスルーム ラボを作成する
> * ラボへのユーザーの追加
> * ラボのスケジュールを設定する
> * 招待メールを学生に送信する

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、自分のクラス用の仮想マシンを含むラボをセットアップします。 ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおいて、所有者、ラボの作成者、または共同作成者ロールのメンバーである必要があります。 ラボ アカウントを作成するために使用したアカウントは、所有者ロールに自動的に追加されます。 そのためクラスルーム ラボは、ラボ アカウントの作成に使用したユーザー アカウントを使って作成できます。 

Azure Lab Services を使用する際の一般的なワークフローは次のとおりです。

1. ラボ アカウントの作成者が、他のユーザーを **ラボの作成者** ロールに追加します。 たとえば、教師が自分の担当するクラスのラボを作成できるよう、ラボ アカウントの作成者または管理者が **ラボの作成者** ロールに教師を追加します。 
2. その後、教師がそのクラス用の VM を含むラボを作成し、クラスの学生に登録リンクを送信します。 
3. 学生は、教師から受け取った登録リンクを使用してラボに登録します。 登録された学生は、ラボの VM を使用してクラスの課題や宿題をすることができます。 

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する
この手順では、自分のクラスのラボを Azure に作成します。 

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New lab]\(新しいラボ\)** を選択します。 
    
    ![[New lab]\(新しいラボ\) ボタンが選択されている "Azure Lab Services" を示すスクリーンショット。](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの **名前** を指定し、 **[次へ]** を選択します。  

        ![クラスルーム ラボを作成する](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. **[Virtual machine credentials]\(仮想マシンの資格情報\)** ページで、ラボ内のすべての VM 用の既定の資格情報を指定します。 ユーザーの **名前** と **パスワード** を指定し、 **[次へ]** を選択します。  

        ![新しいラボのウィンドウ](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **[ラボ ポリシー]** ページで **[完了]** を選択します。 

        ![[Quota for each user]\(各ユーザーのクォータ\)](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. テンプレート VM の作成の状態を示す次の画面が表示されます。 この操作には最大 20 分かかります。 

    ![テンプレート VM の作成の状態](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **[テンプレート]** ページで、次の手順を実行します。これらの手順は、チュートリアルでは **省略可能** です。

    1. **[接続]** を選択してテンプレート VM に接続します。 Linux のテンプレート VM の場合は、SSH と RDP のどちらを使用して接続するかを選択します (RDP が有効な場合)。
    3. クラスに必要なソフトウェアをテンプレート VM にインストールして構成します。 
    4. テンプレート VM を **停止** します。  

    > [!NOTE]
    > テンプレート VM は、実行時に **コスト** が発生するため、実行しておく必要がない場合はテンプレート VM がシャットダウンされるようにしてください。 

## <a name="publish-the-template-vm"></a>テンプレート VM を発行する
この手順では、テンプレート VM を発行します。 テンプレート VM を発行すると、Azure Lab Services によって、そのテンプレートを使用してラボ内に VM が作成されます。 すべての仮想マシンの構成は、テンプレートと同じになります。

1. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 

    ![テンプレートの発行ボタン](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 一度発行すると、再発行することはできません。 
2. **[Publish template]\(テンプレートの発行\)** ページで、ラボに作成する仮想マシンの数を入力し、 **[発行]** を選択します。 

    ![テンプレートの発行 - VM の数](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. ページには、テンプレートの **発行の状態** が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 発行処理が完了するまで待ってから、左側のメニューで **[仮想マシン]** を選択するか、 **[仮想マシン]** タイルを選択して、 **[仮想マシン プール]** ページに切り替えます。 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > 教師が学生の VM をオンにしても、学生のクォータは影響を受けません。 ユーザーのクォータによって指定されるのは、スケジュールされたクラス時間外にユーザーが使用できるラボ時間数です。 クォータの詳細については、「[ユーザーのクォータを設定する](how-to-configure-student-usage.md?#set-quotas-for-users)」を参照してください。

## <a name="set-a-schedule-for-the-lab"></a>ラボのスケジュールを設定する
ラボ内の VM が特定の時刻に自動的に起動または停止されるように、ラボ用にスケジュール化されたイベントを作成します。 前に指定したユーザー クォータ (既定値: 10 時間) は、このスケジュールされた時間以外に各ユーザーに割り当てられる追加時間です。 

1. **[スケジュール]** ページに切り替えて、ツール バーの **[Add scheduled event]\(スケジュール化されたイベントの追加\)** を選択します。 

    ![[スケジュール] ページの [Add scheduled event]\(スケジュール化されたイベントの追加\) ボタンを示すスクリーンショット。](./media/how-to-create-schedules/add-schedule-button.png)
2. **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページで、次の手順を実行します。
    1. **[Event type]\(イベントの種類\)** で **[Standard]\(標準\)** が選択されていることを確認します。  
    2. クラスの **開始日** を選択します。 
    4. VM を起動する **開始時刻** を選択します。
    5. VM をシャットダウンする **停止時刻** を選択します。 
    6. 指定した開始および停止時刻の **タイム ゾーン** を選択します。 
3. 同じ **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページの **[Repeat]\(繰り返し\)** セクションで、現在のスケジュールを選択します。  

    ![[スケジュール] ページの [スケジュールの追加] ボタン](./media/how-to-create-schedules/select-current-schedule.png)
5. **[Repeat]\(繰り返し\)** ダイアログ ボックスで、次の手順を実行します。
    1. **[Repeat]\(繰り返し\)** フィールドに **[毎週]** が設定されていることを確認します。 
    2. スケジュールを有効にする曜日を選択します。 次の例では、月曜日から金曜日までが選択されています。 
    3. スケジュールの **終了日** を選択します。
    8. **[保存]** を選択します。 

        ![繰り返しのスケジュールを設定する](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 次に、 **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページの **[メモ (省略可能)]** に、スケジュールの説明またはメモを入力します。 
4. **[Add scheduled event]\(スケジュール化されたイベントの追加\)** ページで、 **[保存]** を選択します。 

    ![週単位のスケジュール](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. カレンダーで開始日に移動して、スケジュールが設定されていることを確認します。
    
    ![カレンダーでスケジュールを設定する](./media/how-to-create-schedules/schedule-calendar.png)

    クラスのスケジュールの作成と管理の詳細については、[ラボのスケジュールの作成と管理](how-to-create-schedules.md)に関するページを参照してください。


## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加

ユーザーを追加する場合、既定では、 **[アクセスを制限する]** オプションがオンになっています。学生は、ユーザーのリストに含まれていない限り、登録リンクがあってもラボに登録することはできません。 リストに含まれるユーザーだけが、送信された登録リンクを使用してラボに登録できます。 **[アクセスを制限する]** をオフにすることもできます。その場合、学生は、登録リンクがあればラボに登録できます。 

### <a name="add-users-from-an-azure-ad-group"></a>Azure AD グループからユーザーを追加する

ユーザーを手動で追加または削除しなくて済むように、ラボ ユーザー リストを既存の Azure Active Directory (Azure AD) グループに同期することができます。 

組織の Azure Active Directory 内に Azure AD グループを作成して、組織のリソースやクラウドベースのアプリへのアクセスを管理できます。 詳細については、[Azure AD グループ](../active-directory/fundamentals/active-directory-manage-groups.md)に関する記事をご覧ください。 組織で Microsoft Office 365 または Azure サービスを使用している場合、組織には Azure Active Directory を管理する管理者が既に存在します。 

> [!IMPORTANT]
> ユーザー リストが空であることを確認してください。 手動または CSV ファイルのインポートによって追加したユーザーがラボ内に存在する場合、ラボを既存のグループに同期するオプションは表示されません。 

1. 左側のウィンドウで、 **[ユーザー]** を選択します。 
1. **[Sync from group]\(グループから同期\)** をクリックします。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Azure AD グループから同期してユーザーを追加する":::
    
1. ラボを同期する既存の Azure AD グループを選択するよう求められます。 
    
    Azure AD グループがリストに表示されない場合は、次の理由が考えられます。

    -   Azure Active Directory のゲスト ユーザーの場合 (通常は、Azure AD を所有する組織の外部にいる場合)、Azure AD 内のグループを検索することはできません。 この場合、Azure AD グループをラボに追加することはできません。 
    -   Teams を使用して作成された Azure AD グループは、このリストには表示されません。 Teams 内に Azure Lab Services アプリを追加すると、そこから直接ラボを作成して管理できます。 詳細については、[Teams 内からのラボのユーザー リストの管理](how-to-manage-user-lists-within-teams.md)に関する記事をご覧ください。 
1. ラボを同期する Azure AD グループを選択したら、 **[追加]** をクリックします。
1. ラボが同期されると、Azure AD グループ内の全ユーザーがユーザーとしてラボに追加され、ユーザー リストが更新されます。 この Azure AD グループのユーザーだけがラボにアクセスできます。 ユーザー リストは、Azure AD グループの最新のメンバーシップに合わせて 24 時間ごとに更新されます。 [ユーザー] タブの [同期] ボタンをクリックすることで、Azure AD グループの最新の変更に手動で同期することもできます。
1. **[全員を招待]** ボタンをクリックして、ユーザーをラボに招待します。これにより、ラボへの登録リンクが含まれた電子メールがすべてのユーザーに送信されます。 

### <a name="add-users-manually-from-emails-or-csv-file"></a>電子メールまたは CSV ファイルからユーザーを手動で追加する

このセクションでは、学生を (メール アドレスまたは CSV ファイルのアップロードによって) 手動で追加します。 

#### <a name="add-users-by-email-address"></a>メール アドレスでユーザーを追加する

1. 左側のウィンドウで、 **[ユーザー]** を選択します。 
1. **[Add users manually]\(ユーザーを手動で追加する\)** をクリックします。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="[Add users manually]\(ユーザーを手動で追加する\)":::
1. **[メール アドレスによる追加]** (既定値) を選択し、学生のメール アドレスを別々の行に入力するか、セミコロンで区切って 1 行に入力します。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="ユーザーのメール アドレスを追加する":::
1. **[保存]** を選択します。 

    一覧には、ラボに登録されているかどうかにかかわらず、現在のユーザーのメール アドレスと状態が表示されます。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="ユーザー リスト":::

    > [!NOTE]
    > 学生がラボに登録されると、一覧にその名前が表示されます。 一覧に表示される名前は、Azure Active Directory 内の学生の姓と名を使用して作成されます。 

#### <a name="add-users-by-uploading-a-csv-file"></a>CSV ファイルをアップロードしてユーザーを追加する

ユーザーのメール アドレスを含む CSV ファイルをアップロードしてユーザーを追加することもできます。 

CSV テキスト ファイルは、コンマ区切り (CSV) の表形式データ (数値とテキスト) を格納するために使用されます。 CSV ファイルは、(スプレッドシートなどの) 列フィールドに情報を格納するのではなく、コンマで区切られた情報を格納します。 CSV ファイル内の各行には、同じ数のコンマ区切りの "フィールド" があります。 Excel を使用すると、簡単に CSV ファイルを作成および編集できます。

1. Microsoft Excel で、学生のメール アドレスを 1 列で列挙する CSV ファイルを作成します。

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV ファイルのユーザーの一覧":::
1. **[ユーザー]** ウィンドウで、 **[ユーザーの追加]** を選択してから、 **[CSV のアップロード]** を選択します。
1. 学生のメール アドレスを含む CSV ファイルを選択し、 **[開く]** を選択します。

    **[ユーザーの追加]** ウィンドウに、CSV ファイルのメール アドレスの一覧が表示されます。 
1. **[保存]** を選択します。 
1. **[ユーザー]** ウィンドウで、追加された学生の一覧を表示します。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="[ユーザー] ペインの追加されたユーザーのリスト"::: 

## <a name="send-invitation-emails-to-users"></a>招待メールをユーザーに送信する

1. まだこのページが表示されていない場合は **[ユーザー]** ビューに切り替え、ツール バーの **[Invite all]\(全員を招待\)** を選択します。 

    ![学生の選択](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. **[Send invitation by email]\(メールで招待を送信する\)** ページで、オプションのメッセージを入力し、 **[送信]** を選択します。 電子メールには、登録リンクが自動的に含まれます。 この登録リンクを取得するには、ツール バーの **... (省略記号)** 、 **[Registration link]\(登録リンク\)** の順に選択します。 

    ![登録リンクを電子メールで送信する](./media/tutorial-setup-classroom-lab/send-email.png)
4. **[ユーザー]** の一覧に **招待** の状態が表示されます。 この状態は、 **[送信中]** に変更された後、 **[Sent on &lt;date&gt;]\(<日付> に送信済み\)** に変更されます。 

クラスへの学生の追加と学生によるラボの使用の管理について詳しくは、[学生の使用を構成する方法](how-to-configure-student-usage.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、自分のクラスのラボを Azure に作成しました。 学生が登録リンクを使ってラボの VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボの VM に接続する](tutorial-connect-virtual-machine-classroom-lab.md)