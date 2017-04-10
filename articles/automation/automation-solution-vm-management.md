---
title: "ピーク時間外 VM 起動/停止 [プレビュー] ソリューション | Microsoft Docs"
description: "スケジュールに従って Azure Resource Manager 仮想マシンを起動/停止し、Log Analytics からプロアクティブに監視する VM 管理ソリューションについて説明します。"
services: automation
documentationCenter: 
authors: mgoedtel
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 5ae60cb8ba3d391d3babd1ab575b4f32e139a185
ms.openlocfilehash: f2c9a5ef2a8f517b9b2072be57f4d8c51b7694c6
ms.lasthandoff: 02/15/2017

---

# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>ピーク時間外 VM 起動/停止 [プレビュー] ソリューション (Automation)

ピーク時間外 VM 起動/停止 (プレビュー) は、ユーザー定義のスケジュールに従って Azure Resource Manager 仮想マシンを起動/停止することによって、それらの仮想マシンを起動/停止する Automation ジョブが正常に実行されているかどうかを OMS Log Analytics で分析するソリューションです。  

## <a name="prerequisites"></a>前提条件

- [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)を使用した Runbook。  認証方法としては、実行アカウントの使用をお勧めします。有効期限が切れたり頻繁に変わったりするパスワードではなく証明書を使った認証が使用されます。  

- このソリューションで管理できる VM は、Automation アカウントと同じサブスクリプションとリソース グループに属している VM に限られます。  

- このソリューションがデプロイされる Azure リージョンは、オーストラリア南東部、米国東部、東南アジア、西ヨーロッパだけです。  VM のスケジュールを管理する Runbook は、任意のリージョンの VM を対象にすることができます。  

- VM の起動/停止 Runbook が完了したときに電子メール通知を送信するには、Office 365 Business クラスのサブスクリプションが必要となります。  

## <a name="solution-components"></a>ソリューションのコンポーネント

このソリューションは、以下のリソースから成ります。これらのリソースが Automation アカウントにインポートされて追加されます。

### <a name="runbooks"></a>Runbook

Runbook | Description|
--------|------------|
CleanSolution-MS-Mgmt-VM | サブスクリプションからソリューションを削除しようとすると、使用されているすべてのリソースとスケジュールがこの Runbook によって削除されます。|  
SendMailO365-MS-Mgmt | Office 365 Exchange を使用して電子メールを送信します。|
StartByResourceGroup-MS-Mgmt-VM | 特定の Azure リソース グループに存在する VM (クラシック モデルでデプロイされた VM と ARM モデルでデプロイされた VM の両方) を起動するための Runbook です。
StopByResourceGroup-MS-Mgmt-VM | 特定の Azure リソース グループに存在する VM (クラシック モデルでデプロイされた VM と ARM モデルでデプロイされた VM の両方) を停止するための Runbook です。|
<br>

### <a name="variables"></a>変数

