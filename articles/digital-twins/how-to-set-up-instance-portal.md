---
title: インスタンスと認証を設定する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins サービスのインスタンスを設定する方法について説明します
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1f7486f1080c0fbb25b1be6ab70bb647a546ceca
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234992"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Azure Digital Twins インスタンスと認証を設定する (ポータル)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する**手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が整います。

この記事のこのバージョンでは、Azure portal を使用して、これらの手順を 1 つずつ手動で実行します。 Azure portal は、コマンドライン ツールに代えて使用できる、Web ベースの統合コンソールです。
* CLI を使用してこれらの手順を手動で実行するには、こちら記事の CLI バージョンを確認してください: [*方法: インスタンスと認証を設定する (CLI)* ](how-to-set-up-instance-cli.md)。
* デプロイ スクリプトのサンプルを使用して自動での設定を実行するには、こちらの記事のスクリプト化されたバージョンを確認してください: [*方法: インスタンスと認証を設定する (スクリプト化)* ](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを作成する

このセクションでは、[Azure portal](https://ms.portal.azure.com/) を使用して、**Azure Digital Twins の新しいインスタンスを作成**します。 ポータルに移動し、自分の資格情報でログインします。

Azure portal に入ったら、Azure サービスのホーム ページ メニューで _[リソースの作成]_ を選択して開始します。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Azure portal のホーム ページから [リソースの作成] を選択する":::

検索ボックスで "*Azure Digital Twins*" を検索し、結果から **[Azure Digital Twins (Preview)]\(Azure Digital Twins (プレビュー)\)** サービスを選択します。 _[作成]_ ボタンを選択して、サービスの新しいインスタンスを作成します。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Azure Digital Twins サービスのページから [作成] を選択する":::

以下の *[リソースの作成]* ページで、次に示した値を入力します。
* **サブスクリプション**:使用している Azure サブスクリプション
  - **[リソース グループ]** :インスタンスのデプロイ先にするリソース グループ。 検討できる既存のリソース グループがまだない場合は、 *[新規作成]* リンクを選択して新しいリソース グループの名前を入力することで、ここで作成することができます
* **[場所]** :デプロイする Azure Digital Twins 対応のリージョン。 リージョンのサポートに関する詳細については、"[*リージョン別の利用可能な Azure 製品 (Azure Digital Twins)* ](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)" に関するページを参照してください。
* **[リソース名]** : Azure Digital Twins インスタンスの名前。 新しいインスタンスの名前は、サブスクリプションのリージョン内で一意である必要があります (つまり、サブスクリプションのリージョン内に、選択した名前を既に使用している別の Azure Digital Twins インスタンスが存在する場合は、異なる名前を選択するよう求められます)。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="説明に示された値を入力して Azure Digital Twins リソースを作成する":::

終わったら、 _[確認と作成]_ を選択します。 これにより、[概要] ページへ遷移します。ここでは、入力したインスタンスの詳細を確認して、 _[作成]_ を押します。 

### <a name="verify-success-and-collect-important-values"></a>成功を確認して重要な値を収集する

*[作成]* を押した後は、ポータル アイコン バーの Azure 通知にインスタンスのデプロイの状態が表示されます。 この通知はデプロイが成功した時点で示され、作成したインスタンスを表示するための _[リソースに移動]_ ボタンを選択できるようになります。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="デプロイの成功の表示と [リソースに移動] ボタンの強調表示がある Azure 通知のビュー":::

また、デプロイが失敗した場合は、その理由が通知に示されます。 エラー メッセージの通知内容を確認して、インスタンスの作成を再試行します。

>[!TIP]
>インスタンスが作成されたら、Azure portal の検索バーでインスタンスの名前を検索して、いつでもそのページに戻ることができます。

インスタンスの *[概要]* ページで、 *[名前]* 、 *[リソース グループ]* 、および *[ホスト名]* をメモします。 これらはすべて、Azure Digital Twins インスタンスの操作を続行する際に必要になる可能性がある重要な値です。 他のユーザーがそのインスタンスに対してプログラミングする場合は、これらの値を彼らと共有する必要があります。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="インスタンスの [概要] ページの重要な値の強調表示":::

これで、Azure Digital Twins インスタンスの準備が完了しました。 次に、適切な Azure ユーザーにそれを管理するためのアクセス許可を付与します。

## <a name="set-up-user-access-permissions"></a>ユーザーのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

最初に、Azure portal 上の Azure Digital Twins インスタンスのページを開きます。 インスタンスのメニューから *[アクセス制御 (IAM)]* を選択します。 *[ロールの割り当てを追加する]* にある *[追加]* ボタンを選択します。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="[アクセス制御 (IAM)] ページからロールの割り当てを追加することを選ぶ":::

次の *[ロールの割り当てを追加する]* ページで、値を入力します (Azure サブスクリプションにおいて[十分なアクセス許可](#prerequisites-permission-requirements)を持つユーザーが完了する必要があります)。
* **ロール**: ドロップダウン メニューから *[Azure Digital Twins 所有者 (プレビュー)]* を選択します
* **アクセスの割り当て先**:ドロップダウン メニューから *[Azure AD user, group or service principal]\(Azure AD ユーザー、グループ、またはサービス プリンシパル\)* を選択します
* **Select**:割り当てるユーザーの名前または電子メール アドレスを検索します。 結果を選択すると、 *[選択したメンバー]* セクションにそのユーザーが表示されます。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="[ロールの割り当てを追加する] ダイアログに表示されたフィールドへの入力":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

詳細情報の入力が完了したら、 *[保存]* ボタンをクリックします。

### <a name="verify-success"></a>成功を確認する

*[アクセス制御 (IAM)] > [ロールの割り当て]* 下で設定したロールの割り当てを確認できます。 *[Azure Digital Twins 所有者 (プレビュー)]* のロールの一覧に、ユーザーが表示されます。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのビュー":::

これで、Azure Digital Twins インスタンスの準備が完了し、これを管理するためのアクセス許可が割り当てられました。 次に、それにアクセスするためのクライアント アプリのアクセス許可を設定します。

## <a name="set-up-access-permissions-for-client-applications"></a>クライアント アプリケーションのアクセス許可を設定する

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

まず、Azure portal 上で [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) に移動します (このリンクを使用するか、ポータルの検索バーを使って検索できます)。 サービス メニューから *[アプリの登録]* 、 *[+ 新しい登録]* の順に選択します。

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="[アプリの登録] メニュー オプションと [+ 新しい登録] ボタンが強調表示されている Azure portal 上の Azure AD サービス ページのビュー":::

以下の *[アプリケーションの登録]* ページで、要求される次の値を入力します。
* **Name**:登録に関連付ける Azure AD アプリケーションの表示名
* **サポートされているアカウントの種類**: *[この組織ディレクトリのみに含まれるアカウント (既定のディレクトリのみ - シングル テナント)]* を選択します
* **リダイレクト URI**:Azure AD アプリケーションの "*Azure AD アプリケーション応答 URL*"。 `http://localhost` を使用できます。

完了したら、 *[登録]* ボタンを押します。

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="説明に示された値が入力されている [アプリケーションの登録] ページのビュー":::

登録の設定が完了したら、ポータルによって詳細ページにリダイレクトされます。

### <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API のアクセス許可を指定する

次に、Azure Digital Twins API へのベースライン アクセス許可によって、作成したアプリ登録を構成します。

アプリ登録のポータル ページで、メニューから *[API のアクセス許可]* を選択します。 以下のアクセス許可ページで、 *[+ アクセス許可の追加]* ボタンを押します。

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="[API のアクセス許可] メニュー オプションと [+ アクセス許可の追加] ボタンが強調表示されている Azure portal 上のアプリの登録のビュー":::

以下の *[API アクセス許可の要求]* ページで、 *[所属する組織で使用している API]* タブに切り替えて、"*azure digital twins*" を探します。 検索結果から "*Azure Digital Twins*" を選択して、Azure Digital Twins API に対するアクセス許可の割り当てを続行します。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Azure Digital Twins が表示された [API アクセス許可の要求] ページの検索結果のビュー":::

>[!NOTE]
> Azure Digital Twins の以前の (2020 年 7 月より前の) パブリック プレビューで作成されたサービス インスタンスがサブスクリプション内にまだある場合は、代わりに *Azure Smart Spaces Service* を検索する必要があります。 これは同じ API セットの古い名前であり、操作手順に変更はありません。

次に、これらの API に対して付与するアクセス許可を選択します。 **[Read (1)]\(読み取り (1)\)** アクセス許可を展開して、 *[Read.Write]\(読み取り.書き込み\)* と示されたチェック ボックスをオンにし、このアプリ登録に読み取りおよび書き込みのアクセス許可を付与します。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Azure Digital Twins API の [Read.Write]\(読み取り.書き込み\) アクセス許可を選択した [API アクセス許可の要求] ページのビュー":::

完了したら、 *[アクセス許可の追加]* を押します。

### <a name="verify-success"></a>成功を確認する

*[API のアクセス許可]* ページに戻り、読み取り/書き込みのアクセス許可が反映された Azure Digital Twins のエントリがあることを確認します。

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Azure Digital Twins に対する [Read/Write Access]\(読み取り/書き込みアクセス\) が表示された、Azure AD アプリ登録の API のアクセス許可のポータル ビュー":::

また、アプリ登録の *manifest.json* 内で Azure Digital Twins への接続を検証できます。これは、API のアクセス許可を追加したときに、Azure Digital Twins 情報によって自動的に更新されました。

これを行うには、メニューから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 値は、次のスクリーンショットの値と一致している必要があります。

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Azure AD アプリ登録のためのマニフェストのポータル ビュー。"requiredResourceAccess" 下で入れ子になっている "resourceAppId" 値 の 0b07f429-9f4b-4714-9392-cc5e8e80c8b0、および "resourceAccess > id" 値の 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>重要な値を収集する

次に、メニュー バーから *[概要]* を選択して、アプリ登録の詳細を表示します。

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="アプリ登録の重要な値のポータル ビュー":::

**実際の**ページに表示される、"*アプリケーション (クライアント) ID*" と "*ディレクトリ (テナント) ID*" をメモしておきます。 これらの値は、後で [Azure Digital Twins API に対してクライアント アプリを認証する](how-to-authenticate-client.md)ために必要になります。 自分がこのようなアプリケーションのコードの作成を担当しない場合は、これらの値をその担当者に共有する必要があります。

### <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>次のステップ

クライアント アプリの認証コードを作成してクライアント アプリケーションをインスタンスに接続する方法について説明します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)