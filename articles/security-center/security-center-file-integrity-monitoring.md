---
title: Azure Security Center のファイルの整合性の監視 | Microsoft Docs
description: このチュートリアルを使用して、Azure Security Center のファイルの整合性の監視 (FIM) を構成する方法を説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b858903b87dc6c4cc1f7c40338c09bf5b8204b3d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176537"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center のファイルの整合性の監視
このチュートリアルを使用して、Azure Security Center のファイルの整合性の監視 (FIM) を構成する方法を説明します。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[Azure Defender for servers](defender-for-servers-introduction.md) が必要です。<br>FIM は、Log Analytics ワークスペースにデータをアップロードします。 データ料金は、アップロードするデータの量に基づいて適用されます。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)」をご覧ください。|
|必要なロールとアクセス許可:|**ワークスペースの所有者** は、FIM を有効/無効にすることができます (詳細については、[Log Analytics での Azure のロール](/services-hub/health/azure-roles#azure-roles)に関する記事を参照してください)。<br>**閲覧者** は結果を表示できます。|
|クラウド:|![はい](./media/icons/yes-icon.png) 商用クラウド<br>![はい](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)<br>Azure Automation の変更追跡ソリューションが利用可能なリージョンでのみサポートされます。<br>![はい](./media/icons/yes-icon.png) [Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/overview) 対応デバイス。<br>「[リンクされた Log Analytics ワークスペースでサポートされるリージョン](../automation/how-to/region-mappings.md)」を参照してください。<br>[変更追跡についてさらに学習](../automation/change-tracking/overview.md)します。|
|||

## <a name="what-is-fim-in-security-center"></a>Security Center の FIM とは
ファイルの整合性の監視 (FIM) は変更の監視とも呼ばれ、オペレーティング システム ファイル、Windows レジストリ、アプリケーション ソフトウェア、Linux システム ファイルなどを調べて、攻撃の兆候となる変更を確認します。 

Security Center では FIM での監視対象のエンティティが推奨され、独自の FIM ポリシーまたは監視するエンティティを定義することもできます。 FIM を使用すると、次のような疑わしいアクティビティについてアラートが生成されます。

- ファイルとレジストリ キーの作成または削除
- ファイルの変更 (ファイル サイズ、アクセス制御リスト、およびコンテンツのハッシュの変更)
- レジストリの変更 (サイズ、アクセス制御リスト、種類、およびコンテンツの変更)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * FIM での監視対象として推奨されるエンティティの一覧を確認する方法
> * 独自のカスタム FIM ルールを定義する方法
> * 監視対象エンティティに対する変更を監査する方法
> * ワイルドカードを使用して、ディレクトリをまたいだ追跡を簡略化する方法


## <a name="how-does-fim-work"></a>FIM のしくみ

FIM では、これらの項目の現在の状態が前回のスキャン中の状態と比較され、疑わしい変更が加えられた場合にアラートが生成されます。

FIM は、Azure Change Tracking ソリューションを使用して、ユーザーの環境内の変更を追跡して識別します。 ファイルの整合性の監視を有効にすると、種類が **ソリューション** である **Change Tracking** リソースを使用できるようになります。 データ収集の頻度の詳細については、[Change Tracking のデータ収集の詳細](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)に関する記事を参照してください。

> [!NOTE]
> **Change Tracking** リソースを削除すると、Security Center のファイルの整合性の監視機能も無効になります。

## <a name="which-files-should-i-monitor"></a>どのファイルを監視する必要があるか
監視するファイルを選択するときは、システムとアプリケーションにとって重要なファイルを考慮する必要があります。 計画なしで変更されることがないファイルを監視してください。 アプリケーションまたはオペレーティング システムによって頻繁に変更されるファイル (ログ ファイルやテキスト ファイルなど) を選択すると、多数のノイズの発生によって攻撃の識別が困難になります。

Security Center では、既知の攻撃パターンに基づいて、監視が推奨される項目を次のリストに示しています。

|Linux ファイル|Windows ファイル|Windows レジストリ キー (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>ファイルの整合性の監視の有効化 

FIM は、Azure portal の Security Center のページからのみ利用できます。 現在、FIM を操作するための REST API はありません。

1. **Azure Defender** ダッシュボードの **[高度な保護]** 領域で、 **[ファイルの整合性の監視]** を選択します。

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="FIM の起動" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    **[ファイルの整合性の監視]** 構成ページが開きます。

    各ワークスペースの次の情報が提供されます。

    - 過去 1 週間に発生した変更の合計数 (ワークスペースで FIM が有効になっていない場合は、ダッシュ "-" が表示されることがあります)
    - ワークスペースに対して報告を行っているコンピューターと VM の合計数
    - ワークスペースの地理的な場所
    - ワークスペースが存在する Azure サブスクリプション

1. このページを使用して次のことができます。

    - 各ワークスペースの状態と設定にアクセスして表示する

    - ![[アップグレード プラン] アイコン][4] Azure Defender を使用するようにワークスペースをアップグレードする。 このアイコンは、ワークスペースまたはサブスクリプションが Azure Defender によって保護されていないことを示します。 FIM 機能を使用するには、サブスクリプションが Azure Defender によって保護されている必要があります。 詳細については、「[Azure Security Center (無料) と有効化された Azure Defender](security-center-pricing.md)」を参照してください。

    - ![[有効化] アイコン][3] ワークスペースにあるすべてのマシンで FIM を有効にし、FIM オプションを構成する。 このアイコンは、ワークスペースの FIM が有効ではないことを示します。

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="特定のワークスペースに対して FIM を有効にする":::


    > [!TIP]
    > 有効化とアップグレードのどちらのボタンもなく、スペースが空白の場合、そのワークスペースで FIM が既に有効になっていることを意味します。


1. **[有効化]** を選択します。 ワークスペースの詳細が表示されます。ワークスペースにある Windows と Linux のマシンの数も含まれます。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM ワークスペースの詳細ページ":::

   Windows と Linux の推奨設定も表示されます。  **[Windows ファイル]** 、 **[レジストリ]** 、および **[Linux ファイル]** を展開して、推奨される項目の完全な一覧を表示します。

1. FIM で監視しない推奨エンティティのチェックボックスをオフにします。

1. FIM を有効にするには、 **[ファイルの整合性の監視を適用する]** を選択します。

> [!NOTE]
> 設定はいつでも変更できます。 詳細については、「[監視対象エンティティを編集する](#edit-monitored-entities)」を参照してください。



## <a name="audit-monitored-workspaces"></a>監視対象ワークスペースの監査 

**ファイルの整合性の監視** ダッシュボードには、FIM が有効になっているワークスペースが表示されます。 FIM ダッシュボードは、ワークスペースの FIM を有効にした後、または **[ファイルの整合性の監視]** ウィンドウで FIM が既に有効になっているワークスペースを選択したときに開きます。

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM ダッシュボードとそのさまざまな情報パネル":::

ワークスペースの FIM ダッシュボードには、次の詳細が表示されます。

- ワークスペースに接続されているマシンの合計数
- 選択した期間中に発生した変更の合計数
- 変更の種類の内訳 (ファイル、レジストリ)
- 変更のカテゴリの内訳 (変更、追加、削除)

ダッシュボードの上部にある **[フィルター]** を選択して、表示する変更内容の期間を変更します。

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM ダッシュボードの期間フィルター":::

**[サーバー]** タブには、このワークスペースに対して報告を行うマシンが一覧表示されます。 ダッシュボードには、コンピューターごとに以下が一覧表示されます。

- 選択した期間中に発生した変更の合計数
- ファイルの変更またはレジストリの変更としての変更の合計数の内訳

検索フィールドにコンピューター名を入力するか、[コンピューター] タブに一覧表示されているコンピューターを選択すると、 **[ログ検索]** が開きます。ログ検索は、選択された期間中にコンピューターで行われたすべての変更を表示します。 変更を展開して詳細を確認できます。

![ログ検索][8]

**[変更]** タブ (下図参照) には、選択した期間中に行われたワークスペースのすべての変更が一覧表示されます。 ダッシュボードには、変更されたエンティティごとに、以下が一覧表示されます。

- 変更が発生したコンピューター
- 変更の種類 (レジストリまたはファイル)
- 変更のカテゴリ (変更、追加、削除)
- 変更の日時

![ワークスペースに対する変更][9]

**[変更の詳細]** は、検索フィールドに変更を入力するか、 **[変更]** タブに一覧表示されているエンティティを選択したときに開きます。

![変更の詳細][10]

## <a name="edit-monitored-entities"></a>監視対象エンティティを編集する

1. **ファイルの整合性の監視ダッシュボード** に戻り、 **[設定]** を選択します。

   ![設定][11]

   **[ワークスペースの構成]** を開くと 3 つのタブが表示されます: **[Windows レジストリ]** 、 **[Windows ファイル]** 、および **[Linux ファイル]** 。 各タブには、そのカテゴリで編集できるエンティティが一覧表示します。 表示されるエンティティごとに、Security Center は、FIM が有効になっている (true) か有効になっていない (false) かを識別します。  エンティティを編集することで、FIM を有効または無効にすることができます。

   ![ワークスペースの構成][12]

2. ID 保護を選択します。 この例では、[Windows レジストリ] の項目を選択しています。 **[Edit for Change Tracking]\(変更追跡を行うための編集\)** が開きます。

   ![[Edit for Change Tracking]\(変更追跡を行うための編集\)][13]

**[Edit for Change Tracking]\(変更追跡を行うための編集\)** では、以下を実行できます。

- ファイルの整合性の監視を有効 (True) にするか無効 (False) にする
- エンティティ名を指定するか変更する
- 値またはパスを指定するか変更する
- エンティティの削除、変更の破棄、または変更の保存を行う

## <a name="add-a-new-entity-to-monitor"></a>監視する新しいエンティティを追加する
1. **ファイルの整合性の監視ダッシュボード** に戻り、上部にある **[設定]** を選択します。 **[ワークスペースの構成]** が開きます。
2. **[ワークスペースの構成]** で、追加するエンティティの種類のタブを選択します:[Windows レジストリ]、[Windows ファイル]、または [Linux ファイル]。 この例では、 **[Linux ファイル]** が選択されています。

   ![監視する新しい項目を追加する][14]

3. **[追加]** を選択します。 **[Edit for Change Tracking]\(変更追跡を行うための追加\)** が開きます。

   ![要求された情報の入力][15]

4. **[追加]** ページで、要求された情報を入力し、 **[保存]** を選択します。

## <a name="disable-monitored-entities"></a>監視対象エンティティを無効にする
1. **ファイルの整合性の監視** ダッシュボードに戻ります。
2. FIM が現在有効になっているワークスペースを選択します。 [有効化] ボタンまたは [アップグレード プラン] ボタンが表示されていなければ、そのワークスペースの FIM は有効になっています。

   ![FIM が有効になっているワークスペースの選択][16]

3. [ファイルの整合性の監視] で、 **[設定]** を選択します。

   ![[設定] の選択][17]

4. **[ワークスペースの構成]** で、 **[有効化]** が [True] に設定されているグループを選択します。

   ![ワークスペースの構成][18]

5. **[Edit for Change Tracking]\(変更追跡を行うための編集\)** ウィンドウで、 **[有効化]** を [False] に設定します。

   ![[有効化] を False に設定][19]

6. **[保存]** を選択します。

## <a name="folder-and-path-monitoring-using-wildcards"></a>ワイルドカードを使用するフォルダーとパスの監視

ワイルドカードを使用して、ディレクトリ全体の追跡を簡略化します。 ワイルドカードを使用するフォルダーの監視を構成する場合、次のルールが適用されます。
-   複数のファイルを追跡するにはワイルドカードが必要です。
-   ワイルドカードを使用できるのは、C:\folder\file or /etc/*.conf など、パスの最後のセグメントでのみです。
-   環境変数に有効でないパスが含まれている場合、検証は成功しますが、インベントリの実行時にそのパスはエラーになります。
-   パスを設定するときは、漠然としたパス (\* など) は避けてください。走査の対象になるフォルダーが膨大な数になります。

## <a name="disable-fim"></a>FIM を無効にする
FIM を無効にすることができます。 FIM は、Azure Change Tracking ソリューションを使用して、ユーザーの環境内の変更を追跡して識別します。 FIM を無効にすることで、選択したワークスペースから Change Tracking ソリューションを削除します。

1. FIM を無効にするには、**ファイルの整合性の監視** ダッシュボードに戻ります。
2. ワークスペースを選択します。
3. **[ファイルの整合性の監視]** で、 **[無効化]** を選択します。

   ![FIM を無効にする][20]

4. 無効にするには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
この記事では、Security Center のファイルの整合性の監視 (FIM) を使用する方法について説明しました。 セキュリティ センターの詳細については、次のページを参照してください。

* [セキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
* [セキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
* [Azure セキュリティ ブログ](/archive/blogs/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
