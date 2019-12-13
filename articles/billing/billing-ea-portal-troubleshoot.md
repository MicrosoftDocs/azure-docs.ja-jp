---
title: Azure EA Portal のアクセスのトラブルシューティング
description: この記事では、Azure Enterprise Agreement (EA) に関して Azure EA Portal で発生する可能性のある一般的な問題について説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ff8408a098049d860a614900bdeea80da1bd95e4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841389"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA Portal のアクセスのトラブルシューティング

この記事では、Azure Enterprise Agreement (EA) に関して発生する可能性のある一般的な問題について説明します。 Azure EA Portal は、Enterprise Agreement のユーザーとコストを管理するために使用されます。 Azure EA Portal へのアクセスを構成または更新しているときに、次のような問題が発生する可能性があります。

## <a name="issues-adding-an-admin-to-an-enrollment"></a>登録に管理者を追加する際の問題

エンタープライズ登録には、さまざまな種類の認証レベルがあります。 認証レベルが正しく適用されていない場合、Azure EA Portal にサインインしようとすると問題が発生することがあります。

Azure EA Portal を使用して、さまざまな認証レベルのユーザーにアクセス権を付与します。 エンタープライズ管理者は、組織のセキュリティ要件を満たすように認証レベルを更新できます。

### <a name="authentication-level-types"></a>認証レベルの種類

- Microsoft アカウントのみ - Microsoft アカウントを使用してユーザーを使用、作成、管理する組織向け。
- 職場または学校アカウント - クラウドへのフェデレーションを使用して Active Directory を設定しており、1 つのテナントにすべてのアカウントが存在する組織。
- テナント間の職場または学校アカウント - クラウドへのフェデレーションを使用して Active Directory を設定しており、複数のテナントにアカウントが存在する組織。
- 混在アカウント - Microsoft アカウントおよび/または職場または学校アカウントを使用してユーザーを追加できます。

登録に追加された最初の職場または学校アカウントによって、"_既定_" のドメイン、つまり "_マスター_" ドメインが決まります。 職場または学校アカウントを別のテナントに追加するには、登録の認証レベルをテナント間の認証に変更する必要があります。

認証レベルを更新するには、次の手順を実行します。

1. エンタープライズ管理者として Azure EA Portal にサインインします。
2. 左側のナビゲーション パネルで **[管理]** をクリックします。
3. **[登録]** タブをクリックします。
4. **[登録の詳細]** で、 **[認証レベル]** を選択します。
5. 鉛筆アイコンをクリックします。
6. **[Save]** をクリックします。

