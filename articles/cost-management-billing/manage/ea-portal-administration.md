---
title: Azure EA Portal の管理
description: この記事では、Azure EA Portal で管理者が行う一般的なタスクについて説明します。
author: bandersmsft
ms.author: banders
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 9714b3efe7052b837a83769fe24794a373adbc84
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245351"
---
# <a name="azure-ea-portal-administration"></a>Azure EA Portal の管理

この記事では、Azure EA Portal で管理者が行う一般的なタスクについて説明します (https://ea.azure.com) 。 Azure EA Portal は、お客様が Azure EA サービスのコストを管理する際に役立つオンライン管理ポータルです。 Azure EA Portal の概要については、「[Get started with the Azure EA portal (Azure EA Portal の概要)](ea-portal-get-started.md)」を参照してください。

## <a name="associate-an-account-to-a-department"></a>アカウントを部門に関連付ける

エンタープライズ管理者は、加入契約の下の既存のアカウントを部門に関連付けることができます。

### <a name="to-associate-an-account-to-a-department"></a>アカウントを部門に関連付ける方法

1. エンタープライズ管理者として Azure EA Portal にサインインします。
1. 左側のナビゲーションで **[管理]** を選択します。
1. **[部門 (キー)]** を選択します。
1. 目的のアカウントが含まれる行の上にポインターを移動し、右側にある鉛筆アイコンを選択します。
1. ドロップダウン メニューから部門を選択します。
1. **[保存]** を選択します。

## <a name="department-spending-quotas"></a>部署課金クォータ

EA のお客様は、加入契約の下にある部署ごとに、支出クォータを設定または変更できます。 課金クォータの金額は、現在の前払い期間に対して設定されます。 現在の前払い期間の終了時、値が更新されない限り、システムによって既存の課金クォータが次の前払い期間に延長されます。

部門管理者は支出クォータを表示できますが、クォータの金額を更新できるのはエンタープライズ管理者だけです。 エンタープライズ管理者と部門管理者は、クォータが 50%、75%、90%、および 100% に達したときに通知を受けます。

### <a name="enterprise-administrator-to-set-the-quota"></a>エンタープライズ管理者がクォータを設定するには

 1. Azure EA Portal を開きます。
 1. 左側のナビゲーションで **[管理]** を選択します。
 1. **[部門 (キー)]** タブを選択します。
 1. 部門を選択します。
 1. [Department Details]\(部署の詳細\) セクションの鉛筆記号をクリックするか、 **[+ 部署を追加]** 記号を選択して、新しい部署と共に支出クォータを追加します。
 1. [Department Details]\(部署の詳細\) の [Spending Quota $]\(支出クォータ $\) ボックスに、加入契約の通貨で支出クォータの金額を入力します (0 より大きい値を指定)。
    - この時点で、部門名とコストセンターを編集することもできます。
 1. **[保存]** を選択します。

部門の課金クォータが、[部門] タブの部門リスト ビューに表示されるようになります。現在の前払い期間の終了時、Azure EA Portal では、課金クォータが次の前払い期間も維持されます。

部門のクォータの金額は現在の Azure 前払いとは無関係であり、クォータの金額とアラートは、ファースト パーティの使用量にのみ適用されます。 部署の支出クォータは情報提供のみを目的としており、使用制限は適用されません。

### <a name="department-administrator-to-view-the-quota"></a>部門管理者がクォータを表示するには

1. Azure EA Portal を開きます。
1. 左側のナビゲーションで **[管理]** を選択します。
1. **[部門 (キー)]** タブを選択し、支出クォータを含む部署リスト ビューを表示します。

インダイレクトのお客様の場合は、チャネル パートナーがコスト機能を有効にする必要があります。

## <a name="enterprise-user-roles"></a>エンタープライズ ユーザー ロール

Azure EA Portal は、Azure EA のコストと使用状況を管理するのに役立ちます。 Azure EA Portal には、3 つの主要なロールがあります。

- EA 管理者
- 部門管理者
- アカウント所有者

各ロールには、さまざまなレベルのアクセス権と特権があります。

ユーザー ロールの詳細については、[「エンタープライズ ユーザー ロール」](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles)を参照してください。

## <a name="add-an-azure-ea-account"></a>Azure EA アカウントを追加する

Azure EA アカウントは、Azure EA Portal における組織単位です。 これは、サブスクリプションの管理に使用されるほか、レポートにも使用されます。 Azure サービスにアクセスして使用するには、自分でアカウントを作成するか、誰かにアカウントを作成してもらう必要があります。

Azure アカウントの詳細については、「[アカウントの追加](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account)」を参照してください。

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test オファー

Azure エンタープライズ管理者は、組織のアカウント オーナーによる EA Dev/Test プランに基づくサブスクリプションの作成を可能にすることができます。 これを行うには、Azure EA Portal でアカウント オーナーの [開発/テスト] チェック ボックスをオンにします。

[開発/テスト] チェック ボックスをオンにしたら、アカウント オーナーに通知して、アカウント オーナーが Dev/Test サブスクライバーのチームに必要な EA Dev/Test サブスクリプションを設定できるようにします。

このプランにより、アクティブな Visual Studio サブスクライバーは、特別な Dev/Test 料金で Azure 上で開発およびテストのワークロードを実行できます。 Windows 8.1 および Windows 10 を含む Dev/Test イメージの完全なギャラリーへのアクセスが提供されます。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Enterprise Dev/Test オファーを設定するには

1. エンタープライズ管理者としてサインインします。
1. 左側のナビゲーションで **[管理]** を選択します。
1. **[アカウント]** タブを選択します。
1. Dev/Test へのアクセスを有効にするアカウントの行を選択します。
1. 行の右側にある鉛筆記号を選択します。
1. [Dev/Test] チェックボックスをオンにします。
1. **[保存]** を選択します。

ユーザーが Azure EA Portal を通じてアカウント所有者として追加されると、PAYG Dev/Test オファーまたは Visual Studio サブスクライバー向けの月単位のクレジットオファーに基づきアカウント所有者に関連付けられているすべての Azure サブスクリプションは、EA Dev/Test プランに変換されます。 アカウント所有者に関連付けられている PAYG などの他の種類のプランに基づくサブスクリプションは、Microsoft Azure エンタープライズ オファーに変換されます。

現時点では、Dev/Test プランは Azure Gov のお客様には適用されません。

## <a name="delete-subscription"></a>サブスクリプションを削除する

アカウント オーナーであるサブスクリプションを削除するには:

1. アカウントに関連付けられている資格情報を使用して Azure portal にサインインします。
1. ハブ メニューで、 **[サブスクリプション]** を選択します。
1. ページの左上隅にあるサブスクリプション タブで、取り消すサブスクリプションを選択し、 **[Cancel Sub]\(サブスクリプションのキャンセル\)** を選択して [キャンセル] タブを開きます。
1. サブスクリプションの名前を入力し、取り消す理由を選択して、 **[Cancel Sub]\(サブスクリプションのキャンセル\)** ボタンを選択します。

サブスクリプションを取り消すことができるのはアカウント管理者のみです。

詳細については、「[サブスクリプションの取り消し後の流れ](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)」を参照してください。

## <a name="delete-an-account"></a>アカウントの削除

アカウントの削除は、アクティブなサブスクリプションがないアクティブなアカウントに対してのみ実施できます。

1. Enterprise Portal の左側のナビゲーションで **[管理]** を選択します。
1. **[アカウント]** タブを選択します。
1. アカウント テーブルから、削除するアカウントを選択します。
1. アカウント行の右側にある [X] 記号を選択します。
1. アカウントの下にアクティブなサブスクリプションがなくなったら、アカウント行の下にある **[はい]** を選択して、アカウントの削除を確認します。

## <a name="update-notification-settings"></a>通知設定を更新する

エンタープライズ管理者は、登録に関連付けられている使用状況の通知を受信できるように自動的に登録されます。 各エンタープライズ管理者は、個々の通知の間隔を変更することも、通知を完全に無効にすることもできます。

通知の連絡先は、Azure EA Portal の **[通知の連絡先]** セクションに表示されています。 通知の連絡先を管理することにより、組織内の適切なユーザーが Azure EA の通知を受信できるようになります。

現在の通知設定を表示するには、次の手順を実行します。

1. Azure EA Portal で、 **[管理]**  >  **[通知の連絡先]** に移動します。
2. 電子メール アドレス - 通知を受信するエンタープライズ管理者の Microsoft アカウントや職場または学校アカウントに関連付けられている電子メール アドレス。
3. 未請求残高の通知頻度 - 各エンタープライズ管理者に送信されるように設定されている通知の頻度。

連絡先を追加するには、次の手順を実行します。

1. **[+Add Contact]\(+ 連絡先の追加\)** を選択します。
2. 電子メール アドレスを入力し、確認します。
3. **[保存]** を選択します。

新しい通知の連絡先が **[通知の連絡先]** セクションに表示されます。 通知の頻度を変更するには、通知の連絡先を選択し、選択した行の右側にある鉛筆記号を選択します。 頻度を **[日単位]** 、 **[週単位]** 、 **[月単位]** 、または **[なし]** に設定します。

"_対象期間終了日が近づいています_" および "_サービス提供停止日が近づいています_" ライフサイクルの通知を非表示にすることができます。 ライフサイクルの通知を無効にすると、対象期間および契約終了日に関する通知が表示されなくなります。

## <a name="azure-sponsorship-offer"></a>Azure スポンサー プラン

Azure スポンサー プランは、Microsoft Azure の限定スポンサー アカウントです。 Microsoft が選択した一部のお客様のみに送られる招待メールを通じて利用可能になります。 Microsoft Azure スポンサー プランの対象となるお客様には、ご使用のアカウント ID に電子メールの招待状が送信されます。

詳細については、[スポンサー アクティブ化のサポート リクエストを作成してください](https://aka.ms/azrsponsorship)。

## <a name="conversion-to-work-or-school-account-authentication"></a>職場または学校アカウント認証への変換

Azure エンタープライズのユーザーは、認証の種類を Microsoft アカウント (MSA または Live ID) から Azure で Active Directory を使用する職場または学校アカウントに変換できます。

開始するには

1. Azure EA Portal で職場または学校のアカウントを必要なロールに追加します。
1. エラーが発生した場合は、Active Directory でアカウントが有効でない可能性があります。  Azure では、ユーザー プリンシパル名 (UPN) が使用されますが、これは常にメール アドレスと同じであるとは限りません。
1. 職場または学校アカウントを使用して、Azure EA Portal の認証を行います。

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft アカウントから職場または学校アカウントにサブスクリプションを変換するには

1. サブスクリプションを所有している Microsoft アカウントを使用して、管理ポータルにサインインします。
1. アカウント所有権の譲渡を使用して、新しいアカウントに移行します。
1. これで、Microsoft アカウントがアクティブなサブスクリプションから解放され、削除できるようになります。
1. 削除されたアカウントは、課金履歴の理由により、非アクティブ状態でポータルに表示が残ります。  チェックボックスをオンにして表示から除外すると、アクティブなアカウントのみを表示できます。

## <a name="account-subscription-ownership-faq"></a>アカウントのサブスクリプションの所有権に関する FAQ

このドキュメントでは、アカウントのサブスクリプションの所有権に関連する一般的な質問に回答します。

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>サブスクリプションあたりの Azure アカウント所有者は何人までですか?

サブスクリプションごとに 1 人のアカウント所有者のみが許可されます。  [Azure portal](https://portal.azure.com) のページの左上にある [サブスクリプション] タブで、ロールベースのアクセスまたは (アクセス制御 (IAM)) を使用してさらにロールを追加できます。

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Azure アカウント所有者は複数の部門の下に表示できますか?

いいえ。アカウント オーナーは、1 つの部署にのみ関連付けることができます。 このポリシーは、Azure EA Portal の EA 加入契約の下にある部門に関連付けられる、正確なコストと支出の監視と分配を確保するうえで有用です。

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Azure アカウント所有者をセキュリティグループとして表示できますか?

いいえ。サブスクリプションの所有者は、一意の Microsoft アカウント (MSA) 認証または Azure Active Directory (AAD) 認証である必要があります。 組織内で引き継ぐ場合は、汎用アカウントを作成し、AAD を使用してサブスクリプションへのアクセスを管理することを検討してください。

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>単一のユーザーが複数のサブスクリプションを所有できますか?

Azure アカウントの所有者は、無制限の数のサブスクリプションを作成および管理できます。

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>組織のすべてのサブスクリプションにアクセスする、または表示するにはどうすればよいですか?

現時点では、これはポリシーによって行われる必要があります。つまり、すべての作成されたサブスクリプションについて、ロールベースのアクセスを使用してアカウントをサブスクリプションロールに追加する必要があります。

### <a name="where-do-i-go-to-create-a-subscription"></a>サブスクリプションを作成するにはどうすればよいですか?

エンタープライズ Azure (EA) プランのサブスクリプションを作成する前に、Azure EA Portal の EA 加入契約の管理者によってアカウント オーナーのロールにアカウントを追加する必要があります。 その後、Azure EA Portal にサインインして、EA プラン タイプのサブスクリプションを作成するエンタイトルメントを取得する必要があります。 EA Portal の [サブスクリプション] タブにある [+ サブスクリプションの追加] リンクから最初の EA サブスクリプションを作成することをお勧めします。  ただし、アカウントに資格が付与された後は、ページの左上にある [サブスクリプション] タブの portal.azure.com でサブスクリプションを作成する方が簡単な場合があります。ここでは、サブスクリプションの作成と名前の変更の両方を 1 回のステップで行うことができます。

### <a name="who-can-create-a-subscription"></a>サブスクリプションを作成できるのは?

エンタープライズ Azure オファー タイプのサブスクリプションを作成するには、[EA Portal](https://ea.azure.com)でアカウント所有者の資格を持っている必要があります。

## <a name="next-steps"></a>次のステップ

- [仮想マシンの予約](ea-portal-vm-reservations.md)がコスト削減にどのように役立つかについて確認します。
- Azure EA Portal の問題のトラブルシューティングに関するヘルプが必要な場合は、「[Troubleshoot Azure EA portal access (Azure EA Portal へのアクセスのトラブルシューティング)](ea-portal-troubleshoot.md)」を参照してください。
