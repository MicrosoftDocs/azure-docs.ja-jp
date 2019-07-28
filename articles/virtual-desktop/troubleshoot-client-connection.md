---
title: Windows Virtual Desktop でのリモート デスクトップ クライアント接続 - Azure
description: Windows Virtual Desktop テナント環境でクライアント接続を設定するときの問題を解決する方法です。
services: virtual-desktop
author: chjenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b7a6daa791e44227fd146c9c328a939560ebb3b1
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605297"
---
# <a name="remote-desktop-client-connections"></a>リモート デスクトップ クライアントの接続

Windows Virtual Desktop クライアントの接続に関する問題を解決するには、この記事を使います。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop がプレビューの間は、サポート ケースを受け付けていません。 Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="you-cant-open-a-web-client"></a>Web クライアントを開くことができない

別の Web サイト ([www.Bing.com](https://www.bing.com) など) を開いて、インターネット接続があることを確認します。

**nslookup** を使って、DNS で FQDN を解決できることを確認します。

```cmd
nslookup rdweb.wvd.microsoft.com
```

別のクライアント (Windows 7 または Windows 10 用のリモート デスクトップ クライアントなど) で接続してみて、Web クライアントを開くことができるかどうかを確認します。

### <a name="error-opening-another-site-fails"></a>エラー:別のサイトを開くことができない

**原因:** ネットワークの問題または停止。

**解決策:** ネットワーク サポートに連絡します。

### <a name="error-nslookup-cannot-resolve-the-name"></a>エラー:nslookup で名前を解決できない

**原因:** ネットワークの問題または停止。

**解決策:** ネットワーク サポートに連絡します

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>エラー:自分は接続できないが、他のクライアントは接続できる

**原因:** ブラウザーが期待したとおりに動作しておらず、停止しています。

**解決策:** 次の手順に従って、ブラウザーのトラブルシューティングを行います。

1. ブラウザーを再起動します。
2. ブラウザーの Cookie をクリアします。 「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」をご覧ください。
3. ブラウザーのキャッシュをクリアします。 [お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)をご覧ください。
4. プライベート モードでブラウザーを開きます。

## <a name="web-client-stops-responding-or-disconnects"></a>Web クライアントが応答を停止するか切断する

別のブラウザーまたはクライアントを使って接続してみてください。

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>エラー:他のブラウザーやクライアントも正常に機能しないか、開けない

**原因:** ネットワークやオペレーション システムの問題または停止

**解決策:** サポート チームに問い合わせます。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web クライアントが資格情報の入力を求め続ける

Web クライアントで資格情報の入力の要求が出続ける場合は、次の手順に従います。

1. Web クライアントの URL が正しいことを確認します。
2. 資格情報が、URL に関連付けられた Windows Virtual Desktop 環境に対するものであることを確認します。
3. ブラウザーの Cookie をクリアします。 「[Internet Explorer で cookie ファイルを削除する方法](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)」をご覧ください。
4. ブラウザーのキャッシュをクリアします。 [お使いのブラウザーのブラウザー キャッシュをクリアする方法](https://binged.it/2RKyfdU)をご覧ください。
5. プライベート モードでブラウザーを開きます。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 または Windows 10 用のリモート デスクトップ クライアントが応答を停止する、または開くことができない

次の PowerShell コマンドレットを使って、帯域外 (OOB) のクライアントのレジストリをクリーンアップします。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

**%AppData%\RdClientRadc** に移動して、すべての内容を削除します。

Windows 7 および Windows 10 用のリモート デスクトップ クライアントをアンインストールしてから再インストールします。

## <a name="troubleshooting-end-user-connectivity"></a>エンド ユーサーの接続に関するトラブルシューティング

ユーザーが、フィードやローカル リソースにはアクセスできても、構成、可用性、またはパフォーマンスの問題のために、リモート リソースにアクセスできない場合があります。 このような場合は、ユーザーに対して次のようなメッセージが表示されます。

![リモート デスクトップ接続のエラー メッセージ。](media/eb76b666808bddb611448dfb621152ce.png)

![ゲートウェイに接続できない場合のエラー メッセージ。](media/a8fbb9910d4672147335550affe58481.png)

クライアント接続のエラー コードに対する一般的なトラブルシューティング手順に従います。

1. ユーザー名と問題が発生した時刻を確認します。
2. **PowerShell** を開き、問題が報告された Windows Virtual Desktop テナントへの接続を確立します。
3. **Get-RdsTenant** で、適切なテナントに接続していることを確認します。
4. **Get-RdsHostPool** および **Get-RdsSessionHost** コマンドレットを使い、正しいホスト プールでトラブルシューティングを行っていることを確認します。
5. 次のコマンドを実行し、指定した時間枠内で、種類が接続で失敗したすべてのアクティビティの一覧を取得します。

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 前のコマンドレットの出力から **ActivityId** を使って、次のコマンドを実行します。

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. コマンドによって次に示すような出力が生成されます。 **ErrorCodeSymbolic** と **ErrorMessage** を使って、根本原因のトラブルシューティングを行います。

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>エラー:O_ADD_USER_TO_GROUP_FAILED / グループ Remote Desktop Users へのユーザー ≤username≥ の追加に失敗しました。 理由:Win32.ERROR_NO_SUCH_MEMBER

**原因:** VM がユーザー オブジェクトのドメインに参加していません。

**解決策:** 正しいドメインに VM を追加します。 「[Join a Windows Server virtual machine to a managed domain](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)」 (Windows Server 仮想マシンをマネージド ドメインに参加させる) をご覧ください。

### <a name="error-nslookup-cannot-resolve-the-name"></a>エラー:nslookup で名前を解決できない

**原因:** ネットワークの問題または停止。

**解決策:** ネットワーク サポートに連絡します

### <a name="error-connectionfailedclientprotocolerror"></a>エラー:ConnectionFailedClientProtocolError

**原因:** ユーザーが接続しようとしている VM が、ドメインに参加していません。

**解決策:** ホスト プールの一部であるすべての VM を、ドメイン コントローラーに参加させます。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>ユーザーが接続しても何も表示されない (フィードなし)

ユーザーは、リモート デスクトップ クライアントを開始して認証を行うことができますが、Web 検出フィードでアイコンが何も表示されません。

次のコマンド ラインを使用し、問題を報告しているユーザーがアプリケーション グループに割り当てられていることを確認します。

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

ユーザーが正しい資格情報でログインしていることを確認します。

Web クライアントを使っている場合は、キャッシュされた資格情報の問題がないことを確認します。

## <a name="next-steps"></a>次の手順

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop で PowerShell を使用しているときに発生した問題を解決するには、「[Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)」を参照してください。
- プレビュー サービスの詳細については、「[Windows Virtual Desktop プレビュー環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)」を参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)に関するページを参照してください。
