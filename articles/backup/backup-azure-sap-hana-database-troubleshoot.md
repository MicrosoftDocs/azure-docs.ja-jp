---
title: SAP HANA データベースのバックアップ エラーのトラブルシューティング
description: Azure Backup を使用して SAP HANA データベースをバックアップするときに発生する可能性のある一般的なエラーをトラブルシューティングする方法について説明します。
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 22800adc323bda8a60278160f24bc559103fb57e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713339"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure での SAP HANA データベースのバックアップをトラブルシューティングする

この記事では、Azure 仮想マシン上で SAP HANA データベースをバックアップするためのトラブルシューティング情報を提供します。 現在サポートされている SAP HANA バックアップ シナリオの詳細については、「[シナリオのサポート](sap-hana-backup-support-matrix.md#scenario-support)」を参照してください。

## <a name="prerequisites-and-permissions"></a>前提条件とアクセス許可

バックアップを構成する前に、「[前提条件](tutorial-backup-sap-hana-db.md#prerequisites)」セクションと[事前登録スクリプトの内容](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)に関するセクションを参照してください。

## <a name="common-user-errors"></a>一般的なユーザー エラー

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **エラー メッセージ**      | <span style="font-weight:normal">Azure Backup には、バックアップを実行するために必須のロール特権がありません</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **考えられる原因**    | ロールが上書きされている可能性があります。                          |
| **推奨される操作** | この問題を解決するには、 **[Discover DB]\(DB の検出\)** ウィンドウからスクリプトを実行するか、[ここ](https://aka.ms/scriptforpermsonhana)からダウンロードします。 または、"SAP_INTERNAL_HANA_SUPPORT" ロールをワークロード バックアップ ユーザー (AZUREWLBACKUPHANAUSER) に追加します。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| エラー メッセージ      | <span style="font-weight:normal">HANA システムに接続できませんでした</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | SAP HANA インスタンスがダウンしている可能性があります。<br/>Azure Backup が HANA データベースと対話するのに必要なアクセス許可が設定されていません。 |
| **推奨される操作** | SAP HANA データベースが稼働しているかどうかを確認します。 データベースが稼働している場合は、必要なアクセス許可がすべて設定されているかどうかを確認します。 いずれかのアクセス許可がない場合は、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を実行して、不足しているアクセス許可を追加します。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| エラー メッセージ      | <span style="font-weight:normal">指定した SAP HANA インスタンスは無効であるか、見つかりません</span>  |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | 単一の Azure VM 上の複数の SAP HANA インスタンスをバックアップすることはできません。 |
| **推奨される操作** | バックアップする SAP HANA インスタンス上で[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を実行します。 引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。 |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| エラー メッセージ      | <span style="font-weight:normal">指定された SAP HANA 操作はサポートされていません</span>              |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | SAP HANA 用 Azure Backup は、SAP HANA ネイティブ クライアント (Studio/ Cockpit/ DBA Cockpit) で実行される増分バックアップやアクションをサポートしていません |
| **推奨される操作** | 詳しくは、[こちら](./sap-hana-backup-support-matrix.md#scenario-support)を参照してください。 |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| エラー メッセージ      | <span style="font-weight:normal">バックアップ ログ チェーンが壊れています</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | ログ バックアップ先が backint からファイル システムに更新されたか、backint 実行可能ファイルが変更された可能性があります |
| **推奨される操作** | 問題を解決するには、完全バックアップをトリガーします                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| エラー メッセージ      | <span style="font-weight:normal">SDC から MDC へのアップグレードが検出されました</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | SAP HANA インスタンスが、SDC から MDC にアップグレードされました。 更新すると、バックアップは失敗します。 |
| **推奨される操作** | [SDC から MDC にアップグレード](#sdc-to-mdc-upgrade-with-a-change-in-sid)するための手順に従って、問題を解決します |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| エラー メッセージ      | <span style="font-weight:normal">無効な backint 構成が検出されました</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **考えられる原因**    | バッキング パラメーターが Azure Backup に対して正しく指定されていません |
| **推奨される操作** | 次の (backint) パラメーターが設定されているかどうかを確認します。<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>backint ベースのパラメーターが HOST に存在する場合は、それらを削除します。 パラメーターが HOST レベルに存在しないが、データベース レベルで手動で変更されている場合は、それらを既に説明した適切な値に戻します。 または、Azure portal から [[保護を停止してバックアップ データを保持する]](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) を実行してから、 **[バックアップの再開]** を選択します。 |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|エラー メッセージ  |復元のソース システムとターゲット システムには互換性がありません  |
|---------|---------|
|考えられる原因   | 復元のために選択されたソース システムとターゲット システムには互換性がありません        |
|推奨される操作   |   復元シナリオが、次のような互換性のない復元の一覧に含まれていないことを確認します。 <br><br>   **ケース 1:** 復元中に SYSTEMDB の名前を変更することはできません。  <br><br> **ケース 2:** ソースは SDC、ターゲットは MDC: ソース データベースをターゲット上の SYSTEMDB またはテナント DB として復元することはできません。 <br><br> **ケース 3:** ソースは MDC、ターゲットは SDC: ソース データベース (SYSTEMDB またはテナント DB) をターゲットに復元することはできません。 <br><br>  詳細については、[SAP サポート スタート パッド](https://launchpad.support.sap.com)でノート **1642148** を参照してください。 |

## <a name="restore-checks"></a>復元の確認

### <a name="single-container-database-sdc-restore"></a>単一コンテナー データベース (SDC) の復元

HANA 用の単一コンテナー データベース (SDC) を別の SDC マシンに復元するときは、入力に注意してください。 データベース名は小文字で指定し、かっこで囲んだ "sdc" を後に付ける必要があります。 HANA インスタンスは大文字で表示されます。

SDC HANA インスタンス "H21" がバックアップされているとします。 バックアップ項目のページに、バックアップ項目名が **"h21(sdc)"** と表示されます。 このデータベースを別のターゲット SDC (たとえば H11) に復元する場合は、次の入力を指定する必要があります。

![復元する SDC データベース名](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

以下の点に注意してください。

- 既定では、復元されるデータベース名に、バックアップ項目名が設定されます。 この場合は h21(sdc) です。
- ターゲットを H11 として選択しても、復元されるデータベース名は自動的に変更されません。 **h11(sdc) となるように編集する必要があります**。 SDC に関しては、復元されるデータベース名は、小文字のターゲット インスタンス ID の後に sdc をかっこで囲んで付けたものです。
- SDC はデータベースを 1 つしか含むことができないため、復旧ポイント データによる既存のデータベース データのオーバーライドを許可するチェックボックスをオンにすることも必要です。
- Linux では大文字と小文字が区別されます。 そのため、大文字と小文字を維持するように注意してください。

### <a name="multiple-container-database-mdc-restore"></a>複数コンテナー データベース (MDC) の復元

HANA 用の複数コンテナー データベースの場合、標準構成は SYSTEMDB と 1 つ以上のテナント DB です。 SAP HANA インスタンス全体の復元は、SYSTEMDB とテナント DB の両方を復元することを意味します。 最初に SYSTEMDB を復元してから、テナント DB の処理に進みます。 基本的にシステム DB では、選択したターゲットのシステム情報がオーバーライドされます。 この復元では、ターゲット インスタンス内の BackInt 関連情報もオーバーライドされます。 そのため、システム DB がターゲット インスタンスに復元された後に、事前登録スクリプトを再実行します。 その後でのみ、後続のテナント DB の復元が成功します。

## <a name="back-up-a-replicated-vm"></a>レプリケートされた VM のバックアップ

### <a name="scenario-1"></a>シナリオ 1

元の VM は Azure Site Recovery または Azure VM バックアップを使用してレプリケートされています。 新しい VM は古い VM をシミュレートするように構築されています。 つまり、設定はまったく同じです (これは、元の VM が削除され、VM バックアップまたは Azure Site Recovery から復元が行われたためです)。

このシナリオでは、2 つのケースが考えられます。 両方の場合について、レプリケートされた VM をバックアップする方法を説明します。

1. 新しく作成された VM は同じ名前で、削除された VM と同じリソース グループとサブスクリプションに存在する場合。

    - 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
    - 事前登録スクリプトを実行します
    - Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
    - (削除された VM からの) 既存のバックアップ データベースが正常にバックアップされるようになります

2. 新しく作成された VM が次のいずれかの場合。

    - 削除された VM とは名前が異なる
    - 削除された VM と同じ名前だが、(削除された VM とは) 別のリソース グループまたはサブスクリプションに存在する

    この場合は、次の手順を実行します。

    - 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
    - 事前登録スクリプトを実行します
    - 新しいデータベースを検出して保護すると、ポータルに重複するアクティブなデータベースが表示されます。 これを回避するには、古いデータベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します。 その後、残りの手順に進みます。
    - バックアップを有効にするデータベースを検出します
    - これらのデータベースでバックアップを有効にします
    - (削除された VM からの) 既存のバックアップ データベースは、引き続きコンテナーに格納されています (バックアップはポリシーに従って保持されます)

### <a name="scenario-2"></a>シナリオ 2

元の VM は Azure Site Recovery または Azure VM バックアップを使用してレプリケートされています。 新しい VM は、テンプレートとして使用するために、コンテンツから構築されています。 これは新しい SID を持つ新しい VM です。

新しい VM でバックアップを有効にするには、次の手順に従います。

- 拡張機能は VM に既に存在していますが、どのサービスにも表示されていません
- 事前登録スクリプトを実行します。 新しい VM の SID に基づいて、次の 2 つのシナリオが考えられます
  - 元の VM と新しい VM に同じ SID が割り当てられている。 事前登録スクリプトは正常に実行されます。
  - 元の VM と新しい VM に異なる SID が割り当てられている。 事前登録スクリプトは失敗します。 このシナリオについては、サポートにお問い合わせください。
- バックアップするデータベースを検出する
- これらのデータベースでバックアップを有効にします

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>同じ VM 上の SDC バージョン アップグレードまたは MDC バージョン アップグレード

SID が変更されない OS のアップグレード、SDC バージョンの変更、または MDC バージョンの変更は、次のように処理できます。

- 新しい OS バージョン、SDC、または MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- そのデータベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します
- アップグレードまたは更新を実行します
- 事前登録スクリプトを再実行します。 多くの場合、アップグレード プロセスで[必要なロール](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)が削除されます。 事前登録スクリプトを実行すると、必要なすべてのロールを確認できます。
- データベースの保護を再開します

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SID の変更がない SDC から MDC へのアップグレード

SID が変更されない SDC から MDC へのアップグレードは、次のように処理できます。

- 新しい MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- 古い SDC データベースの[データを保持して保護を停止](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)します
- アップグレードを実行します。 完了後、HANA システムはシステム DB とテナント DB を備えた MDC になります
- [事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します
- Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
- 同じ VM に対して **[DB の再検出]** を選択します。 このアクションにより、手順 3 の新しい DB が表示されます (SDC ではなく SYSTEMDB とテナント DB)
- 古い SDC データベースは引き続きコンテナーに存在し、ポリシーに従って古いバックアップ データが保持されます
- これらのデータベースのバックアップを構成します

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SID が変更される SDC から MDC へのアップグレード

SID が変更される SDC から MDC へのアップグレードは、次のように処理できます。

- 新しい MDC バージョンが現在 [Azure Backup でサポートされている](sap-hana-backup-support-matrix.md#scenario-support)ことを確認してください
- 古い SDC データベースの **データを保持して保護を停止** します
- アップグレードを実行します。 完了後、HANA システムはシステム DB とテナント DB を備えた MDC になります
- 適切な詳細 (新しい SID と MDC) を指定して、[事前登録スクリプト](https://aka.ms/scriptforpermsonhana)を再実行します。 SID の変更により、スクリプトを正常に実行する際に問題が発生する可能性があります。 問題が発生した場合は、Azure Backup サポートにお問い合わせください。
- Azure portal で同じコンピューターに対して拡張機能を再登録します ( **[バックアップ]**  ->  **[詳細の表示]** -> [関連する Azure VM の選択] -> [再登録])
- 同じ VM に対して **[DB の再検出]** を選択します。 このアクションにより、手順 3 の新しい DB が表示されます (SDC ではなく SYSTEMDB とテナント DB)
- 古い SDC データベースは引き続きコンテナーに存在し、ポリシーに従って古いバックアップ データが保持されます
- これらのデータベースのバックアップを構成します

## <a name="re-registration-failures"></a>再登録エラー

再登録操作をトリガーする前に、次の兆候が 1 つ以上ないか確認してください。

- VM ですべての操作 (バックアップ、復元、バックアップの構成など) が次のいずれかのエラー コードで失敗している。**WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg**。
- バックアップ項目の **[バックアップ状態]** 領域に **[到達できません]** が表示されている場合は、同じ状態になる可能性のある他のすべての原因を除外します。

  - VM でバックアップ関連の操作を実行する権限がない
  - VM がシャットダウンされているため、バックアップが実行できない
  - ネットワークの問題

これらの兆候は、次の 1 つ以上の理由によって発生する可能性があります。

- 拡張機能がポータルから削除またはアンインストールされた。
- VM がインプレース ディスク復元を使用して時間内に復元された。
- VM が長期間シャットダウンされたため、その拡張機能構成の期限が切れた。
- VM が削除され、削除された VM と同じ名前で同じリソース グループに別の VM が作成された。

前のシナリオにおいて、VM で再登録操作をトリガーすることをお勧めします。

## <a name="next-steps"></a>次のステップ

- Azure VM 上の SAP HANA データベースのバックアップに関する[よく寄せられる質問](./sap-hana-faq-backup-azure-vm.md)を確認する。
