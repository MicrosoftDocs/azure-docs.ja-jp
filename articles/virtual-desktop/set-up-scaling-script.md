---
title: Windows Virtual Desktop プレビューのセッション ホストの自動スケール - Azure
description: Windows Virtual Desktop プレビューのセッション ホストの自動スケール スクリプトを設定する方法について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 379e73c33aa4570c3e56f902b011d75944c94a8d
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497899"
---
# <a name="automatically-scale-session-hosts"></a>セッション ホストを自動的にスケーリングする

Azure に多くの Windows Virtual Desktop プレビューをデプロイする場合、仮想マシンのコストは、Windows Virtual Desktop 展開の合計コストの大部分を占めます。 コストを削減するには、ピーク使用時間外にセッション ホスト仮想マシン (VM) をシャットダウンしてセッションの割り当てを解除し、ピーク使用時間中に再起動することをお勧めします。

この記事では、単純なスケーリング スクリプトを使用して、Windows Virtual Desktop 環境内のセッション ホスト仮想マシンを自動的にスケーリングします。 スケーリング スクリプトのしくみについて詳しくは、「[スケーリング スクリプトのしくみ](#how-the-scaling-script-works)」のセクションをご覧ください。

## <a name="prerequisites"></a>前提条件

スクリプトを実行する環境には、以下のものが必要です。

- Windows Virtual Desktop のテナントとアカウント、またはそのテナントのクエリを実行するためのアクセス許可を持つサービス プリンシパル (RDS 共同作成者など)。
- 構成されて Windows Virtual Desktop サービスに登録されたセッション ホスト プール VM。
- タスク スケジュールを介してスケジュールされたタスクを実行し、セッション ホストへのネットワークのアクセス権を持つ追加のスケーラー VM。
- スケジュールされたタスクを実行している VM にインストールされている Microsoft Azure Resource Manager PowerShell モジュール。
- スケジュールされたタスクを実行している VM にインストールされている Windows Virtual Desktop PowerShell モジュール。

## <a name="recommendations-and-limitations"></a>推奨事項と制限事項

スケーリング スクリプトを実行するときには、次の点にご注意ください。

- このスケーリング スクリプトでは、スケーリング スクリプトを実行しているスケジュールされたタスクのインスタンスごとに 1 つのホスト プールだけを処理できます。
- スケーリング スクリプトを実行するスケジュールされたタスクは、常に電源オンになっている VM 上にある必要があります。
- スケーリング スクリプトとその構成のインスタンスごとに別々のフォルダーを作成します。
- このスクリプトでは、多要素認証を使用するアカウントはサポートされません。 Windows Virtual Desktop サービスと Azure へのアクセスにはサービス プリンシパルを使用することをお勧めします。
- Azure の SLA 保証は、可用性セット内の VM にのみ適用されます。 ドキュメントの現在のバージョンでは、スケーリングを行う 1 台の VM がある環境について説明しています。これは可用性の要件を満たしていない可能性があります。

## <a name="deploy-the-scaling-script"></a>スケーリング スクリプトのデプロイ

次の手順では、スケーリング スクリプトをデプロイする方法を示します。

### <a name="prepare-your-environment-for-the-scaling-script"></a>スケーリング スクリプト用の環境の準備

まず、スケーリング スクリプト用の環境を準備します。

1. ドメイン管理者のアカウントを使用して、スケジュールされたタスクを実行する VM (**スケーリング VM**) にサインインします。
2. スケーリング VM 上に、スケーリング スクリプトとその構成を保持するフォルダーを作成します (たとえば、**C:\\scaling-HostPool1**)。
3. **basicScaler.ps1**、**Config.xml**、**Functions-PSStoredCredentials.ps1** ファイル、**PowershellModules** フォルダーを[スケーリング スクリプト リポジトリ](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script)からダウンロードし、手順 2. で作成したフォルダーにコピーします。

### <a name="create-securely-stored-credentials"></a>安全に保存された資格情報の作成

次に、安全に保存された資格情報を作成する必要があります。

1. PowerShell ISE を管理者として開きます。
2. 編集ウィンドウを開き、**Function-PSStoredCredentials.ps1** ファイルを読み込みます。
3. 次のコマンドレットを実行します。
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    たとえば、**Set-Variable -Name KeyPath -Scope Global -Value "c:\\scaling-HostPool1"** などです
4. **New-StoredCredential -KeyPath \$KeyPath** コマンドレットを実行します。 入力を求められたら、ホスト プール (ホスト プールは **config.xml** で指定されます) のクエリを実行するためのアクセス許可を持つ Windows Virtual Desktop 資格情報を入力します。
    - 別のサービス プリンシパルまたは標準アカウントを使用する場合は、アカウントごとに 1 回ずつ **New-StoredCredential -KeyPath \$KeyPath** コマンドレットを実行して、ローカルに格納された資格情報を作成します。
5. **Get-StoredCredentials -List** を実行して、資格情報が正常に作成されたことを確認します。

### <a name="configure-the-configxml-file"></a>config.xml ファイルの構成

次のフィールドに妥当な値を入力して、config.xml 内のスケーリング スクリプト設定を更新します。

| フィールド                     | 説明                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | セッション ホスト VM を実行しているサブスクリプションを関連付ける Azure AD テナント ID     |
| AADApplicationId              | サービス プリンシパルのアプリケーション ID                                                       |
| AADServicePrincipalSecret     | これはテスト フェーズ中に入力できますが、**Functions-PSStoredCredentials.ps1** を使用して資格情報を作成した後は空のままにします    |
| currentAzureSubscriptionId    | セッション ホスト VM が実行される Azure サブスクリプションの ID                        |
| tenantName                    | Windows Virtual Desktop のテナント名                                                    |
| hostPoolName                  | Windows Virtual Desktop のホスト プール名                                                 |
| RDBroker                      | WVD サービスの URL。既定値は https:\//rdbroker.wvd.microsoft.com             |
| ユーザー名                      | サービス プリンシパル アプリケーション ID (AADApplicationId の場合と同じサービス プリンシパルを持つことができます) または多要素認証なしの標準ユーザー |
| isServicePrincipal            | 使用可能な値は **true** または **false** です。 使用されている 2 セット目の資格情報がサービス プリンシパルと標準アカウントのどちらであるかを示します。 |
| BeginPeakTime                 | ピーク使用時間の開始時刻                                                            |
| EndPeakTime                   | ピーク使用時間の終了時刻                                                              |
| TimeDifferenceInHours         | 現地時刻と UTC の時間差 (時間単位)                                   |
| SessionThresholdPerCPU        | 新しい RDSH サーバーをピーク時間中に開始する必要がある場合に、それを判断するために使用される CPU あたりの最大セッション数のしきい値。  |
| MinimumNumberOfRDSH           | ピーク使用時間外中も実行を続けるホスト プール VM の最小数             |
| LimitSecondsToForceLogOffUser | ユーザーにサインアウトを強制する前に待機する秒数。0 に設定されている場合、ユーザーは強制的にサインアウトされません。  |
| LogOffMessageTitle            | サインアウトを強制する前にユーザーに送信されるメッセージのタイトル                  |
| LogOffMessageBody             | サインアウトの前にユーザーに送信される警告メッセージの本文。たとえば、"このマシンは X 分後にシャットダウンされます。 作業内容を保存してサインアウトしてください。" |

### <a name="configure-the-task-scheduler"></a>タスク スケジューラの構成

構成 .xml ファイルを構成した後、一定の間隔で basicScaler.ps1 ファイルを実行するようにタスク スケジューラを構成する必要があります。

1. **タスク スケジューラ**を起動します。
2. **[タスク スケジューラ]** ウィンドウで、**[タスクの作成]** を選択します
3. **[タスクの作成]** ダイアログで、**[全般]** タブを選択し、**[名前]** (たとえば、"Dynamic RDSH") を入力して、**[ユーザーがログオンしているかどうかにかかわらず実行する]** と **[最上位の特権で実行する]** を選択します。
4. **[トリガー]** タブに移動し、**[新規]** を選択します
5. **[新しいトリガー]** ダイアログの **[詳細設定]** で、**[繰り返し間隔]** をオンにし、適切な期間と継続時間 (たとえば、**[15 分]** または **[無期限]**) を選択します。
6. **[アクション]** タブと **[新規]** を選択します
7. **[新しいアクション]** ダイアログで、**powershell.exe** を **[プログラム/スクリプト]** フィールドに入力し、**C:\\scaling\\RDSScaler.ps1** を **[引数の追加 (オプション)]** フィールドに入力します。
8. **[条件]** タブと **[設定]** タブに移動し、**[OK]** を選択してそれぞれの既定の設定を受け入れます。
9. スケーリング スクリプトの実行を計画している管理アカウントのパスワードを入力します。

## <a name="how-the-scaling-script-works"></a>スケーリング スクリプトのしくみ

このスケーリング スクリプトでは、config.xml ファイルから、その日のピーク使用期間の開始と終了などの設定を読み取ります。

ピーク使用時間中、スクリプトでは、各コレクションの現在のセッション数と現在実行中の RDSH 容量が確認されます。 実行中の RDSH サーバーに、config.xml ファイルで定義されている SessionThresholdPerCPU パラメーターに基づいて既存のセッションをサポートするために十分な容量があるかどうかが計算されます。 ない場合は、スクリプトによってコレクション内の追加の RDSH サーバーが起動されます。

ピーク使用時間外中、スクリプトでは、config.xml ファイル内の MinimumNumberOfRDSH パラメーターに基づいてシャットダウンする必要のある RDSH サーバーが決定されます。 スクリプトでは、RDSH サーバーがドレイン モードに設定され、新しいセッションがホストに接続することを防ぎます。 config.xml ファイル内で **LimitSecondsToForceLogOffUser** パラメーターを 0 以外の正の値に設定した場合は、スクリプトによって、現在サインインしているユーザーに作業内容を保存するよう通知され、構成された時間待機してからユーザーが強制的にサインアウトされます。RDSH サーバー上のすべてのユーザー セッションがサインオフした後、スクリプトによってサーバーがシャットダウンされます。

config.xml ファイル内の **LimitSecondsToForceLogOffUser** パラメーターをゼロに設定した場合、スクリプトでは、コレクション プロパティ内のセッション構成設定でユーザー セッションのサインオフを処理できます。 RDSH サーバー上にセッションがある場合は、RDSH サーバーが実行されたままになります。 セッションがない場合は、スクリプトによって RDSH サーバーがシャットダウンされます。

スクリプトは、タスク スケジューラを使用してスケーラー VM サーバー上で定期的に実行されるように設計されています。 リモート デスクトップ サービス環境のサイズに基づいて適切な時間間隔を選択し、仮想マシンの起動とシャットダウンにしばらく時間がかかることを忘れないでください。 スケーリング スクリプトを 15 分ごとに実行することをお勧めします。

## <a name="log-files"></a>ログ ファイル

スケーリング スクリプトでは、**WVDTenantScale.log** と **WVDTenantUsage.log** の 2 つのログ ファイルが作成されます。 **WVDTenantScale.log** ファイルには、スケーリング スクリプトの各実行中のイベントとエラー (ある場合) が記録されます。

**WVDTenantUsage.log** ファイルには、スケーリング スクリプトを実行するたびアクティブなコア数とアクティブな仮想マシン数が記録されます。 この情報を使用して、Microsoft Azure VM の実際の使用料とコストを見積もることができます。 ファイルはコンマ区切り値として書式設定され、各項目に次の情報が含まれます。

>時間、コレクション、コア、VM

ファイル名を変更して .csv 拡張子を付け、Microsoft Excel に読み込んで分析することもできます。