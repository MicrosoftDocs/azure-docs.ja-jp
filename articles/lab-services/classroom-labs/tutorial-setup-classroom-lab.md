---
title: Azure Lab Services を使用してクラスルーム ラボを設定する | Microsoft Docs
description: このチュートリアルでは、クラスルームで使用するためのラボを設定します。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 964ecca015e440439885bbbd85cb720a3abd10a9
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883513"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>チュートリアル:クラスルーム ラボを設定する 
このチュートリアルでは、クラスルームで学生が使用する仮想マシンで、クラスルーム ラボを設定します。  

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * クラスルーム ラボを作成する
> * ラボへのユーザーの追加
> * 登録リンクを学生に送信する

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおいて、所有者、ラボの作成者、または共同作成者ロールのメンバーである必要があります。 ラボ アカウントを作成するために使用したアカウントは、所有者ロールに自動的に追加されます。

ラボの所有者は、他のユーザーを**ラボの作成者**ロールに追加できます。 たとえば、ラボの所有者は、ラボの作成者ロールに教授たちを追加します。 その後、教授は、クラス用に VM を使用してラボを作成します。 学生は、教授から受け取った登録リンクを使用してラボに登録します。 登録された学生は、ラボの VM を使用してクラスの課題や宿題をすることができます。 ラボの作成者ロールにユーザーを追加するための詳細な手順については、「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」を参照してください。


## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. ラボでの**仮想マシンの数**の最大値を指定します。 ラボの作成後、または既存のラボで、VM の数を増やしたり減らしたりできます。 詳しくは、「[ラボ内の仮想マシンの数を更新する](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)」をご覧ください
    6. **[保存]** を選択します。

        ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Select virtual machine specifications]** \(仮想マシンの仕様の選択\) ページで、次の手順を実行します。
    1. ラボで作成する仮想マシン (VM) の **[サイズ]** を選択します。 現時点では、 **[小]** 、 **[中]** 、 **[中 (仮想化)]** 、 **[大]** 、および **[GPU]** のサイズが許可されます。
    3. ラボで VM の作成に使用する **VM イメージ**を選択します。 Linux イメージを選択した場合は、リモート デスクトップ接続を有効にするためのオプションが表示されます。 詳細については、[Linux のリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。
    4. **[次へ]** を選択します。

        ![VM 仕様の指定](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **[資格情報の設定]** ページで、ラボ内のすべての VM に使う既定の資格情報を指定します。 
    1. ラボ内のすべての VM に使う**ユーザーの名前**を指定します。
    2. ユーザーの**パスワード**を指定します。 

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. **作成** を選択します。 

        ![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **[Configure template**]\(テンプレートの構成\) ページで、ラボの作成プロセスの状態を確認します。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレートの構成](../media/tutorial-setup-classroom-lab/configure-template.png)
7. テンプレートの構成が完了すると、次のページが表示されます。 

    ![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. **[Configure template]\(テンプレートの構成\)** ページで、次の手順のようにします。これらの手順は、チュートリアルでは**省略可能**です。
    2. **[接続]** を選択してテンプレート VM に接続します。 Linux のテンプレート VM の場合は、SSH と RDP のどちらを使用して接続するかを選択します (RDP が有効な場合)。
    1. **[パスワードのリセット]** を選択して、VM のパスワードをリセットします。 
    1. テンプレート VM にソフトウェアをインストールして構成します。 
    1. VM を**停止**します。  
    1. テンプレートの**説明**を入力します。
9. [Configure template]\(テンプレートの構成\) ページの **[次へ]** を選択します。 
10. **[Publish the template]** \(テンプレートの発行\) ページで、次の操作を行います。 
    1. テンプレートをすぐに発行するには、 **[Publish]\(発行\)** を選択します。  

        > [!WARNING]
        > 一度発行すると、再発行することはできません。 
    2. 後で発行する場合は、 **[後のために保存]** を選択します。 ウィザードが終了した後に、テンプレート VM を発行することができます。 ウィザード終了後の構成および発行方法の詳細については、[クラスルーム ラボの管理方法](how-to-manage-classroom-labs.md)に関する記事の[テンプレートの発行](how-to-create-manage-template.md#publish-the-template-vm)に関するセクションを参照してください。

        ![テンプレートを発行する](../media/tutorial-setup-classroom-lab/publish-template.png)
11. テンプレートの**発行に関する進行状況**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. テンプレートが正常に発行されると、次のページが表示されます。 **[完了]** を選択します。

    ![テンプレートの発行 - 正常](../media/tutorial-setup-classroom-lab/publish-success.png)
1. ラボの**ダッシュボード**が表示されます。 
    
    ![クラスルーム ラボのダッシュボード](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 左側のメニューで [仮想マシン] を選択するか、[仮想マシン] タイルを選択して、 **[仮想マシン]** ページに切り替えます。 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加

1. 左側のメニューの **[ユーザー]** を選択します。 既定では、 **[アクセスを制限する]** オプションが有効になっています。 この設定がオンの場合、ユーザーは、ユーザーの一覧に存在しない限り、登録リンクを持っていてもラボに登録できません。 リストに存在するユーザーだけが、受け取った登録リンクを使用してラボに登録できます。 この手順では、ユーザーをリストに追加します。 または、 **[アクセスを制限する]** をオフにすることもできます。その場合ユーザーは、登録リンクさえあればラボに登録することができます。 
2. ツール バーの **[ユーザーの追加]** を選択します。 

    ![[ユーザーの追加] ボタン](../media/how-to-configure-student-usage/add-users-button.png)
1. **[ユーザーの追加]** ページで、ユーザーのメール アドレスを入力します。複数のメール アドレスは別々の行に入力するか、1 つの行にセミコロンで区切って入力します。 

    ![ユーザーのメール アドレスを追加](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **[保存]** を選択します。 ユーザーのメール アドレスとそのステータス (登録または未登録) がリストに表示されます。 

    ![ユーザー リスト](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>ユーザーのクォータを設定する
次の手順を使用して、ユーザーごとのクォータを設定できます。 

1. ページがまだアクティブになっていない場合は、左側のメニューで **[ユーザー]** を選択します。 
2. ツール バーで **[Quota per user: 10 hours]\(ユーザーあたりのクォータ: 10 時間\)** を選択します。 
3. **[ユーザーあたりのクォータ:]** ページで、各ユーザー (学生) に付与する時間数を指定します。 
    1. **[Total number of lab hours per user]\(ユーザーごとのラボ時間の合計数\)** 。 ユーザーは、**スケジュールされた時間に加え**、設定された時間数 (このフィールドに指定された時間) の間、各自の VM を使用できます。 このオプションを選ぶ場合は、テキスト ボックスに**時間数**を入力します。 

        ![ユーザーあたりの時間数](../media/how-to-configure-student-usage/number-of-hours-per-user.png)。 
    1. **[0 hours (schedule only)]\(0 時間 (スケジュールされた時間のみ)\)** 。 ユーザーは、スケジュールされた時間、またはラボの所有者がユーザーのために VM をオンにしたときのみ、各自の VM を使用できます。

        ![0 時間 - スケジュールされた時間のみ](../media/how-to-configure-student-usage/zero-hours.png)
    4. **[保存]** を選択します。 
5. ツールバーに変更後の値が表示されます。 **[Quota per user: &lt;number of hours&gt;]\(ユーザーあたりのクォータ: <時間数>\)** 。 

    ![ユーザーあたりのクォータ](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>ラボのスケジュールを設定する
クォータの設定を **[0 hours (schedule only)]\(0 時間 (スケジュールされた時間のみ)\)** に構成した場合は、ラボのスケジュールを設定する必要があります。 このチュートリアルでは、毎週繰り返されるスケジュールになるスケジュールを設定します。

1. **[スケジュール]** ページに切り替えて、ツール バーの **[スケジュールの追加]** を選択します。 

    ![[スケジュール] ページの [スケジュールの追加] ボタン](../media/how-to-create-schedules/add-schedule-button.png)
2. **[スケジュールの追加]** ページで、上部にある **[Weekly]\(週単位\)** に切り替えます。 
3. **[Schedule days (required)]\(スケジュールする曜日 (必須)\)** で、スケジュールを有効にする曜日を選択します。 次の例では、月曜日から金曜日までが選択されています。 
4. **[From]\(開始\)** フィールドで、**スケジュールの開始日**を入力するか、または**カレンダー** ボタンを選択して日付を選択します。 このフィールドは必須です。 
5. **[Schedule end date]\(スケジュールの終了日\)** で、VM をシャットダウンする終了日を入力するか選択します。 
6. **[Start time]\(開始時刻\)** で、VM を起動する時刻を選択します。 停止時刻が設定されていない場合、開始時刻は必須です。 停止時刻のみを指定する場合は、 **[Remove start event]\(開始イベントを削除する\)** を選択します。 **[Start time]\(開始時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add start event]\(開始イベントを追加する\)** を選択して有効にします。 
7. **[Stop time]\(停止時刻\)** で、VM をシャットダウンする時刻を選択します。 開始時刻が設定されていない場合、停止時刻は必須です。 開始時刻のみを指定する場合は、 **[Remove stop event]\(停止イベントを削除する\)** を選択します。 **[Stop time]\(停止時刻\)** が無効になっている場合は、ドロップダウン リストの横にある **[Add stop event]\(停止イベントを追加する\)** を選択して有効にします。
8. **[Time zone (required)]\(タイム ゾーン (必須)\)** で、指定した開始時刻と停止時刻のタイム ゾーンを選択します。  
9. **[Notes]\(備考\)** に、スケジュールの説明やメモを入力します。 
10. **[保存]** を選択します。 

    ![週単位のスケジュール](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>登録リンクが記載された電子メールを送信する

1. このページ上で、まだ **[ユーザー]** ビューが表示されていない場合は、[ユーザー] ビューに切り替えます。 
2. 一覧で特定のユーザーまたはすべてのユーザーを選択します。 特定のユーザーを選択するには、一覧の最初の列のチェック ボックスを選択します。 すべてのユーザーを選択するには、最初の列のタイトル ( **[名前]** ) の前にあるチェック ボックスを選択するか、一覧ですべてのユーザーのすべてのチェック ボックスを選択します。 この一覧で、**招待状態**の状態を確認できます。  次の図では、すべての学生の招待状態が **[Invitation not sent]\(招待未送信\)** に設定されています。 

    ![学生の選択](../media/tutorial-setup-classroom-lab/select-students.png)
1. いずれかの行の**メール アイコン (封筒)** を選択するか、ツール バーの **[招待状の送信]** を選択します。 メール アイコンは、一覧内の学生の名前の上にマウス ポインターを置いたときにも表示されます。 

    ![登録リンクを電子メールで送信する](../media/tutorial-setup-classroom-lab/send-email.png)
4. **[登録リンクを電子メールで送信する]** ページで、次の手順に従います。 
    1. 学生に送信する**オプション メッセージ**を入力します。 電子メールには、登録リンクが自動的に含まれます。 
    2. **[登録リンクを電子メールで送信する]** ページで、 **[送信]** を選択します。 招待の状態が **[招待状を送信しています]** に変わり、次に **[招待を送信しました]** に変わります。 
        
        ![招待が送信された状態](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、クラスルーム ラボを作成し、ラボを構成しました。 学生が登録リンクを使ってラボの VM にアクセスする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボの VM に接続する](tutorial-connect-virtual-machine-classroom-lab.md)

