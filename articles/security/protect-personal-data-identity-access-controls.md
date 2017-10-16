---
title: "Azure ID とアクセスの制御で個人データを保護する | Microsoft Docs"
description: "Azure ID とアクセスの制御を使った個人データの保護"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 7c66a95d5a056f59e0f28dba4e0880e72e74dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory と Multi-Factor Authentication: ID とアクセスの制御による個人のデータの保護

この記事では、Azure Active Directory と Multi-Factor Authentication のセキュリティ機能およびサービスを使って個人データを保護するために使用できる情報と手順を示します。

## <a name="scenario"></a>シナリオ

米国に本社を置く大手クルーズ会社が、地中海、アドリア海、バルト海だけでなくイギリス諸島での旅程を提供できるように、事業を拡張しています。 この会社は、これらの取り組みを支援するために、イタリア、ドイツ、デンマーク、英国に拠点を置くいくつかの小規模なクルーズ会社を買収しました。 

その会社では、クラウドに会社のデータを保存するために Microsoft Azure を使います。 これには、グローバルな顧客ベースの名前や住所、電話番号、クレジット カード情報など、個人を特定できる情報が含まれます。 また、住所、電話番号、納税者番号など、あらゆる場所の会社の従業員に関する標準的な人事情報も含まれます。 このクルーズ会社では、現在と過去の顧客とのリレーションシップを追跡するための個人情報を含む、特典やロイヤリティ プログラム メンバーの大規模なデータベースも管理しています。

この企業の従業員は、会社の支社からネットワークにアクセスし、世界各地に存在する旅行代理店の社員は、一部の会社リソースにアクセスできます。

## <a name="problem-statement"></a>問題の説明

会社では、セキュリティに欠陥のある ID を使ってアクセスしようとしている攻撃者から顧客と従業員の個人データのプライバシーを保護する必要があります。 また、正当なユーザーによる個人データへのアクセスも、自分の職務を遂行するためにアクセスを必要とするユーザーのみに制限する必要があります。

## <a name="company-goal"></a>会社の目標

会社の目標は、個人データへのアクセスが厳しく制御されるようにすることです。 個人データにアクセスできるユーザーの ID を強力な認証によって保護することが重要です。 [最低限の特権] のポリシー (https://en.wikipedia.org/wiki/Principle_of_least_privilege) を適用して、正当なユーザーが必要なレベルのアクセス権だけを持ち、それを超える権限を持たないようにする必要があります。

## <a name="solutions"></a>解決方法

Microsoft Azure では、個人データを含むリソースにアクセスできるユーザーを企業が制御するのに役立つ ID とアクセス管理のツールを提供しています。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) は ID を管理し、Azure に加えてその他のオンプレミスとクラウドのリソース、データ、アプリケーションへのアクセスを制御します。 [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) は、Azure 管理者が、個人データなどの特定の情報にアクセスできるユーザーの数を最小化するのに役立ちます。 特権を持つ ID とその ID によるリソースへのアクセスを検出、制限、監視し、一時的な Just-In-Time (JIT) 管理権限を対象ユーザーに割り当てることができます。 AAD 管理特権を持つユーザーを把握することもできます。

AAD PIM の使用に関連するアクティビティは、次のとおりです。

- ディレクトリで Privileged Identity Management を有効にする

- Privileged Identity Management の管理ダッシュボードを使って、重要な情報を一目で確認する

- 各ロールに永続的管理者または管理者候補を追加または削除することによって、特権 ID (管理者) を管理する

- ロールのアクティブ化設定を構成する

- ロールをアクティブ化する

- ロール アクティビティを確認する

#### <a name="how-do-i-enable-aad-pim"></a>AAD PIM を有効にする方法

ディレクトリでの PIM の使用を開始するには、以下の手順を実行します。

1. ディレクトリのグローバル管理者として Azure Portal にサインインします。

2. 組織に複数のディレクトリがある場合は、Azure Portal の右上隅に表示されているユーザー名を選択し、 Azure AD Privileged Identity Management を使用するディレクトリを選択します。

3. **[その他のサービス]** を選択し、**[フィルター]** ボックスを使って "Azure AD Privileged Identity Management" を検索します。

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 Privileged Identity Management アプリケーションが起動します。

Azure AD Privileged Identity Management を設定すると、アプリケーションを開くたびにナビゲーション ブレードが表示されます。

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

AAD PIM の詳細と開始手順については、「[Azure AD Privileged Identity Management の使用開始](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)」をご覧ください。

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

[Azure ロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) によって、Azure 管理者は、ユーザーの割り当てられたロールに基づくアクセス権の付与を有効にして Azure リソースへのアクセスを管理できます。 チーム内の職務を分離し、それぞれの職務に必要なアクセス権のみをユーザー、グループ、アプリケーションに付与することができます。

ロールベースのアクセス権をユーザーに付与するには、Azure ポータル、Azure コマンドライン ツール、Azure Management API を使用します。

Azure RBAC の基礎について詳しくは、「[Azure Portal でのロールベースの Access Control の基礎を確認する](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)」をご覧ください。

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>PowerShell を使ってAzure RBAC を管理する方法

