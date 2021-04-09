---
title: Azure File Sync のトラブルシューティング | Microsoft Docs
description: Windows Server の Azure ファイル共有の高速キャッシュへの変換に使用可能な Azure File Sync での、デプロイに関する一般的な問題のトラブルシューティング。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 2/1/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4439346a065f17f5b2136f51fd5e8b990fa1b914
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491301"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure File Sync のトラブルシューティング
Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事は、Azure File Sync のデプロイで発生する可能性がある問題のトラブルシューティングと解決を支援することを目的としています。 問題をさらに調査する必要がある場合に、システムから重要なログを収集する方法についても説明します。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. [Azure Storage に関する Microsoft Q&A 質問ページ](/answers/products/azure?product=storage)。
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。
3. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、 **[ヘルプとサポート]** ボタンを選択し、 **[新しいサポート要求]** を選択します。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>サーバーで Azure File Sync に関する問題 (同期、クラウド階層化など)が発生しています。 サーバー エンドポイントを削除して再作成する必要がありますか。
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>エージェントのインストールとサーバー登録
<a id="agent-installation-failures"></a>**エージェントのインストール エラーをトラブルシューティングする**  
管理者特権でのコマンド プロンプトで Azure File Sync エージェントのインストールに失敗した場合は、次のコマンドを実行してエージェントのインストール時のログ記録を有効にします。

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

installer.log をレビューして、インストールが失敗した原因を特定します。

<a id="agent-installation-on-DC"></a>**Active Directory ドメイン コントローラーにエージェントをインストールできない**  
同期エージェントを Active Directory ドメイン コントローラーにインストールしようとしたが、PDC ロール所有者が Windows Server 2008 R2 (またはそれより前のバージョン) に存在する場合は、同期エージェントのインストールが失敗するという問題が発生することがあります。

この問題を解決するには、Windows Server 2012 R2 以降を実行している別のドメイン コントローラーに PDC ロールを転送してから、同期エージェントをインストールします。

<a id="parameter-is-incorrect"></a>**Windows Server 2012 R2 でボリュームにアクセスできず、エラーが表示される:パラメーターが正しくありません**  
Windows Server 2012 R2 でサーバー エンドポイントを作成した後、ボリュームにアクセスすると次のエラーが発生する:

driveletter:\ にアクセスできません。  
パラメーターが正しくありません。

この問題を解決するには、[KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) をインストールしてサーバーを再起動します。 新しい更新プログラムが既にインストールされているためにこの更新プログラムがインストールされない場合は、Windows Update にアクセスして、Windows Server 2012 R2 の最新の更新プログラムをインストールし、サーバーを再起動します。

<a id="server-registration-missing-subscriptions"></a>**サーバー登録で一部の Azure サブスクリプションが一覧に表示されない**  
ServerRegistration.exe を使用してサーバーを登録している場合、[Azure サブスクリプション] ドロップダウンをクリックしたとき、サブスクリプションが表示されません。

この問題は、ServerRegistration.exe が最初の 5 つの Azure AD テナントからのみサブスクリプションを取得するために発生します。 

サーバーのサーバー登録テナントの制限を増やすには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync の下に 5 より大きい値を持つ ServerRegistrationTenantLimit という名前の DWORD 値を作成します。

また、次の PowerShell コマンドを使用してサーバーを登録することで、この問題を回避することもできます。

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a> **[サーバー登録] に"前提条件が見つからない" というメッセージが表示される**  
このメッセージは、Az または AzureRM PowerShell モジュールが PowerShell 5.1 にインストールされていない場合に表示されます。 

> [!Note]  
> ServerRegistration.exe では、PowerShell 6.x はサポートされていません。 サーバーを登録するには、PowerShell 6.x で AzStorageSyncServer コマンドレットを使用できます。

PowerShell 5.1 に Az または AzureRM モジュールをインストールするには、次の手順を実行します。

1. 管理者特権のプロンプトで「**powershell**」と入力して、Enter キーを押します。
2. 次のドキュメントに従って、最新の Az または AzureRM モジュールをインストールします。
    - [Az モジュール (.NET 4.7.2 が必要)](/powershell/azure/install-az-ps)
    - [AzureRM モジュール](https://go.microsoft.com/fwlink/?linkid=856959)
3. ServerRegistration.exe を実行し、ストレージ同期サービスにサーバーを登録するウィザードを完了します。

<a id="server-already-registered"></a> **[サーバー登録] に"このサーバーは既に登録されています" というメッセージが表示される** 

!["server is already registered" (このサーバーは既に登録されています) エラー メッセージが表示された [サーバーの登録] ダイアログのスクリーンショット](media/storage-sync-files-troubleshoot/server-registration-1.png)

このメッセージは、以前にサーバーがストレージ同期サービスに登録されていた場合に表示されます。 現在のストレージ同期サービスからサーバーを登録解除し、新しいストレージ同期サービスに登録するには、[Azure File Sync でのサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関する記事に記載されている手順を完了します。

ストレージ同期サービスの **[登録済みサーバー]** にサーバーが一覧表示されていない場合は、登録解除するサーバー上で次の PowerShell コマンドを実行します。

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> サーバーがクラスターの一部である場合は、省略可能な *Reset-StorageSyncServer -CleanClusterRegistration* パラメーターを使用して、クラスターの登録を削除することもできます。

<a id="web-site-not-trusted"></a>**サーバーの登録時に多数の "Web サイトが信頼されていない" という応答が表示されます。なぜですか?**  
このエラーは、サーバーの登録中に **Enhanced Internet Explorer Security** ポリシーが有効になった場合に発生します。 **Enhanced Internet Explorer Security** ポリシーを適切に無効にする方法の詳細については、「[Azure File Sync で使用する Windows Server の準備](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)」および [Azure File Sync のデプロイ方法](storage-sync-files-deployment-guide.md)に関する記事をご覧ください。

<a id="server-registration-missing"></a>**Azure Portal の [登録済みサーバー] にサーバーが表示されない**  
サーバーがストレージ同期サービスの **[登録済みサーバー]** に表示されない場合:
1. 登録するサーバーにサインインします。
2. ファイル エクスプローラーを開き、ストレージ同期エージェントのインストール ディレクトリ (既定の場所は C:\Program Files\Azure\StorageSyncAgent) に移動します。 
3. ServerRegistration.exe を実行し、ストレージ同期サービスにサーバーを登録するウィザードを完了します。

## <a name="sync-group-management"></a>同期グループ管理

### <a name="cloud-endpoint-creation-errors"></a>クラウド エンドポイントの作成エラー

<a id="cloud-endpoint-using-share"></a>**クラウド エンドポイントの作成が "The specified Azure FileShare is already in use by a different CloudEndpoint (指定された Azure ファイル共有は別の CloudEndpoint で既に使用されています)" というエラーで失敗する**  
このエラーは、Azure ファイル共有が別のクラウド エンドポイントによって既に使用されている場合に発生します。 

このメッセージが表示されたときに、Azure ファイル共有が現在クラウド エンドポイントで使用されていない場合は、次の手順を完了して、Azure ファイル共有上の Azure File Sync メタデータをクリアします。

> [!Warning]  
> 現在クラウド エンドポイントによって使用されている Azure ファイル共有上のメタデータを削除すると、Azure File Sync の操作は失敗します。 その後、別の同期グループでこのファイル共有を同期に使用すると、古い同期グループ内のファイルのデータ損失がほぼ確実になります。

1. Azure ポータルで、Azure ファイル共有に移動します。  
2. Azure ファイル共有を右クリックし、 **[メタデータの編集]** を選択します。
3. **[SyncService]** を右クリックし、 **[削除]** を選択します。

<a id="cloud-endpoint-authfailed"></a>**クラウド エンドポイントの作成が "AuthorizationFailed" というエラーで失敗する**  
このエラーは、ユーザー アカウントがクラウド エンドポイントを作成するための十分な権限を持っていない場合に発生します。 

クラウド エンドポイントを作成するには、次の Microsoft 承認アクセス許可を持つユーザー アカウントが必要です。  
* Read:ロール定義の取得
* 書き込み:カスタムのロール定義の作成または更新
* Read:Get role assignment
* 書き込み:ロール割り当ての作成

次の組み込みロールには、必要な Microsoft 承認アクセス許可が付与されています。  
* 所有者
* User Access Administrator

現在のユーザー アカウントのロールに必要なアクセス許可が付与されているかどうかを確認するには:  
1. Azure portal で、 **[リソース グループ]** を選択します。
2. ストレージ アカウントのあるリソース グループを選択し、 **[アクセス制御 (IAM)]** を選択します。
3. **[ロールの割り当て]** タブを選択します。
4. ユーザー アカウントに割り当てる **[ロール]** (所有者や共同作成者など) を選択します。
5. **[リソース プロバイダー]** 一覧で、 **[Microsoft 承認]** を選択します。 
    * **[ロールの割り当て]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。
    * **[ロール定義]** のアクセス許可が **[読み取り]** と **[書き込み]** になっている必要があります。

### <a name="server-endpoint-creation-and-deletion-errors"></a>サーバー エンドポイントの作成と削除のエラー

<a id="-2134375898"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134375898 または 0x80c80226)** というエラーで失敗する  
このエラーは、サーバー エンドポイントのパスがシステム ボリューム上にあり、クラウドを使った階層化が有効な場合に発生します。 システム ボリュームでは、クラウドの階層化はサポートされていません。 システム ボリュームにサーバー エンドポイントを作成するには、サーバー エンドポイントを作成するときにクラウドの階層化を無効にします。

<a id="-2147024894"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2147024894 または 0x80070002)** というエラーで失敗する  
このエラーは、指定したサーバー エンドポイントのパスが有効でない場合に発生します。 指定したサーバー エンドポイントのパスがローカルに接続された NTFS ボリュームであることを確認します。 Azure File Sync は、サーバー エンドポイント パスとして、マップされたドライブをサポートしていないことに注意してください。

<a id="-2134375640"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134375640 or 0x80c80328)**  
このエラーは、指定したサーバー エンドポイント パスが NTFS ボリュームでない場合に発生します。 指定したサーバー エンドポイントのパスがローカルに接続された NTFS ボリュームであることを確認します。 Azure File Sync は、サーバー エンドポイント パスとして、マップされたドライブをサポートしていないことに注意してください。

<a id="-2134347507"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134347507 または 0x80c8710d)** というエラーで失敗する  
このエラーは、Azure File Sync が、圧縮されたシステム ボリューム情報フォルダーがあるボリューム上でサーバー エンドポイントをサポートしていないことが原因で発生します。 この問題を解決するには、システム ボリューム情報フォルダーを圧縮解除します。 システム ボリューム情報フォルダーがボリュームにある唯一の圧縮フォルダーである場合は、次の手順を実行します。

