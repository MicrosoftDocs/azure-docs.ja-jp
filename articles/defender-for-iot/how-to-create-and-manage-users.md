---
title: ユーザーの作成と管理
description: センサーとオンプレミス管理コンソールのユーザーを作成および管理します。 ユーザーには、管理者、セキュリティ アナリスト、または読み取り専用ユーザーのロールを割り当てることができます。
ms.date: 03/03/2021
ms.topic: article
ms.openlocfilehash: 2afc3cb2b9cfc0ac6b75c98198d9f0965b6dc04c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779020"
---
# <a name="about-defender-for-iot-console-users"></a>About Defender for IoT コンソール ユーザーについて

この記事では、センサーとオンプレミス管理コンソールのユーザーを作成および管理する方法について説明します。 ユーザー ロールには、管理者、セキュリティアナリスト、または読み取り専用ユーザーが含まれます。 各ロールには、センサーまたはオンプレミス管理コンソール用のツールに対する一連のアクセス許可が関連付けられています。 ロールは、Azure Defender for IoT に対してセキュリティで保護されたきめ細かいアクセスを容易にするように設計されています。

ユーザー アクティビティを追跡したり Active Directory サインインを有効にしたりするための機能も使用できます。

既定では、各センサーおよびオンプレミス管理コンソールのインストールには、"*cyberx およびサポート*" ユーザーが使用されます。 これらのユーザーは、トラブルシューティングやセットアップのための高度なツールにアクセスできます。 管理者ユーザーは、これらのユーザー資格情報を使用してサインインし、管理者ユーザーを作成してから、セキュリティ アナリストおよび読み取り専用ユーザー用に別のユーザーを作成する必要があります。

## <a name="role-based-permissions"></a>ロール ベース アクセス許可
次のユーザー ロールを使用できます。

- **読み取り専用**:読み取り専用ユーザーは、デバイス マップ上にアラートやデバイスを表示するなどのタスクを実行します。 これらのユーザーは **[ナビゲーション]** の下に表示されるオプションにアクセスできます。

- **セキュリティ アナリスト**:セキュリティ アナリストは、読み取り専用ユーザーのアクセス許可を持ちます。 また、デバイスでアクションを実行したり、アラートを確認したり、調査ツールを使用したりすることもできます。 これらのユーザーは **[ナビゲーション]** と **[分析]** の下に表示されるオプションにアクセスできます。

- **管理者**: 管理者は、システム構成の定義やユーザーの作成と管理など、すべてのツールにアクセスできます。 これらのユーザーは **[ナビゲーション]** 、 **[分析]** 、 **[管理]** の下に表示されるオプションにアクセスできます。

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>オンプレミス管理コンソール ツールに対するロール ベース アクセス許可

このセクションでは、オンプレミス管理コンソールで、管理者、セキュリティ アナリスト、および読み取り専用ユーザーに提供されるアクセス許可について説明します。  

| 権限 | 読み取り専用 | セキュリティ アナリスト | 管理者 |
|--|--|--|--|
| エンタープライズ マップの表示とフィルター処理 | ✓ | ✓ | ✓ |
| サイトの構築 |  |  | ✓ |
| サイトの管理 (ゾーンの追加と編集) |  |  | ✓ |
| デバイス インベントリの表示とフィルター処理 | ✓ | ✓ | ✓ |
| アラートの表示と管理: 確認、学習、およびピン留め | ✓ | ✓ | ✓ |
| レポートの生成 |  | ✓ | ✓ |
| リスク評価レポートの表示 |  | ✓ | ✓ |
| アラート除外の設定 |  | ✓ | ✓ |
| アクセス グループの表示または定義 |  |  | ✓ |
| システム設定の管理 |  |  | ✓ |
| ユーザーの管理 |  |  | ✓ |
| アラート データのパートナーへの送信 |  |  | ✓ |
| 証明書の管理 |  |  | ✓ |
| ユーザーがアクティブでないときのセッション タイムアウト | 30 分 | 30 分  | 30 分  |

#### <a name="assign-users-to-access-groups"></a>ユーザーをアクセス グループに割り当てる

管理者は、ユーザーを特定の "*アクセス グループ*" に割り当てることで、Defender for IoT のユーザー アクセス制御を強化できます。 アクセス グループは、センサーが配置されている、ゾーン、サイト、リージョン、およびビジネス ユニットに割り当てられます。 ユーザーをアクセス グループに割り当てることによって、管理者は、ユーザーがデバイスの検出を管理および分析する場所について特に制御できるようになります。 

