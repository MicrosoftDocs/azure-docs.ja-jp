---
title: "Azure Backup Server のトラブルシューティング | Microsoft Docs"
description: "Azure Backup Server のインストール、登録、およびアプリケーション ワークロードのバックアップと復元のトラブルシューティング"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 71da98bf6d53ab50df4f6e40cf0b548752d10f93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Azure Backup Server のトラブルシューティング

次の表に示す情報を使って、Azure Backup Server の使用中に発生したエラーのトラブルシューティングを行うことができます。

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>エラー: 無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません 

この問題を解決するには、次の [トラブルシューティング手順] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) に従います。

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>エラー: Server の DPM エージェント コーディネーター サービスとの通信エラーのため、エージェント操作に失敗しました 

この問題を解決するには、次の [トラブルシューティング手順] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) に従います。

## <a name="error-setup-could-not-update-registry-metadata"></a>エラー: セットアップでレジストリのメタデータを更新できませんでした

この問題を解決するには、次の [トラブルシューティング手順] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) に従います。


## <a name="installation-issues"></a>インストールに関する問題

| 操作 | エラーの詳細 | 対処法 |
|-----------|---------------|------------|
|インストール | セットアップでレジストリのメタデータを更新できませんでした。 この更新エラーによって記憶域の消費量が過剰になる可能性があります。 これを回避するには、ReFS Trimming レジストリ エントリを更新します。 | レジストリ キー、SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim を調整します。 Dword の値を 1 に設定します。 |
|インストール | セットアップでレジストリのメタデータを更新できませんでした。 この更新エラーによって記憶域の消費量が過剰になる可能性があります。 これを回避するには、ボリューム SnapOptimization レジストリ エントリを更新します。 | 空の文字列値を持つ SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds レジストリ キーを作成します。 |