変数 | Description|
---------|------------|
**SendMailO365-MS-Mgmt** Runbook ||
SendMailO365-IsSendEmail-MS-Mgmt | 完了時に Runbook (StartByResourceGroup-MS-Mgmt-VM と StopByResourceGroup-MS-Mgmt-VM) から電子メール通知を送信できるかどうかを指定します。  電子メール通知を有効にするには **True** を、無効にするには **False** を選択します。 既定値は **False** です。| 
**StartByResourceGroup-MS-Mgmt-VM** Runbook ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | 管理操作から除外する VM の名前を入力します。複数指定する場合は、セミコロン (;) で区切ります。 値は大文字と小文字が区別され、ワイルドカード (アスタリスク) がサポートされます。|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 電子メール メッセージ本文の冒頭に追加されるテキスト。|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 電子メール Runbook がある Automation アカウントの名前を指定します。  **この変数は変更しないでください。**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | 電子メール Runbook の名前を指定します。  StartByResourceGroup-MS-Mgmt-VM と StopByResourceGroup-MS-Mgmt-VM の Runbook が電子メールを送信するときに使用します。  **この変数は変更しないでください。**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 電子メール Runbook があるリソース グループの名前を指定します。  **この変数は変更しないでください。**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 電子メールの件名に使用するテキストを指定します。|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 電子メールの受信者を指定します。  複数の名前は、セミコロン (;) で区切って入力してください。|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 管理操作から除外する VM の名前を入力します。複数指定する場合は、セミコロン (;) で区切ります。 値は大文字と小文字が区別され、ワイルドカード (アスタリスク) がサポートされます。  既定値 (アスタリスク) では、サブスクリプションに含まれているすべてのリソース グループが対象となります。|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | このソリューションで管理する VM があるサブスクリプションを指定します。  このソリューションの Automation アカウントと同じサブスクリプションを指定してください。|
**StopByResourceGroup-MS-Mgmt-VM** Runbook ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | 管理操作から除外する VM の名前を入力します。複数指定する場合は、セミコロン (;) で区切ります。 値は大文字と小文字が区別され、ワイルドカード (アスタリスク) がサポートされます。|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | 電子メール メッセージ本文の冒頭に追加されるテキスト。|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | 電子メール Runbook がある Automation アカウントの名前を指定します。  **この変数は変更しないでください。**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | 電子メール Runbook があるリソース グループの名前を指定します。  **この変数は変更しないでください。**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | 電子メールの件名に使用するテキストを指定します。|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | 電子メールの受信者を指定します。  複数の名前は、セミコロン (;) で区切って入力してください。|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | 管理操作から除外する VM の名前を入力します。複数指定する場合は、セミコロン (;) で区切ります。 値は大文字と小文字が区別され、ワイルドカード (アスタリスク) がサポートされます。  既定値 (アスタリスク) では、サブスクリプションに含まれているすべてのリソース グループが対象となります。|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | このソリューションで管理する VM があるサブスクリプションを指定します。  このソリューションの Automation アカウントと同じサブスクリプションを指定してください。|  
<br>

### <a name="schedules"></a>スケジュール

スケジュール | Description|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | このソリューションによって管理される VM の開始を実行する、StartByResourceGroup Runbook のスケジュール。 作成時の既定値は UTC タイム ゾーンです。|
StopByResourceGroup-Schedule-MS-Mgmt | このソリューションによって管理される VM の停止を実行する、StopByResourceGroup Runbook のスケジュール。 作成時の既定値は UTC タイム ゾーンです。|

### <a name="credentials"></a>資格情報

資格情報 | Description|
-----------|------------|
O365Credential | 電子メールを送信する有効な Office 365 ユーザー アカウントを指定します。  SendMailO365-IsSendEmail-MS-Mgmt 変数が **True** に設定されている場合のみ必要になります。

## <a name="configuration"></a>構成

ピーク時間外 VM 起動/停止 [プレビュー] ソリューションをご利用の Automation アカウントに追加し、変数を設定してソリューションをカスタマイズするには、以下の手順を実行します。

