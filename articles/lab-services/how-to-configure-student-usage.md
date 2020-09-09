---
title: Azure Lab Services のクラスルーム ラボの使用設定を構成する
description: ラボの学生数の構成、学生のラボへの登録、学生が VM を使用できる時間数の制御などを行う方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 229147891d1456d7b7b11fd5297755842d85e600
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445952"
---
# <a name="add-and-manage-lab-users"></a>ラボ ユーザーを追加および管理する

この記事では、ラボへの学生ユーザーの追加、学生ユーザーのラボへの登録、学生ユーザーが仮想マシン (VM) を使用できる追加時間数の制御などを行う方法について説明します。 

## <a name="add-users-to-a-lab"></a>ラボへのユーザーの追加

このセクションでは、ラボへの学生の追加を、手動で、または CSV ファイルをアップロードして行います。 次の操作を行います。

1. 左側のウィンドウで、 **[ユーザー]** を選択します。 

    既定では、 **[アクセスの制限]** オプションはオンになっています。学生は、ユーザーの一覧に含まれていなければ、登録リンクがある場合でもラボに登録できません。 リストに含まれるユーザーだけが、送信された登録リンクを使用してラボに登録できます。 この手順では、ユーザーをリストに追加します。 または、 **[アクセスを制限する]** をオフにすることもできます。その場合学生は、登録リンクさえあればラボに登録することができます。 

1. **[ユーザー]** ウィンドウの上部で、 **[ユーザーの追加]** を選択し、 **[メール アドレスによる追加]** を選択します。 

    ![[ユーザーの追加] ボタン](./media/how-to-configure-student-usage/add-users-button.png)