1. [PsExec](/sysinternals/downloads/psexec) ツールをダウンロードします。
2. 管理者特権でのコマンド プロンプトから次のコマンドを実行して、システム アカウントで実行されるコマンド プロンプトを起動します。**PsExec.exe -i -s -d cmd**
3. システム アカウントで実行されているコマンド プロンプトから、次のコマンドを入力し、Enter キーを押します。   
    **cd /d "drive letter:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134376345 または 0x80C80067)** というエラーで失敗する  
このエラーは、サーバーあたりのサーバー エンドポイント数が制限に達した場合に発生します。 Azure File Sync は、現在、サーバーあたり最大で 30 のサーバー エンドポイントをサポートしています。 詳細については、「[Azure File Sync のスケール ターゲット](./storage-files-scale-targets.md#azure-file-sync-scale-targets)」をご覧ください。

<a id="-2134376427"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2134376427 または 0x80c80015) というエラーで失敗する**  
このエラーは、指定したサーバー エンドポイント パスが別のサーバー エンドポイントによって既に同期されている場合に発生します。 Azure File Sync では、同じディレクトリまたはボリュームを複数のサーバー エンドポイントで同期することはサポートされていません。

<a id="-2160590967"></a>**サーバー エンドポイントの作成が "MgmtServerJobFailed" (エラー コード: -2160590967 または 0x80c80077) というエラーで失敗する**  
このエラーは、サーバー エンドポイントのパスに孤立した階層化ファイルが含まれている場合に発生します。 サーバー エンドポイントが最近削除された場合は、孤立した階層化ファイルのクリーンアップが完了するまで待ちます。 孤立した階層化ファイルのクリーンアップが開始されると、イベント ID 6662 がテレメトリ イベント ログに記録されます。 孤立した階層化ファイルのクリーンアップが完了し、パスを使用してサーバー エンドポイントを再度作成できるようになったら、イベント ID 6661 が記録されます。 階層化されたファイルのクリーンアップが完了した後にサーバー エンドポイントの作成が失敗する場合、またはイベント ログがロールオーバーしたためにテレメトリ イベント ログにイベント ID 6661 が見つからない場合は、「[サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)」セクションの手順を実行して、孤立した階層化ファイルを削除します。

<a id="-2134347757"></a>**サーバー エンドポイントの削除が "MgmtServerJobExpired" (エラー コード: -2134347757 または 0x80c87013)** というエラーで失敗する  
このエラーは、サーバーがオフラインの場合、またはネットワークに接続されていない場合に発生します。 サーバーを使用できなくなったら、ポータルでサーバーの登録を解除します。これで、サーバー エンドポイントが削除されます。 サーバー エンドポイントを削除するには、[Azure File Sync 使用したサーバーの登録解除](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)に関するセクションで説明されている手順を実行します。

### <a name="server-endpoint-health"></a>サーバー エンドポイントの正常性

<a id="server-endpoint-provisioningfailed"></a> **[サーバー エンドポイントのプロパティ] ページが開かない、またはクラウドの階層化ポリシーを更新できない**  
この問題は、サーバー エンドポイントでの管理操作が失敗する場合に発生することがあります。 Azure Portal で [サーバー エンドポイントのプロパティ] ページが開かない場合は、サーバーから PowerShell コマンドでサーバー エンドポイント を更新すると、この問題が解決する場合があります。 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**サーバー エンドポイントの正常性状態が "アクティビティなし" または "保留中" で、登録済みサーバー ブレードのサーバーの状態が "オフラインのようです" になっている**  

この問題は、ストレージ同期モニター プロセス (AzureStorageSyncMonitor.exe) が実行されていない場合、またはサーバーが Azure File Sync サービスにアクセスできない場合に発生する可能性があります。

ポータルに "オフラインのようです" と表示されているサーバーで、(イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にある) テレメトリ イベント ログのイベント ID 9301 を確認し、そのサーバーが Azure File Sync サービスにアクセスできない理由を特定します。 

- **GetNextJob の完了状態が 0** であると記録されている場合、サーバーは Azure File Sync サービスと通信できます。 
    - サーバーでタスク マネージャーを開き、ストレージ同期モニター (AzureStorageSyncMonitor.exe) プロセスが実行されていることを確認します。 プロセスが実行されていない場合は、最初にサーバーの再起動を試みます。 サーバーを再起動しても問題が解決しない場合は、Azure File Sync [エージェントのバージョン](./storage-files-release-notes.md)を最新のものにアップグレードします。 

