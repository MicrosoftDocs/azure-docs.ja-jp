---
title: "VMware から Azure への Azure Site Recovery のトラブルシューティング | Microsoft Docs"
description: "Azure 仮想マシンのレプリケート時に発生するエラーに関するトラブルシューティング"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>モビリティ サービスのプッシュ インストールに関する問題のトラブルシューティング

この記事では、保護を有効にするためにソース サーバー上にモビリティ サービスのインストールを試行した場合に発生する、一般的な問題について詳しく説明します。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(エラー コード 95107) 保護を有効にできませんでした。
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95107 </br>***メッセージ -*** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード ***EP0858*** で失敗しました。 <br> モビリティ サービスをインストールするために指定された資格情報が正しくないか、ユーザー アカウントに十分な権限がありません。 | ソース マシンでモビリティ サービスをインストールするために指定されたユーザー資格情報が正しくありません | 構成サーバー上にあるソース マシンに指定されたユーザー資格情報が正しいことを確認します。 <br> ユーザー資格情報を追加/編集するには、構成サーバーに移動して、[Cspsconfigtool] アイコン > [アカウントの管理] の順にクリックします。 </br> さらに、正常にプッシュ インストールを完了するための下記の前提条件を必ずチェックしてください。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(エラー コード 95015) 保護を有効にできませんでした。
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95105 </br>***メッセージ -*** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード ***EP0856*** で失敗しました。 <br> [ファイルとプリンターの共有] がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります| ファイルとプリンターの共有が有効になっていません | Windows ファイアウォールでソース マシン上の [ファイルとプリンターの共有] を許可します。ソース マシンに移動して、[Windows ファイアウォールの設定]、 [ファイアウォールを介したアプリまたは機能を許可] の順に移動し、すべてのプロファイルで [ファイルとプリンターの共有] を選択します。 </br> さらに、正常にプッシュ インストールを完了するための下記の前提条件を必ずチェックしてください。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(エラー コード 95117) 保護を有効にできませんでした。
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95117 </br>***メッセージ -*** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード ***EP0865*** で失敗しました。 <br> ソース マシンが実行されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります | プロセス サーバーとソース サーバー間のネットワーク接続 | プロセス サーバーとソース サーバー間のネットワーク接続を確認します。 </br> さらに、正常にプッシュ インストールを完了するための下記の前提条件を必ずチェックしてください。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(エラー コード 95103) 保護を有効にできませんでした。
**エラー コード** | **考えられる原因** | **エラー固有の推奨事項**
--- | --- | ---
95103 </br>***メッセージ -*** ソース マシンに対するモビリティ サービスのプッシュ インストールが、エラー コード ***EP0854*** で失敗しました。 <br> [Windows Management Instrumentation (WMI)] がソース マシンで許可されていないか、プロセス サーバーとソース マシン間のネットワーク接続に問題があります| Windows ファイアウォールで Windows Management Instrumentation (WMI) がブロックされています | Windows ファイアウォールで Windows Management Instrumentation (WMI) を許可します。 [Windows ファイアウォールの設定] > [ファイアウォールを介したアプリまたは機能を許可] の順に選択し、すべてのプロファイルで [WMI] を選択します。 </br> さらに、正常にプッシュ インストールを完了するための下記の前提条件を必ずチェックしてください。

## <a name="check-push-install-logs-for-errors"></a>エラーのプッシュ インストール ログのチェック

構成/プロセス サーバーで、<Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ にある 'PushinstallService' ファイルに移動して問題の原因を理解し、以下に示すトラブルシューティングの手順を使用して問題を解決します。</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Windows のプッシュ インストールの前提条件
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>[ファイルとプリンターの共有] が有効になっていることを確認する
Windows ファイアウォールのソース マシン上で、[ファイルとプリンターの共有] および [Windows Management Instrumentation] を許可します。 </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>ソース マシンがドメインに参加している場合:  </br>
グループ ポリシー管理コンソール (GPMC) を使ってファイアウォール設定を構成します。
1. Active directory ドメイン コンピューターに管理者としてログインし、グループ ポリシー管理コンソール (GPMC.MSC、[スタート] > [実行] から実行) を開きます。</br>
3. GPMC がインストールされていない場合、「[GPMC をインストール](https://technet.microsoft.com/library/cc725932.aspx)」の手順に従います。 </br>
4. GPMC コンソール ツリーで、フォレストおよびドメインの [グループ ポリシー オブジェクト] をダブルクリックし、[既定のドメイン ポリシー] に移動します。 </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. [既定のドメイン ポリシー] を右クリックして、[編集] をクリックすると、新しい [グループ ポリシー管理エディター] ウィンドウが開かれます。 </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. グループ ポリシー管理エディターで、[コンピューターの構成] > [ポリシー] > [管理用テンプレート] > [ネットワーク] > [ネットワーク接続] > [Windows ファイアウォール] の順に移動します。 </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. ドメイン プロファイルと標準プロファイルの次の設定を有効にします。 </br>
a)  "Windows ファイアウォール: 着信ファイルとプリンターの共有の例外を許可する" という例外をダブルクリックします。 [有効] を選択し、[OK] をクリックします。 </br>
b)  "Windows ファイアウォール: 着信リモート管理の例外を許可する" という例外をダブルクリックします。 [有効] を選択し、[OK] をクリックします。 </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>ソース マシンがドメインに参加せずワークグループの一部ではない場合 </br>
リモート コンピューター上でファイアウォール設定を次のように構成します (ワークグループに対応)。
1. ソース マシンに移動します。</br>
2. [Windows ファイアウォールの設定] > [ファイアウォールを介したアプリまたは機能を許可] の順に移動し、すべてのプロファイルで [ファイルとプリンターの共有] を選択します。 </br>
3. [Windows ファイアウォールの設定] > [ファイアウォールを介したアプリまたは機能を許可] の順に選択し、すべてのプロファイルで [WMI] を選択します。 </br>

