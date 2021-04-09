---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする間の構成サーバーでの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用した VMware VM と物理サーバーの Azure へのディザスター リカバリーのために構成サーバーをデプロイする場合のトラブルシューティング情報を提供します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371955"
---
# <a name="troubleshoot-configuration-server-issues"></a>構成サーバーの問題のトラブルシューティング

この記事は、[Azure Site Recovery](site-recovery-overview.md) 構成サーバーをデプロイおよび管理するときの問題のトラブルシューティングに役立ちます。 構成サーバーは管理サーバーとして機能します。 構成サーバーは、Site Recovery を使用してオンプレミスの VMware VM と物理サーバーを Azure にディザスター リカバリーするように設定するときに使用されます。 以降のセクションでは、新しい構成サーバーを追加するときや構成サーバーを管理するときに発生する可能性がある最も一般的なエラーについて説明します。

## <a name="registration-failures"></a>登録エラー

ソース マシンは、モビリティ エージェントをインストールするときに構成サーバーに登録されます。 この手順中に発生したエラーはすべて、以下のガイドラインに従ってデバッグできます。

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log ファイルを開きます (ProgramData フォルダーは非表示のフォルダーである場合があります。 ProgramData フォルダーが表示されない場合は、エクスプローラーの **[表示]** タブにある **[表示/非表示]** セクションで、 **[Hidden items]\(非表示の項目\)** チェック ボックスをオンにします)。エラーは、複数の問題が原因で発生する可能性があります。

2. 文字列「**No Valid IP Address found (有効な IP アドレスが見つかりません)** 」を検索します。 この文字列が見つかった場合:
   1. 要求されているホスト ID がソース マシンのホスト ID と同じであることを確認します。
   2. 物理 NIC に 1 つ以上の IP アドレスが割り当てられていることを確認します。 構成サーバーへのエージェントの登録が成功するためには、ソース マシンの物理 NIC に 1 つ以上の有効な IP v4 アドレスが割り当てられている必要があります。
   3. ソース マシン上で次のいずれかのコマンドを実行して、ソース マシンのすべての IP アドレスを取得します。
      - Windows の場合: `> ipconfig /all`
      - Linux の場合: `# ifconfig -a`

3. 文字列「**No Valid IP Address found (有効な IP アドレスが見つかりません)** 」が見つからない場合は、文字列「**Reason=>NULL (理由 =>NULL)** 」を検索します。 このエラーは、ソース マシンが空のホストを使用して構成サーバーに登録された場合に発生します。 この文字列が見つかった場合:
    - 問題を解決した後で、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を手動で再試行してください。

4. 文字列「**Reason=>NULL (理由 =>NULL)** 」が見つからない場合は、ソース マシン上で C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log ファイルを開きます (ProgramData フォルダーは非表示のフォルダーである場合があります。 ProgramData フォルダーが表示されない場合は、エクスプローラーの **[表示]** タブにある **[表示/非表示]** セクションで、 **[Hidden items]\(非表示の項目\)** チェック ボックスをオンにします)。エラーは、複数の問題が原因で発生する可能性があります。 

5. 文字列「**post request:(7) - Couldn't connect to server (POST 要求: (7) - サーバーに接続できませんでした)** 」を検索します。 この文字列が見つかった場合:
    1. ソース マシンと構成サーバーの間におけるネットワークの問題を解決します。 ping、traceroute、Web ブラウザーなどのネットワーク ツールを使用して、ソース マシンから構成サーバーにアクセスできることを確認します。 ソース マシンがポート 443 経由で構成サーバーにアクセスできることを確認します。
    2. ソース マシン上のファイアウォール規則によって、ソース マシンと構成サーバー間の接続がブロックされていないかどうかを調べます。 接続の問題がある場合は、お客様のネットワーク管理者と協力して解決してください。
    3. 「[ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)」の一覧に記載されているフォルダーがウイルス対策プログラムから除外されていることを確認します。
    4. ネットワークの問題を解決したら、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を再試行してください。