PowerShell コマンドレットを使って Azure RBAC を管理できます。次のような管理タスクがあります。

- ロールの一覧表示

- アクセスできるユーザーの確認

- アクセス権の付与

- アクセス権の削除

- カスタム ロールの作成

- リソース プロバイダーの操作を取得する

- カスタム ロールの修正

- カスタム ロールの削除

- カスタム ロールの一覧表示

PowerShell で Azure RBAC を管理する手順については、「[Azure PowerShell を使用したロールベースのアクセス制御の管理](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell)」をご覧ください。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) は、2 段階認証ソリューションです。シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護できます。 電話、テキスト メッセージ、モバイル アプリによる確認など、一連の照合方法を通じて確実な認証を行うことができます。

Azure クラウドで MFA をデプロイするには、最初にそれを有効にしてから、ユーザーの 2 段階認証をオンにする必要があります。

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Azure で MFA の使用を有効にする方法

ユーザーが Azure Multi-Factor Authentication を利用できるライセンスをお持ちであれば、Azure MFA を有効にするうえで必要な作業は特にありません。 そうでない場合は、ディレクトリに多要素認証プロバイダーを作成する必要があります。 そのためには、次の手順に従います。

1. Azure クラシック ポータル (管理者としてログオン) で **[Active Directory]** を選択します。

2. **Multi-Factor Authentication プロバイダー**を選択します。

3. **[新規]** を選択し、**[App Services]** の下の **[多要素認証プロバイダー]** を選択します。

4. **[簡易作成]** を選択します。

5. [名前] フィールドに入力し、(認証ごとまたは有効なユーザーごとに) 使用モデルを選びます。

6. MFA プロバイダーが関連付けられているディレクトリを指定します。

7. **[作成]** ボタンをクリックします。

![](media/protect-personal-data-identity-access-controls/quick-create.png)

多要素認証プロバイダーを管理する手順については、「[Azure Multi-Factor Auth プロバイダーの概要](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)」をご覧ください。

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>ユーザーに対して 2 段階認証をオンにする方法

すべてのサインインに対して 2 段階認証を実施するか、条件付きアクセス ポリシーを作成して特定の条件が適用されるときにのみ 2 段階認証を要求することができます。

ユーザーの状態を変更することで Azure MFA を有効にする方法は、2 段階認証を要求するための従来のアプローチです。 有効にしたすべてのユーザーの要件が同じになり、サインインするたびに 2 段階認証が実行されます。 ユーザーを有効にすると、そのユーザーに影響を与える可能性があるすべての条件付きアクセス ポリシーが無効になります。

条件付きアクセス ポリシーを使って Azure MFA を有効にする方法の方が、より柔軟に 2 段階認証を要求できます。 個々のユーザーだけでなくグループに適用する条件付きアクセス ポリシーを作成することができます。 危険度の高いグループには、危険度の低いグループよりも多くの制限を割り当てることや、危険度の高いクラウド アプリにのみ 2 段階認証を要求して、危険度の低いクラウド アプリではスキップすることができます。 ただし、条件付きアクセスは、Azure Active Directory の有料機能です。

ユーザーの状態を変更することで MFA を有効にするには、以下の手順を実行します。

1. Azure Portal に管理者としてサインインします。
2. **[Azure Active Directory] \> [ユーザーとグループ] \> [すべてのユーザー]** に移動します。
3. **[Multi-Factor Authentication]** を選択します。
4. Azure MFA で有効にするユーザーを見つけます。 上部でビューを変更することが必要になる場合があります。
5. ユーザーの名前の横にあるチェック ボックスをオンにします。
6. 右側にある [クイック操作] の下の **[有効にする]** を選択します。

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. 開いたポップアップ ウィンドウで選択内容を確認します。  MFA が有効になっているユーザーは、次回のサインイン時に登録するよう求められます。

条件付きアクセス ポリシーを使って Azure MFA を有効にするには、以下の手順を実行します。

1. Azure Portal に管理者としてサインインします。

2. **[Azure Active Directory] \> [条件付きアクセス]** に移動します。

3. **[新しいポリシー]** を選択します。

4. **[割り当て]** で、**[ユーザーとグループ]** を選択します。 **[含める]** タブと **[除外]** タブを使って、ポリシーによって管理するユーザーとグループを指定します。

5. **[割り当て]** で、**[クラウド アプリ]** を選択します。 **[すべてのクラウド アプリ]** を含めることを選択します。
6.  **[アクセス制御]** で、**[許可]** を選択します。 **[多要素認証が必要です]** を選択します。
7.  **[ポリシーを有効にする]** を **[オン]** にして、**[保存]** を選択します。

Azure MFA 設定を構成して、不正アクセスのアラートの設定、ワンタイム バイパスの作成、カスタム音声メッセージの使用、キャッシュの構成、信頼できる IP の指定、アプリ パスワードの作成、ユーザーが信頼するデバイスに対する MFA の記憶、検証方法の選択を行う方法については、「[Azure Multi-Factor Authentication の設定を構成する](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure AD での特権アクセスのセキュリティ保護](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Azure Multi-Factor Authentication についてよく寄せられる質問](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [ロールベースのアクセス制御のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
