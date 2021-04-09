---
title: 'Azure Digital Twins 要求が失敗しました。状態: 403 (許可されていません)'
description: "'サービス要求が失敗しました。 状態:403 (許可されていません)' が Azure Digital Twins 上で発生する場合の原因と解決策。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: ad28b3300e9107c7f6d4b1987205583ef60ed658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100545702"
---
# <a name="service-request-failed-status-403-forbidden"></a>サービス要求が失敗しました。 状態:403 (許可されていません)

この記事では、Azure Digital Twins へのサービス要求から 403 エラーを受信した場合の原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

このエラーは、認証を必要とするさまざまな種類のサービス要求で発生する可能性があります。 影響としては、API 要求が失敗して `403 (Forbidden)` のエラーの状態が返されます。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

多くの場合、このエラーは、サービスに対して Azure ロールベースのアクセス制御 (Azure RBAC) のアクセス許可が正しく設定されていないことを示しています。 Azure Digital Twins インスタンスの多くのアクションを行うには、**管理しようとしているインスタンス** に対して、"*Azure Digital Twins データ所有者*" ロールを保持している必要があります。 

### <a name="cause-2"></a>原因 #2

[アプリ登録](how-to-create-app-registration.md)で認証を行っている Azure Digital Twins と通信するためにクライアント アプリを使用している場合は、Azure Digital Twins サービスに対するアクセス許可がアプリ登録で設定されていないという理由で、このエラーが発生することがあります。

アプリ登録には、Azure Digital Twins API に対するアクセス許可が構成されている必要があります。 その後、アプリ登録に対してクライアント アプリが認証されると、そのアプリ登録に構成されたアクセス許可が付与されます。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

1 つ目の解決策として、管理しようとしているインスタンスに対して、Azure ユーザーが "_**Azure Digital Twins データ所有者**_" ロールを保持していることを確認します。 このロールがない場合は、設定します。

このロールは以下とは異なることに注意してください。
* 以前、プレビュー期間中に付けられていたこのロールの名前 "*Azure Digital Twins 所有者 (プレビュー)* " (ロールは同じですが、名前が変更されています)
* Azure サブスクリプション全体に対する "*所有者*" ロール。 "*Azure Digital Twins データ所有者*" は、Azure Digital Twins 内のロールであり、この個別の Azure Digital Twins インスタンスにスコープが設定されています。
* Azure Digital Twins での "*所有者*" ロール。 これらは 2 つの個別の Azure Digital Twins 管理ロールであり、"*Azure Digital Twins データ所有者*" は管理に使用する必要があるロールです。

#### <a name="check-current-setup"></a>現在の設定を確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>問題を修正する 

このロールが割り当てられていない場合は、**Azure サブスクリプション** 内で所有者ロールを保持するユーザーが次のコマンドを実行して、Azure ユーザーに **Azure Digital Twins インスタンス** 上での "*Azure Digital Twins データ所有者*" ロールを付与します。 

サブスクリプション上でご自身が所有者になっている場合は、このコマンドを自分で実行できます。 そうでない場合は、所有者に連絡して、このコマンドを代わりに実行してもらいます。

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

このロールの要件と割り当てプロセスの詳細については、「[*ユーザーのアクセス許可の設定*」セクション](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)を参照してください。"*方法:インスタンスと認証を設定する (CLI またはポータル)* " に関するページに含まれています

このロールが既に割り当てられており、"*かつ*" クライアント アプリを認証するために Azure AD アプリ登録を使用しており、この解決策で 403 の問題が解決されなかった場合は、次の解決策に進むことができます。

### <a name="solution-2"></a>解決策 #2

クライアント アプリを認証するために Azure AD アプリ登録を使用している場合は、2 つ目の解決策として、アプリ登録に Azure Digital Twins サービスに対するアクセス許可が構成されていることを確認します。 構成されていない場合は、設定を行います。

#### <a name="check-current-setup"></a>現在の設定を確認する

アクセス許可が正しく構成されたかどうかを確認するには、Azure portal の [Azure AD アプリ登録の概要ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)に移動します。 ポータルの検索バーで "*アプリの登録*" を検索することで、このページにアクセスできます。

*[すべてのアプリケーション]* タブに切り替えると、サブスクリプションで作成されたすべてのアプリ登録が表示されます。

先ほど作成したアプリ登録がこの一覧に表示されます。 それを選択すると、その詳細が開かれます。

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure portal のアプリの登録ページ":::

最初に、Azure Digital Twins のアクセス許可の設定が登録時に正しく設定されたことを確認します。 これを行うには、メニュー バーから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 値は、次のスクリーンショットの値と一致している必要があります。

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure AD アプリ登録のためのマニフェストのポータル ビュー":::

次に、メニュー バーから *[API のアクセス許可]* を選択して、このアプリ登録に Azure Digital Twins に対する読み取り/書き込みアクセス許可が含まれていることを確認します。 次のようなエントリが表示されます。

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure Digital Twins に対する '読み取り/書き込みアクセス' を示す、Azure AD アプリ登録のための API のアクセス許可のポータル ビュー":::

#### <a name="fix-issues"></a>問題を修正する

これが説明とは異なる表示になっている場合は、["*アプリ登録を作成する方法*"](how-to-create-app-registration.md) に関するページを参照して、アプリ登録を設定する方法の手順に従います。

## <a name="next-steps"></a>次のステップ

新しい Azure Digital Twins インスタンスを作成して認証するための設定手順を確認してください。
* [*方法: インスタンスと認証を設定する (CLI)*](how-to-set-up-instance-cli.md)

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)