- **GetNextJob completed with status: -2134347756** がログに記録されている場合、サーバーはファイアウォール、プロキシ、または TLS 暗号スイートの順序の構成が原因で Azure File Sync サービスと通信できません。 
    - サーバーがファイアウォールの背後にある場合は、送信ポート 443 が許可されていることを確認します。 ファイアウォールで特定のドメインへのトラフィックが制限されている場合は、ファイアウォールの[ドキュメント](./storage-sync-files-firewall-and-proxy.md#firewall)に記載されているドメインにアクセスできることを確認します。
    - サーバーがプロキシの背後にある場合は、プロキシの[ドキュメント](./storage-sync-files-firewall-and-proxy.md#proxy)に記載されている手順に従って、コンピューター全体またはアプリ固有のプロキシ設定を構成します。
    - このサービス エンドポイントへのネットワーク接続を確認するには、Test-StorageSyncNetworkConnectivity コマンドレットを使用します。 詳細については、[サービス エンドポイントへのネットワーク接続のテスト](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)に関するページを参照してください。
    - TLS 暗号スイートの順序がサーバーで構成されている場合は、グループ ポリシーまたは TLS コマンドレットを使用して、暗号スイートを追加できます。
        - グループ ポリシーを使用するには、「[グループ ポリシーを使用した TLS 暗号スイートの順序の構成](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)」を参照してください。
        - TLS コマンドレットを使用するには、「[TLS PowerShell コマンドレットを使用した TLS 暗号スイートの順序の構成](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)」を参照してください。
    
        現在、Azure File Sync では TLS 1.2 プロトコル用の次の暗号スイートがサポートされています。  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- **GetNextJob の完了状態が -2134347764** であると記録されている場合、サーバーは証明書の有効期限切れまたは削除が原因で Azure File Sync サービスと通信できません。  
    - サーバーで次の PowerShell コマンドを実行して、認証に使用される証明書をリセットしてください。
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**サーバー エンドポイントの正常性状態が "アクティビティなし" で、登録済みサーバー ブレードのサーバーの状態が "オンライン" になっている**  

サーバー エンドポイントの正常性状態 [アクティビティなし] とは、過去 2 時間にわたってサーバー エンドポイントで同期アクティビティが記録されていないことを意味します。

サーバー上の現在の同期アクティビティを確認するには、「[現在の同期セッションの進行状況を監視するにはどうすればよいですか。](#how-do-i-monitor-the-progress-of-a-current-sync-session)」を参照してください。

バグまたはシステム リソースの不足によって、サーバー エンドポイントで数時間、同期アクティビティを記録できていない場合があります。 最新の Azure File Sync [エージェント バージョン](./storage-files-release-notes.md)がインストールされていることを確認してください。 問題が解決しない場合は、サポート リクエストを開いてください。

> [!Note]  
> [登録済みサーバー] ブレードで、サーバーの状態が "オフラインのようです" になっている場合は、「[サーバー エンドポイントの正常性状態が "アクティビティなし" または "保留中" で、登録済みサーバー ブレードのサーバーの状態が "オフラインのようです" になっている](#server-endpoint-noactivity)」セクションに記載されている手順を行ってください。

## <a name="sync"></a>同期
<a id="afs-change-detection"></a>**SMB またはポータルを使用して Azure ファイル共有内にファイルを直接作成した場合、ファイルが同期グループ内のサーバーと同期されるまでどのくらい時間がかかりますか?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**サーバー エンドポイントの正常性が数時間にわたって保留状態になる**  
この問題は、クラウド エンドポイントを作成してデータが格納されている Azure ファイル共有を使用した場合に発生することが予期されます。 クラウドとサーバー エンドポイント間でファイルを同期する前に、Azure ファイル共有の変更をスキャンする変更列挙ジョブが完了している必要があります。 このジョブの完了にかかる時間は、Azure ファイル共有内の名前空間のサイズに依存します。 変更列挙ジョブが完了したら、サーバー エンドポイントの正常性を更新する必要があります。

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>同期の正常性を監視するにはどうすればよいですか。
# <a name="portal"></a>[ポータル](#tab/portal1)
各同期グループ内で、個別のサーバー エンドポイントをドリルダウンして、最後に完了した同期セッションの状態を確認できます。 [正常性] 列が緑色で、[Files Not Syncing]\(同期していないファイル数\) の値が 0 の場合は、同期が予期したとおりに動作していることを示します。 そうでない場合は、下に示す一般的な同期エラーの一覧と、同期していないファイルの処理方法を参照してください。 

![Azure portal のスクリーンショット](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[サーバー](#tab/server)
イベント ビューアーの `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` にあるテレメトリ ログに移動します。 イベント 9102 は、完了した同期セッションに該当します。同期の最新の状態を調べるには、ID 9102 の最新のイベントを探します。 SyncDirection は、このセッションがアップロードとダウンロードのどちらだったかを示しています。 HResult が 0 の場合、同期セッションは成功しています。 HResult が 0 以外の場合は、同期中にエラーが発生したことを意味します。以下に示す一般的なエラーの一覧を参照してください。 PerItemErrorCount が 0 より大きい場合は、一部のファイルまたはフォルダーが正しく同期されなかったことを意味します。 HResult が 0 であるのに、PerItemErrorCount が 0 より大きい場合もあります。

成功したアップロードの例を次に示します。 簡潔にするために、下の一覧では、各 9102 イベントに含まれる一部の値のみを示しています。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

これに対して、失敗したアップロードは次のようになります。

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

同期セッション全体が失敗するか、または PerItemErrorCount が 0 以外であるのに、セッションが進行して一部のファイルが正常に同期される場合もあります。 このことは、成功しているセッションの数を示す Applied* フィールド (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount、および AppliedSizeBytes) で確認できます。 行内に失敗している同期セッションが複数あるのに Applied* の数が増えている場合は、サポート チケットを開く前に、時間をとって同期の再試行を待つ必要があります。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>現在の同期セッションの進行状況を監視するにはどうすればよいですか。
# <a name="portal"></a>[ポータル](#tab/portal1)
同期グループ内で、問題のサーバー エンドポイントに移動して [同期アクティビティ] セクションを調べ、現在の同期セッション内のアップロード済みファイルとダウンロード済みファイルの数を確認します。 この状態は約 5 分遅れて表示されるため、この時間内に完了するほど小規模な同期セッションの場合は、ポータルでレポートされない可能性があります。 

# <a name="server"></a>[サーバー](#tab/server)
サーバーのテレメトリ ログ (イベント ビューアーで [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] に移動) で、最新の 9302 イベントを調べます。 このイベントは、現在の同期セッションの状態を示します。 TotalItemCount は同期が必要なファイルの数、AppliedItemCount はこれまでに同期されたファイルの数、PerItemErrorCount は同期が失敗しているファイルの数 (処理方法については後で説明します) を示します。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>自分のサーバーが互いに同期されているかどうかを確認するにはどうすればよいですか。
# <a name="portal"></a>[ポータル](#tab/portal1)
特定の同期グループ内の各サーバーについて、以下を確認します。
- アップロードとダウンロードの両方で、[最後に試行された同期] のタイムスタンプが最新であること。
- アップロードとダウンロードの両方で、状態が緑色であること。
- [同期アクティビティ] フィールドに表示された残りの同期ファイル数が非常に少ないか、0 であること。
- アップロードとダウンロードの両方で、[Files Not Syncing]\(同期していないファイル数\) フィールドが 0 であること。

# <a name="server"></a>[サーバー](#tab/server)
完了した同期セッションを調べます。これは、各サーバーのテレメトリ イベント ログ (イベント ビューアーで `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` に移動) で 9102 イベントとマークされています。 

1. 特定のサーバー上で、アップロード セッションとダウンロード セッションが正常に完了したことを確認します。 そのためには、アップロードとダウンロードの両方で HResult と PerItemErrorCount が 0 であることを確認します (SyncDirection フィールドは、そのセッションがアップロード セッションとダウンロード セッションのどちらであるかを示します)。 最近完了した同期セッションが表示されていない場合は、同期セッションが現在進行中である可能性があります。この状況は、大量のデータを追加または変更した直後に発生することがあります。
2. サーバーがクラウドに対して完全に最新の状態であり、どちらの方向にも同期が必要な変更がない場合は、空の同期セッションが表示されます。 これは、すべての Sync* フィールド (SyncFileCount、SyncDirCount、SyncTombstoneCount、および SyncSizeBytes) が 0 である (同期する対象がなかったことを意味する) アップロード イベントおよびダウンロード イベントによって示されます。チャーンが高いサーバーでは、同期する新しい対象が常にあるため、このような空の同期セッションは発生しません。同期アクティビティがない場合は、30 分ごとに発生します。 
3. すべてのサーバーがクラウドに対して最新の状態である、つまり、最近のアップロード セッションとダウンロード セッションが空の同期セッションである場合は、システム全体が同期されていると合理的な確実性を持って言うことができます。 
    
Azure ファイル共有内で直接変更を加えた場合、Azure File Sync は変更列挙が実行されるまでこの変更を検出しません。変更列挙は 24 時間ごとに実行されます。 サーバーがクラウドに対して最新の状態であると示していても、実際には、Azure ファイル共有内で直接加えられた最近の変更を検出していないこともあり得ます。 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>同期していない特定のファイルやフォルダーがあるかどうかを確認するにはどうすればよいですか。
特定の同期セッションに対して、サーバーの PerItemErrorCount またはポータルの [Files Not Syncing]\(同期していないファイル数\) の数が 0 より大きい場合は、何らかの項目の同期が失敗していることを意味します。ファイルやフォルダーに、同期を妨げる特性がある可能性があります。 これらの特性は、永続的で、同期を再開するためには明示的なアクション (たとえば、ファイル名やフォルダー名からサポートされていない文字を削除するなど) が必要な場合があります。 また、特性は一時的であり、ファイルやフォルダーの同期が自動的に再開される場合もあります。たとえば、開くハンドルを含むファイルは、ファイルが閉じられたときに自動的に同期を再開します。 Azure File Sync エンジンがそのような問題を検出すると、エラー ログが生成されます。これを解析して、現在正しく同期していない項目を一覧表示できます。

これらのエラーを確認するには、**FileSyncErrorsReport.ps1** PowerShell スクリプト (Azure File Sync エージェントのエージェント インストール ディレクトリ内にあります) を実行して、開くハンドルやサポートされていない文字などの問題によって同期が失敗したファイルを識別します。 ItemPath フィールドは、ルート同期ディレクトリを基準としたファイルの相対的な場所を示します。 修復の手順については、下に示す一般的な同期エラーの一覧を参照してください。

> [!Note]  
> FileSyncErrorsReport.ps1 スクリプトから "ファイル エラーは見つかりませんでした" が返された場合、または同期グループの項目ごとのエラーが一覧表示されない場合は、次のいずれかの原因が考えられます。
>
>- 原因 1:最後に完了した同期セッションに項目単位のエラーがありませんでした。 同期していないファイルが 0 個であることを示すために、ポータルをすぐに更新する必要があります。 
>    - テレメトリ イベント ログの[イベント ID 9102](?tabs=server%252cazure-portal#broken-sync) を調べ、PerItemErrorCount が 0 であることを確認します。 
>
>- 原因 2:項目ごとのエラーが多すぎるため、サーバー上の ItemResults イベント ログが折り返されました。イベント ログには、この同期グループのエラーはもう含まれていません。
>    - この問題を回避するには、ItemResults イベント ログのサイズを増やします。 ItemResults イベント ログは、イベント ビューアーの "Applications and Services Logs\Microsoft\FileSync\Agent" にあります。 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>ファイル/ディレクトリ単位の同期エラーのトラブルシューティング
**ItemResults log - 項目単位の同期エラー**  

| HRESULT | HRESULT (10 進値) | エラー文字列 | 問題 | Remediation |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | サーバー上の階層化されたファイルにアクセスできません。 この問題は、サーバー エンドポイントを削除する前に、階層化されたファイルが取り消されなかった場合に発生します。 | この問題を解決する方法は、「[サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)」を参照してください。 |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 依存フォルダーがまだ同期されていないため、ファイルまたはディレクトリの変更を同期できません。 この項目は、依存する変更が同期された後に同期されます。 | 必要なアクションはありません。 エラーが数日間継続する場合は、PowerShell の FileSyncErrorsReport.ps1 スクリプトを使用して、依存フォルダーがなぜまだ同期されないかを確認します。 |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | 依存フォルダーがまだ同期されていないため、ファイルまたはディレクトリの変更を同期できません。 この項目は、依存する変更が同期された後に同期されます。 | 必要なアクションはありません。 エラーが数日間継続する場合は、PowerShell の FileSyncErrorsReport.ps1 スクリプトを使用して、依存フォルダーがなぜまだ同期されないかを確認します。 |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 依存フォルダーがまだ同期されておらず、同期セッションが失敗したため、ファイルまたはディレクトリの変更を同期できません。 この項目は、依存する変更が同期された後に同期されます。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、同期セッションの失敗を調査します。 |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | ファイルまたはディレクトリの名前が無効です。 | 問題のファイルまたはディレクトリの名前を変更します。 詳しくは、「[サポートされていない文字の処理](?tabs=portal1%252cazure-portal#handling-unsupported-characters)」をご覧ください。 |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | ファイルまたはディレクトリの名前が無効です。 | 問題のファイルまたはディレクトリの名前を変更します。 詳しくは、「[サポートされていない文字の処理](?tabs=portal1%252cazure-portal#handling-unsupported-characters)」をご覧ください。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | ファイルは使用中のため、同期できません。 ファイルは使用されなくなると同期されます。 | 必要なアクションはありません。 Azure File Sync は、1 日 1 回サーバー上で一時 VSS スナップショットを作成して、開くハンドルを含むファイルを同期します。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | ファイルが変更されましたが、まだ同期によって変更が検出されていません。この変更が検出された後に同期が復旧します。 | 必要なアクションはありません。 |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | ファイルが削除されており、同期で変更が認識されません。 | 必要なアクションはありません。 変更の検出によってファイルが削除されたことが検出されると、同期によってこのエラーのログ記録が停止されます。 |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 同期先で項目が既に削除されているのに同期で変更が認識されないため、ファイルまたはディレクトリの削除を同期できません。 | 必要なアクションはありません。 同期先で変更の検出が実行されて、削除済みの項目が同期で検出されると、同期によってこのエラーのログ記録が停止されます。 |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | ファイルまたはディレクトリがスキップされましたが、次の同期セッション中に同期されます。 項目のダウンロード時にこのエラーがレポートされる場合は、ファイルまたはディレクトリの名前が高確率で無効になっています。 | ファイルのアップロード時にこのエラーがレポートされる場合は、必要なアクションはありません。 ファイルのダウンロード時にこのエラーがレポートされる場合は、問題のファイルまたはディレクトリの名前を変更します。 詳しくは、「[サポートされていない文字の処理](?tabs=portal1%252cazure-portal#handling-unsupported-characters)」をご覧ください。 |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 同期先で項目が既に存在しているのに同期で変更が認識されないため、ファイルまたはディレクトリの作成を同期できません。 | 必要なアクションはありません。 同期先で変更の検出が実行されて、この新しい項目が同期で認識されると、同期によってこのエラーのログ記録が停止されます。 |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure ファイル共有の制限に達したため、ファイルを同期できません。 | この問題を解決するには、トラブルシューティング ガイドの「[Azure のファイル共有ストレージの上限に到達しました](?tabs=portal1%252cazure-portal#-2134351810)」をご覧ください。 |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | ファイルは、サポートされていないソリューション (NTFS EFS など) によって暗号化されています。 | ファイルの暗号化を解除し、サポートされている暗号化ソリューションを使用します。 サポートされているソリューションの一覧については、計画ガイドの「[暗号化ソリューション](./storage-sync-files-planning.md#encryption)」セクションをご覧ください。 |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | ファイルは、DFS-R 読み取り専用レプリケーション フォルダーにあります。 | ファイルは、DFS-R 読み取り専用レプリケーション フォルダーにあります。 Azure Files Sync は DFS-R 読み取り専用レプリケーション フォルダーにおけるサーバー エンドポイントをサポートしません。 詳細については、[計画ガイド](./storage-sync-files-planning.md#distributed-file-system-dfs)を参照してください。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | ファイルは削除保留中の状態です。 | 必要なアクションはありません。 開いているファイルのすべてのハンドルが閉じられると、ファイルは削除されます。 |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | ストレージ アカウントでファイアウォールと仮想ネットワークの設定が有効になっていて、サーバーにそのストレージ アカウントへのアクセス権がないため、ファイルを同期できません。 | デプロイ ガイドの「[ファイアウォールと仮想ネットワークの設定を構成する](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)」セクションに記載されている手順に従い、サーバー IP アドレスまたは仮想ネットワークを追加します。 |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | セキュリティ記述子のサイズが 64 KiB の制限を超えているため、ファイルを同期できません。 | この問題を解決するには、ファイルのアクセス制御エントリ (ACE) を削除して、セキュリティ記述子のサイズを減らします。 |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 予期しないエラーが発生したため、ファイルを同期できません。 | エラーが数日間継続して発生する場合は、サポート ケースを開いてください。 |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | ファイルは使用中のため、同期できません。 ファイルは使用されなくなると同期されます。 | 必要なアクションはありません。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 同期中にファイルが変更されたため、再度同期する必要があります。 | 必要なアクションはありません。 |
| 0x80070017 | -2147024873 | ERROR_CRC | CRC エラーが発生したため、ファイルを同期できません。 このエラーは、サーバー エンドポイントを削除する前に、階層化されたファイルがリコールされなかったか、ファイルが壊れている場合に発生することがあります。 | この問題を解決する方法は、「[サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)」を参照し、孤立状態の階層化されたファイルを削除してください。 孤立状態にあった階層化されたファイルを削除した後もエラーが解消されない場合は、ボリューム上で [chkdsk](/windows-server/administration/windows-commands/chkdsk) を実行します。 |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 競合ファイルの最大数に達したため、ファイルを同期できません。 Azure File Sync は、1 つのファイルにつき 100 個の競合ファイルをサポートします。 ファイル競合の詳細については、Azure File Sync の[よく寄せられる質問 (FAQ)](./storage-files-faq.md#afs-conflict-resolution) を参照してください。 | この問題を解決するには、競合ファイルの数を減らします。 競合ファイルの数が 100 個未満になると、ファイルは同期されます。 |

#### <a name="handling-unsupported-characters"></a>サポートされていない文字の処理
**FileSyncErrorsReport.ps1** PowerShell スクリプトで、サポートされていない文字が原因で項目単位の同期エラー (エラー コード 0x8007007b または 0x80c80255) が示されている場合は、該当するファイル名から問題のある文字を削除するか、ファイル名を変更する必要があります。 これらの文字の大部分には標準のビジュアル エンコードがないため、PowerShell はこれらの文字を疑問符または空の四角形として出力します。 
> [!Note]  
> [評価ツール](storage-sync-files-planning.md#evaluation-cmdlet)を使用して、サポートされていない文字を識別できます。 データセットに無効な文字が含まれている複数のファイルがある場合は、[ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) スクリプトを使用して、サポートされていない文字を含むファイルの名前を変更します。

下の表に、Azure File Sync でまだサポートされていない Unicode 文字をすべて示します。

| 文字セット | 文字数 |
|---------------|-----------------|
| <ul><li>0x0000009D (osc オペレーティング システム コマンド)</li><li>0x00000090 (dcs デバイス コントロール文字列)</li><li>0x0000008F (ss3 シングル シフト 3)</li><li>0x00000081 (ハイ オクテット プリセット)</li><li>0x0000007F (del 削除)</li><li>0x0000008D (ri 逆改行)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (アラビア文字表示形 - a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (特殊文字) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (非文字)</li><li>0x0002FFFE - 0x0002FFFF = 2 (非文字)</li><li>0x0003FFFE - 0x0003FFFF = 2 (非文字)</li><li>0x0004FFFE - 0x0004FFFF = 2 (非文字)</li><li>0x0005FFFE - 0x0005FFFF = 2 (非文字)</li><li>0x0006FFFE - 0x0006FFFF = 2 (非文字)</li><li>0x0007FFFE - 0x0007FFFF = 2 (非文字)</li><li>0x0008FFFE - 0x0008FFFF = 2 (非文字)</li><li>0x0009FFFE - 0x0009FFFF = 2 (非文字)</li><li>0x000AFFFE - 0x000AFFFF = 2 (非文字)</li><li>0x000BFFFE - 0x000BFFFF = 2 (非文字)</li><li>0x000CFFFE - 0x000CFFFF = 2 (非文字)</li><li>0x000DFFFE - 0x000DFFFF = 2 (非文字)</li><li>0x000EFFFE - 0x000EFFFF = 2 (未定義)</li><li>0x000FFFFE - 0x000FFFFF = 2 (補助私用領域)</li></ul> | 30 |
| 0x0010FFFE、0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>一般的な同期エラー
<a id="-2147023673"></a>**同期セッションが取り消されました。**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (10 進値)** | -2147023673 | 
| **エラー文字列** | ERROR_CANCELLED |
| **修復が必要か** | いいえ |

同期セッションは、サーバーの再起動や更新、VSS スナップショットなど、さまざまな理由によって失敗することがあります。このエラーはフォローアップが必要なように見えますが、数時間にわたって続かない限り無視してかまいません。

<a id="-2147012889"></a>**サービスとの接続を確立できませんでした。**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (10 進値)** | -2147012889 | 
| **エラー文字列** | WININET_E_NAME_NOT_RESOLVED |
| **修復が必要か** | はい |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**ユーザーの要求がサービスによってスロットルされました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (10 進値)** | -2134376372 |
| **エラー文字列** | ECS_E_USER_REQUEST_THROTTLED |
| **修復が必要か** | いいえ |

必要なアクションはありません。サーバーは再試行します。 このエラーが数時間継続して発生する場合は、サポート要求を作成してください。

<a id="-2134364043"></a>**復元後に変更検出が完了するまでは同期がブロックされます**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (10 進値)** | -2134364043 |
| **エラー文字列** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **修復が必要か** | いいえ |

必要な操作はありません。 Azure Backup を使用してファイルまたはファイル共有 (クラウド エンドポイント) が復元されるとき、Azure ファイル共有に対する変更検出が完了するまでは同期がブロックされます。 復元が完了すると変更検出がただちに実行され、その期間はファイル共有内のファイル数に基づきます。

<a id="-2147216747"></a>**同期データベースがアンロードされたため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (10 進値)** | -2147216747 |
| **エラー文字列** | SYNC_E_METADATA_INVALID_OPERATION |
| **修復が必要か** | いいえ |

このエラーは、通常、バックアップ アプリケーションによって VSS スナップショットが作成され、同期データベースがアンロードされた場合に発生します。 このエラーが数時間継続して発生する場合は、サポート要求を作成してください。

<a id="-2134364065"></a>**Sync が、クラウド エンドポイントで指定された Azure ファイル共有にアクセスできません。**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (10 進値)** | -2134364065 |
| **エラー文字列** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **修復が必要か** | はい |

このエラーは、Azure File Sync エージェントが Azure ファイル共有にアクセスできないために発生します。原因としては、Azure ファイル共有またはそれをホストしているストレージ アカウントが存在しなくなったことが考えられます。 次の手順を行うと、このエラーを解決できます。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [Azure ファイル共有が存在することを確認します。](#troubleshoot-azure-file-share)
3. [Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。](#troubleshoot-rbac)
4. [ストレージ アカウントに対するファイアウォールと仮想ネットワークの設定が適切に構成されていることを確認します (有効な場合)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**この要求にこの操作の実行権限がないために同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (10 進値)** | -2134351804 |
| **エラー文字列** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **修復が必要か** | はい |

このエラーは、Azure File Sync エージェントが Azure ファイル共有へのアクセスを承認されていないために発生します。 次の手順を行うと、このエラーを解決できます。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [Azure ファイル共有が存在することを確認します。](#troubleshoot-azure-file-share)
3. [ストレージ アカウントに対するファイアウォールと仮想ネットワークの設定が適切に構成されていることを確認します (有効な場合)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**使用されているストレージ アカウント名を解決できませんでした。**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (10 進値)** | -2134364064 |
| **エラー文字列** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **修復が必要か** | はい |

1. サーバーからストレージの DNS 名を解決できることを確認します。

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
3. [ストレージ アカウントに対するファイアウォールと仮想ネットワークの設定が適切に構成されていることを確認します (有効な場合)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**ストレージ アカウントへのアクセス中に不明なエラーが発生しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (10 進値)** | -2134364022 |
| **エラー文字列** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **修復が必要か** | はい |

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [ストレージ アカウントに対するファイアウォールと仮想ネットワークの設定が適切に構成されていることを確認します (有効な場合)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**ストレージ アカウントがロックされているため、同期できませんでした。**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (10 進値)** | -2134364014 |
| **エラー文字列** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **修復が必要か** | はい |

このエラーは、ストレージ アカウントに読み取り専用の[リソース ロック](../../azure-resource-manager/management/lock-resources.md)がある場合に発生します。 この問題を解決するには、ストレージ アカウント上の読み取り専用のリソース ロックを削除します。 

<a id="-1906441138"></a>**同期データベースの問題により、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (10 進値)** | -1906441138 |
| **エラー文字列** | JET_errWriteConflict |
| **修復が必要か** | はい |

このエラーは、Azure File Sync で使用される内部データベースに問題がある場合に発生します。この問題が発生した場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

<a id="-2134364053"></a>**サーバーにインストールされている Azure File Sync エージェントのバージョンはサポートされていません。**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (10 進値)** | -2134364053 |
| **エラー文字列** | ECS_E_AGENT_VERSION_BLOCKED |
| **修復が必要か** | はい |

このエラーは、サーバーにインストールされている Azure File Sync エージェントのバージョンがサポートされていない場合に発生します。 この問題を解決するには、[サポートされているエージェントのバージョン]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)に[アップグレード]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)します。

<a id="-2134351810"></a>**Azure ファイル共有ストレージの上限に達しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (10 進値)** | -2134351810 |
| **エラー文字列** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **修復が必要か** | はい |

このエラーは、Azure ファイル共有ストレージの上限に達したときに発生します。Azure ファイル共有にクォータが適用されている場合や、使用量が Azure ファイル共有の制限を超えた場合に発生する可能性があります。 詳細については、[Azure ファイル共有の現在の制限](storage-files-scale-targets.md)に関する記事を参照してください。

1. ストレージ同期サービス内で同期グループに移動します。
2. 同期グループ内でクラウド エンドポイントを選択します。
3. 開いているウィンドウ内の Azure ファイル共有名をメモします。
4. リンクされているストレージ アカウントを選択します。 このリンクが失敗する場合は、参照されているストレージ アカウントが削除されています。

    ![ストレージ アカウントへのリンクが表示されたクラウド エンドポイントの詳細ウィンドウを示すスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. **[ファイル]** を選択して、ファイル共有の一覧を表示します。
6. クラウド エンドポイントによって参照されている Azure ファイル共有の行の末尾にある 3 つの点をクリックします。
7. **[クォータ]** の下に **[使用量]** があることを確認します。 このクォータは、代替クォータが指定されていない限り [Azure ファイル共有の最大サイズ](storage-files-scale-targets.md)と同じになります。

    ![Azure ファイル共有のプロパティのスクリーンショット](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

共有がいっぱいでクォータが設定されていない場合、この問題を解決する 1 つの方法は、現在のサーバー エンドポイントの各サブフォルダーを、個別の専用の同期グループの専用のサーバー エンドポイントにすることです。 これにより、各サブフォルダーが個々の Azure ファイル共有に同期されるようになります。

<a id="-2134351824"></a>**Azure ファイル共有が見つかりません。**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (10 進値)** | -2134351824 |
| **エラー文字列** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **修復が必要か** | はい |

このエラーは、Azure ファイル共有にアクセスできない場合に発生します。 トラブルシューティング方法は次のとおりです。

1. [ストレージ アカウントが存在することを確認します。](#troubleshoot-storage-account)
2. [Azure ファイル共有が存在することを確認します。](#troubleshoot-azure-file-share)

Azure ファイル共有が削除されている場合は、新しいファイル共有を作成してから同期グループを再作成する必要があります。 

<a id="-2134364042"></a>**この Azure サブスクリプションが中断されている間は同期が一時停止されます。**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (10 進値)** | -2134364042 |
| **エラー文字列** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **修復が必要か** | はい |

このエラーは、Azure サブスクリプションが中断されている場合に発生します。 Azure サブスクリプションが復元されると、同期は再度有効になります。 詳細については、「[私の Azure サブスクリプションが無効になっています。その理由と、再度有効にする方法を教えてください。](../../cost-management-billing/manage/subscription-disabled.md)」を参照してください。

<a id="-2134375618"></a>**ストレージ アカウントにファイアウォールまたは仮想ネットワークが構成されています。**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (10 進値)** | -2134375618 |
| **エラー文字列** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **修復が必要か** | はい |

このエラーは、ストレージ アカウントにファイアウォールがあるか、またはストレージ アカウントが仮想ネットワークに属しているという理由により、Azure ファイル共有にアクセスできない場合に発生します。 ストレージ アカウントに対するファイアウォールと仮想ネットワークの設定が適切に構成されていることを確認します。 詳細については、「[ファイアウォールと仮想ネットワークの設定を構成する](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)」を参照してください。 

<a id="-2134375911"></a>**同期データベースの問題により、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (10 進値)** | -2134375911 |
| **エラー文字列** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **修復が必要か** | いいえ |

このエラーは通常、自動的に解決されます。これは次のような場合に発生する可能性があります。

* 同期グループ内のサーバー全体でファイルの変更数が多い。
* 個々のファイルやディレクトリでのエラーの数が多い。

このエラーが数時間を超えて続く場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

<a id="-2146762487"></a>**サーバーはセキュリティで保護された接続を確立できませんでした。クラウド サービスが予期しない証明書を受信しました。**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (10 進値)** | -2146762487 |
| **エラー文字列** | CERT_E_UNTRUSTEDROOT |
| **修復が必要か** | はい |

このエラーは、組織で TLS 終端プロキシを使用している場合、または悪意のあるエンティティがサーバーと Azure File Sync サービスの間のトラフィックをインターセプトしている場合に発生する可能性があります。 これが (組織で TLS 終端プロキシを使用しているため) 予測されることが確信できる場合は、レジストリのオーバーライドによって証明書の検証をスキップします。

1. SkipVerifyingPinnedRootCertificate レジストリ値を作成します。

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 登録済みサーバー上で同期サービスを再起動します。

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

このレジストリ値を設定すると、Azure File Sync エージェントは、サーバーとクラウド サービスの間でデータを転送するときに、ローカルに信頼される TLS/SSL 証明書をすべて受け入れます。

<a id="-2147012894"></a>**サービスとの接続を確立できませんでした。**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (10 進値)** | -2147012894 |
| **エラー文字列** | WININET_E_TIMEOUT |
| **修復が必要か** | はい |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**認証の問題により、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (10 進値)** | -2134375680 |
| **エラー文字列** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **修復が必要か** | はい |

このエラーは、通常、サーバーの時刻が正しくないことが原因で発生します。 サーバーが仮想マシンで実行されている場合は、ホストの時刻が正しいことを確認してください。

<a id="-2134364040"></a>**証明書の有効期限が切れたため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (10 進値)** | -2134364040 |
| **エラー文字列** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **修復が必要か** | はい |

このエラーは、認証に使用される証明書が期限切れであることが原因で発生します。

証明書が有効期限切れかどうか確認するには、次の手順を実行します。  
1. [証明書] MMC スナップインを開き、[コンピューター アカウント] を選択して、[証明書 (ローカル コンピューター)]\[個人]\[証明書] に移動します。
2. クライアント認証証明書の有効期限が切れていないか確認します。

クライアント認証証明書の有効期限が切れている場合は、次の手順を実行して問題を解決します。

1. Azure File Sync エージェント バージョン 4.0.1.0 以降がインストールされていることを確認します。
2. サーバーで、次の PowerShell コマンドを実行します。

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**認証証明書が見つからないため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (10 進値)** | -2134375896 |
| **エラー文字列** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **修復が必要か** | はい |

このエラーは、認証に使用される証明書が見つからないことが原因で発生します。

この問題を解決するには、次の手順を実行します。

1. Azure File Sync エージェント バージョン 4.0.1.0 以降がインストールされていることを確認します。
2. サーバーで、次の PowerShell コマンドを実行します。

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**認証 ID が見つからないため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (10 進値)** | -2134364039 |
| **エラー文字列** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **修復が必要か** | はい |

このエラーは、サーバー エンドポイントの削除が失敗し、エンドポイントが部分的に削除された状態になったことが原因で発生します。 この問題を解決するには、サーバー エンドポイントの削除を再試行します。

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**サーバー エンドポイントが配置されているボリュームのディスク領域が少なくなっています。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (10 進値)** | -1906441711 |
| **エラー文字列** | JET_errLogDiskFull |
| **修復が必要か** | はい |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (10 進値)** | -2134375654 |
| **エラー文字列** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **修復が必要か** | はい |

このエラーは、ボリュームがいっぱいになったために発生します。 このエラーは一般に、サーバー エンドポイントの外部のファイルによってボリューム上の領域が使い果たされていることが原因で発生します。 サーバー エンドポイントを追加するか、別のボリュームにファイルを移動するか、またはサーバー エンドポイントが配置されているボリュームのサイズを大きくして、ボリューム上の領域を解放します。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**サービスは、このサーバー エンドポイントと同期する準備がまだできていません。**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (10 進値)** | -2134364145 |
| **エラー文字列** | ECS_E_REPLICA_NOT_READY |
| **修復が必要か** | いいえ |

このエラーは、Azure ファイル共有に既に存在するコンテンツを使用してクラウド エンドポイントが作成されたために発生します。 Azure File Sync は、サーバー エンドポイントに初期同期の続行を許可する前に、Azure ファイル共有ですべてのコンテンツをスキャンする必要があります。

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**多数の個別ファイルに問題があるため同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (10 進値)** | -2134375877 |
| **エラー文字列** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **修復が必要か** | はい |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (10 進値)** | -2134375908 |
| **エラー文字列** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **修復が必要か** | はい |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (10 進値)** | -2134375853 |
| **エラー文字列** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **修復が必要か** | はい |

項目ごとのエラーで同期できないファイルが多数ある場合、同期セッションはこれらのエラーのいずれかで失敗します。 項目ごとのエラーを解決するには、「[同期していない特定のファイルやフォルダーがあるかどうかを確認するにはどうすればよいですか。](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)」の説明にある手順を実行してください。 同期エラー ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED については、サポート ケースを開いてください。

> [!NOTE]
> Azure File Sync は、1 日 1 回サーバー上で一時 VSS スナップショットを作成して、開くハンドルを含むファイルを同期します。

<a id="-2134376423"></a>**サーバー エンドポイントのパスの問題により、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (10 進値)** | -2134376423 |
| **エラー文字列** | ECS_E_SYNC_INVALID_PATH |
| **修復が必要か** | はい |

パスが存在し、ローカル NTFS ボリューム上にあって、再解析ポイントや既存のサーバー エンドポイントにはなっていないことを確認します。

<a id="-2134375817"></a>**フィルター ドライバーのバージョンとエージェントのバージョンに互換性がないため、同期が失敗しました**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (10 進値)** | -2134375817 |
| **エラー文字列** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **修復が必要か** | はい |

このエラーは、読み込まれたクラウド階層化フィルター ドライバー (StorageSync.sys) のバージョンと、ストレージ同期エージェント (FileSyncSvc) サービスとの間に互換性がないために発生します。 Azure File Sync エージェントがアップグレードされた場合は、サーバーを再起動してインストールを完了します。 エラーが引き続き発生する場合は、エージェントをアンインストールし、サーバーを再起動して、Azure File Sync エージェントを再インストールします。

<a id="-2134376373"></a>**サービスは現在使用できません。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (10 進値)** | -2134376373 |
| **エラー文字列** | ECS_E_SERVICE_UNAVAILABLE |
| **修復が必要か** | いいえ |

このエラーは、Azure File Sync サービスを使用できないことが原因で発生します。 このエラーは、Azure File Sync サービスが再度使用可能になると自動的に解決されます。

<a id="-2146233088"></a>**例外が発生したため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (10 進値)** | -2146233088 |
| **エラー文字列** | COR_E_EXCEPTION |
| **修復が必要か** | いいえ |

このエラーは、例外により同期が失敗したことが原因で発生します。 このエラーが数時間継続して発生する場合は、サポート要求を作成してください。

<a id="-2134364045"></a>**ストレージ アカウントが別のリージョンにフェールオーバーされたため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (10 進値)** | -2134364045 |
| **エラー文字列** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **修復が必要か** | はい |

このエラーは、ストレージ アカウントが別のリージョンにフェールオーバーされたことが原因で発生します。 Azure File Sync は、ストレージ アカウントのフェールオーバー機能をサポートしていません。 Azure File Sync でクラウド エンドポイントとして使用されている Azure ファイル共有を含むストレージ アカウントは、フェールオーバーしないでください。 それを行うと、同期の動作が停止し、新しく階層化されたファイルの場合は予期せずデータが失われる可能性があります。 この問題を解決するには、ストレージ アカウントをプライマリ リージョンに移動します。

<a id="-2134375922"></a>**同期データベースの一時的な問題により、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (10 進値)** | -2134375922 |
| **エラー文字列** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **修復が必要か** | いいえ |

このエラーは、同期データベースに関する内部的な問題が原因で発生します。 このエラーは、同期の再試行時に自動的に解決されます。 このエラーが長期間にわたって続く場合は、サポート要求を作成してください。問題解決のために Microsoft からご連絡を差し上げます。

<a id="-2134364024"></a>**Azure Active Directory テナントでの変更が原因で同期が失敗しました**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (10 進値)** | -2134364024 | 
| **エラー文字列** | ECS_E_INVALID_AAD_TENANT |
| **修復が必要か** | はい |

最新の Azure File Sync エージェントがあることを確認します。 エージェント V10 では、Azure File Sync で別の Azure Active Directory テナントへのサブスクリプションの移動がサポートされます。
 
最新のエージェント バージョンを入手したら、Microsoft.StorageSync アプリケーションにストレージ アカウントへのアクセス権を付与する必要があります (「[Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します](#troubleshoot-rbac)」を参照してください)。

<a id="-2134364010"></a>**ファイアウォールと仮想ネットワークの例外が構成されていないことが原因で同期が失敗しました**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (10 進値)** | -2134364010 | 
| **エラー文字列** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **修復が必要か** | はい |

このエラーは、ストレージ アカウントに対してファイアウォールと仮想ネットワークの設定が有効になっていて、[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] 例外のチェック ボックスがオンになっていない場合に発生します。 この問題を解決するには、デプロイ ガイドの「[ファイアウォールと仮想ネットワークの設定を構成する](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)」セクションに記載されている手順に従います。

<a id="-2147024891"></a>**システム ボリューム情報フォルダーのアクセス許可が正しくないため、同期が失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (10 進値)** | -2147024891 |
| **エラー文字列** | ERROR_ACCESS_DENIED |
| **修復が必要か** | はい |

このエラーは、サーバー エンドポイントが配置されているボリューム上のシステム ボリューム情報フォルダーへのアクセス許可が NT AUTHORITY\SYSTEM アカウントにない場合に発生する可能性があります。 個々のファイルが ERROR_ACCESS_DENIED との同期に失敗する場合は、「[ファイル/ディレクトリ単位の同期エラーのトラブルシューティング](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)」セクションに記載されている手順を実行してください。

この問題を解決するには、次の手順を実行します。

1. [Psexec](/sysinternals/downloads/psexec) ツールをダウンロードします。
2. 管理者特権でのコマンド プロンプトから次のコマンドを実行して、システム アカウントを使用してコマンド プロンプトを起動します。**PsExec.exe -i -s -d cmd** 
3. システム アカウントで実行されているコマンド プロンプトで、次のコマンドを実行して NT AUTHORITY\SYSTEM アカウントにシステム ボリューム情報フォルダーへのアクセス許可がないかどうか確認します: **cacls "drive letter:\system volume information" /T /C**
4. NT AUTHORITY\SYSTEM アカウントにシステム ボリューム情報フォルダーへのアクセス許可がない場合は、次のコマンドを実行します: **cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - アクセス拒否により手順 #4 が失敗した場合は、次のコマンドを実行して、システム ボリューム情報フォルダーの所有権を取得し、その後、手順 #4 を繰り返します: **takeown /A /R /F "drive letter:\System Volume Information"**

<a id="-2134375810"></a>**Azure ファイル共有が削除されて再作成されたため、同期に失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (10 進値)** | -2134375810 |
| **エラー文字列** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **修復が必要か** | はい |

このエラーは、Azure File Sync では同じ同期グループ内の Azure ファイル共有の削除と再作成がサポートされていないために発生します。 

この問題を解決するには、次の手順のようにして、同期グループを削除して再作成します。

1. 同期グループ内のすべてのサーバー エンドポイントを削除します。
2. クラウド エンドポイントを削除します。 
3. 同期グループを削除します。
4. サーバー エンドポイントでクラウドを使った階層化が有効になっていた場合は、「[サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)」セクションの手順を実行して、サーバー上の孤立した階層化ファイルを削除します。
5. 同期グループを作成し直します。

<a id="-2145844941"></a>**HTTP 要求がリダイレクトされたため、同期に失敗しました**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (10 進値)** | -2145844941 |
| **エラー文字列** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **修復が必要か** | はい |

このエラーは、Azure File Sync が HTTP リダイレクトをサポートしていないために発生します (3xx 状態コード)。 この問題を解決するには、プロキシ サーバーまたはネットワーク デバイスで HTTP リダイレクトを無効にします。

<a id="-2134364027"></a>**オフラインのデータ転送中にタイムアウトが発生したものの、まだ進行中である。**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (10 進値)** | -2134364027 |
| **エラー文字列** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **修復が必要か** | いいえ |

このエラーは、データ インジェスト操作のタイムアウト期間が経過したときに発生します。 同期が進行中である (AppliedItemCount が 0 より大きい) 場合、このエラーは無視してもかまいません。 「[現在の同期セッションの進行状況を監視するにはどうすればよいですか。](#how-do-i-monitor-the-progress-of-a-current-sync-session)」を参照してください。

<a id="-2134375814"></a>**サーバーでサーバー エンドポイント パスが見つからないため、同期に失敗しました。**  

| | |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (10 進値)** | -2134375814 |
| **エラー文字列** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **修復が必要か** | はい |

このエラーは、サーバー エンドポイント パスとして使用されているディレクトリの名前が変更または削除されている場合に発生します。 ディレクトリの名前が変更されている場合は、ディレクトリの名前を元の名前に戻し、ストレージ同期エージェント サービス (FileSyncSvc) を再起動します。

ディレクトリが削除されている場合は、次の手順を実行して既存のサーバー エンドポイントを削除し、新しいパスを使用して新しいサーバー エンドポイントを作成します。

1. 「[サーバー エンドポイントを削除する](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)」に記載されている手順に従って、同期グループのサーバー エンドポイントを削除します。
2. 「[サーバー エンドポイントを追加する](./storage-sync-files-server-endpoint.md#add-a-server-endpoint)」に記載されている手順に従って、同期グループに新しいサーバー エンドポイントを作成します。

### <a name="common-troubleshooting-steps"></a>一般的なトラブルシューティング手順
<a id="troubleshoot-storage-account"></a>**ストレージ アカウントが存在することを確認します。**  
# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. ストレージ同期サービス内で同期グループに移動します。
2. 同期グループ内でクラウド エンドポイントを選択します。
3. 開いているウィンドウ内の Azure ファイル共有名をメモします。
4. リンクされているストレージ アカウントを選択します。 このリンクが失敗する場合は、参照されているストレージ アカウントが削除されています。
    ![ストレージ アカウントへのリンクが表示されたクラウド エンドポイントの詳細ウィンドウを示すスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure ファイル共有が存在することを確認します。**  
# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. 左側の目次で **[概要]** をクリックして、ストレージ アカウントのメイン ページに戻ります。
2. **[ファイル]** を選択して、ファイル共有の一覧を表示します。
3. クラウド エンドポイントによって参照されているファイル共有 (上の手順 1 でメモしたもの) がファイル共有の一覧に表示されていることを確認します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。**  
# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. 左側の目次で **[アクセス制御 (IAM)]** をクリックします。
1. **[ロールの割り当て]** タブをクリックして、ストレージ アカウントにアクセスできるユーザーとアプリケーション (*サービス プリンシパル*) を一覧表示します。
1. **Microsoft.StorageSync** または **ハイブリッド ファイル同期サービス** (古いアプリケーション名) が一覧に **閲覧者とデータ アクセス** ロールで表示されるか確認します。 

    ![ストレージ アカウントのアクセス制御タブに表示された [Hybrid File Sync Service]\(ハイブリッド ファイル同期サービス\) サービス プリンシパルのスクリーンショット](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **Microsoft.StorageSync** または **ハイブリッド ファイル同期サービス** が一覧の表示されない場合は、次の手順を行います。

    - **[追加]** をクリックします。
    - **[ロール]** フィールドで、 **[閲覧者とデータ アクセス]** を選択します。
    - **[選択]** フィールドに「**Microsoft.StorageSync**」と入力してロールを選択し、 **[保存]** をクリックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>ユーザーがサーバー上でサポートされていない文字を含むファイルを作成できないようにするにはどうすればよいですか。
[ファイル サーバー リソース マネージャー (FSRM) のファイル スクリーン](/windows-server/storage/fsrm/file-screening-management)を使用すると、サーバー上でサポートされていない文字を名前に含むファイルが作成されるのをブロックできます。 サポートされていない文字の大部分は印刷不可能であるため、この作業は PowerShell を使用して行う必要があります。そのために、最初にそれらの文字の 16 進数表現を文字としてキャストする必要があります。

まず、[New-FsrmFileGroup コマンドレット](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)を使用して FSRM ファイル グループを作成します。 この例では、サポートされていない文字を 2 つだけ含むグループを定義しますが、必要な数の文字をファイル グループに含めることができます。

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

FSRM ファイル グループを定義したら、New-FsrmFileScreen コマンドレットを使用して、FSRM ファイル スクリーンを作成できます。

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> ファイル スクリーンは、Azure File Sync でサポートされない文字の作成をブロックする目的でのみ使用してください。ファイル スクリーンを他のシナリオで使用すると、同期は Azure ファイル共有からサーバーへのダウンロードを継続的に試行し、ファイル スクリーンによってブロックされます。その結果、データ エグレス値が高くなります。 

## <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化の障害パスは 2 つあります。

- ファイルを階層化できないことがあります。これは、Azure File Sync がファイルを Azure Files に階層化しようとして失敗することを意味します。
- ファイルを再呼び出しできないことがあります。これは、ユーザーが階層化されたファイルにアクセスしようとしたときに、Azure File Sync ファイル システム フィルター (StorageSync.sys) がデータのダウンロードに失敗することを意味します。

いずれかの障害パスで発生する可能性がある障害には 2 つの主要なクラスがあります。

- クラウド ストレージの障害
    - "*一時的なストレージ サービスの可用性の問題*"。 詳しくは、[Azure Storage のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) に関するページをご覧ください。
    - "*アクセスできない Azure ファイル共有*"。 この障害は通常、Azure ファイル共有がまだ同期グループ内のクラウド エンドポイントであるときにこれを削除した場合に発生します。
    - "*アクセスできないストレージ アカウント*"。 この障害は通常、同期グループ内のクラウド エンドポイントである Azure ファイル共有がまだあるときにストレージ アカウントを削除した場合に発生します。 
- サーバーの障害 
  - "*Azure File Sync ファイル システム フィルター (StorageSync.sys) が読み込まれていない*"。 階層化/再呼び出し要求に応答するためには、Azure File Sync ファイル システム フィルターが読み込まれている必要があります。 フィルターが読み込まれない理由はいくつかありますが、最も一般的な理由は、管理者が手動でアンロードしたことです。 Azure File Sync が正常に機能するためには、Azure File Sync ファイル システム フィルターが常に読み込まれている必要があります。
  - "*不足、破損、切断している再解析ポイント*"。 再解析ポイントは、ファイルの特別なデータ構造であり、次の 2 つの部分で構成されています。
    1. Azure File Sync ファイル システム フィルター (StorageSync.sys) がファイルへの IO になんらかのアクションを行う必要があることをオペレーティング システムに示す再解析タグ。 
    2. 関連付けられているクラウド エンドポイント (Azure ファイル共有) 上のファイルの URI をファイル システムがフィルターすることを示す再解析データ。 
        
       再解析ポイントが破損する最も一般的な原因は、管理者がタグまたはそのデータを変更しようとしたことです。 
  - "*ネットワーク接続性の問題*"。 ファイルを階層化または再呼び出しするには、サーバーにインターネット接続が必要です。

次の各セクションでは、クラウド階層化の問題のトラブルシューティングを行い、問題がクラウド ストレージの問題かサーバーの問題かを確認する方法を示します。

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>サーバー上の階層化アクティビティを監視する方法  
サーバー上の階層化アクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下) にあるイベント ID 9003、9016、9029 を使用します。

- イベント ID 9003 は、サーバー エンドポイントのエラー分布を示します。 たとえば、Total Error Count、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。
- イベント ID 9016 は、ボリュームの非実体化の結果を示します。 たとえば、空き領域の割合、セッション内の非実体化されたファイルの数、非実体化が失敗したファイルの数などです。
- イベント ID 9029 では、サーバー エンドポイントの非実体化セッション情報が提供されます。 たとえば、セッション内の試行されたファイルの数、セッション内の階層化されたファイルの数、既に階層化されているファイルの数などです。

### <a name="how-to-monitor-recall-activity-on-a-server"></a>サーバー上の呼び戻しアクティビティを監視する方法
サーバー上の呼び戻しアクティビティを監視するには、テレメトリ イベント ログ (イベント ビューアーの [アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下) にあるイベント ID 9005、9006、9009、9059 を使用します。

- イベント ID 9005 は、サーバー エンドポイントの呼び戻しの信頼性を示します。 たとえば、アクセスされた一意のファイルの合計数、アクセスが失敗した一意のファイルの合計数などです。
- イベント ID 9006 は、サーバー エンドポイントの呼び戻しエラーの分布を示します。 たとえば、失敗した要求の合計数、ErrorCode などです。エラー コードごとに 1 つのイベントがログ記録されます。
- イベント ID 9009 では、サーバー エンドポイントの呼び戻しセッション情報が提供されます。 たとえば、DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed などです。
- イベント ID 9059 では、サーバー エンドポイントのアプリケーション呼び戻し分布が提供されます。 たとえば、ShareId、Application Name、TotalEgressNetworkBytes です。

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>階層化に失敗するファイルのトラブルシューティングを行う方法
ファイルが Azure ファイルへの階層化に失敗する場合:

1. イベント ビューアーで、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下にあるテレメトリ ログ、操作イベント ログ、および診断イベント ログをレビューします。 
   1. ファイルが Azure ファイル共有に存在することを確認します。

      > [!NOTE]
      > 階層化の前に、ファイルが Azure ファイル共有と同期されている必要があります。

   2. サーバーがインターネットに接続できることを確認します。 
   3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
       - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

> [!NOTE]
> イベント ID 9003 は、ファイルの階層化が失敗した場合に、テレメトリ イベント ログに 1 時間に 1 回ログ記録されます (エラー コードごとに 1 つのイベントがログ記録されます)。 「[階層化エラーと修復](#tiering-errors-and-remediation)」セクションを参照して、エラー コードに対する修復手順が示されているかどうかを確認します。

### <a name="tiering-errors-and-remediation"></a>階層化エラーと修復

| HRESULT | HRESULT (10 進値) | エラー文字列 | 問題 | Remediation |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | 初期アップロードが進行中のため、ファイルを階層化できませんでした。 | 必要なアクションはありません。 初期アップロードが完了したら、ファイルは階層化されます。 |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | ファイルは使用中のため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは使用されなくなると階層化されます。 |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | ファイルは同期によって除外されているため、階層化できませんでした。 | 必要なアクションはありません。 同期除外リスト内のファイルは階層化できません。 |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | ファイルはサーバー上で見つからなかったため、階層化できませんでした。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、ファイルがサーバー上に存在するかどうかを確認します。 |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | ファイルは Azure ファイル共有で削除されたため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、次のダウンロード同期セッションが実行されたときにサーバー上で削除されるはずです。 |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | ネットワークの問題が発生したため、ファイルを階層化できませんでした。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、Azure ファイル共有へのネットワーク接続を確認します。 |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | ネットワークの問題が発生したため、ファイルを階層化できませんでした。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、Azure ファイル共有へのネットワーク接続を確認します。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | アクセス拒否エラーが発生したため、ファイルを階層化できませんでした。 このエラーは、ファイルが DFS-R 読み取り専用レプリケーション フォルダーにある場合に発生することがあります。 | Azure Files Sync は DFS-R 読み取り専用レプリケーション フォルダーにおけるサーバー エンドポイントをサポートしません。 詳細については、[計画ガイド](./storage-sync-files-planning.md#distributed-file-system-dfs)を参照してください。 |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | ネットワークの問題が発生したため、ファイルを階層化できませんでした。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、Azure ファイル共有へのネットワーク接続を確認します。 |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | ファイルのサイズがサポートされているサイズを下回っているため、ファイルを階層化できませんでした。 | エージェントのバージョンが 9.0 未満の場合、サポートされるファイルの最小サイズは 64 KB です。 エージェントのバージョンが 9.0 以降の場合、サポートされるファイルの最小サイズは、ファイル システムのクラスター サイズ (ファイル システム クラスターのサイズの倍) に基づきます。 たとえば、ファイル システム クラスターのサイズが 4 KB の場合、ファイルの最小サイズは 8 KB です。 |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Azure Storage の問題が発生したため、ファイルを階層化できませんでした。 | エラーが解決しない場合は、サポート リクエストを開いてください。 |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | ファイルは同時に呼び戻しされたため、階層化できませんでした。 | 必要なアクションはありません。 このファイルは、呼び戻しが完了し、ファイルが使用中でなくなると階層化されます。 |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | ファイルは Azure ファイル共有と同期されていないため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、Azure ファイル共有と同期されると階層化されます。 |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | クラウドの階層化フィルター ドライバー (storagesync.sys) が実行されていないため、ファイルを階層化できませんでした。 | この問題を解決するには、管理者特権でコマンド プロンプトを開き、次のコマンドを実行します: `fltmc load storagesync`<br>fltmc コマンドの実行時に storagesync フィルター ドライバーの読み込みが失敗した場合は、Azure File Sync エージェントをアンインストールし、サーバーを再起動して、Azure File Sync エージェントを再インストールします。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | サーバー エンドポイントが配置されているボリュームのディスク領域が不足しているため、ファイルを階層化できませんでした。 | この問題を解決するには、サーバー エンドポイントが配置されているボリューム上で、少なくとも 100 MB のディスク領域を解放します。 |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | ファイルは Azure ファイル共有と同期されていないため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、Azure ファイル共有と同期されると階層化されます。 |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | ファイルはサポートされていない再解析ポイントであるため、階層化できませんでした。 | ファイルがデータ重複除去の再解析ポイントである場合は、[計画ガイド](./storage-sync-files-planning.md#data-deduplication)の手順に従って、データ重複除去のサポートを有効にします。 データ重複除去以外の再解析ポイントを含むファイルはサポートされておらず、階層化されません。  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | ファイルは変更されているため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、変更されたファイルが Azure ファイル共有と同期されると階層化されます。 |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | ファイルは Azure ファイル共有と同期されていないため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、Azure ファイル共有と同期されると階層化されます。 |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | ネットワークの問題が発生したため、ファイルを階層化できませんでした。 | 必要なアクションはありません。 エラーが引き続き発生する場合は、Azure ファイル共有へのネットワーク接続を確認します。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | ファイルは変更されているため、階層化できませんでした。 | 必要なアクションはありません。 ファイルは、変更されたファイルが Azure ファイル共有と同期されると階層化されます。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | システム リソースが不足しているため、ファイルを階層化できませんでした。 | エラーが引き続き発生する場合は、どのアプリケーションまたはカーネル モード ドライバーがシステム リソースを消費しているかを調べます。 |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | クラウドを使った階層化のデータベースへの書き込み中に I/O エラーが発生したため、ファイルを階層化できませんでした。 | エラーが引き続き発生する場合は、ボリュームで chkdsk を実行して、ストレージのハードウェアを確認してください。 |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | クラウドを使った階層化のデータベースが実行されていないため、ファイルを階層化できませんでした。 | この問題を解決するには、FileSyncSvc サービスまたはサーバーを再起動します。 エラーが引き続き発生する場合は、ボリュームで chkdsk を実行して、ストレージのハードウェアを確認してください。 |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>再呼び出しに失敗するファイルのトラブルシューティングを行う方法  
ファイルが再呼び出しに失敗する場合:
1. イベント ビューアーで、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下にあるテレメトリ ログ、操作イベント ログ、および診断イベント ログをレビューします。
    1. ファイルが Azure ファイル共有に存在することを確認します。
    2. サーバーがインターネットに接続できることを確認します。 
    3. サービス MMC スナップインを開き、Storage 同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。
    4. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
        - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

> [!NOTE]
> イベント ID 9006 は、ファイルの呼び戻しが失敗した場合に、テレメトリ イベント ログに 1 時間に 1 回ログ記録されます (エラー コードごとに 1 つのイベントがログ記録されます)。 「[呼び戻しエラーと修復](#recall-errors-and-remediation)」セクションを確認して、エラー コードに対する修復手順が一覧表示されているかどうかを確認します。

### <a name="recall-errors-and-remediation"></a>呼び戻しエラーと修復

| HRESULT | HRESULT (10 進値) | エラー文字列 | 問題 | Remediation |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | I/O タイムアウトのため、ファイルの呼び戻しに失敗しました。 この問題は、サーバー リソースの制約、ネットワーク接続の低下、または Azure Storage の問題 (調整など) といったいくつかの理由によって発生する可能性があります。 | 必要なアクションはありません。 エラーが数時間継続して発生する場合は、サポート ケースを開いてください。 |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | ネットワークの問題が発生したため、ファイルの呼び戻しに失敗しました。  | エラーが引き続き発生する場合は、Azure ファイル共有へのネットワーク接続を確認します。 |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | サーバー エンドポイントが削除されたため、ファイルの呼び戻しに失敗しました。 | この問題を解決する方法は、「[サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)」を参照してください。 |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | アクセス拒否エラーが発生したため、ファイルの呼び戻しに失敗しました。 この問題は、ストレージ アカウントでファイアウォールと仮想ネットワークの設定が有効になっていて、サーバーにそのストレージ アカウントへのアクセス権がない場合に発生します。 | この問題を解決するには、デプロイ ガイドの「[ファイアウォールと仮想ネットワークの設定を構成する](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)」セクションに記載されている手順に従い、サーバー IP アドレスまたは仮想ネットワークを追加します。 |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Azure ファイル共有にアクセスできないため、ファイルの呼び戻しに失敗しました。 | この問題を解決するには、ファイルが Azure ファイル共有に存在することを確認します。 ファイルが Azure ファイル共有に存在する場合は、最新の Azure File Sync [エージェントのバージョン](./storage-files-release-notes.md#supported-versions)にアップグレードします。 |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | ストレージ アカウントへの承認エラーが発生したため、ファイルの呼び戻しに失敗しました。 | この問題を解決するには、[Azure File Sync にストレージ アカウントへのアクセス権がある](?tabs=portal1%252cazure-portal#troubleshoot-rbac)ことを確認します。 |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Azure ファイル共有にアクセスできないため、ファイルの呼び戻しに失敗しました。 | ファイル共有が存在し、アクセス可能であることを確認します。 ファイル共有が削除され、再作成された場合は、「[Azure ファイル共有が削除されて再作成されたため、同期に失敗しました](?tabs=portal1%252cazure-portal#-2134375810)」セクションに記載されている手順を行い、同期グループを削除して再作成します。 |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | システム リソースが不足しているため、ファイルの呼び戻しに失敗しました。 | エラーが引き続き発生する場合は、どのアプリケーションまたはカーネル モード ドライバーがシステム リソースを消費しているかを調べます。 |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | メモリが不足しているため、ファイルの呼び戻しに失敗しました。 | エラーが引き続き発生する場合は、メモリ不足の原因になっているアプリケーションまたはカーネル モード ドライバーを調べます。 |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | ディスク領域が不足しているため、ファイルの呼び戻しに失敗しました。 | この問題を解決するには、ファイルを別のボリュームに移動することでボリュームの領域を解放するか、ボリュームのサイズを増やすか、または Invoke-StorageSyncCloudTiering コマンドレットを使用することでファイルを強制的に階層化します。 |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>サーバー エンドポイントを削除した後、サーバー上で階層化されたファイルにアクセスできない
サーバーのエンドポイントを削除する前にファイルが再呼び出しされない場合、サーバー上の階層化ファイルはアクセスできなくなります。

階層化されたファイルにアクセスできない場合にログに記録されるエラー
- ファイルを同期すると、エラー コード -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) が ItemResults イベント ログに記録されます。
- ファイルを再呼び出しすると、エラー コード -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) が RecallResults イベント ログに記録されます。

次の条件が満たされている場合、階層化されたファイルへのアクセスを復元できます。
- 過去 30 日以内にサーバー エンドポイントが削除されました
- クラウド エンドポイントは削除されませんでした 
- ファイル共有は削除されませんでした
- 同期グループは削除されませんでした

上記の条件が満たされる場合、30 日以内に同じ同期グループ内のサーバー上の同じパスにサーバー エンドポイントを再作成することで、サーバー上のファイルへのアクセスを復元できます。 

上記の条件が満たされない場合、サーバー上の階層化されたファイルが孤立しているため、アクセスを復元することはできません。 次の手順に従って、孤立した階層化ファイルを削除します。

**メモ**
- 階層化されたファイルにサーバーでアクセスできない場合でも、Azure ファイル共有に直接アクセスするなら、完全なファイルにアクセスできます。
- 今後、階層化されたファイルの孤立を防ぐには、サーバーエンド ポイントを削除するとき、「[サーバー エンドポイントを削除する](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)」に記載されている手順に従ってください。

<a id="get-orphaned"></a>**孤立した階層化ファイルの一覧を取得する方法** 

1. Azure File Sync エージェント バージョン v5.1 以降がインストールされていることを確認します。
2. 次の PowerShell コマンドを実行すると、孤立した階層化ファイルが一覧表示されます。
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. ファイルの削除後、バックアップから復元する必要がある場合、OrphanTieredFiles.txt 出力ファイルを保存してください。

<a id="remove-orphaned"></a>**孤立した階層化ファイルを削除する方法** 

*オプション 1: 孤立した階層化ファイルを削除する*

このオプションでは、Windows Server 上で孤立している階層化ファイルが削除されますが、サーバー エンドポイントが 30 日後の再作成に起因して存在する場合、あるいは異なる同期グループに接続されている場合、サーバー エンドポイントを削除する必要があります。 サーバー エンドポイントが再作成される前に Windows Server または Azure ファイル共有でファイルが更新された場合、ファイル競合が発生します。

1. Azure File Sync エージェント バージョン v5.1 以降がインストールされていることを確認します。
2. Azure ファイル共有とサーバーエンド ポイントの場所をバックアップします。
3. 同期グループにサーバー エンドポイントが存在する場合、「[サーバー エンドポイントを削除する](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)」に記載されている手順でそれを削除します。

> [!Warning]  
> Remove-StorageSyncOrphanedTieredFiles コマンドレットを使用する前にサーバー エンドポイントが削除されていない場合、サーバー上で孤立している階層化ファイルを削除すると、Azure ファイル共有で完全なファイルが削除されます。 

4. 次の PowerShell コマンドを実行すると、孤立した階層化ファイルが一覧表示されます。

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. ファイルの削除後、バックアップから復元する必要がある場合、OrphanTieredFiles.txt 出力ファイルを保存してください。
6. 次の PowerShell コマンドを実行すると、孤立した階層化ファイルが削除されます。

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**メモ** 
- サーバー上で変更された階層化ファイルが Azure ファイル共有と同期していない場合、ファイルは削除されます。
- アクセス可能な (孤立していない) 階層化ファイルは削除されません。
- 階層化されていないファイルはサーバーに残ります。

7. 省略可能:手順 3 で削除した場合、サーバー エンドポイントを再作成します。

*オプション 2: Azure ファイル共有をマウントし、サーバー上で孤立しているファイルをローカルにコピーする*

このオプションでは、サーバー エンドポイントを削除する必要がありませんが、完全なファイルをローカルにコピーするために十分なディスク領域が必要になります。

1. Windows Server に、階層化ファイルが孤立した Azure ファイル共有を[マウント](./storage-how-to-use-files-windows.md)します。
2. 次の PowerShell コマンドを実行すると、孤立した階層化ファイルが一覧表示されます。
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. OrphanTieredFiles.txt 出力ファイルを使用し、サーバー上で孤立している階層化ファイルを特定します。
4. Azure ファイル共有から Windows Server に完全なファイルをコピーすることで、孤立した階層化ファイルを上書きします。

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>サーバーで予期せず呼び戻されるファイルのトラブルシューティング方法  
ウイルス対策、バックアップ、および多数のファイルを読み取るその他のアプリケーションは、オフライン スキップ属性を尊重してそれらのファイルのコンテンツの読み取りをスキップする場合を除いて、意図しない再呼び出しの原因となります。 オフライン ファイルをスキップするオプションをサポートしている製品でスキップを実行すると、ウィルス対策スキャンやバックアップ ジョブなどの操作中の意図しない再呼び出しを回避できます。

オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法については、ソフトウェア ベンダーにお問い合わせください。

意図しない呼び出しは、ファイル エクスプローラーでファイルを参照する場合などの他のシナリオで発生することもあります。 サーバー上でクラウド階層化されたファイルがあるフォルダーをファイル エクスプローラーで開くと、意図しない再呼び出しが発生することがあります。 ウイルス対策ソリューションがサーバーで有効になっている場合よりも、こちらのほうが発生する可能性が高まります。

> [!NOTE]
>テレメトリ イベント ログでイベント ID 9059 を使用して、呼び戻しの原因となっているアプリケーションを特定します。 このイベントは、サーバー エンドポイントのアプリケーション呼び戻し分布を提供し、1 時間に 1 回ログに記録されます。

### <a name="tls-12-required-for-azure-file-sync"></a>Azure File Sync には TLS 1.2 が必要

サーバーで TLS 設定を表示するには、[レジストリ設定](/windows-server/security/tls/tls-registry-settings)を参照します。 

プロキシを使用している場合は、プロキシのドキュメントを参照し、TLS 1.2 を使用するように構成されていることを確実にします。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング
サーバーで Azure File Sync の問題が発生する場合は、次の手順を完了します。
1. イベント ビューアーで、テレメトリ イベント ログ、操作イベント ログ、および診断イベント ログをレビューします。
    - 同期、階層化、および再呼び出しの問題は、[アプリケーションとサービス]\[Microsoft]\[FileSync]\[Agent] の下のテレメトリ イベント ログ、診断イベント ログ、および操作イベント ログに記録されます。
    - サーバーの管理 (構成設定など) に関連する問題は、Applications と Services\Microsoft\FileSync\Management の下の診断イベント ログと操作イベント ログに記録されます。
2. Azure File Sync サービスがサーバーで実行されていることを確認します。
    - サービスの MMC スナップインを開き、ストレージ同期エージェント サービス (FileSyncSvc) が実行されていることを確認します。
3. Azure File Sync フィルター ドライバー (StorageSync.sys と StorageSyncGuard.sys) が実行されていることを確認します。
    - 管理者特権でのコマンド プロンプトで、`fltmc` を実行します。 ファイル システム フィルター ドライバーの StorageSync.sys と StorageSyncGuard.sys が表示されることを確認します。

問題が解決されない場合は、AFSDiag ツールを実行し、その .zip ファイルの出力を、ケースに割り当てられたサポート エンジニアに送信して、さらに詳しい診断を依頼します。

AFSDiag を実行するには、次の手順に従います。

エージェント バージョン v11 以降の場合:
1. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    > [!NOTE]
    >AFSDiag はログを収集する前に、出力ディレクトリとその中の一時フォルダーを作成し、実行後に一時フォルダーを削除します。 データが含まれていない出力場所を指定してください。
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. 問題を再現します。 操作が終了したら、 **[D]** を入力します。
3. ログファイルとトレース ファイルを含む .zip ファイルが、指定した出力ディレクトリに保存されます。 

エージェント バージョン v10 以前の場合:
1. AFSDiag の出力が保存されるディレクトリを作成します (例: C:\Output)。
    > [!NOTE]
    >AFSDiag では、ログが収集される前に、出力ディレクトリ内のすべてのコンテンツが削除されます。 データが含まれていない出力場所を指定してください。
2. 管理者特権で PowerShell ウィンドウを開き、次のコマンドを実行します (各コマンドの後で Enter キーを押します)。

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure File Sync のカーネル モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
4. Azure File Sync のユーザー モード トレース レベルには、(より詳細なトレースの作成を指定しない限り) **1** を入力し、Enter キーを押します。
5. 問題を再現します。 操作が終了したら、 **[D]** を入力します。
6. ログファイルとトレース ファイルを含む .zip ファイルが、指定した出力ディレクトリに保存されます。


## <a name="see-also"></a>関連項目
- [Azure File Sync の監視](storage-sync-files-monitoring.md)
- [Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md)
- [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