## <a name="registration-and-agent-related-issues"></a>登録とエージェントに関する問題
| 操作 | エラーの詳細 | 対処法 |
| --- | --- | --- |
| コンテナーへの登録 | 無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません | このエラーを解決するには、次の手順に従います。 <ol><li> コンテナーから最新の資格情報ファイルをダウンロードしてから、やり直します <br>(または)</li> <li> 上記の方法で解決できなかった場合は、資格情報を別のローカル ディレクトリにダウンロードするか、新しいコンテナーを作成します <br>(または)</li> <li> [このブログ](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)で説明されているように、日時の設定を更新します <br>(または)</li> <li> c:\windows\temp にあるファイルの数が 65,000 を超えているかどうかを確認します。 古いファイルを別の場所に移動するか、Temp フォルダー内のアイテムを削除します <br>(または)</li> <li> 証明書の状態を確認します <br> a. (コントロール パネルの) [コンピューター証明書の管理] を開きます <br> b. [個人] ノードとその子ノードの [証明書] を展開します <br> c.  "Windows Azure Tools" 証明書を削除します <br> d. Azure Backup クライアントで登録を再試行します <br> (または) </li> <li> グループ ポリシーが存在するかどうかを確認します </li></ol> |
| 保護されたサーバーへのエージェントのプッシュ | \<ServerName> の DPM エージェント コーディネーター サービスとの通信エラーのため、エージェント操作に失敗しました | **製品の推奨操作が機能しない場合** <ol><li> 信頼されていないドメインのコンピューターを接続している場合は、[こちら](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)の手順に従います <br> (または) </li><li> 信頼されているドメインのコンピューターを接続している場合は、[このブログ](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)で説明されている手順でトラブルシューティングを行います <br>(または)</li><li> トラブルシューティング手順としてウイルス対策ソフトウェアを無効にします。 それで問題が解決する場合は、[この記事](https://technet.microsoft.com/library/hh757911.aspx)で推奨されているとおりにウイルス対策の設定を変更します</li></ol> |
| 保護されたサーバーへのエージェントのプッシュ | サーバーに対して指定された資格情報が無効です | **製品の推奨操作が機能しない場合** <br> [この記事](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)に示されているように、運用サーバーに保護エージェントを手動でインストールします|
| Azure Backup エージェントは、Azure Backup サービスに接続できませんでした (ID: 100050) | Azure Backup エージェントは、Azure Backup サービスに接続できませんでした | **製品の推奨操作が機能しない場合** <br>1.管理者特権のコマンド プロンプトから、コマンドpsexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe" を実行します。Internet Explorer のウィンドウが開きます。 <br/> 2.[ツール]、[インターネット オプション]、[接続]、[LAN の設定] の順に進みます。 <br/> 3.システム アカウントのプロキシ設定を確認します。 プロキシの IP アドレスとポートを設定します。 <br/> 4.Internet Explorer を閉じます。|
| Azure Backup エージェントインストールに失敗しました | Microsoft Azure Recovery Services のインストールに失敗しました。 Microsoft Azure Recovery Services のインストールによってシステムに対し実行されたすべての変更はロールバックされました。 (ID: 4024) | Azure エージェントを手動でインストールする


## <a name="configuring-protection-group"></a>保護グループの構成
| 操作 | エラーの詳細 | 対処法 |
| --- | --- | --- |
| 保護グループの構成 | 保護コンピューター (保護コンピューター名) 上のアプリケーション コンポーネントを列挙できませんでした | 保護グループの構成 UI 画面で関連するデータソースまたはコンポーネント レベルで [更新] をクリックします |
| 保護グループの構成 | 保護を構成できません | 保護されるサーバーが SQL Server の場合は、[この記事](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)で説明されているように、保護コンピューターのシステム アカウント (NTAuthority\System) に sysadmin ロールのアクセス許可が付与されているかどうかを確認してください
| 保護グループの構成 | この保護グループの記憶域プールには十分な空き領域がありません | 記憶域プールに追加するディスクには、[パーティションが含まれていない必要があります](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx)。 ディスク上の既存のボリュームをすべて削除してから、記憶域プールに追加します|
| ポリシーの変更 |バックアップ ポリシーを変更できませんでした。 エラー: サービスの内部エラー [0x29834] が発生したため、現在の操作を実行できませんでした。 しばらくしてから、操作をやり直してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |**原因:**<br/>このエラーは、セキュリティ設定が有効になっており、リテンション範囲を上記の最小値を下回るように減らそうとしたものの、サポートされていないバージョン (MAB バージョン 2.0.9052 以前および Azure Backup サーバー update 1 以前) を使用していると発生します。 <br/>**推奨される操作:**<br/> このケースでは、指定した最小リテンション期間 (日次の場合は 7 日間、週次の場合は 4 週間、月次の場合は 3 か月、年次の場合は 1 年) を上回るようにリテンション期間を設定し、ポリシー関連の更新を進めます。 他にもお勧めの方法として、バックアップ エージェントおよび Azure Backup Server を更新して、すべてのセキュリティ更新プログラムを適用する方法があります。 |

## <a name="backup"></a>Backup
| 操作 | エラーの詳細 | 対処法 |
| --- | --- | --- |
| バックアップ | レプリカに整合性がありません | レプリカの不整合の原因に関する詳細や関連する推奨事項については、[こちら](https://technet.microsoft.com/library/cc161593.aspx)を参照してください <br> <ol><li> システム状態/BMR のバックアップの場合は、保護されるサーバーに Windows Server バックアップがインストールされているかどうかを確認してください </li><li> DPM/MABS サーバー上のDPM 記憶域プールの領域に関連する問題の有無を確認し、必要に応じて、記憶域を割り当てます </li><li> 保護されるサーバーのボリューム シャドウ コピー サービスの状態を確認します。 無効な状態の場合は、手動で開始するように設定し、サーバーでサービスを開始します。 DPM/MABS コンソールに戻り、整合性チェック ジョブとの同期を開始します。</li></ol>|
| バックアップ | ジョブの実行中に予期しないエラーが発生しました。デバイスの準備ができていません | **製品の推奨操作が機能しない場合** <br> <ol><li>保護グループ内の項目でシャドウ コピーの記憶域の領域を無制限に設定し、整合性チェックを実行します <br></li> (または) <li>既存の保護グループを削除してから、複数の保護グループを新しく作成します (個々の項目ごとに)</li></ol> |
| バックアップ | システム状態だけをバックアップする場合は、システム状態のバックアップを格納する保護コンピューターに十分な空き領域があることを確認してください | <ol><li>保護されたマシンに WSB がインストールされていることを確認します</li><li>システム状態のバックアップを格納する保護コンピューターに十分な空き領域があることを確認します。これを行う最も簡単な方法は、保護コンピューターに移動し、WSB を開き、選択項目をクリックし、BMR を選択することです。 この方法では、必要な領域の量が UI に表示されます。 WSB を開き、[ローカル バックアップ] -> [バックアップ スケジュール] -> [バックアップの構成の選択] -> [サーバー全体] の順に選択します (サイズが表示されます)。 このサイズを検証に使用します。</li></ol>
| バックアップ | オンライン回復ポイントを作成できませんでした | "Microsoft Azure Backup Agent は、選択されたボリュームのスナップショットを作成できませんでした。" というエラー メッセージが表示された場合は、レプリカと回復ポイントのボリュームの領域を増やしてください。
| バックアップ | オンライン回復ポイントを作成できませんでした | "Windows Azure Backup エージェントが OBEngine サービスに接続できません"というエラー メッセージが表示された場合は、コンピューター上で実行中のサービスの一覧に OBEngine が表示されていることを確認します。 OBEngine サービスが実行されていない場合は、"net start OBEngine" コマンドを使用して、OBEngine サービスを開始します。
| バックアップ | オンライン回復ポイントを作成できませんでした | "このサーバーの暗号化のパスフレーズが設定されていません。パスフレーズを構成してください。" という エラー メッセージが表示された場合は、暗号化のパスフレーズを構成します。 それでも解決しない場合は、 <br> <ol><li>スクラッチ場所が存在するかどうかを確認します。 レジストリの HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config に "ScratchLocation" という名前で場所が存在している必要があります。</li><li> スクラッチ場所が存在する場合は、以前のパスフレーズを使用して再登録します。 **暗号化のパスフレーズを構成したら、必ず安全な場所に保管してください**</li><ol>
| バックアップ | BMR のバックアップの失敗 | BMR のサイズが大きい場合は、一部のアプリケーション ファイルを OS ドライブに移動した後で再試行します |
| バックアップ | ファイル/共有フォルダーへのアクセス中のエラー | [ここで](https://technet.microsoft.com/library/hh757911.aspx)推奨されているとおりにウイルス対策の設定を変更します|
| バックアップ | VMware VM のオンライン回復ポイント作成ジョブが失敗しました。 DPM での ChangeTracking 情報の取得中に VMware でエラーが発生しました。 ErrorCode - FileFaultFault (ID 33621) |  1.影響を受けた仮想マシンの VMWare で ctk をリセットします <br/> 独立したディスクが VMWare 上にないことを確認します <br/> 影響を受けた仮想マシンの保護を停止し、[更新] ボタンを押して再度保護します <br/> 影響を受けた仮想マシンに CC を実行します|


## <a name="change-passphrase"></a>パスフレーズの変更
| 操作 | エラーの詳細 | 対処法 |
| --- | --- | --- |
| パスフレーズの変更 |入力されたセキュリティ PIN が正しくありません。 この操作を完了するには、正しいセキュリティ PIN を指定してください。 |**原因:**<br/> このエラーは、重大な操作 (パスフレーズの変更など) を実行している間に、無効または有効期限の切れたセキュリティ PIN を入力すると発生します。 <br/>**推奨される操作:**<br/> 操作を完了するには、有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にログインし、Recovery Services コンテナーで [設定]、[プロパティ]、[セキュリティ PIN の生成] の順に移動します。 パスフレーズの変更にはこの PIN を使用します。 |
| パスフレーズの変更 |操作に失敗しました。 ID: 120002 |**原因:**<br/>このエラーは、セキュリティ設定が有効になっており、パスフレーズを変更しようとしたものの、サポートされていないバージョンを使用していると発生します。<br/>**推奨される操作:**<br/> パスフレーズを変更するには、まずバックアップ エージェントを 2.0.9052 以上、Azure Backup Server を update 1 以上に更新してから有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にログインし、Recovery Services コンテナーで [設定]、[プロパティ]、[セキュリティ PIN の生成] の順に移動します。 パスフレーズの変更にはこの PIN を使用します。 |


## <a name="configure-email-notifications"></a>電子メール通知の構成
| 操作 | エラーの詳細 | 対処法 |
| --- | --- | --- |
| Office 365 アカウントを使用して電子メール通知を設定しようとしています。 | エラー ID: 2013| **原因:**<br/> Office 365 アカウントを使用しようとしています。 <br/> **推奨される操作:**<br/> まず、Exchange で DPM サーバーが “受信コネクタで匿名リレーを許可する” と設定されていることを確認します。 この構成方法についてのリンクを次に示します: http://technet.microsoft.com/en-us/library/bb232021.aspx <br/> 内部 SMTP リレーを使用できず、Office 365 サーバーを使用して設定する必要がある場合は、このリレーとして IIS を設定することができます。 <br/> IIS https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx を使用して、DPM サーバーが SMTP を O365 にリレーし、IIS が O365 にリレーするよう設定する必要があります。 <br/> 重要な注意事項: 手順 3 の g、ii では、ドメイン\ユーザーではなく、user@domain.com 形式を使用してください。 <br/> DPM で SMTP サーバーとしてローカル サーバー名を使用するよう設定し、ポート 587、電子メールの送信元となるユーザー メールを設定します。 <br/> SMTP の DPM のセットアップ ページのユーザー名とパスワードには、ドメイン DPM があるドメイン アカウントのものを入力します。 <br/> 注: SMTP サーバーのアドレスを変更するときは、新しい設定を変更し、設定ボックスを閉じてからもう一度開いて、新しい値が反映されていることを確認してください。  変更してテストしただけでは、新しい設定が反映されていない場合があるため、この方法でテストすることをお勧めします。 <br/> DPM コンソールを閉じて次のレジストリ キーを編集すれば、この操作中にいつでもこれらの設定を削除できます。<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> SMTPPassword と SMTPUserName キーを削除します。 <br/> もう一度起動したときに、UI からそれらを追加して戻すことができます。