この方法を行うと、ユーザーのアクセス許可が複雑であったり、グローバル組織のセキュリティ ポリシーによって決定されたりする大規模な組織に対応できます。 詳細については、「[グローバル アクセス制御の定義](how-to-define-global-user-access-control.md)」を参照してください。

### <a name="role-based-permissions-to-sensor-tools"></a>センサー ツールに対するロール ベース アクセス許可

このセクションでは、センサーの管理者、セキュリティ アナリスト、および読み取り専用ユーザーに提供されるアクセス許可について説明します。  

| 権限 | 読み取り専用 | セキュリティ アナリスト | 管理者 |
|--|--|--|--|
| ダッシュボードの表示 | ✓ | ✓ | ✓ |
| マップ拡大表示の制御 |  |  | ✓ |
| アラートを表示する | ✓ | ✓ | ✓ |
| アラートの管理: 確認、学習、およびピン留め |  | ✓ | ✓ |
| タイムラインでのイベントの表示 |  | ✓ | ✓ |
| デバイス、既知のスキャン デバイス、プログラミング デバイスの承認 |  | ✓ | ✓ |
| 調査データの表示 | ✓ | ✓ | ✓ |
| システム設定の管理 |  |  | ✓ |
| ユーザーの管理 |  |  | ✓ |
| 逆引き参照用の DNS サーバー |  |  | ✓ |
| アラート データのパートナーへの送信 |  | ✓ | ✓ |
| アラート コメントの作成 |  | ✓ | ✓ |
| プログラミング変更履歴の表示 | ✓ | ✓ | ✓ |
| カスタマイズしたアラート ルールの作成 |  | ✓ | ✓ |
| 複数の通知の同時管理 |  | ✓ | ✓ |
| 証明書の管理 |  |  | ✓ |
| ユーザーがアクティブでないときのセッション タイムアウト | 30 分 | 30 分 | 30 分 |

## <a name="define-users"></a>ユーザーを定義する

このセクションでは、ユーザーを定義する方法について説明します。 Cyberx、サポート、および管理者のユーザーは、他のユーザー定義を追加、削除、および更新できます。

ユーザーを定義するには:

1. センサーまたはオンプレミス管理コンソールの左側のウィンドウで、 **[ユーザー]** を選択します。
1. **[ユーザー]** ウィンドウで、 **[ユーザーの作成]** を選択します。
1. **[ユーザーの作成]** ウィンドウで、次のパラメーターを定義します。

   - **[ユーザー名]** : ユーザー名を入力します。
   - **電子メール**: ユーザーの電子メール アドレスを入力します。
   - **名**:ユーザーの名前 (名) を入力します。
   - **姓**:ユーザーの名前 (姓) を入力します。
   - **ロール**: ユーザーのロールを定義します。 「[ロール ベース アクセス許可](#role-based-permissions)」を参照してください。
   - **アクセス グループ**:オンプレミス管理コンソールのユーザーを作成している場合は、ユーザーのアクセス グループを定義します。 「[グローバル アクセス制御の定義](how-to-define-global-user-access-control.md)」を参照してください。
   - **パスワード**:以下のユーザーの種類を選択します。
     - **ローカル ユーザー**:センサーまたはオンプレミス管理コンソールのユーザーのパスワードを定義します。 パスワードには 6 文字以上を含める必要があり、文字と数字を含める必要があります。
     - **Active Directory ユーザー**:ユーザーが Active Directory の資格情報を使用して、センサーまたは管理コンソールにサインインできるようにすることができます。 定義した Active Directory グループに、特定のアクセス許可レベルを関連付けることができます。 たとえば、特定の Active Directory グループを構成し、そのグループ内のすべてのユーザーを読み取り専用ユーザー タイプに割り当てます。

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="ユーザーの管理。":::

## <a name="user-session-timeout"></a>ユーザー セッション タイムアウト

指定の時間、ユーザーがキーボードまたはマウスを使用しない場合、ユーザーはセッションからサインアウトされ、もう一度サインインする必要があります。

ユーザーがコンソールでマウスやキーボードを 30 分間操作していないと、セッションのサインアウトが強制されます。

この機能は既定でアップグレード時に有効になっていますが、無効にすることができます。 また、セッションのカウント時間を更新することもできます。 セッション時間は秒単位で定義されます。 定義は、センサーおよびオンプレミス管理コンソールそれぞれに適用されます。

非アクティブ タイムアウトが経過すると、セッション タイムアウト メッセージがコンソールに表示されます。

### <a name="control-inactivity-sign-out"></a>非アクティブ サインアウトを制御する

管理者ユーザーは、非アクティブ サインアウトの有効と無効を切り替えたり、非アクティブのしきい値を調整したりできます。

コマンドにアクセスするには:

1. Defender for IoT の管理者資格情報を使用して、センサーまたはオンプレミス管理コンソールの CLI にサインインします。

1. 「`sudo nano /var/cyberx/properties/authentication`」と入力します。

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

機能を無効にするには、`infinity_session_expiration = true` を `infinity_session_expiration = false` に変更します。

サインアウト カウント期間を更新するには、`= <number>` 値を必要な時間に調整します。

## <a name="track-user-activity"></a>ユーザー アクティビティを追跡する 

各センサーのイベント タイムラインでユーザー アクティビティを追跡できます。 タイムラインには、イベントまたは影響を受けたデバイス、およびユーザーがアクティビティを実行した日時が表示されます。

ユーザー アクティビティを表示するには:

1. センサーにサインインします。
1. イベント タイムラインで、 **[User Operations]\(ユーザー操作\)** オプションを有効にします。 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="ユーザーのアクティビティの表示。":::

## <a name="integrate-with-active-directory-servers"></a>Active Directory サーバーと統合する 

Active Directory と統合するようにセンサーまたはオンプレミス管理コンソールを構成します。 これにより、Active Directory ユーザーが Active Directory の資格情報を使用して、Defender for IoT コンソールにアクセスできるようになります。

2 種類の LDAP ベースの認証がサポートされています。

- **完全認証**:ユーザーの詳細情報が LDAP サーバーから取得されます。 たとえば、名、姓、電子メール、ユーザーのアクセス許可です。

- **信頼されたユーザー**:ユーザー パスワードのみが取得されます。 取得されるその他のユーザー詳細は、センサーで定義されているユーザーに基づきます。

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory および Defender for IoT のアクセス許可

ここで定義した Active Directory グループに、特定のアクセス許可レベルを関連付けることができます。 たとえば、特定の Active Directory グループを構成し、Read Only アクセス許可をそのグループ内のすべてのユーザーに割り当てます。

Active Directory を構成するには:

1. 左側のペインで、 **[システム設定]** を選択します。

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Active Directory のシステム設定を表示します。":::

2. **[システム設定]** ペインで、 **[Active Directory]** を選択します。

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Active Directory 構成を編集します。":::

3. **[Edit Active Directory Configuration]\(Active Directory 構成の編集\)** ダイアログ ボックスで、 **[Active Directory Integration Enabled]\(Active Directory 統合の有効化\)**  >  **[保存]** を選択します。 **[Edit Active Directory Configuration]\(Active Directory 構成の編集\)** ダイアログ ボックスが展開され、Active Directory を構成するためのパラメーターを入力できるようになります。

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Active Directory を構成するためのパラメーターを入力します。":::

    > [!NOTE]
    > - ここでは、Active Directory に表示されるとおりに LDAP パラメーターを定義する必要があります。
    > - すべての Active Directory パラメーターには、小文字のみを使用します。 Active Directory の構成で大文字が使用されている場合でも、小文字を使用します。
    > - 同じドメインに対して、LDAP と LDAPS の両方を構成することはできません。 ただし、別のドメインに対しては、これらの両方を同時に使用できます。

4. 次の Active Directory サーバーのパラメーターを設定します。

   | サーバー パラメーター | [説明] |
   |--|--|
   | ドメイン コントローラー FQDN | 完全修飾ドメイン名 (FQDN) を、LDAP サーバーに表示されているとおりに設定します。 たとえば、「`host1.subdomain.domain.com`」と入力します。 |
   | ドメイン コントローラー ポート | LDAP が構成されているポートを定義します。 |
   | プライマリ ドメイン | LDAP 構成に従って、ドメイン名 (`subdomain.domain.com` など) と接続の種類を設定します。 |
   | Active Directory グループ | LDAP サーバーの Active Directory の構成で定義されているグループ名を入力します。 |
   | 信頼されたドメイン | 信頼されたドメインを追加するには、信頼されたドメインのドメイン名と接続の種類を追加します。 <br />信頼されたドメインは、[ユーザー] の下で定義されたユーザーに対してのみ構成できます。 |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>オンプレミス管理コンソール用の ActiveDirectory グループ

オンプレミス管理コンソール ユーザーの Active Directory グループを作成する場合は、Active Directory グループごとにアクセス グループ ルールを作成する必要があります。 Active Directory ユーザー グループのアクセス グループ ルールが存在しない場合、オンプレミス管理コンソールの Active Directory 資格情報は機能しません。 「[グローバル アクセス制御の定義](how-to-define-global-user-access-control.md)」を参照してください。

1. **[保存]** を選択します。

2. 信頼されたサーバーを追加するには、 **[サーバーの追加]** を選択し、別のサーバーを構成します。

## <a name="resetting-passwords"></a>パスワードのリセット

### <a name="cyberx-or-support-user"></a>CyberX またはサポート ユーザー

**パスワードの復元** 機能にアクセスできるのは、**CyberX** と **サポート** ユーザーのみです。 **CyberX** または **サポート** ユーザーがパスワードを忘れた場合は、Defender for IoT のサインイン ページで **[パスワードの復元]** オプションを使用してパスワードをリセットできます。

CyberX またはサポート ユーザーのパスワードをリセットするには、次の手順を実行します。

1. Defender for IoT のサインイン画面で、 **[パスワードの復元]** オプションを選択します。 **[パスワードの復元]** 画面が開きます。

1. **[CyberX]** または **[サポート]** を選択し、一意識別子をコピーします。

1. Azure portal に移動し、 **[Sites and Sensors]\(サイトとセンサー\)** を選択します。  

1. 上部のツールバーにある **[サブスクリプション フィルター]** アイコン :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false"::: を選択し、センサーが接続されているサブスクリプションを選択します。

1. **[Recover on-premises management console password]\(オンプレミス管理コンソールのパスワードを復元\)** タブを選択します。

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="オンプレミス管理を復元するボタンを選択して、復元ファイルをダウンロードします。":::

1. **[パスワードの復元]** 画面で受信した一意識別子を入力し、 **[復元]** を選択します。 `password_recovery.zip` ファイルがダウンロードされます。

    > [!NOTE]
    > パスワードの復元ファイルは変更しないでください。 これは署名されたファイルであり、改ざんすると機能しません。

1. **[パスワードの復元]** 画面で **[アップロード]** を選択します。 **[Upload Password Recovery File]\(パスワード復元ファイルのアップロード\)** ウィンドウが開きます。

1. **[参照]** を選択して `password_recovery.zip` ファイルを見つけるか、`password_recovery.zip` をウィンドウにドラッグします。

    > [!NOTE]
    > ファイルが無効であることを示すエラーメッセージが表示される場合があります。 このエラー メッセージを解決するには、`password_recovery.zip` をダウンロードしてからもう一度ダウンロードする前に、適切なサブスクリプションを選択していることを確認します。  

1. **[次へ]** を選択すると、管理コンソールのユーザーとシステムが生成したパスワードが表示されます。

### <a name="administrator-security-analyst-and-read-only-user"></a>管理者、セキュリティ アナリスト、読み取り専用ユーザー

読み取り専用ユーザーとセキュリティ アナリストは、自分のパスワードをリセットできません。パスワードをリセットするには、管理者、サポート、または CyberX のいずれかのロールを持つユーザーに問い合わせる必要があります。 管理者ユーザーがこれらのパスワードをリセットする場合は、**CyberX** または **サポート** ユーザーに問い合わせる必要があります。

センサーでユーザーのパスワードをリセットするには、次の手順を実行します。

1. 管理者、サポート、または CyberX ロールのユーザーは、センサーにサインインする必要があります。

1. 左側のパネルで **[ユーザー]** を選択します。

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="左側のペインで [ユーザー] オプションを選択します。":::

1. ユーザーを見つけ、 **[操作]** ドロップダウン メニューから **[編集]** を選択します。

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="[操作] ドロップダウン メニューから [編集] を選択します。":::

1. **[新しいパスワード]** フィールドと **[新しいパスワードの確認]** フィールドに新しいパスワードを入力します。

1. **[更新]** を選択します。

オンプレミス管理コンソールでユーザーのパスワードをリセットするには、次の手順を実行します。

1. 管理者、サポート、または CyberX ロールのユーザーは、センサーにサインインする必要があります。

1. 左側のパネルで **[ユーザー]** を選択します。

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="左側のパネルでユーザーのオプションを選択します。":::

1. ユーザーを見つけて、編集アイコン :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: を選択します。

1. **[新しいパスワード]** フィールドと **[新しいパスワードの確認]** フィールドに新しいパスワードを入力します。

1. **[更新]** を選択します。

## <a name="see-also"></a>関連項目

[センサーをアクティブにしてセットアップする](how-to-activate-and-set-up-your-sensor.md)
[オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)
[センサーのアクティビティを追跡する](how-to-track-sensor-activity.md)