6. 文字列「**post request:(7) - Couldn't connect to server (POST 要求: (7) - サーバーに接続できませんでした)** 」が見つからない場合は、同じログ ファイル内で文字列「**request:(60) - Peer certificate cannot be authenticated with given CA certificates (要求: (60) - ピア証明書は与えられた CA 証明書で認証できません)** 」を検索します。 このエラーは、構成サーバーの証明書の有効期限が切れているか、またはソース マシンで TLS 1.0 以降のプロトコルがサポートされていないことが原因で発生する可能性があります。 また、ファイアウォールによってソース マシンと構成サーバー間の TLS 通信がブロックされている場合にも発生する可能性があります。 この文字列が見つかった場合: 
    1. 解決するには、ソース マシン上の Web ブラウザーを使用して構成サーバーの IP アドレスに接続します。 URI https:\/\/<configuration server IP address\>:443/ を使用します。 ソース マシンがポート 443 経由で構成サーバーにアクセスできることを確認します。
    2. ソース マシンから構成サーバーにアクセスするためにソース マシン上のファイアウォール規則を追加または削除する必要がないかどうかを調べます。 使用されている可能性のあるファイアウォール ソフトウェアにはさまざまな種類があるため、必要なすべてのファイアウォール構成の一覧をここで示すことはできません。 接続の問題がある場合は、お客様のネットワーク管理者と協力して解決してください。
    3. 「[ウイルス対策プログラムからの Azure Site Recovery フォルダーの除外](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)」の一覧に記載されているフォルダーがウイルス対策プログラムから除外されていることを確認します。  
    4. 問題を解決したら、「[ソース マシンを構成サーバーに登録する](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)」に記載されたガイドラインに従って、登録を再試行してください。

7. Linux では、<INSTALLATION_DIR\>/etc/drscout.conf 内のプラットフォームの値が破損していると登録に失敗します。 この問題を特定するには、/var/log/ua_install.log ファイルを開きます。 文字列「**Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure (VM_PLATFORM の値が null であるか、VmWare/Azure でないため、構成を中止しています)** 」を検索します。 プラットフォームは **VmWare** または **Azure** に設定する必要があります。 drscout.conf ファイルが破損している場合は、[モビリティ エージェントをアンインストール](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)してから再インストールすることをお勧めします。 アンインストールに失敗した場合は、次の手順を実行します。 a. Installation_Directory/uninstall.sh ファイルを開き、**StopServices** 関数の呼び出しをコメントアウトします。
    b. Installation_Directory/Vx/bin/uninstall.sh ファイルを開き、**stop_services** 関数の呼び出しをコメントアウトします。
    c. Installation_Directory/Fx/uninstall.sh ファイルを開き、Fx サービスの停止を試行するセクション全体をコメントアウトします。
    d. モビリティ エージェントを[アンインストール](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)します。 アンインストールが成功したら、システムを再起動して、モビリティ エージェントの再インストールを試みます。

8. ユーザー アカウントの多要素認証が有効になっていないことを確認します。 Azure Site Recovery では現在のところ、ユーザー アカウントで多要素認証をサポートしていません。 多要素認証が有効になっているユーザー アカウントなしで構成サーバーを登録します。  

## <a name="installation-failure-failed-to-load-accounts"></a>インストールの失敗: アカウントを読み込めませんでした

このエラーは、モビリティ エージェントをインストールして構成サーバーに登録するときにサービスがトランスポート接続からデータを読み取れない場合に発生します。 この問題を解決するには、お客様のソース マシン上で TLS 1.0 を有効にします。

## <a name="vcenter-discovery-failures"></a>vCenter の検出エラー

vCenter の検出エラーを解決するには、バイパス リスト プロキシ設定に vCenter サーバーを追加します。 

