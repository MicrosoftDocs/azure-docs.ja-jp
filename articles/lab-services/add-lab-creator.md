---
title: Azure Lab Services でラボ作成者としてユーザーを追加する
description: この記事では、Azure Lab Services でラボ アカウントのラボ作成者ロールにユーザーを追加する方法を紹介します。 ラボ作成者はこのラボ アカウントでラボを作成できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435962"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Azure Lab Services でラボ アカウントにラボ作成者を追加する
この記事では、Azure Lab Services でラボ アカウントにラボ作成者としてユーザーを追加する方法を紹介します。 追加されたユーザーは、そのラボ アカウントでラボを作成できます。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Microsoft ユーザー アカウントをラボ作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける **ラボの作成者** ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 クラスルーム ラボの作成に、同じユーザー アカウントを使用する場合は、この手順をスキップすることができます。 クラスルーム ラボの作成に、別のユーザー アカウントを使用する場合は、次の手順に従います。 

クラスのラボを作成するアクセス許可を教師に与えるには、教師を **ラボの作成者** ロールに追加します。

1. **[ラボ アカウント]** ページで、 **[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ ロール割り当ての追加]** をクリックします。 

    ![[アクセス制御] -> [ロールの割り当ての追加] ボタン](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **[ロールの割り当ての追加]** ページで、 **[ロール]** から **[ラボの作成者]** を選択し、ラボの作成者ロールに追加するユーザーを選択した後、 **[保存]** を選択します。 

    ![ラボ作成者の追加](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Microsoft 以外のアカウント ユーザーをラボ作成者として追加する場合は、「[Microsoft 以外のアカウント ユーザーをラボ作成者として追加する](#add-a-non-microsoft-account-user-as-a-lab-creator)」セクションを参照してください。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Microsoft 以外のアカウント ユーザーをラボ作成者として追加する
ユーザーをラボ作成者として追加するには、そのユーザーのメール アカウントを使用します。 次の種類のメール アカウントを使用できます。

- 大学の Azure Active Directory (AAD) によって提供されるメール アカウント。
- Microsoft のメール アカウント (`@outlook.com`、`@hotmail.com`、`@msn.com`、`@live.com` など)。
- Microsoft 以外のメール アカウント (Yahoo または Google によって提供されるものなど)。 ただし、これらの種類のアカウントは、Microsoft アカウントとリンクされている必要があります。
- GitHub アカウント。 このアカウントは、Microsoft アカウントとリンクされている必要があります。

### <a name="using-a-non-microsoft-email-account"></a>Microsoft 以外のメール アカウントの使用
ラボ作成者/講師は、Microsoft 以外のメール アカウントを使用して、クラスルーム ラボに登録してサインインすることができます。  ただし、Lab Services ポータルへのサインインには、講師が最初に、Microsoft 以外のメール アドレスにリンクされている Microsoft アカウントを作成する必要があります。

多くの講師は、Microsoft 以外のメール アドレスにリンクされた Microsoft アカウントを既に持っている可能性があります。 たとえば、講師が Microsoft の他の製品やサービス (Office、Skype、OneDrive、Windows など) でメール アドレスを使用したことがある場合は、既に Microsoft アカウントを持っています。  

講師が Lab Services ポータルにサインインするときは、メール アドレスとパスワードの入力を求められます。 講師が、Microsoft アカウントがリンクされていない Microsoft 以外のアカウントでサインインしようとすると、講師は次のエラー メッセージを受け取ります。 

![エラー メッセージ](./media/how-to-configure-student-usage/cant-find-account.png)

Microsoft アカウントにサインアップするには、講師は [http://signup.live.com](http://signup.live.com) にアクセスする必要があります。  


### <a name="using-a-github-account"></a>GitHub アカウントの使用
講師は、既存の GitHub アカウントを使用して、クラスルーム ラボに登録してサインインすることもできます。 講師が GitHub アカウントにリンクされている Microsoft アカウントを既に持っている場合は、前のセクションで示したように、サインインしてパスワードを入力できます。 まだ GitHub アカウントを Microsoft アカウントにリンクしていない場合は、 **[サインイン オプション]** を選択する必要があります。

![サインイン オプションのリンク](./media/how-to-configure-student-usage/signin-options.png)

**[サインイン オプション]** ページで、 **[GitHub でサインイン]** を選択します。

![GitHub リンクを使用してサインインする](./media/how-to-configure-student-usage/signin-github.png)

最後に、GitHub アカウントにリンクされている Microsoft アカウントを作成するように求められます。 これは、講師が **[次へ]** を選択したときに自動的に行われます。  講師はすぐにサインインし、クラスルーム ラボに接続されます。


## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてラボにアクセスする](how-to-use-classroom-lab.md)
