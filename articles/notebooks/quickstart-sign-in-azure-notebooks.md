---
title: Azure Notebooks プレビューにサインインする
description: Azure Notebooks プレビューにすばやくサインインし、ユーザー ID を設定します。これにより、保存済みのプロジェクトにアクセスしたり、ノートブックを他のユーザーと共有したりできるようになります。
ms.topic: quickstart
ms.date: 04/15/2019
ms.openlocfilehash: 8a2d9836cd005ab11106c569929a6776d605da50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85830419"
---
# <a name="quickstart-sign-in-and-set-a-user-id-for-azure-notebooks-preview"></a>クイック スタート:Azure Notebooks プレビューにサインインしてユーザー ID を設定する

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

サインインしなくても Azure Notebooks を常に表示できますが、ノートブックを実行し、保存されたプロジェクトとノートブックにアクセスし、他のユーザーとノートブックを共有するには、サインインする必要があります。

## <a name="sign-in"></a>サインイン

1. [notebooks.azure.com](https://notebooks.azure.com/) の右上にある **[Sign in]\(サインイン\)** を選択します。

    ![Azure Notebooks でのサインイン コマンドの場所](media/accounts/sign-in-command.png)

1. 入力を求められたら、Microsoft アカウントか、職場または学校アカウントのメール アドレスを入力して、 **[次へ]** を選択します。 アカウントの種類は、「[Your user account for Azure Notebooks (Azure Notebook のユーザー アカウント)](azure-notebooks-user-account.md)」で説明されています。 Microsoft アカウントがない場合、または Azure Notebooks 専用のアカウントにしたい場合は、 **[Create one]\(作成\)** を選択します。

    ![サインイン プロンプトでの Microsoft アカウント新規作成コマンド](media/accounts/create-new-microsoft-account.png)

    > [!Tip]
    > 既にアカウントが関連付けられているメール アドレスで新しいアカウントを作成しようとすると、"職場または学校のメール アドレスではここにサインアップできません。 Gmail や Yahoo! などの個人用メールを使用するか、新しい Outlook メールを取得してください" というメッセージが表示される場合があります。 その場合は、新しいアカウントを作成せずに仕事用メール アドレスでサインインしてみてください。

1. パスワードを求められたら、入力します。

1. 初めてサインインする場合は、Azure Notebooks でアカウントへのアクセス許可を求められます。 **[はい]** を選択して続行します。

    ![アカウントのアクセス許可プロンプト](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>ユーザー ID を設定する

1. 初めてサインインするときに、"anon-idrca3" のような一時的なユーザー ID が割り当てられます。 "anon-" で始まるユーザー ID を使用しているときは常に、独自の ID の作成を求めるメッセージが表示されます。 ユーザー ID はプロジェクトとノートブックを共有するために取得する URL で使用されるので、一意でわかりやすいものを選択します。

    ![Azure Notebooks 用のユーザー ID を入力するプロンプト](media/accounts/create-user-id.png)

    **[No Thanks]\(必要ありません\)** を選択すると、サインインするたびにユーザー ID を要求するメッセージが表示され続けます。 ユーザー ID は、[ユーザー プロファイル](azure-notebooks-user-profile.md)でいつでも設定できます。

1. 正常にサインインすると、Azure Notebooks のパブリック プロファイル ページに移動します。そこでは、 **[Edit Profile Information]\(プロファイル情報の編集\)** を選択して、情報の残りの部分を入力できます (詳しくは、「[Your profile and user ID (プロファイルとユーザー ID)](azure-notebooks-user-profile.md)」をご覧ください)。

    ![Azure Notebooks プロファイル ページの初期表示](media/accounts/profile-page-new.png)

> [!NOTE]
> "User ID is already in use"\(ユーザー ID は既に使用中です\) というメッセージが表示された場合は、別の ID を試してください。 ユーザー ID はすべての Azure Notebooks アカウントで一意です。また、Azure Notebooks では、Microsoft のブランド名など、特定のユーザー ID が予約されています。

## <a name="sign-out"></a>サインアウトする

サインアウトするには、ページの右上のユーザー名を選択して、 **[Sign out]\(サインアウト\)** を選択します。

![Azure Notebooks でのサインアウト コマンドの場所](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: ノートブックを作成して共有する](quickstart-create-share-jupyter-notebook.md)