![認証レベルの表示例 ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft アカウントには、[https://signup.live.com](https://signup.live.com/) で作成した関連 ID 必要です。

職場または学校アカウントは、フェデレーションを使用して Active Directory を設定し、1 つのテナントにすべてのアカウントが存在する組織で使用できます。 社内の Active Directory がフェデレーションされている場合は、職場または学校のフェデレーション ユーザー認証を使用してユーザーを追加できます。

組織が Active Directory フェデレーションを使用していない場合、職場または学校の電子メール アドレスを使用することはできません。 代わりに、新しい電子メール アドレスを作成し、Microsoft アカウントとして登録します。

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA Portal にアクセスできない

Azure EA Portal にサインインしようとしたときにエラー メッセージが表示される場合は、次のトラブルシューティングの手順を使用します。

- 正しい Azure EA Portal の URL (https://ea.azure.com ) を使用していることを確認します。
- Azure EA Portal へのアクセス権が職場または学校アカウントとして追加されているか、それとも Microsoft アカウントとして追加されているかを確認します。
  - 職場のアカウントを使用している場合は、職場の電子メール アドレスと職場のパスワードを入力します。 職場のパスワードは組織から提供されます。 問題が発生した場合は、パスワードをリセットする方法について IT 部門に確認してください。
  - Microsoft アカウントを使用している場合は、Microsoft アカウントの電子メール アドレスとパスワードを入力します。 Microsoft アカウントのパスワードを忘れた場合は、[https://account.live.com/password/reset](https://account.live.com/password/reset) でリセットできます。
- InPrivate または Incognito のブラウザー セッションを使用してサインインします。これにより、以前のセッションまたは既存セッションの Cookie やキャッシュされた情報は保持されません。 ブラウザーのキャッシュを消去し、InPrivate または Incognito ウィンドウを使用して https://ea.azure.com を開きます。
- Microsoft アカウントの使用時に "_無効なユーザー_" エラーが発生する場合、複数の Microsoft アカウントを持っていることが原因である可能性があります。 サインインに使用しているアドレスが、プライマリ電子メール アドレスではありません。
また、"_無効なユーザー_" エラーが発生する場合、ユーザーを登録に追加したときに、間違ったアカウントの種類を使用したことが原因である可能性があります。 たとえば、Microsoft アカウントの代わりに職場または学校アカウントを使用した場合です。 この場合、別の EA 管理者に適切なアカウントを追加してもらうか、[サポート](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c)に連絡する必要があります。
  - プライマリ エイリアスを確認する必要がある場合は、[https://account.live.com](https://account.live.com) にアクセスしてください。 次に、 **[ユーザーの情報]** をクリックし、 **[Manage how to sign in to Microsoft] (Microsoft にサインインする方法の管理)** をクリックします。 画面の指示に従って連絡用メール アドレスを確認し、機密情報にアクセスするためのコードを取得します。 セキュリティ コードを入力します。 2 要素認証を設定しない場合は、 **[後で設定する]** を選択します。
  - **[Microsoft にサインインする方法の管理]** ページが表示され、ご自分のアカウント エイリアスを確認できます。 プライマリ エイリアスが、Azure EA Portal へのサインインに使用しているエイリアスであることを確認します。 そうでない場合は、これをプライマリ エイリアスにすることができます。 また、代わりに Azure EA Portal のプライマリ エイリアスを使用することもできます。

## <a name="no-activation-email-received"></a>アクティブ化メールが届かない

Azure EA Portal からのアクティブ化メールは、 *waep@microsoft.com* から送信されます。 アクティブ化メールが届いていない場合は、スパムまたは迷惑メール フォルダーにこのメールが入っていないか確認してください。 このメールは、_Invitation to View/Manage the Microsoft Azure service_subject_ という件名で送信されます。 これは、新しく追加されたすべての EA 管理者宛てに送信されます。

EA 管理者として設定されていることがわかっている場合は、Azure EA Portal にサインインするためにアクティブ化メールが届くまで待つ必要はありません。 代わりに、電子メールアドレス (職場、学校、または Microsoft アカウント) とパスワードを使用して、 https://ea.azure.com にアクセスしてサインインできます。

## <a name="azure-ea-activation-faq"></a>Azure EA のアクティブ化に関する FAQ

記事のこのセクションでは、Azure EA のアクティブ化に関する一般的な問題の解決策について説明します。

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>登録に新しい EA 管理者を追加したい

既存のエンタープライズ管理者は、新しいエンタープライズ管理者を追加できます。 ご自分が EA 管理者である場合は、EA Portal にサインインし、 **[管理]** をクリックし、右上隅にある **[+ 管理者の追加]** をクリックして、新しい EA 管理者を追加してください。 ユーザーを追加できるように、ユーザーのメール アドレスと推奨されるサインイン方法 (職場または学校の認証や Microsoft Live ID など) を確認してください。

ご自分が EA 管理者でない場合は、社内の EA 管理者に連絡して、自分を登録に追加するように依頼してください。 登録に追加された後、アクティブ化メールを受け取ります。

ただし、EA 管理者の支援を受けることができない場合は、次の情報を提供すれば、自分を追加してもらうことができます。
- 登録番号。
- 追加するメール アドレスと認証の種類 (職場、学校、MS)。
- EA 管理者からのメールによる承認。

必要な情報をすべて指定したら、[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) で要求を送信してください。

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>登録の最初の EA 管理者を更新したい

ボリューム ライセンス サービス センターで最初の EA 管理者を更新するには、ポータルで通知連絡先とオンライン管理者を更新します。 EA Portal が更新されるまでに約 24 時間かかります。 更新された後、新しい EA 管理者はアクティブ化メールを受け取ります。

VLSC ポータルにアクセスできない場合、または最初の EA 管理者が登録を管理できなくなり、EA Portal にアクセスできない場合は、[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) で要求を送信して更新を要求し、次の情報を提供してください。
- 加入契約番号
- 追加するメール アドレスと認証の種類 (職場、学校、MS)
- 最初の EA 管理者を変更する理由
- 最初の EA 管理者からのメールによる承認

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>現在の EA 管理者が退職した

EA 登録には複数の EA 管理者を含めることができます。別の EA 管理者に連絡して、新しい EA 管理者、アカウント所有者、部門管理者を追加してもらうことができます。 ただし、社内の EA 管理者が誰かわからない場合や、連絡可能な他の EA 管理者が登録に含まれていない場合は、次の情報を用意してお問い合わせください。
- 加入契約番号
- 追加するメール アドレスと認証の種類 (職場、学校、MS)
- 退職した現在の EA 管理者に関する情報

他の EA 管理者が登録に含まれている場合は、その EA 管理者に連絡して、登録の管理上の変更に対する承認を求めるので注意してください。

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>登録が保留中の状態で表示されます。 登録をアクティブ化するにはどうすればよいですか?

最初の EA 管理者が登録にまだログオンしていない場合、登録は保留中の状態になります。 ご自分が EA 管理者である場合は、Azure EA Portal にサインインしてください。 すべての登録番号を含むランディング ページには、保留中の登録が表示されない場合があります。 EA Portal の右上隅にある [アクティブ] ボックスをオフにしてください。このアクションにより、保留中の登録が表示されます。 登録をクリックして情報にアクセスしてください。登録の [管理] ページに移動すると、状態が [保留中] から [アクティブ] に更新されます。

### <a name="why-is-my-account-stuck-in-pending-status"></a>アカウントが保留中のままになっているのはなぜですか?

登録に初めて追加された新しいアカウント所有者 (AO) は、常に "保留中" の状態で表示されます。 アクティブ化のウェルカム メールを受け取った AO は、サインインして自分のアカウントをアクティブ化できます。 サインインすることで、アカウントの状態が "保留中" から "アクティブ" に更新されます。

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Azure EA Portal へのサインイン時にエラーが発生した

サインイン時に Azure EA Portal にエラー メッセージが表示される理由はいくつかあります。次のトラブルシューティングのステップに従ってください。

 1. [https://ea.azure.com](https://ea.azure.com) で正しい EA Portal URL を使用していることを確認してください。
 1. Azure EA Portal へのアクセス権が職場または学校アカウントあるいは Microsoft Live ID として追加されているかどうかを確認します。 職場のアカウントを使用する場合は、職場のメール アドレスとパスワードを入力してください。 Microsoft Live ID を使用する場合は、Microsoft Live ID のメール アドレスとパスワードを入力してください。 Microsoft Live ID のパスワードを忘れた場合は、[https://account.live.com/password/reset](https://account.live.com/password/reset) でパスワードをリセットしてください。
 1. プライベート ブラウザーを使用してサインインすることをお勧めします。これにより、以前のセッションや既存のセッションの Cookie やキャッシュが保持されなくなります。 キャッシュをクリアし、プライベート ブラウズ モードまたは Incognito ウィンドウを使用して [https://ea.azure.com](https://ea.azure.com) を開きます。
 1. Microsoft アカウントの使用時に無効なユーザー エラーが発生する場合は、複数の Microsoft アカウントがあり、サインインしようとしているアカウントがプライマリ エイリアスではないことが原因である可能性があります。 プライマリ エイリアスを確認するには、account.live.com にアクセスします。
    - [Your Info] (あなたの情報) > [Manage your sign-in email or phone number] (サインイン用のメール アドレスまたは電話番号を管理) に移動します。
    - 画面の指示に従って連絡用メール アドレスを確認し、機密情報にアクセスするためのコードを取得します。
    - セキュリティ コードを入力します。
    - 2 段階認証を後で設定する場合は、[後で設定する] を選択します。
    - [アカウント エイリアスの管理] ページに移動し、設定されているアカウント エイリアスが表示されます。 プライマリ エイリアスが Azure EA Portal へのログインに使用しているエイリアスであることを再確認します。 そうでない場合は、使用中のエイリアスをプライマリ エイリアスにするか、EA Portal のプライマリ エイリアスを代わりに使用できます。

上記のトラブルシューティングのステップで問題が解決しない場合は、次のような情報を用意して [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) でリクエストを送信してください。
- 使用しているブラウザーとバージョン。
- エラー メッセージのスクリーンショット。
- エラーを表示しているページの URL。  
- エラーが発生した日付、時刻、タイム ゾーン。
- さらに、ログ ファイルを取得しておくと役に立ちます。 以下の情報を使用してネットワーク トレースをキャプチャするステップは次のとおりです。
  1. Internet Explorer を起動します。
  1. F12 キーを押します。IE の下部にボックスが開きます。
  1. **[ネットワーク]** タブを選択します。
  1. **[キャプチャの開始]** をクリックします。
  1. エラーの原因となっているアクションを実行します。
  1. エラーが表示されたら、 **[キャプチャの停止]** をクリックします。
  1. ファイルを保存し、その情報をサポート リクエストに含めます。
  1. サポート リクエストには、必ず登録番号とメール アドレスを含めるようにしてください。

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>職場または学校アカウントと Microsoft アカウントの違いは何ですか?

**Microsoft アカウント:** [https://signup.live.com](https://signup.live.com) の Live ID に関連付けられているアカウント。

**職場または学校アカウント:** クラウドへのフェデレーションを使用して Active Directory を設定しており、1 つのテナントにすべてのアカウントが存在する会社のみが使用できます。 社内の Active Directory がクラウドにフェデレーションされている場合は、職場または学校の認証の種類を使用してユーザーを追加できます。

  2016 年 9 月以降、職場または学校のメール アドレスは Microsoft アカウントとして登録できなくなりました。 詳細については、以下の資料を参照してください。[https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/)

  組織がクラウドにフェデレーションされていない場合は、職場または学校のメール アドレスを使用できません。 代わりに、新しいメール アドレスを登録または作成し、それを Microsoft アカウントとして登録してください。

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Azure EA Portal にアクセスするためのパスワードを忘れた

Microsoft Live ID のパスワードを忘れた場合は、[https://account.live.com/password/reset](https://account.live.com/password/reset) でパスワードをリセットしてください。

職場のパスワードを忘れた場合は、社内の IT 管理者にお問い合わせください。

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>有効な職場または学校アカウントを持っているが、EA Portal に追加できない

別のテナントに職場または学校アカウントがある場合は、登録の詳細ページで承認レベルを "テナント間の職場または学校アカウント" に変更すると、そのアカウントを追加できるようになります。

## <a name="next-steps"></a>次の手順

- Azure EA Portal の管理者は「[Azure EA Portal の管理](billing-ea-portal-administration.md)」を読んで、一般的な管理タスクについて学習する必要があります。