#### <a name="disable-remote-user-account-control-uac"></a>リモート ユーザー アカウント制御 (UAC) を無効にする
以下の手順に従い、レジストリ キーを使って UAC を無効にし、モビリティ サービスをプッシュします。
1. [スタート] > [実行] の順にクリックし、「regedit」と入力して、ENTER キーを押します。
2. 次のレジストリ サブキーを見つけてクリックします。HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. LocalAccountTokenFilterPolicy レジストリ エントリが存在しない場合は、以降の手順に従います。
4. [編集] メニュー > [新規] > [DWORD 値] の順にクリックします。
5. 「LocalAccountTokenFilterPolicy」と入力し、ENTER キーを押します。
6. LocalAccountTokenFilterPolicy を右クリックし、[変更] をクリックします。
7. [値] データ ボックスに「1」と入力し、[OK] をクリックします。
8. レジストリ エディターを終了します。


## <a name="push-install-pre-requisites-for-linux"></a>Linux のプッシュ インストールの前提条件

1. ソース Linux サーバーにルート ユーザーを作成します  (このアカウントは、プッシュ インストールと更新にのみ使用します)。</br>
2. ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが存在することを確認します。 </br>
3. 最新の openssh、openssh-server、および openssl パッケージがソース Linux サーバーにインストールされていることを確認します。 </br>
ssh ポート 22 が有効であり、実行中になっていることを確認します。 </br>
4. エージェントの古いエントリがソース サーバー上に既に存在するかどうかを確認し、古いエージェントをアンインストールし、サーバーを再起動して、エージェントを再インストールします。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にする
1. ソース サーバーにルートとしてサインインします。 </br>
2. /etc/ssh/sshd_config ファイルで、</br> "PasswordAuthentication" で始まる行を見つけます。 </br>
3. d.   この行のコメントを解除し、値 "no" を "yes" に変更します。 </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. "Subsystem" で始まる行を見つけ、その行のコメントを解除します。 </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. 変更を保存し、sshd サービスを再起動します。 </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>構成/プロセス サーバーでのプッシュ インストールのチェック
#### <a name="validate-credentials-for-discovery-and-installation"></a>以下の手順に従って、検出とインストールのための資格情報を検証します。

1. 構成サーバーから、Cspsconfigtool を起動します。</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 保護に使用されるアカウントに、ソース マシンの管理者権限があることを確認します。 </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>プロセス サーバーとソース サーバー間のネットワーク接続を確認する
1. プロセス サーバーがインターネット接続を保持していることを確認します。
2. wbemtest.exe を使って WMI 接続を検証します。 </br>
プロセス サーバーで、[スタート] > [実行] > [wbemtest.exe] の順にクリックして、[Windows Management Instrumentation テスト] ウィンドウを開きます。</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
[接続] をクリックし、[名前空間] にソース サーバーの IP を入力し、ユーザー名とパスワード (ソース マシンがドメインに参加している場合は、"domainName\username" としてユーザー名と共にドメイン名を指定します。 ソース マシンがワークグループ内にある場合は、ユーザー名のみを指定します)。認証レベルに [パケットのプライバシー] を選択します。 </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   [接続] をクリックします。 これで、WMI 接続は、指定されたデータで正常に確立され、[Windows Management Instrumentation テスト] ウィンドウには次のように表示されます。 </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   WMI 接続が正常に確立されない場合、エラー メッセージのポップアップが表示されます。 次のスクリーン ショットは、Windows ファイアウォールが許可されたアプリで WMI/リモート管理が有効になっていない場合に、試行が失敗に終わっている状態を示しています。 </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. WMI の状態と接続を確認します。</br>
構成/プロセス サーバーで、 </br>
[スタート] > [実行] > [wmimgmt.msc] > [操作] > [More Actions\(その他の操作\)] の順にクリックし、別のコンピューター (ソース マシン) に接続します。 </br>
保護に使用されるアカウントの資格情報を入力し、接続に問題がないかを確認します。 </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>指定された資格情報を使って、プロセス サーバー (PS) からソース マシンのネットワーク共有フォルダーにリモートでアクセスできることを確認します。
  1. プロセス サーバー (PS) コンピューターにログオンして [ファイル エクスプローラー] を開き、アドレス バーに "\\\source-machine-ip\C$" と入力して、Enter キーを押します。 </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. ファイル エクスプローラーから、資格情報が求められます。 ユーザー名とパスワードを入力して、[OK] をクリックします。</br>
   ソース マシンがドメインに参加している場合は、"domainName\username" としてユーザー名と共にドメイン名を指定します。</br>
   ソース マシンがワークグループ内にある場合は、"username" のみを指定します。 </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. 接続に成功した場合は、プロセス サーバー (PS) からソース マシンのフォルダーをリモートで表示できます。 </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 接続に失敗した場合は、すべての前提条件を満たしているかどうか確認してください。
>

"Windows Management Instrumentation" を開きたくない場合は、ソース マシンにモビリティ サービスを手動でインストールすることもできます。</br> [GUI 経由でのモビリティ サービスの手動インストール](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[構成マネージャーを使ったインストールのガイダンス](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>次のステップ
- [VMware 仮想マシンのレプリケーションを有効にする](vmware-walkthrough-enable-replication.md)

