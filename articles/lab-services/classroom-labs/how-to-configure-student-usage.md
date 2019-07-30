---
title: Azure Lab Services のクラスルーム ラボの使用設定を構成する | Microsoft Docs
description: ラボのユーザー数の構成、それらのユーザーのラボへの登録、ユーザーが VM を使用できる時間数の制御などの方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 67faf268d265fd045c21b75b6f64840511a371d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067312"
---
# <a name="configure-usage-settings-and-policies"></a>使用設定とポリシーを構成する
この記事では、ラボへのユーザーの追加、それらのユーザーのラボへの登録、ユーザーが VM を使用できる時間数の制御などの方法について説明します。 


## <a name="add-users-to-the-lab"></a>ラボへのユーザーの追加
**[アクセスを制限する]** を有効にしている場合は、ユーザー (メール アドレス) をリストに追加します。

1. 左側のメニューの **[ユーザー]** を選択します。
2. ツール バーの **[ユーザーの追加]** を選択します。 

    ![[ユーザーの追加] ボタン](../media/how-to-configure-student-usage/add-users-button.png)
1. **[ユーザーの追加]** ページで、ユーザーのメール アドレスを入力します。複数のメール アドレスは別々の行に入力するか、1 つの行にセミコロンで区切って入力します。 

    ![ユーザーのメール アドレスを追加](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **[保存]** を選択します。 ユーザーのメール アドレスとそのステータス (登録または未登録) がリストに表示されます。 

    ![ユーザー リスト](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>学生と登録リンクを共有する
登録リンクを学生に送信するには、以下のいずれかの方法を使用します。 最初の方法では、登録リンクとオプションのメッセージが含まれた電子メールを学生にどのように送信するかを示します。 2 番目の方法では、他のユーザーと任意の方法で共有できる登録リンクをどのように取得するかを示します。 

ラボの **[アクセスを制限する]** が有効になっている場合、登録リンクを使用してラボに登録できるのは、ユーザーの一覧に含まれるユーザーのみです。 このオプションは、既定で有効です。 

### <a name="send-email-to-users"></a>ユーザーに電子メールを送信する
Azure Lab Services を使用すると、教師はすべての学生または選択した学生にラボの招待をメールで送信できます。別のメール クライアントを使用する必要はありません。 教師は一覧内の各学生の上にマウス ポインターを置いて各学生のメール アイコンを表示するか、1 人以上の学生を選択し、ツール バーの **[招待状の送信]** を使用することができます。 この機能では、登録リンクと教師が追加したメッセージ (追加した場合) を含むメールが送信されます。 招待が送信されると、招待の状態が **[招待を送信しました]** になり、教師は登録リンクを既に受け取っている学生とその送信日を追跡できます。

1. このページ上で、まだ **[ユーザー]** ビューが表示されていない場合は、[ユーザー] ビューに切り替えます。 
2. 一覧で特定のユーザーまたはすべてのユーザーを選択します。 特定のユーザーを選択するには、一覧の最初の列のチェック ボックスを選択します。 すべてのユーザーを選択するには、最初の列のタイトル ( **[名前]** ) の前にあるチェック ボックスを選択するか、一覧ですべてのユーザーのすべてのチェック ボックスを選択します。 この一覧で、**招待状態**の状態を確認できます。  次の図では、すべての学生の招待状態が **[Invitation not sent]\(招待未送信\)** に設定されています。 

    ![学生の選択](../media/tutorial-setup-classroom-lab/select-students.png)
1. いずれかの行の**メール アイコン (封筒)** を選択するか、ツール バーの **[招待状の送信]** を選択します。 メール アイコンは、一覧内の学生の名前の上にマウス ポインターを置いたときにも表示されます。 

    ![登録リンクを電子メールで送信する](../media/tutorial-setup-classroom-lab/send-email.png)
4. **[登録リンクを電子メールで送信する]** ページで、次の手順に従います。 
    1. 学生に送信する**オプション メッセージ**を入力します。 電子メールには、登録リンクが自動的に含まれます。 
    2. **[登録リンクを電子メールで送信する]** ページで、 **[送信]** を選択します。 招待の状態が **[招待状を送信しています]** に変わり、次に **[招待を送信しました]** に変わります。 
        
        ![招待が送信された状態](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>登録リンクを取得する
1. 左側のメニューの **[Users]\(ユーザー\)** を選択して **[Users]\(ユーザー\)** ビューに切り替えます。 
2. **[Get registration link]\(登録リンクの取得\)** タイルを選択します。

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **[User registration]\(ユーザー登録\)** ダイアログ ボックスで、 **[コピー]** ボタンを選びます。 リンクがクリップボードにコピーされます。 それを電子メール エディターに貼り付け、学生に電子メールを送信します。 

    ![学生登録リンク](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **[User registration]\(ユーザー登録\)** ダイアログ ボックスの **[閉じる]** を選択します。 
4. 学生がクラスに登録できるように、**登録リンク**を学生と共有します。 

## <a name="view-users-registered-with-the-lab"></a>ラボに登録されているユーザーを確認する

左側のメニューで **[ユーザー]** を選択して、ラボに登録されているユーザーの一覧を表示します。 

![ラボに登録されているユーザーの一覧](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>ユーザーごとのクォータを設定する
次の手順を使用して、ユーザーごとのクォータを設定できます。 

1. 左側のメニューの **[ユーザー]** を選択します。
2. ツールバーの **[ユーザーあたりのクォータ:]** を選択します。 
3. **[ユーザーあたりのクォータ:]** ページで、各ユーザー (学生) に付与する時間数を指定します。 
    1. **[0 hours (schedule only)]\(0 時間 (スケジュールされた時間のみ)\)** 。 ユーザーは、スケジュールされた時間、またはラボの所有者がユーザーのために VM をオンにしたときのみ、各自の VM を使用できます。

        ![0 時間 - スケジュールされた時間のみ](../media/how-to-configure-student-usage/zero-hours.png)
    1. **[Total number of lab hours per user]\(ユーザーごとのラボ時間の合計数\)** 。 ユーザーは、**スケジュールされた時間に加え**、設定された時間数 (このフィールドに指定された時間) の間、各自の VM を使用できます。 このオプションを選ぶ場合は、テキスト ボックスに**時間数**を入力します。 

        ![ユーザーあたりの時間数](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. **[保存]** を選択します。 
5. ツールバーに変更後の値が表示されます。 **[Quota per user: &lt;number of hours&gt;]\(ユーザーあたりのクォータ: <時間数>\)** 。 

    ![ユーザーあたりのクォータ](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> 登録リンクを学生に送信する前に、教師は、クォータ時間として 0 を選択している場合はクラスのスケジュールを設定する必要があります。または、ラボのクォータ時間を指定する必要があります。
>
> [スケジュールされている VM の実行時間](how-to-create-schedules.md)は、ユーザーに割り当てられるクォータにカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV ファイルをアップロードしてユーザーを追加する
また、ユーザーのメール アドレスを指定した CSV ファイルをアップロードしてユーザーを追加することもできます。

1. CSV ファイルを作成し、1 つの列にユーザーの電子メール アドレスを列記します。

    ![ユーザーあたりのクォータ](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. ラボの **[ユーザー]** ページで、ツールバーの **[CSV のアップロード]** を選択します。

    ![[CSV のアップロード] ボタン](../media/how-to-configure-student-usage/upload-csv-button.png)
3. ユーザーのメール アドレスを指定した CSV ファイルを選択します。 CSV ファイルを選択した後に **[開く]** を選択すると、次のような **[ユーザーの追加]** ウィンドウが表示されます。 電子メール アドレスの一覧には、CSV ファイルから取得された電子メール アドレスが表示されます。 

    ![CSV ファイルからの電子メール アドレスが表示された [ユーザーの追加] ウィンドウ](../media/how-to-configure-student-usage/add-users-window.png)
4. **[ユーザーの追加]** ウィンドウで **[保存]** を選択します。 
5. ユーザーの一覧にユーザーが表示されていることを確認します。 

    ![追加されたユーザーの一覧](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>ユーザー VM を管理する
提供された登録リンクを使用して学生が Azure Lab Services に登録すると、 **[仮想マシン]** タブに学生に割り当てられた VM が表示されます。 

![学生に割り当てられた仮想マシン](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

学生の VM 上では、次のタスクを実行できます。 

- VM が実行中の場合は、VM を停止します。 
- VM が停止している場合は、VM を起動します。 
- VM に接続します 
- VM を削除します。 
- ユーザーが仮想マシンを使用した時間数を表示します。 

## <a name="update-number-of-virtual-machines-in-lab"></a>ラボ内の仮想マシンの数を更新する
ラボ内の仮想マシンの数を更新するには、 **[仮想マシン]** ページで次の手順を実行します。

1. 左側のメニューで **[仮想マシン]** を選択します。 
2. ツール バーの **[Lab capacity: &lt;number&gt; machine(s)]\(ラボの容量: <数> 台のマシン\)** を選択します。 
3. 仮想マシンの**数**を入力します。
4. **[保存]** を選択します。

    ![ラボ内の仮想マシン](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
