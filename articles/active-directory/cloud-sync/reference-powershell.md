---
title: Azure AD Connect クラウド同期用 AADCloudSyncTools PowerShell モジュール
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 486a1c6c8103db8dc938a956eb1f77da3f15f49c
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612589"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期用 AADCloudSyncTools PowerShell モジュール

パブリック プレビュー更新 2 のリリースでは、Microsoft によって AADCloudSyncTools PowerShell モジュールが導入されました。  このモジュールには、Azure AD Connect クラウド同期のデプロイを管理するために使用できる便利な一連のツールが用意されています。

## <a name="pre-requisites"></a>前提条件
次の前提条件が必要です。
- このモジュールでは MSAL 認証が使用されるため、MSAL.PS モジュールがインストールされている必要があります。 Azure AD または Azure AD Preview に依存しなくなりました。   確認するには、管理者用 PowerShell ウィンドウで `Get-module MSAL.PS` を実行します。 モジュールが正しくインストールされている場合は、応答が返されます。  `Install-AADCloudSyncToolsPrerequisites` を使用して、MSAL.PS の最新バージョンをインストールできます
- AzureAD PowerShell モジュール。  一部のコマンドレットは、タスクを実行するために AzureAD PowerShell モジュールの一部に依存しています。  確認するには、管理者用 PowerShell ウィンドウで `Get-module AzureAD` を実行します。 応答が返されるはずです。  `Install-AADCloudSyncToolsPrerequisites` を使用して、AzureAD PowerShell モジュールの最新バージョンをインストールできます。
- PowerShell からモジュールをインストールすると、TLS 1.2 の使用が強制される場合があります。  モジュールを確実にインストールできるようにするには、次を設定します: \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>AADCloudSyncTools PowerShell モジュールをインストールする
AADCloudSyncTools モジュールをインストールして使用するには、次の手順を実行します。

1.  管理者特権で Windows PowerShell を開きます
2.  「`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`」と入力して Enter キーを押します。
3.  次を入力するか、コピーして貼り付けます。 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Enter キーを押します。
4.  モジュールがインストールされたことを確認するには、次のように入力するか、コピーして貼り付けます。
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  モジュールに関する情報が表示されます。
6.  次に、以下を実行します
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  これにより、PowerShell Get モジュールがインストールされます。  PowerShell ウィンドウを閉じます。
8.  管理者特権で Windows PowerShell を開きます
9.  手順 3 を使用してモジュールを再度インポートします。
10. `Install-AADCloudSyncToolsPrerequisites` を実行して MSAL および AzureAD モジュールをインストールします
11. すべての前提要件が正常にインストールされているはずです ![モジュールのインストール](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools コマンドレット
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
AzureAD モジュールを使用して Azure AD に接続し、Microsoft Graph のトークンを要求するために MSAL.PS モジュールを使用します


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
次のように、圧縮ファイルにすべてのトラブルシューティング データをエクスポートしてパッケージ化します。
 1. Start-AADCloudSyncToolsVerboseLogs によって詳細トレースが開始されます。  これらのトレース ログは、C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace フォルダーにあります。
 2. トレース ログが 3 分間収集されます。
   -TracingDurationMins を使用して別の時間を指定するか、-SkipVerboseTrace を使用して詳細トレースをスキップすることができます
 3. Stop-AADCloudSyncToolsVerboseLogs によって詳細トレースが停止されます
 4. 過去 24 時間のイベント ビューアー ログが収集されます
 5. すべてのエージェント ログ、詳細ログ、およびイベント ビューアー ログが、圧縮された .zip ファイルとしてユーザーのドキュメント フォルダーに圧縮されます。 
 </br>-OutputPath \<folder path\> を使用して別の出力フォルダーを指定することもできます

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Azure AD テナントの詳細と内部変数の状態を表示します

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブの情報を返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのスケジュールを返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのスキーマを返します。

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Graph を使用して指定された同期ジョブ ID の同期ジョブのスキーマを取得し、すべてのフィルター グループのスコープを出力します。

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブの設定を返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Graph を使用して AD2AAD サービス プリンシパルを取得し、同期ジョブのステータスを返します。
これは、特定の同期ジョブ ID をパラメーターとして使用して呼び出すこともできます。

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Graph を使用して AD2AAD または SyncFabric のサービス プリンシパルを取得します。
パラメーターを指定しない場合、AD2AAD サービス プリンシパルだけが返されます。

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
PowerShellGet v2.2.4.1 以降、および Azure AD と MSAL.PS モジュールが存在するかどうかを確認し、無い場合はインストールします。

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
パラメーターとして指定された URI、メソッド、および本文の Web 要求を呼び出します

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Azure AD PowerShell を使用して現在のアカウント (存在する場合) を削除し、Azure AD の同期アカウント認証を新しい同期アカウントでリセットします。

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
完全同期を再開します。

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
前のウォーターマークから同期を続行します。

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
'AADConnectProvisioningAgent.exe.config' を変更して詳細トレースを有効にし、AADConnectProvisioningAgent サービスを再起動します。-SkipServiceRestart を使用してサービスの再起動を阻止することはできますが、構成の変更は有効になりません。  これらのトレース ログは、C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace フォルダーにあります。

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
'AADConnectProvisioningAgent.exe.config' を変更して詳細トレースを無効にし、AADConnectProvisioningAgent サービスを再起動します。 -SkipServiceRestart を使用してサービスの再起動を阻止することはできますが、構成の変更は有効になりません。

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
同期を一時停止します。

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)

