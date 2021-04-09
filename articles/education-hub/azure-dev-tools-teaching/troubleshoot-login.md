---
title: Azure Dev Tools for Teaching のログイン エラーを解決する
description: 学生が Azure Dev Tools for Teaching にログインするときにエラー メッセージが表示された場合に、実行するアクションについて説明しています。
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094598"
---
# <a name="troubleshooting-student-login-issues"></a>学生のログインに関する問題のトラブルシューティング
Azure Dev Tools for Teaching にアクセスするには、ユーザーが Microsoft アカウント (MSA) を持っている必要があります。 学生のアカウントがまだ MSA でない場合、または MSA にリンクされていない場合は、自動的に MSA を作成するように指示されます。 ドメインが Active Directory に関連付けられている場合、そのドメインのすべてのアカウントは既に MSA であると見なされます。

学生がログインしようとして次のエラー メッセージを受け取る場合は、以下に示す解決策のいずれかを使用してください。

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="サインインのエラー メッセージ。" border="false":::

2 つの解決策があります。新しい Microsoft アカウントを作成するか、既存の Microsoft アカウントを使用します。

## <a name="create-a-new-microsoft-account"></a>新しい Microsoft アカウントを作成する
### <a name="use-a-university-email-address"></a>大学のメール アドレスを使用する
大学のメール アドレス (`John.Smith@university.edu` など) を使用してログインしている場合は、そのメール アドレスを使用して Microsoft アカウントを作成する必要があります。 アカウントの作成は無料であり、数分で完了します。 Microsoft アカウントがあれば、1 つのユーザー名とパスワードですべての Microsoft 製品に自動的にログインできます。

### <a name="use-a-personal-email-address"></a>個人用のメール アドレスを使用する
個人用のメール アドレス (`John.Smith@email.com` など) を使用してログインし、大学のメール アドレスも持っている場合は、大学のメール アドレスを使用してみてください。 以前に個人用のメール アドレスを使用して教育機関の Web ストアにログインした場合、または大学のメール アドレスを持っていない場合は、個人用のメール アドレスを使用して Microsoft アカウントを作成またはリンクする必要があります。

## <a name="use-an-existing-microsoft-account"></a>既存の Microsoft アカウントを使用する
学生が既存の Microsoft アカウント (Xbox など) を持っている場合、そのアカウントを Azure Dev Tools アカウントに接続することができます。

1. https://account.microsoft.com にアクセスします。
1. Microsoft アカウントの資格情報を使用してログインします。
1. 上部のリボン メニューから **[ユーザーの情報]** を選択します。

1. **[Microsoft にサインインする方法の管理]** をクリックします。 本人確認を求めるメッセージが表示されます。 セキュリティ コードがメールで送信されます。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="サインインを管理します。" border="false":::

1. メールで送信されたセキュリティ コードを入力します。

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="サインイン コードを入力します。" border="false":::

1. アカウントに **[メールを追加]** をクリックし、大学のメール アドレスを入力します。
次にサインインするときに、大学のメール アドレスを使用して Azure Dev Tools for Teaching にアクセスできるようになります。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Microsoft にサインインする方法を管理します。" border="false":::

## <a name="next-steps"></a>次のステップ
- [FAQ](program-faq.md)

- [サポート オプション](program-support.md)