1. Azure Portal のホーム画面から **[Marketplace]** タイルを選択します。  このタイルがホーム画面にピン留めされていない場合は、左側のナビゲーション ウィンドウから **[新規]** を選択します。  
2. [Marketplace] ブレードの検索ボックスに「**Start VM**」と入力し、検索結果から **[Start/Stop VMs during off-hours [Preview] \(ピーク時間外 VM 起動/停止 [プレビュー])]** を選択します。  
3. 選択したソリューションの **[Start/Stop VMs during off-hours [Preview] \(ピーク時間外 VM 起動/停止 [プレビュー])]** ブレードで概要を確認し、**[作成]** をクリックします。  
4. **[ソリューションの追加]** ブレードが表示されます。Automation サブスクリプションにインポートするソリューションの設定を事前に行うよう求められます。<br><br> ![VM 管理の [ソリューションの追加] ブレード](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  **[ソリューションの追加]** ブレードで **[ワークスペース]** を選択し、ここで、Automation アカウントと同じ Azure サブスクリプションに関連付けられている OMS ワークスペースを選択するか、新しい OMS ワークスペースを作成します。  OMS ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択し、**[OMS Workspace (OMS ワークスペース)]** ブレードで次の手順を実行します。 
   - 新しい **OMS ワークスペース**の名前を指定します。
   - 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
   - **[リソース グループ]** には、新しいリソース グループを作成するか、既にあるリソース グループを選択することができます。  
   - **[場所]**を選択します。  現在選択肢として提供されている場所は、**オーストラリア南東部**、**米国東部**、**東南アジア**、**西ヨーロッパ**のみです。
   - **[価格レベル]** を選択します。  このソリューションは、2 つのレベルで提供されています (Free レベルと OMS Paid レベル)。  Free レベルは、1 日に収集できるデータの量、リテンション期間、Runbook ジョブの実行時間 (分) に上限が設けられています。  OMS Paid レベルでは&1; 日に収集できるデータの量に上限がありません。  

        > [!NOTE]
        > オプションとしてスタンドアロン Paid レベルが表示されますが、適用することはできません。  サブスクリプション内でこのオプションを選択してこの記事のソリューションの作成を続行すると失敗します。  この問題は、このソリューションが正式にリリースされたときに解決されます。<br>このソリューションを使うと、Automation ジョブ分数とログの取り込みのみが使用されます。  ソリューションによって OMS ノードが環境に追加されることはありません。  

6. **[OMS workspace (OMS ワークスペース)]** ブレードに必要な情報を入力したら、**[作成]** をクリックします。  情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  その後、自動的に **[ソリューションの追加]** ブレードに戻ります。  
7. **[ソリューションの追加]** ブレードで **[Automation アカウント]** を選択します。  OMS ワークスペースを新たに作成する場合はさらに、先ほど指定した新しい OMS ワークスペース (Azure サブスクリプション、リソース グループ、リージョンを含む) に関連付ける新しい Automation アカウントを作成する必要があります。  **[Automation アカウントの作成]** を選択し、**[Automation アカウントの追加]** ブレードで次の情報を入力します。 
  - **[名前]** フィールドに、Automation アカウントの名前を入力します。

    それ以外のオプションはすべて、選択した OMS ワークスペースに基づいて自動的に入力されます。これらのオプションを変更することはできません。  このソリューションに含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。  **[OK]** をクリックすると、設定したオプションが検証され、Automation アカウントが作成されます。  進行状況は、メニューの **[通知]** で追跡できます。 

    それ以外の方法として、既存の Automation 実行アカウントを選択することもできます。  既に別の OMS ワークスペースに関連付けられているアカウントは選択できないので注意してください。選択すると、その旨を伝えるメッセージがブレードに表示されます。  既に関連付けられている場合は、別の Automation 実行アカウントを選択するか、新たに作成する必要があります。<br><br> ![Automation Account Already Linked to OMS Workspace](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. 最後に、**[ソリューションの追加]** ブレードで **[構成]** を選択すると、**[パラメーター]** ブレードが表示されます。  **[パラメーター]** ブレードでは、次の作業が求められます。  
   - **[Target ResourceGroup Names (ターゲット リソース グループ名)]** に、このソリューションで管理する VM を含んだリソース グループの名前を指定します。  複数の名前を入力する場合は、それぞれセミコロンで区切って入力してください (値は大文字と小文字が区別されます)。  ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。
   - ターゲット リソース グループ内の VM を定期的に起動/停止する日時を **[スケジュール]** で選択します。  スケジュールは既定で UTC タイム ゾーンに構成され、別のリージョンを選ぶことはできません。  ソリューション構成後にスケジュールを特定のタイム ゾーンに構成したい場合は、後の「[開始と停止のスケジュールの変更](#modifying-the-startup-and-shutdown-schedule)」をご覧ください。    

10. ソリューションに必要な初期設定が完了したら、**[作成]** を選択します。  すべての設定が検証された後、ご利用のサブスクリプションへのソリューションのデプロイが試行されます。  このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。 

## <a name="collection-frequency"></a>収集の頻度

Automation ジョブのログとジョブのストリーム データは、5 分おきに OMS リポジトリに取り込まれます。  

## <a name="using-the-solution"></a>ソリューションの使用

OMS ワークスペースに VM 管理ソリューションを追加すると、OMS ダッシュボードに **[StartStopVM View (StartStopVM ビュー)]** タイルが追加されます。  このタイルには、ソリューションに関して開始された Runbook ジョブと正常に完了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![VM 管理の [StartStopVM View (StartStopVM ビュー)] タイル](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

ご利用の Automation アカウントから目的のソリューションにアクセスして管理するには、**[ソリューション]** タイルを選択し、**[ソリューション]** ブレードのソリューション一覧から **[Start-Stop-VM<ワークスペース名>]** を選択します。<br><br> ![Automation ソリューションの一覧](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

ソリューションを選択すると、**[Start-Stop-VM<ワークスペース名>]** ソリューション ブレードが表示されて、重要な情報を確認できます。たとえば、OMS ワークスペースの **[StartStopVM]** タイルと同様、そのソリューションに関して開始された Runbook ジョブと正常に完了した Runbook ジョブの数、およびそのグラフが表示されます。<br><br> ![Automation VM ソリューションのブレード](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

このブレードから、OMS ワークスペースを開いたり、ジョブ レコードの分析をさらに実行したりすることもできます。  **[すべての設定]** をクリックし、**[設定]** ブレードで **[クイック スタート]** を選択してから、**[クイック スタート]** ブレードで **[OMS Portal (OMS ポータル)]** を選択します。   新しいタブまたは新しいブラウザー セッションが開き、Automation のアカウントとサブスクリプションに関連付けられた OMS ワークスペースが表示されます。  


### <a name="configuring-e-mail-notifications"></a>電子メール通知の構成

VM の起動/停止 Runbook が完了したときの電子メール通知を有効にするには、**O365Credential** の資格情報を変更したうえで、少なくとも次の変数に変更を加える必要があります。

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

**O365Credential** 資格情報の設定を変更するには、次の手順に従います。

1. Automation アカウントから、ウィンドウ上部にある **[すべての設定]** をクリックします。 
2. **[設定]** ブレードの **[Automation リソース]** セクションで **[資産]** を選択します。 
3. **[資産]** ブレードで **[資格情報]** タイルを選択し、**[資格情報]** ブレードから **[O365Credential]** を選択します。  
4. Office 365 の有効なユーザー名とパスワードを入力し、**[保存]** をクリックして変更内容を保存します。  

前述の変数に変更を加えるには、次の手順を実行します。

1. Automation アカウントから、ウィンドウ上部にある **[すべての設定]** をクリックします。 
2. **[設定]** ブレードの **[Automation リソース]** セクションで **[資産]** を選択します。 
3. **[資産]** ブレードの **[変数]** タイルを選択して、上に挙げた変数を **[変数]** ブレードから選択し、前出の「[変数](##variables)」セクションの説明に従ってその値を変更します。  
4. **[保存]** をクリックして、変数に対する変更を保存します。   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>開始と停止のスケジュールの変更

このソリューションの開始と停止のスケジュールを管理する際には、「[Azure Automation の Runbook をスケジュール設定する](automation-schedules.md)」で説明されている手順に従います。  ただし、スケジュールの構成を変更することはできません。  既存のスケジュールを無効にしてから新しいものを作成し、スケジュールを適用する **StartByResourceGroup-MS-Mgmt-VM** または **StopByResourceGroup-MS-Mgmt-VM** Runbook に関連付ける必要があります。   

## <a name="log-analytics-records"></a>Log Analytics のレコード

Automation により&2; 種類のレコードが OMS リポジトリに作成されます。

### <a name="job-logs"></a>ジョブ ログ

プロパティ | Description|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobLogs です。|
CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
resourceId | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | Runbook ジョブの状態。  次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
resultDescription | Runbook ジョブの結果の状態について説明します。  次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
RunbookName | Runbook の名前を指定します。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager になります。|
StreamType | イベントの種類を指定します。 次のいずれかの値になります。<br>- 詳細<br>- 出力<br>- エラー<br>- 警告|
SubscriptionId | ジョブのサブスクリプション ID を指定します。
Time | Runbook ジョブが実行された日付と時刻。|


### <a name="job-streams"></a>ジョブ ストリーム

プロパティ | Description|
----------|----------|
Caller |  操作を開始したユーザー。  スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
カテゴリ | データの種類の分類。  Automation の場合、値は JobStreams です。|
JobId | GUID。Runbook ジョブの ID です。|
operationName | Azure で実行された操作の種類を指定します。  Automation の場合、値はジョブになります。|
ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
resourceId | Azure のリソース ID を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。  Automation の場合、その値は Azure Automation になります。|
ResourceType | Azure のリソースの種類を指定します。  Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
resultType | イベントが生成された時点における Runbook ジョブの結果。  次のいずれかの値になります。<br>- 処理中|
resultDescription | Runbook からの出力ストリームが含まれます。|
RunbookName | Runbook の名前。|
SourceSystem | 送信されたデータのソース システムを指定します。  Automation の場合、値は OpsManager になります。|
StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 進行状況<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|
Time | Runbook ジョブが実行された日付と時刻。|

**JobLogs** カテゴリまたは **JobStreams** カテゴリのレコードを取得する何らかのログ検索を実行するときは、**JobLogs** ビューまたは **JobStreams** ビューを選択すると、検索から返された最新情報が一連のタイルに集約されて表示されます。

## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、このソリューションによって収集されたジョブ レコードを探すログ検索の例です。 

クエリ | Description|
----------|----------|
正常完了した StartVM Runbook のジョブを検索する | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
正常完了した StopVM Runbook のジョブを検索する | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
StartVM と StopVM の Runbook についてジョブの経時ステータスを表示する | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>ソリューションの削除

ソリューションを使用する必要がなくなった場合、ソリューションを Automation アカウントから削除できます。  ソリューションを削除すると、Runbook のみが削除され、ソリューションの追加時に作成されたソリューションまたは変数は削除されません。  これらの資産は、他の Runbook で使用していない限り、手動で削除する必要があります。  

ソリューションを削除するには、次の手順を実行します。

1.  [Automation アカウント] から **[ソリューション]** タイルを選択します。  
2.  **[ソリューション]** ブレードで、ソリューション **[Start-Stop-VM[Workspace] (Start-Stop-VM[ワークスペース])]** を選択します。  **[VMManagementSolution[Workspace] (VMManagementSolution[ワークスペース])]** ブレードで、メニューから **[削除]** をクリックします。<br><br> ![VM 管理ソリューションの削除](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  **[ソリューションの削除]** ウィンドウで、ソリューションを削除するかどうかを確定します。
4.  情報が検証され、ソリューションが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  ソリューションの削除プロセスが開始した後、**[VMManagementSolution[Workspace] (VMManagementSolution[ワークスペース])]** ブレードに自動的に戻ります。  

Automation アカウントと OMS ワークスペースは、このプロセスの一環として削除されません。  OMS ワークスペースを保持しない場合は、これを手動で削除する必要があります。  これは、Azure Portal からも行えます。   Azure Portal のホーム画面から **[Log Analytics]** を選択し、**[Log Analytics]** ブレードでワークスペースを選択して、ワークスペースの設定ブレードのメニューから **[削除]** をクリックします。  
      
## <a name="next-steps"></a>次のステップ

- 各種検索クエリの作成方法と、Log Analytics での Automation ジョブの確認方法の詳細については、「 [Log Analytics におけるログの検索](../log-analytics/log-analytics-log-searches.md)
- Runbook の実行、Runbook ジョブの監視方法、その他の技術的な詳細については、 [Runbook ジョブの追跡](automation-runbook-execution.md)
- OMS Log Analytics とデータ収集ソースの詳細については、 [Log Analytics での Azure Storage データの収集の概要](../log-analytics/log-analytics-azure-storage.md)






   


