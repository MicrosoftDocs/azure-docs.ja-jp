---
title: Windows Virtual Desktop エージェントに関する問題をトラブルシューティングする - Azure
description: エージェントと接続に関する一般的な問題を解決する方法。
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: b0fc5bd16aaa455ce3f6d634ce35e9a389a6f13b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732583"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Windows Virtual Desktop エージェントに関する一般的な問題をトラブルシューティングする

Windows Virtual Desktop エージェントでは、次の複数の要因のために接続に関する問題が発生することがあります。
   - エージェントのサービスを停止させるブローカーでのエラー。
   - 更新に関する問題。
   - セッション ホストへの接続を中断させる、エージェントのインストール中のインストールに関する問題。

この記事では、これらの一般的なシナリオの解決策と、接続に関する問題に対処する方法について説明します。

>[!NOTE]
>セッションの接続性と Windows Virtual Desktop エージェントに関連する問題のトラブルシューティングについては、 **[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** でイベント ログを確認することをお勧めします。 問題を特定するために、次のいずれかのソースがあるイベントを探してください。
>
>- WVD-Agent
>- WVD-Agent-Updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>エラー:RDAgentBootLoader またはリモート デスクトップ エージェント ローダー、あるいはその両方が動作を停止した

次のいずれかの問題が発生している場合は、エージェントを読み込むブート ローダーがエージェントを正しくインストールできなかったため、エージェント サービスが動作していないことを示します。
- **RDAgentBootLoader** が停止したか、または動作していない。
- **リモート デスクトップ エージェント ローダー** の状態がない。

この問題を解決するには、RDAgent ブート ローダーを起動します。

1. [サービス] ウィンドウで、 **[リモート デスクトップ エージェント ローダー]** を右クリックします。
2. **[スタート]** を選択します。 このオプションが灰色表示されている場合は、管理者のアクセス許可がないため、これを取得してサービスを開始する必要があります。
3. 10 秒待ってから、 **[リモート デスクトップ エージェント ローダー]** を右クリックします。
4. **[最新の情報に更新]** を選択します。
5. 開始して更新した後にサービスが停止する場合は、登録エラーが発生している可能性があります。 詳細については、「[INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)」を参照してください。

## <a name="error-invalid_registration_token"></a>エラー:INVALID_REGISTRATION_TOKEN

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では **INVALID_REGISTRATION_TOKEN** と示されます) が表示されている場合は、現在の登録トークンが有効として認識されていません。

この問題を解決するには、有効な登録トークンを作成します。

1. 新しい登録トークンを作成するには、「[VM の新しい登録キーを生成する](#step-3-generate-a-new-registration-key-for-the-vm)」セクションの手順に従います。
2. レジストリ エディターを開きます。 
3. **[HKEY_LOCAL_MACHINE]**  >  **[SOFTWARE]**  >  **[Microsoft]**  >  **[RDInfraAgent]** の順に移動します。
4. **[IsRegistered]** を選択します。 
5. **[値のデータ:]** エントリ ボックスに「**0**」と入力し、 **[OK]** を選択します。 
6. **[RegistrationToken]** を選択します。 
7. **[値のデータ:]** エントリ ボックスで、手順 1. の登録トークンを貼り付けます。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0 のスクリーンショット](media/isregistered-token.png)

8. コマンド プロンプトを管理者として開きます。
9. 「**net stop RDAgentBootLoader**」と入力します。 
10. 「**net start RDAgentBootLoader**」と入力します。 
11. レジストリ エディターを開きます。
12. **[HKEY_LOCAL_MACHINE]**  >  **[SOFTWARE]**  >  **[Microsoft]**  >  **[RDInfraAgent]** の順に移動します。
13. **[IsRegistered]** が 1 に設定されており、 **[RegistrationToken]** のデータ列に何もないことを確認します。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1 のスクリーンショット](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>エラー: エージェントを INVALID_FORM ブローカーに接続できない

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では "INVALID_FORM" と示されます) が表示されている場合は、エージェントとブローカーの間の通信で何かの問題が発生しました。 特定のファイアウォールまたは DNS の設定が原因で、エージェントをブローカーに接続できないか、特定の URL へのアクセスを有効にできません。

