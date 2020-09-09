---
title: 'Azure Digital Twins 要求が失敗しました。状態: 403 (許可されていません)'
description: "'サービス要求が失敗しました。 状態:403 (許可されていません)' が Azure Digital Twins 上で発生する場合の原因と解決策。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: a971291dd423894e4d04158abe873a7222f9802c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405613"
---
# <a name="service-request-failed-status-403-forbidden"></a>サービス要求が失敗しました。 状態:403 (許可されていません)

この記事では、Azure Digital Twins へのサービス要求から 403 エラーを受信した場合の原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

このエラーは、認証を必要とするさまざまな種類のサービス要求で発生する可能性があります。 影響としては、API 要求が失敗して `403 (Forbidden)` のエラーの状態が返されます。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

多くの場合、このエラーは、サービスに対するロールベースのアクセス制御 (RBAC) アクセス許可が正しく設定されていないことを示しています。 Azure Digital Twins インスタンスの多くのアクションを行うには、**管理しようとしているインスタンス**に対して "*Azure Digital Twins Owner (プレビュー)* " ロールを保持している必要があります。 

### <a name="cause-2"></a>原因 #2

クライアント アプリを使用して Azure Digital Twins と通信する場合に、[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) アプリ登録に Azure Digital Twins サービスに対するアクセス許可が設定されていないために、このエラーが発生することがあります。

アプリ登録に、Azure Digital Twins API に対するアクセス許可を構成する必要があります。 その後、アプリ登録に対してクライアント アプリが認証されると、そのアプリ登録に構成されたアクセス許可が付与されます。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

1 つ目の解決策として、Azure ユーザーが、管理しようとしているインスタンスに対して "_**Azure Digital Twins Owner (プレビュー)**_" ロールを保持していることを確認します。 このロールがない場合は、設定します。

このロールは以下とは異なることに注意してください。
* Azure サブスクリプション全体に対する "*所有者*" ロール。 "*Azure Digital Twins Owner (プレビュー)* " は、Azure Digital Twins 内のロールであり、この個別の Azure Digital Twins インスタンスにスコープが設定されています。
* Azure Digital Twins での "*所有者*" ロール。 これらは 2 つの個別の Azure Digital Twins 管理ロールであり、"*Azure Digital Twins Owner (プレビュー)* " はプレビュー期間中の管理に使用する必要があるロールです。

#### <a name="check-current-setup"></a>現在の設定を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>問題を修正する 

このロールが割り当てられていない場合は、**Azure サブスクリプション**内で所有者ロールを保持するユーザーが次のコマンドを実行して、Azure ユーザーに **Azure Digital Twins インスタンス**上での "*Azure Digital Twins Owner (プレビュー)* " ロールを付与します。 

サブスクリプション上でご自身が所有者になっている場合は、このコマンドを自分で実行できます。 そうでない場合は、所有者に連絡して、このコマンドを代わりに実行してもらいます。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

このロールの要件と割り当てプロセスの詳細については、「[*ユーザーのアクセス許可の設定*」セクション](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)を参照してください。"*方法:インスタンスと認証を設定する (CLI またはポータル)* " に関するページに含まれています

このロールが既に割り当てられていても、403 の問題が引き続き発生する場合は、次のソリューションへ進みます。

### <a name="solution-2"></a>解決策 #2

2 つ目の解決策として、Azure AD アプリ登録に Azure Digital Twins サービスに対するアクセス許可が構成されていることを確認します。 これが構成されていない場合は、設定します。

#### <a name="check-current-setup"></a>現在の設定を確認する

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

最初に、Azure Digital Twins のアクセス許可の設定が登録に対して正しく設定されたことを確認します。 これを行うには、メニュー バーから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 値は、次のスクリーンショットの値と一致している必要があります。

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>問題を修正する

これが説明とは異なる表示になっている場合は、「[*クライアント アプリケーションのアクセス許可の設定*」セクション](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications)にあるアプリ登録の設定方法の手順に従ってください。"*方法: インスタンスと認証を設定する (CLI またはポータル)* " に関するページに含まれています

## <a name="next-steps"></a>次のステップ

新しい Azure Digital Twins インスタンスを作成して認証するための設定手順を確認してください。
* [*方法: インスタンスと認証を設定する (CLI)* ](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)