- システム ユーザーのコンテンツにアクセスするために、[こちら](/sysinternals/downloads/psexec)から PsExec ツールをダウンロードします。
- コマンド ライン psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" を実行して、インターネット エクスプローラーでシステム ユーザーのコンテンツを開きます。
- IE にプロキシ設定を追加し、tmanssvc サービスを再開します。
- DRA プロキシ設定を構成するために、cd C:\Program Files\Microsoft Azure Site Recovery Provider を実行します。
- 次に、DRCONFIGURATOR.EXE /configure /AddBypassUrls を実行します (「[構成サーバーをデプロイする](vmware-azure-deploy-configuration-server.md#configure-settings)」の **vCenter Server/vSphere ESXi Server を構成する** 手順の間に提供された vCenter Server の IP アドレス/FQDN を追加します)。

## <a name="change-the-ip-address-of-the-configuration-server"></a>構成サーバーの IP アドレスの変更

構成サーバーの IP アドレスは変更しないことを強くお勧めします。 構成サーバーに割り当てられているすべての IP アドレスが静的 IP アドレスであるようにしてください。 DHCP IP アドレスは使用しないでください。

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008:SAML トークンが無効です

このエラーを回避するには、お客様のシステム クロックの時刻と現地時刻に 15 分以上の差がないようにします。 インストーラーを再実行して登録を完了します。

## <a name="failed-to-create-a-certificate"></a>証明書を作成できませんでした

Site Recovery の認証に必要な証明書を作成できません。 ローカル管理者として設定を実行していることを確認してから、設定を再実行します。

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Server Standard 評価版から Server Standard への Windows ライセンスのアクティブ化が失敗する

1. OVF を通じた構成サーバーのデプロイの一部として、評価ライセンスが使用されます。このライセンスは 180 日間有効です。 この有効期限が切れる前に、このライセンスをアクティブ化する必要があります。 そうしないと、構成サーバーが頻繁にシャットダウンされ、それによりレプリケーション アクティビティが妨げられることがあります。
2. Windows ライセンスをアクティブ化できない場合、問題を解決するために [Windows サポート チーム](https://aka.ms/Windows_Support)にお問い合わせください。

## <a name="register-source-machine-with-configuration-server"></a>ソース マシンを構成サーバーに登録する

### <a name="if-the-source-machine-runs-windows"></a>ソース マシン上で Windows が実行されている場合

ソース マシン上で次のコマンドを実行します。

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

設定 | 詳細
--- | ---
使用法 | UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address\> /PassphraseFilePath <passphrase file path\>
エージェント構成ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log の下にあります。
/CSEndPoint | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
/PassphraseFilePath |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。

### <a name="if-the-source-machine-runs-linux"></a>ソース マシン上で Linux が実行されている場合

ソース マシン上で次のコマンドを実行します。

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

設定 | 詳細
--- | ---
使用法 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <configuration server IP address\> -P <passphrase file path\>
-i | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
-P |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。

## <a name="unable-to-configure-the-configuration-server"></a>構成サーバーを構成できない

仮想マシンに構成サーバー以外のアプリケーションをインストールすると、マスター ターゲットを構成できない場合があります。 

構成サーバーは専用のサーバーである必要があります。共有サーバーとしての使用はサポートされません。 

詳細については、[構成サーバーのデプロイ](vmware-azure-deploy-configuration-server.md#faqs)に関するページの構成 FAQ を参照してください。 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>構成サーバー データベースから保護項目の古いエントリを削除する 

構成サーバーで古い保護マシンを削除するには、次の手順を使用します。 
 
1. 古いエントリのソース マシンと IP アドレスを特定するには: 

    1. MYSQL コマンドラインを管理者モードで開きます。 
    2. 次のコマンドを実行します。 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        これにより、登録されているマシンとそれらの IP アドレスおよび直近のハート ビートの一覧が返されます。 古いレプリケーション ペアのあるホストを検索します。

2. 管理者特権でのコマンド プロンプトを開き、C:\ProgramData\ASR\home\svsystems\bin に移動します。 
4. 構成サーバーから登録されているホストの詳細と古いエントリ情報を削除するには、古いエントリのソース マシンと IP アドレスを使用して次のコマンドを実行します。 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    IP アドレスが 10.0.0.4 の "OnPrem-VM01" というソース サーバー エントリがある場合、代わりに次のコマンドを使用します。
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. ソース マシンで次のサービスを再起動して構成サーバーに登録します。 
 
    - InMage Scout アプリケーション サービス
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>サービスの停止に失敗するときにアップグレードが失敗する

特定のサービスが停止していない場合、構成サーバーのアップグレードは失敗します。 

問題を特定するために、構成サーバーで C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile に移動します。 次のエラーが見つかった場合、次の手順を使用して問題を解決します。 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

この問題を解決するには:

次のサービスを手動で停止します。

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost 
- Microsoft Azure Recovery Services エージェント 
- Microsoft Azure Site Recovery サービス 
- tmansvc
  
構成サーバーを更新するために、[統合セットアップ](service-updates-how-to.md#links-to-currently-supported-update-rollups)を再実行します。

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory アプリケーションの作成が失敗する

[Open Virtualization Application (OVA)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) テンプレートを使用して Azure Active Directory (AAD) でアプリケーションを作成するためのアクセス許可が不十分です。

この問題を解決するには、Azure portal にサインインし、次のいずれかを行います。

- AAD でアプリケーション開発者ロールを要求します。 アプリケーション開発者ロールの詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/roles/permissions-reference.md)」を参照してください。
- AAD で **[User can create application]\(ユーザーがアプリケーションを作成できる\)** フラグが *true* に設定されていることを確認します。 詳細については、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)」のガイダンスに従って、サービス プリンシパルを作成します。

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>プロセス サーバー/マスター ターゲットが構成サーバーと通信できない 

プロセス サーバー (PS) とマスター ターゲット (MT) モジュールが構成サーバー (CS) と通信できず、それらの状態が未接続として Azure portal に表示されます。

通常、これはポート 443 でのエラーが原因です。 次の手順を使用して、ポートのブロックを解除し、CS との通信を再度有効にしてください。

**MARS エージェントがマスター ターゲット エージェントによって呼び出されているか確認する**

マスター ターゲット エージェントが構成サーバー IP の TCP セッションを作成できるか確認するには、マスター ターゲット エージェントのログで次のようなトレースを探します。

TCP \<Replace IP with CS IP here>:52739 \<Replace IP with CS IP here>:443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // ここでは IP は CS IP に置き換えます

MT エージェントのログで次のようなトレースが見つかった場合、MT エージェントはポート 443 でのエラーを報告しています。

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
このエラーは、他のアプリケーションもポート 443 を使用している場合、またはファイアウォール設定によってポートがブロックされている場合に発生する可能性があります。

この問題を解決するには:

- ポート 443 がファイアウォールによってブロックされていないことを確認します。
- このポートを使用している別のアプリケーションが原因でこのポートに到達できない場合、そのアプリを停止し、アンインストールします。
  - アプリを停止できない場合は、新しいクリーン CS をセットアップします。
- 構成サーバーを再起動します。
- IIS サービスを再起動します。

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>UUID エントリが正しくないため構成サーバーに接続されない

このエラーは、データベースに複数の構成サーバー (CS) インスタンス UUID のエントリがある場合に発生することがあります。 この問題は、構成サーバー VM を複製したときによく発生します。

この問題を解決するには:

1. vCenter から古い CS VM を削除します。 詳細については、[サーバーの削除と保護の無効化](site-recovery-manage-registration-and-protection.md)に関するページを参照してください。
2. 構成サーバー VM にサインインし、MySQL svsdb1 データベースに接続します。 
3. 次のクエリを実行します。

    > [!IMPORTANT]
    >
    > 複製した構成サーバーの UUID 詳細を入力しているか、または仮想マシンを保護するために使用しなくなった構成サーバーの古いエントリを入力しているか確認します。 正しくない UUID を入力すると、既存のすべての保護項目の情報が失われることになります。
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. ポータル ページを更新します。

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>資格情報の入力時に無限サインイン ループが発生する

構成サーバー OVF で正しいユーザー名とパスワードを入力した後、Azure サインインで引き続き正しい資格情報の入力を求めるプロンプトが表示されます。

この問題は、システム時刻が正しくない場合に発生することがあります。

この問題を解決するには:

コンピューターで正しい時刻を設定し、サインインを再試行します。 