この問題を解決するには、BrokerURI と BrokerURIGlobal に到達できることを確認します。
1. レジストリ エディターを開きます。 
2. **[HKEY_LOCAL_MACHINE]**  >  **[SOFTWARE]**  >  **[Microsoft]**  >  **[RDInfraAgent]** の順に移動します。 
3. **BrokerURI** と **BrokerURIGlobal** の値をメモします。

   > [!div class="mx-imgBorder"]
   > ![ブローカー URI とブローカー URI グローバルのスクリーンショット](media/broker-uri.png)

 
4. ブラウザーを開き、 *\<BrokerURI\>api/health* に移動します。 
   - **BrokerURI** で、手順 3. の値を使用していることを確認します。 このセクションの例では、これは <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> になります。
5. ブラウザーで別のタブを開き、 *\<BrokerURIGlobal\>api/health* に移動します。 
   - **BrokerURIGlobal** リンクで、手順 3. の値を使用していることを確認します。 このセクションの例では、これは <https://rdbroker.wvd.microsoft.com/api/health> になります。
6. ネットワークによってブローカー接続がブロックされていない場合は、両方のページが正常に読み込まれ、次のスクリーンショットに示すように **"RD Broker is Healthy"** (RD ブローカーは正常です) というメッセージが表示されます。 

   > [!div class="mx-imgBorder"]
   > ![正常に読み込まれたブローカー URI へのアクセスのスクリーンショット](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![正常に読み込まれたブローカー グローバル URI へのアクセスのスクリーンショット](media/broker-global.png)
 

7. ネットワークによってブローカー接続がブロックされている場合は、次のスクリーンショットに示すように、ページは読み込まれません。 

   > [!div class="mx-imgBorder"]
   > ![失敗した読み込まれたブローカーへのアクセスのスクリーンショット](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![失敗した読み込まれたブローカー グローバルへのアクセスのスクリーンショット](media/unsuccessful-broker-global.png)

8. ネットワークによってこれらの URL がブロックされている場合は、必要な URL のブロックを解除する必要があります。 詳細については、「[必要な URL リスト](safe-url-list.md)」を参照してください。
9. これで問題が解決されない場合は、エージェントからブローカーへの接続をブロックする、暗号化されたグループ ポリシーが存在しないことを確認します。 Windows Virtual Desktop では、[Azure Front Door](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door) と同じ TLS 1.2 暗号化を使用します。 詳細については、「[接続のセキュリティ](network-connectivity.md#connection-security)」を参照してください。

## <a name="error-3703"></a>エラー: 3703

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3703 のイベント (説明では "RD Gateway Url: is not accessible" (RD ゲートウェイ URL にアクセスできません) と示されます) が表示されている場合は、エージェントのゲートウェイ URL へのアクセスを有効にできません。 セッション ホストに正常に接続し、これらのエンドポイントへのネットワーク トラフィックを許可して制限をバイパスするには、「[必要な URL リスト](safe-url-list.md)」にある URL のブロックを解除する必要があります。 また、ファイアウォールまたはプロキシの設定によってこれらの URL がブロックされていないことも確認してください。 これらの URL のブロックの解除は、Windows Virtual Desktop を使用するために必要です。

この問題を解決するには、ファイアウォールまたは DNS の設定によってこれらの URL がブロックされていないことを確認します。
1. [Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護](../firewall/protect-windows-virtual-desktop.md)します。
2. [Azure Firewall の DNS 設定](../firewall/dns-settings.md)を構成します。

## <a name="error-3019"></a>エラー: 3019

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3019 のイベントが表示されている場合は、エージェントの Web ソケット トランスポートの URL へのアクセスを有効にできないことを意味します。 セッション ホストに正常に接続し、ネットワーク トラフィックを許可してこれらの制限をバイパスするには、「[必要な URL リスト](safe-url-list.md)」にリストされている URL のブロックを解除する必要があります。 Azure ネットワーク チームと協力して、ファイアウォール、プロキシ、DNS 設定でそれらの URL がブロックされていないことを確認してください。 また、ネットワーク トレース ログを確認して、Windows Virtual Desktop サービスがブロックされている場所を特定することもできます。 この特定の問題に対するサポート リクエストを開く場合は、必ずネットワーク トレース ログをリクエストに添付してください。

## <a name="error-installationhealthcheckfailedexception"></a>エラー: InstallationHealthCheckFailedException

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では "InstallationHealthCheckFailedException" と示されます) が表示されている場合は、ターミナル サーバーによってスタック リスナーのレジストリ キーが切り替えられたため、そのスタック リスナーが機能していないことを意味します。

この問題を解決するには、次の手順を実行します。
1. [スタック リスナーが動作](#error-stack-listener-isnt-working-on-windows-10-2004-vm)していることを確認します。
2. スタック リスナーが動作していない場合は、[手動でスタック コンポーネントをアンインストールして再インストール](#error-vms-are-stuck-in-unavailable-or-upgrading-state)してください。

## <a name="error-endpoint_not_found"></a>エラー: ENDPOINT_NOT_FOUND

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では "ENDPOINT_NOT_FOUND" と示されます) が表示されている場合は、ブローカーで接続を確立するエンドポイントを見つけることができなかったことを意味します。 この接続の問題は、次のいずれか 1 つが原因で発生することがあります。

- ホスト プールに VM がない
- ホスト プール内の VM がアクティブでない
- ホスト プール内のすべての VM がセッションの上限を超えている
- ホスト プール内のどの VM でも、エージェント サービスが実行されていない

この問題を解決するには、次の手順を実行します。

1. VM の電源が入っており、かつホスト プールから削除されていないことを確認します。
2. VM がセッションの上限を超えていないことを確認します。
3. [エージェント サービスが実行](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running)されていて、かつ[スタック リスナーが動作](#error-stack-listener-isnt-working-on-windows-10-2004-vm)していることを確認します。
4. [エージェントをブローカーに接続できる](#error-agent-cannot-connect-to-broker-with-invalid_form)ことを確認します。
5. [VM に有効な登録トークンが](#error-invalid_registration_token)あることを確認します。
6. [VM 登録トークンの有効期限が切れていない](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues)ことを確認します。 

## <a name="error-installmsiexception"></a>エラー:InstallMsiException

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では **InstallMsiException** と示されます) が表示されている場合は、エージェントをインストールしようとするときにインストーラーが既に別のアプリケーションのために動作しているか、またはポリシーによって msiexec.exe プログラムの実行がブロックされています。

この問題を解決するには、次のポリシーを無効にします。
   - Windows インストーラーをオフにする  
      - カテゴリ パス: コンピューターの構成\管理用テンプレート\Windows コンポーネント\Windows インストーラー
   
>[!NOTE]
>これはポリシーの包括的な一覧ではなく、現在認識されているものだけです。

ポリシーを無効にするには、次の手順を実行します。
1. コマンド プロンプトを管理者として開きます。
2. 「**rsop.msc**」と入力して実行します。
3. ポップアップ表示される **[ポリシーの結果セット]** ウィンドウで、カテゴリ パスに移動します。
4. ポリシーを選択します。
5. **[無効]** をクリックします。
6. **[適用]** を選択します。   

   > [!div class="mx-imgBorder"]
   > ![[ポリシーの結果セット] での Windows インストーラー ポリシーのスクリーンショット](media/gpo-policy.png)

## <a name="error-win32exception"></a>エラー:Win32Exception

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では **InstallMsiException** と示されます) が表示されている場合は、ポリシーによって cmd.exe の起動がブロックされています。 このプログラムをブロックすると、エージェントが更新されるたびにサービスを再開するために使用する必要のあるコンソール ウィンドウが実行されなくなります。

この問題を解決するには、次のポリシーを無効にします。
   - コマンド プロンプトへのアクセス禁止   
      - カテゴリ パス: ユーザーの構成\管理用テンプレート\システム
    
>[!NOTE]
>これはポリシーの包括的な一覧ではなく、現在認識されているものだけです。

ポリシーを無効にするには、次の手順を実行します。
1. コマンド プロンプトを管理者として開きます。
2. 「**rsop.msc**」と入力して実行します。
3. ポップアップ表示される **[ポリシーの結果セット]** ウィンドウで、カテゴリ パスに移動します。
4. ポリシーを選択します。
5. **[無効]** をクリックします。
6. **[適用]** を選択します。   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>エラー:スタック リスナーが Windows 10 2004 VM で動作していない

コマンド プロンプトで **qwinsta** を実行し、**rdp-sxs** の横に表示されるバージョン番号をメモします。 **qwinsta** を実行した後に **rdp-tcp** および **rdp-sxs** コンポーネントの横に **[リッスン]** が表示されていないか、またはそれらのコンポーネントがまったく表示されない場合は、スタックの問題が発生していることを示します。 スタックの更新プログラムはエージェントの更新プログラムと共にインストールされ、このインストールが失敗すると、Windows Virtual Desktop リスナーは機能しません。

この問題を解決するには、次の手順を実行します。
1. レジストリ エディターを開きます。
2. **[HKEY_LOCAL_MACHINE]**  >  **[SYSTEM]**  >  **[CurrentControlSet]**  >  **[Control]**  >  **[Terminal Server]**  >  **[WinStations]** の順に移動します。
3. **[WinStations]** には、さまざまなスタック バージョン用のいくつかのフォルダーが表示されることがあります。コマンド プロンプトで **qwinsta** を実行したときに表示されたバージョン情報に一致するフォルダーを選択します。
4. **fReverseConnectMode** を見つけ、そのデータ値が **1** であることを確認します。 また、**fEnableWinStation** が **1** に設定されていることも確認してください。

   > [!div class="mx-imgBorder"]
   > ![fReverseConnectMode のスクリーンショット](media/fenable-2.png)

5. **fReverseConnectMode** が **1** に設定されていない場合は、 **[fReverseConnectMode]** を選択し、その値フィールドに「**1**」と入力します。 
6. **fEnableWinStation** が **1** に設定されていない場合は、 **[fEnableWinStation]** を選択し、その値フィールドに「**1**」と入力します。
7. VM を再起動する。 

>[!NOTE]
>複数の VM の **fReverseConnectMode** または **fEnableWinStation** モードを一度に変更するには、次の 2 つの操作のいずれかを実行できます。
>
>- 既に動作しているコンピューターからレジストリ キーをエクスポートし、この変更を必要とするその他のすべてのコンピューターにインポートします。
>- この変更を必要とするコンピューターのレジストリ キー値を設定する一般的なポリシー オブジェクト (GPO) を作成します。

7. **[HKEY_LOCAL_MACHINE]**  >  **[SYSTEM]**  >  **[CurrentControlSet]**  >  **[Control]**  >  **[Terminal Server]**  >  **[ClusterSettings]** の順に移動します。
8. **[ClusterSettings]** で、**SessionDirectoryListener** を見つけ、そのデータ値が **rdp-sxs...** であることを確認します。
9. **SessionDirectoryListener** が **rdp-sxs...** に設定されていない場合は、[エージェントとブート ローダーのアンインストール](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs)のセクションの手順に従って、最初にエージェント、ブート ローダー、スタック コンポーネントをアンインストールし、次に [エージェントとブート ローダーを再インストールする](#step-4-reinstall-the-agent-and-boot-loader)必要があります。 これにより、サイドバイサイド スタックが再インストールされます。

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>エラー: ユーザーがセッション ホストから切断されたままになるハートビートの問題

サーバーで Windows Virtual Desktop サービスからのハートビートが収集されていない場合は、ハートビートのしきい値を変更する必要があります。 次のシナリオが 1 つ以上当てはまる場合は、このセクションの手順に従ってください。

- **CheckSessionHostDomainIsReachableAsync** エラーを受け取っている
- **ConnectionBrokenMissedHeartbeatThresholdExceeded** エラーを受け取っている
- **ConnectionEstablished:UnexpectedNetworkDisconnect** エラーを受け取っている
- ユーザー クライアントが切断された状態のままである
- ユーザーがそれらのセッション ホストから切断されたままである

ハートビートのしきい値を変更するには、次のようにします。
1. 管理者としてコマンド プロンプトを開きます。
2. **qwinsta** コマンドを入力して実行します。
3. **rdp-tcp** と **rdp-sxs** の 2 つのスタック コンポーネントが表示されます。 
   - 使用している OS のバージョンによっては、**rdp-sxs** の後にビルド番号が表示されることがあります。 その場合は、後のために、この番号を書き留めておくようにしてください。
4. レジストリ エディターを開きます。
5. **[HKEY_LOCAL_MACHINE]**  >  **[SYSTEM]**  >  **[CurrentControlSet]**  >  **[Control]**  >  **[Terminal Server]**  >  **[WinStations]** の順に移動します。
6. **[WinStations]** には、さまざまなスタック バージョン用のいくつかのフォルダーが表示されることがあります。 手順 3. のバージョン番号に一致するフォルダーを選択します。
7. レジストリ エディターを右クリックし、 **[新規]**  >  **[DWORD (32 ビット) 値]** の順に選択して、新しいレジストリ DWORD を作成します。 この DWORD を作成するとき、次の値を入力します。
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. VM を再起動する。

>[!NOTE]
>ハートビートのしきい値を変更しても問題が解決しない場合は、Azure ネットワーク チームに問い合わせる必要がある潜在的なネットワークの問題が発生している可能性があります。

## <a name="error-downloadmsiexception"></a>エラー:DownloadMsiException

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3277 のイベント (説明では **DownloadMsiException** と示されます) が表示されている場合は、ディスク上に RDAgent 用の十分な領域がありません。

この問題を解決するには、次を実行してディスク上の領域を確保します。
   - ユーザーが使用しなくなったファイルを削除する
   - VM のストレージ容量を増やす

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>エラー: MissingMethodException によりエージェントを更新できない

**[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** の順に移動します。 ID 3389 のイベント (説明では "MissingMethodException: Method not found" (MissingMethodException: メソッドが見つからない) と示されます) が表示されている場合は、Windows Virtual Desktop エージェントが正常に更新されず、以前のバージョンに戻されていることを意味します。 これは、VM に現在インストールされている .NET Framework のバージョン番号が 4.7.2 よりも低いことが原因である可能性があります。 この問題を解決するには、[.NET Framework のドキュメント](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)に記載されているインストール手順に従って、.NET をバージョン 4.7.2 以降にアップグレードする必要があります。


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>エラー:VM が [使用不可] または [アップグレード中] 状態でスタックしている

管理者として PowerShell ウィンドウを開き、次のコマンドレットを実行します。

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

ホスト プール内の 1 つまたは複数のセッション ホストに対して表示されている状態が常に "使用不可" または "アップグレード中" である場合は、エージェントやスタックが正しくインストールされなかった可能性があります。

この問題を解決するには、サイドバイサイド スタックを再インストールします。
1. コマンド プロンプトを管理者として開きます。
2. 「**net stop RDAgentBootLoader**」と入力します。 
3. **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。
4. **リモート デスクトップ サービス SxS ネットワーク スタック** の最新バージョン、または **[HKEY_LOCAL_MACHINE]**  >  **[SYSTEM]**  >  **[CurrentControlSet]**  >  **[Control]**  >  **[Terminal Server]**  >  **[WinStations]** の **[ReverseConnectListener]** に示されているバージョンをアンインストールします。
5. 管理者としてコンソール ウィンドウを開き、 **[プログラム ファイル]**  >  **[Microsoft RDInfra]** の順に移動します。
6. **SxSStack** コンポーネントを選択するか、または **msiexec /i SxsStack-<version>.msi** コマンドを実行して MSI をインストールします。
8. VM を再起動する。
9. コマンド プロンプトに戻り、**qwinsta** コマンドを実行します。
10. 手順 6. でインストールされたスタック コンポーネントの横に **[リッスン]** が表示されていることを確認します。
   - その場合は、コマンド プロンプトで「**net start RDAgentBootLoader**」と入力し、VM を再起動します。
   - そうでない場合は、[VM を再登録し、エージェント コンポーネントを再インストールする](#your-issue-isnt-listed-here-or-wasnt-resolved)必要があります。

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>エラー:接続が見つからない: RDAgent にはブローカーへのアクティブな接続がない

VM が接続の制限に達している可能性があるため、VM は新しい接続を受け入れることができません。

この問題を解決するには、次の手順を実行します。
   - 最大セッション数を減らします。 これにより、リソースがセッション ホストにまたがってより均等に分散されるようになるため、リソースの枯渇が防止されます。
   - VM のリソース容量を増やします。

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>エラー:Pro VM またはその他のサポートされていない OS を操作している

サイドバイサイド スタックは、Windows Enterprise または Windows Server SKU でのみサポートされています。つまり、Pro VM などのオペレーティング システムはサポートされません。 Enterprise や Server SKU がない場合、スタックは VM にインストールされますが、アクティブ化されないため、コマンド ラインで **qwinsta** を実行したときに表示されません。

この問題を解決するには、Windows Enterprise または Windows Server である VM を作成します。
1. 「[仮想マシンの詳細](create-host-pools-azure-marketplace.md#virtual-machine-details)」に移動し、手順 1. ～ 12. に従って、次の推奨されるイメージのいずれかを設定します。
   - Windows 10 Enterprise マルチセッション、バージョン 1909
   - Windows 10 Enterprise マルチセッション、バージョン 1909 + Microsoft 365 アプリ
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise マルチセッション、バージョン 2004
   - Windows 10 Enterprise マルチセッション、バージョン 2004 + Microsoft 365 アプリ
2. **[確認と作成]** を選択します。

## <a name="error-name_already_registered"></a>エラー:NAME_ALREADY_REGISTERED

VM の名前が既に登録されており、重複している可能性があります。

この問題を解決するには、次の手順を実行します。
1. 「[ホスト プールからセッション ホストを削除する](#step-2-remove-the-session-host-from-the-host-pool)」セクションの手順に従います。
2. [別の VM を作成](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal)します。 この VM には一意の名前を選択するようにしてください。
3. [Azure portal](https://portal.azure.com) に移動し、VM が存在していたホスト プールの **[概要]** ページを開きます。 
4. **[セッション ホスト]** タブを開き、すべてのセッション ホストがそのホスト プール内にあることを確認します。
5. セッション ホストの状態が **[使用可能]** と表示されるまで 5 ～ 10 分待ちます。

   > [!div class="mx-imgBorder"]
   > ![使用可能なセッション ホストのスクリーンショット](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>問題がここに示されていないか、または解決されなかった

問題がこの記事に見つからないか、またはその手順が役に立たなかった場合は、Windows Virtual Desktop エージェントをアンインストールし、再インストールして、再登録することをお勧めします。 このセクションの手順では、すべてのエージェント、ブート ローダー、スタック コンポーネントをアンインストールし、ホスト プールからセッション ホストを削除し、VM の新しい登録キーを生成し、エージェントとブート ローダーを再インストールすることによって VM を Windows Virtual Desktop サービスに再登録する方法を示します。 次のシナリオの 1 つまたは複数に当てはまる場合は、これらの手順に従ってください。
- VM が **[アップグレード中]** または **[使用不可]** でスタックしている
- スタック リスナーが動作しておらず、Windows 10 1809、1903、または 1904 上で実行している
- **EXPIRED_REGISTRATION_TOKEN** エラーが表示されている
- VM がセッション ホストの一覧に表示されていない
- [サービス] ウィンドウに **[リモート デスクトップ エージェント ローダー]** が表示されない
- タスク マネージャーに **RdAgentBootLoader** コンポーネントが表示されない
- カスタム イメージ VM で **接続ブローカーで設定を検証できなかった** エラーを受け取っている
- この記事の手順によって問題が解決されなかった

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>手順 1:すべてのエージェント、ブート ローダー、スタック コンポーネント プログラムをアンインストールする

エージェント、ブート ローダー、スタックを再インストールする前に、VM から既存のコンポーネント プログラムをすべてアンインストールする必要があります。 すべてのエージェント、ブート ローダー、スタック コンポーネント プログラムをアンインストールするには、次の手順を実行します。
1. 管理者として VM にサインインします。 
2. **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]** に移動します。
3. 次のプログラムを削除します。
   - リモート デスクトップ エージェント ブート ローダー
   - リモート デスクトップ サービス インフラストラクチャ エージェント
   - リモート デスクトップ サービス インフラストラクチャ Geneva エージェント
   - リモート デスクトップ サービス SxS ネットワーク スタック
   
>[!NOTE]
>これらのプログラムの複数のインスタンスが表示されることがあります。 それらをすべて削除するようにしてください。

   > [!div class="mx-imgBorder"]
   > ![プログラムのアンインストールのスクリーンショット](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>手順 2:ホスト プールからセッション ホストを削除する

ホスト プールからセッション ホストを削除すると、このセッション ホストはそのホスト プールに登録されていない状態になります。 これは、セッション ホストの登録のリセットとして機能します。 ホスト プールからセッション ホストを削除するには、次の手順を実行します。
1. [Azure portal](https://portal.azure.com) で、この VM が存在するホスト プールの **[概要]** ページに移動します。 
2. **[セッション ホスト]** タブに移動して、そのホスト プール内のすべてのセッション ホストの一覧を表示します。
3. セッション ホストの一覧を確認し、削除する VM を選択します。
4. **[削除]** を選択します。  

   > [!div class="mx-imgBorder"]
   > ![ホスト プールからの VM の削除のスクリーンショット](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>手順 3: VM の新しい登録キーを生成する

VM をホスト プールとサービスに再登録するために使用される新しい登録キーを生成する必要があります。 VM の新しい登録キーを生成するには、次の手順を実行します。
1. [Azure portal](https://portal.azure.com) を開き、編集する VM のホスト プールの **[概要]** ページに移動します。
2. **[登録キー]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![ポータル内の登録キーのスクリーンショット](media/reg-key.png)

3. **[登録キー]** タブを開き、 **[新しいキーの生成]** を選択します。
4. 有効期限を入力し、 **[OK]** を選択します。  

>[!NOTE]
>有効期限は、その生成日時から 1 時間以上で、かつ 27 日以内にする必要があります。 有効期限を 27 日の最大値に設定することを強くお勧めします。

5. 新しく生成されたキーをクリップボードにコピーします。 このキーは、後で必要になります。

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>手順 4:エージェントとブート ローダーを再インストールする

エージェントとブート ローダーの最新バージョンを再インストールすると、サイドバイサイド スタックと Geneva 監視エージェントも自動的にインストールされます。 エージェントとブート ローダーを再インストールするには、次の手順を実行します。
1. 管理者として VM にサインインし、VM が実行されている Windows のバージョンに応じて、適切なバージョンのエージェント インストーラーをデプロイに使用します。 Windows 10 の VM がある場合は、[仮想マシンの登録](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)に関するページの手順に従って、**Windows Virtual Desktop エージェント** と **Windows Virtual Desktop エージェント ブートローダー** をダウンロードします。 Windows 7 の VM がある場合は、[仮想マシンの登録](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine)に関するページの手順 13 と 14 に従って、**Windows Virtual Desktop エージェント** と **Windows Virtual Desktop エージェント マネージャー** をダウンロードします。

   > [!div class="mx-imgBorder"]
   > ![エージェントとブートローダーのダウンロード ページのスクリーンショット](media/download-agent.png)

2. ダウンロードしたエージェントとブート ローダーのインストーラーを右クリックします。
3. **[プロパティ]** を選択します。
4. **[Unblock]\(ブロック解除\)** を選択します。
5. **[OK]** を選択します。
6. エージェント インストーラーを実行します。
7. インストーラーから登録トークンの入力を求められたら、クリップボードから登録キーを貼り付けます。 

   > [!div class="mx-imgBorder"]
   > ![貼り付けられた登録トークンのスクリーンショット](media/pasted-agent-token.png)

8. ブート ローダー インストーラーを実行します。
9. VM を再起動する。 
10. [Azure portal](https://portal.azure.com) に移動し、VM が属しているホスト プールの **[概要]** ページを開きます。
11. **[セッション ホスト]** タブに移動して、そのホスト プール内のすべてのセッション ホストの一覧を表示します。
12. これで、ホスト プールに登録されているセッション ホストが **[使用可能]** の状態で表示されます。 

   > [!div class="mx-imgBorder"]
   > ![使用可能なセッション ホストのスクリーンショット](media/hostpool-portal.png)

## <a name="next-steps"></a>次の手順

引き続き問題が発生する場合は、サポート ケースを作成し、発生している問題とそれを解決しようとして実行したすべてのアクションに関する詳細情報を含めます。 次の一覧には、Windows Virtual Desktop のデプロイでの問題をトラブルシューティングするために使用できるその他のリソースが含まれています。

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でホスト プールを作成しているときに発生した問題のトラブルシューティングを行うには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントに関する問題をトラブルシューティングするには、「[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md)」を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