1. **[ユーザーの追加]** ウィンドウで、学生のメール アドレスを別個の行に入力します。または、セミコロンで区切って 1 行に入力します。 

    ![ユーザーのメール アドレスを追加する](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. **[保存]** を選択します。 

    一覧には、ラボに登録されているかどうかにかかわらず、現在のユーザーのメール アドレスと状態が表示されます。 

    ![ユーザー リスト](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 学生がラボに登録されると、一覧にその名前が表示されます。 一覧に表示される名前は、Azure Active Directory 内の学生の姓と名を使用して作成されます。 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV ファイルをアップロードしてユーザーを追加する

ユーザーのメール アドレスを含む CSV ファイルをアップロードしてユーザーを追加することもできます。

1. Microsoft Excel で、学生のメール アドレスを 1 列で列挙する CSV ファイルを作成します。

    ![CSV ファイルのユーザーの一覧](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. **[ユーザー]** ウィンドウで、 **[ユーザーの追加]** を選択してから、 **[CSV のアップロード]** を選択します。

    ![[CSV のアップロード] ボタン](./media/how-to-configure-student-usage/upload-csv-button.png)

1. 学生のメール アドレスを含む CSV ファイルを選択し、 **[開く]** を選択します。

    **[ユーザーの追加]** ウィンドウに、CSV ファイルのメール アドレスの一覧が表示されます。 

    ![CSV ファイルのメール アドレスが表示された [ユーザーの追加] ウィンドウ](./media/how-to-configure-student-usage/add-users-window.png)

1. **[保存]** を選択します。 

1. **[ユーザー]** ウィンドウで、追加された学生の一覧を表示します。 

    ![[ユーザー] ウィンドウの追加されたユーザーの一覧](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>招待をユーザーに送信する

登録リンクを新しいユーザーに送信するには、以下のいずれかの方法を使用します。 

ラボの **[アクセスを制限する]** オプションが有効になっている場合、登録リンクを使用してラボに登録できるのは、一覧に含まれるユーザーのみです。 このオプションは、既定で有効です。 

### <a name="invite-all-users"></a>すべてのユーザーを招待する

この方法では、一覧に含まれるすべての学生に、登録リンクとオプションのメッセージを記載したメールを送信する方法を示します。

1. **[ユーザー]** ウィンドウで、 **[全員を招待]** を選択します。 

    ![[全員を招待] ボタン](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. **[Send invitation by email] (メールで招待を送信する)** ウィンドウで、オプションのメッセージを入力してから、 **[送信]** を選択します。 

    電子メールには、登録リンクが自動的に含まれます。 登録リンクを個別に取得して保存するには、 **[ユーザー]** ウィンドウの上部にある省略記号 ( **[...]** ) を選択し、 **[登録リンク]** を選択します。 

    !["登録リンクをメールで送信する" ウィンドウ](./media/tutorial-setup-classroom-lab/send-email.png)

    **[ユーザー]** の一覧の **[招待]** 列には、追加された各ユーザーの招待の状態が表示されます。 この状態は、 **[送信中]** に変更された後、 **[Sent on \<date>]\(<日付> に送信済み\)** に変更されます。 

### <a name="invite-selected-users"></a>選択したユーザーを招待する

この方法では、特定の学生だけを招待し、他のユーザーと共有できる登録リンクを取得する方法を示します。

1. **[ユーザー]** ウィンドウの一覧から、1 人または複数の学生を選択します。 

1. 選択した学生の行で、**封筒**アイコンを選択するか、ツールバーの **[招待]** を選択します。 

    ![選択したユーザーを招待する](./media/how-to-configure-student-usage/invite-selected-users.png)

1. **[Send invitation by email]\(メールで招待を送信する\)** ウィンドウで、オプションの**メッセージ**を入力し、 **[送信]** を選択します。 

    ![選択したユーザーに電子メールを送信する](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    **[ユーザー]** ウィンドウでは、表の **[招待]** 列にこの操作の状態が表示されます。 招待メールには、学生がラボに登録するために使用できる登録リンクが含まれています。

## <a name="get-the-registration-link"></a>登録リンクを取得する

このセクションでは、ポータルから登録リンクを取得し、独自のメール アプリケーションを使用して送信することができます。 

1. **[ユーザー]** ウィンドウで、 **[登録リンク]** を選択します。

    ![学生登録リンク](./media/how-to-configure-student-usage/registration-link-button.png)

1. **[User registration] (ユーザー登録)** ウィンドウで、 **[コピー]** を選択してから、 **[完了]** を選択します。 

    ![[User registration] (ユーザー登録) ウィンドウ](./media/how-to-configure-student-usage/registration-link.png)

    リンクがクリップボードにコピーされます。 
    
1. メール アプリケーションで、登録リンクを貼り付けたら、学生がクラスに登録できるように、メールを学生に送信します。 

## <a name="view-registered-users"></a>登録されているユーザーを表示する

1. [Azure Lab Services](https://labs.azure.com) Web サイトに移動します。 
1. **[サインイン]** を選択してから、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。
1. **[My labs] (自分のラボ)** ページで、追跡するラボを選択します。 
1. 左側のウィンドウで、 **[ユーザー]** を選択するか、 **[ユーザー]** タイルを選択します。 

    **[ユーザー]** ウィンドウには、ラボに登録した学生の一覧が表示されます。  

    ![登録されたユーザーの一覧](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>ユーザーのクォータを設定する

以下の操作を行うと、各学生の時間クォータを設定できます。 

1. **[ユーザー]** ペインで、ツールバーの **[ユーザーあたりのクォータ: \<number> 時間]** を選択します。 
1. **[ユーザーあたりのクォータ]** ウィンドウで、予定されている授業時間以外に各学生に与える時間数を指定してから、 **[保存]** を選択します。

    ![[ユーザーあたりのクォータ] ウィンドウ](./media/how-to-configure-student-usage/quota-per-user.png)    

    次に示すように、変更された値が、ツールバーの **[ユーザーあたりのクォータ: \<number of hours>]** ボタンと、ユーザーの一覧に表示されるようになっています。

    ![ユーザーあたりのクォータ時間数](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [スケジュールされている VM の実行時間](how-to-create-schedules.md)は、学生に割り当てられたクォータにカウントされません。 クォータは、学生が VM で消費するとスケジュールされている時間以外の時間です。 

## <a name="set-additional-quotas-for-specific-users"></a>特定のユーザーに追加のクォータを設定する

前のセクションですべてのユーザーに対して設定された共通のクォータを超えて、特定の学生に対してクォータを指定することができます。 たとえば、あなたがインストラクターとして、すべての学生のクォータを 10 時間に設定し、特定の学生に対して 5 時間の追加クォータを設定した場合、その学生は 15 (10 + 5) 時間のクォータを取得します。 共通クォータを後でたとえば 15 時間に変更すると、学生は 20 (15 + 5) 時間のクォータを取得します。 このクォータ全体が、スケジュールされた時間以外にあることに注意してください。 スケジュールされた時間中に学生がラボ VM で費やす時間は、このクォータにはカウントされません。 

追加のクォータを設定するには、以下の手順を実行します。

1. **[ユーザー]** ウィンドウで、一覧から学生を選択し、ツールバーの **[Adjust quota] (クォータの調整)** を選択します。 

    ![[Adjust quota] (クォータの調整) ボタン](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. **[Adjust quota for \<selected user or users email address>]\(<選択した 1 人以上のユーザーのメール アドレス> のクォータを調整\)** で、選択した 1 人以上の学生に付与する追加のラボ時間数を入力してから、 **[適用]** を選択します。 

    !["クォータの調整..." ウィンドウ](./media/how-to-configure-student-usage/additional-quota.png)

    **[使用状況]** 列には、選択した学生についての、更新されたクォータが表示されます。 

    ![ユーザーに関する新しい使用状況](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生アカウント

学生をクラスルーム ラボに追加するには、電子メール アカウントを使用します。 学生は、以下の種類のメール アカウントを使用できます。

- 大学の、Office 365 用の Azure Active Directory インスタンスによって提供される学生用メール アカウント。 
- *outlook.com*、*hotmail.com*、*msn.com*、*live.com* などの Microsoft ドメインのメール アカウント。
- Microsoft 以外のメール アカウント (Yahoo! や Google によって提供されるものなど)。 ただし、これらの種類のアカウントは、Microsoft アカウントとリンクされている必要があります。
- GitHub アカウント。 このアカウントは、Microsoft アカウントとリンクされている必要があります。

### <a name="use-a-non-microsoft-email-account"></a>Microsoft 以外のメール アカウントを使用する
学生は、Microsoft 以外のメール アカウントを使用して、クラスルーム ラボに登録してサインインすることができます。  ただし、登録を行うには、最初に、Microsoft 以外のメール アドレスにリンクされている Microsoft アカウントを作成する必要があります。

多くの学生は既に、Microsoft 以外のメール アドレスにリンクされた Microsoft アカウントを持っていることがあります。 たとえば学生は、Office、Skype、OneDrive、Windows などの Microsoft の他の製品やサービスで自分のメール アドレスを使用したことがある場合は、既に Microsoft アカウントを持っています。  

学生が登録リンクを使用してクラスルームにサインインすると、メール アドレスとパスワードの入力を求められます。 Microsoft アカウントにリンクされていない Microsoft 以外のアカウントを使用してサインインしようとする学生は、次のエラー メッセージを受け取ります。 

![サインイン時のエラー メッセージ](./media/how-to-configure-student-usage/cant-find-account.png)

学生が Microsoft アカウントにサインアップするためのリンクは[こちら](http://signup.live.com)です。  

> [!IMPORTANT]
> 学生がクラスルーム ラボにサインインするときに、Microsoft アカウントを作成するオプションは表示されません。 このため、Microsoft 以外のアカウントを使用している学生に送信する、クラスルーム ラボの登録メールには、このサインアップ リンク http://signup.live.com を含めることが推奨されます。

### <a name="use-a-github-account"></a>GitHub アカウントを使用する
学生は、既存の GitHub アカウントを使用して、クラスルーム ラボに登録してサインインすることもできます。 学生が GitHub アカウントにリンクされている Microsoft アカウントを既に持っている場合は、前のセクションで示したように、サインインしてパスワードを入力できます。 

GitHub アカウントをまだ Microsoft アカウントにリンクしていない場合は、以下の操作を実行できます。

1. 次に示すように、 **[サインイン オプション]** リンクを選択します。

    ![[サインイン オプション] リンク](./media/how-to-configure-student-usage/signin-options.png)

1. **[サインイン オプション]** ウィンドウで、 **[GitHub でサインイン]** を選択します。

    ![[GitHub でサインイン] リンク](./media/how-to-configure-student-usage/signin-github.png)

    プロンプトで、学生は次に、GitHub アカウントにリンクされている Microsoft アカウントを作成します。 **[次へ]** を選択すると、リンクが自動的に作成されます。 その後すぐにサインインされて、クラスルーム ラボに接続されます。

## <a name="export-a-list-of-users-to-a-csv-file"></a>ユーザーの一覧を CSV ファイルにエクスポートする

1. **[ユーザー]** ウィンドウに移動します。
1. ツール バーの省略記号 ( **[...]** ) を選択してから、 **[CSV のエクスポート]** を選択します。 

    ![[CSV のエクスポート] ボタン](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- 管理者の場合:[ラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- ラボ所有者の場合:[ラボを作成および管理](how-to-manage-classroom-labs.md)し、[テンプレートを設定および発行する](how-to-create-manage-template.md)
- ラボ ユーザーの場合:[クラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)
