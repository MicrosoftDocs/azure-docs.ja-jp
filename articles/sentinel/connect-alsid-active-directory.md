---
title: Azure Sentinel に Alsid for Active Directory を接続する | Microsoft Docs
description: Alsid for Active Directory コネクタを使用して Alsid ログを Azure Sentinel にプルする方法について学習します。 Alsid データをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566592"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Azure Sentinel に Alsid for Active Directory (AD) を接続する

> [!IMPORTANT]
> Alsid for Active Directory コネクタは現在、**プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Alsid for AD ソリューションを Azure Sentinel に接続する方法について説明します。 Alsid for Active Directory データ コネクタを使用すると、Alsid for AD ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックに表示し、クエリを実行してカスタム アラートを作成し、調査の改善のために取り込むことができます。 Alsid for AD と Azure Sentinel の統合では、Log Analytics エージェントがインストールされた Syslog サーバーが使用されます。 また、Kusto 関数に基づくカスタムビルドのログ パーサーも使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースへの書き込みアクセス許可が必要です。

- Syslog を使用してログをエクスポートするように Alsid for AD ソリューションを構成する必要があります。

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Alsid for AD ログを Syslog エージェントを使用して Azure Sentinel に送信する

Syslog エージェントを使用して、お使いの Azure Sentinel ワークスペースに Syslog メッセージを転送するように Alsid for AD を構成します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Alsid for Active Directory (Preview)]\(Alsid for Active Directory (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Alsid for Active Directory]** コネクタ ページの指示に従います。

    1. Syslog サーバーを構成する

        1. まだお持ちではない場合は、ログを送信するために Alsid for AD 用の Linux Syslog サーバーを作成します。 Azure Sentinel では、**rsyslog** および **syslog** デーモンがサポートされています。 

        1. Alsid for AD ログを別のファイルに出力するように Syslog サーバーを構成します。

    1. Syslog サーバーにログを送信するように Alsid for AD を構成する

        1. **Alsid for AD** ポータルで、 *[システム]* 、 *[構成]* 、 *[Syslog]* の順に選択します。 そこから、Syslog サーバーに対して新しい Syslog アラートを作成できます。 リモート サーバーの場合は、Linux エージェントをインストールした Linux マシンの IP アドレスを使用します。

        1. ログがサーバー上の別のファイルに正しく収集されていることを確認します (これを行うには、Alsid for AD の *Syslog* アラート構成で **[Test the configuration]\(構成をテスト\)** ボタンを使用できます)。

    1. Linux 用 Log Analytics エージェントをインストールしてオンボードする

        - Azure Linux VM または Azure 以外の Linux マシン (物理または仮想) を選択します。 画面のリンクと指示に従います。 詳細については、「[Linux マシンまたはアプライアンスを構成する](connect-syslog.md#configure-your-linux-machine-or-appliance)」を参照してください。

    1. Log Analytics エージェントによって収集されるようにログを構成する

        - ワークスペースの詳細設定の構成で、機能と重大度を選択します。

            1. コネクタ ページの **[Open your workspace advanced settings configuration >]\(ワークスペースの詳細設定の構成を開く >\)** リンクをクリックします。

            1. **[詳細設定]** 画面で、 **[データ]** を選択し、 **[カスタム ログ]** を選択します。

            1. **[Apply below configuration to my linux machines]\(以下の構成を Linux マシンに適用する\)** チェック ボックスをオンにし、 **[追加]** をクリックします。

            1. **[ファイルの選択]** をクリックして、Syslog サーバーを実行している Linux マシンから Alsid for AD Syslog ファイルのサンプルをアップロードし、 **[次へ]** をクリックします。

            1. **[Set record delimiter]\(レコード区切り記号の設定\)** が **[改行]** に設定されていることを確認し、 **[次へ]** をクリックします。

            1. **[Linux]** を選択し、Syslog ファイルへのファイル パスを入力して、 **[+]** をクリックし、 **[次へ]** をクリックします。

            1. [名前] フィールドに、「*AlsidForADLog*」に続けて _CL サフィックスを入力し、 **[完了]** をクリックします。
    
Log Analytics でログの表示が開始されるまで、最大 20 分かかる場合があります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、データが、*AlsidForADLog_CL* テーブルの **[カスタム ログ]** の下にある **[ログ]** に表示されます。

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 次の手順に従って、クエリおよびブックで使用する **afad_parser** Kusto 関数を設定します。

1. Azure Sentinel のナビゲーション メニューから **[ログ]** を選択します。

1. 次のクエリをコピーして、クエリ ウィンドウに貼り付けます。
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. **[保存]** ドロップダウンをクリックし、 **[保存]** をクリックします。 **[保存]** パネルで、次の手順に従います。

    1. **[名前]** に「**afad_parser**」と入力します。

    1. **[名前を付けて保存]** で、 **[関数]** を選択します。

    1. **[関数のエイリアス]** に「**afad_parser**」と入力します。

    1. **[カテゴリ]** で、「**関数**」と入力します。

    1. **[保存]** をクリックします。

    関数アプリのアクティブ化には、通常、10 から 15 分かかります。

これで、クエリ ウィンドウの一番上の行に `afad_parser` を入力すると、Alsid for AD データのクエリを実行する準備が整います。

その他のクエリの例については、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Alsid for AD を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
