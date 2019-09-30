---
title: Azure Security Center のファイルの整合性の監視 | Microsoft Docs
description: " Azure Security Center のファイルの整合性の監視を有効にする方法を説明します。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: f7f5c257b23cd273a2cda40f874f5edfc62c0dbd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202184"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center のファイルの整合性の監視
このチュートリアルを使用して、Azure Security Center のファイルの整合性の監視 (FIM) を構成する方法を説明します。

## <a name="what-is-fim-in-security-center"></a>Security Center の FIM とは
ファイルの整合性の監視 (FIM) は変更の監視とも呼ばれ、オペレーティング システムやアプリケーション ソフトウェアなどのファイルとレジストリを調べて、攻撃の兆候となる変更を確認します。 比較方式を使用して、ファイルの現在の状態がファイルの前回のスキャンと異なっているかどうかが判別されます。 この比較を利用して、有効なものであれ、疑わしいものであれ、ファイルに変更が加えられていないかを判別できます。

Security Center のファイルの整合性の監視では、Windows ファイル、Windows レジストリ、および Linux ファイルの整合性が検証されます。 FIM の有効化によって監視されるファイルを選択します。 Security Center は、FIM が有効になっているファイルに対する次のようなアクティビティを監視します。

- ファイルとレジストリの作成と削除
- ファイルの変更 (ファイル サイズ、アクセス制御リスト、およびコンテンツのハッシュの変更)
- レジストリの変更 (サイズ、アクセス制御リスト、種類、およびコンテンツの変更)

Security Center が監視することをお勧めする、FIM を簡単に有効にすることができるエンティティがあります。 独自の FIM ポリシーまたは監視するエンティティを定義することもできます。 このチュートリアルでは、その実行方法を示します。

> [!NOTE]
> ファイルの整合性の監視 (FIM) 機能は、Windows コンピューター、Linux コンピューター、および VM で動作し、Security Center の Standard レベルで利用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。 FIM は、Log Analytics ワークスペースにデータをアップロードします。 データ料金は、アップロードするデータの量に基づいて適用されます。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/log-analytics/)」をご覧ください。

FIM は、Azure Change Tracking ソリューションを使用して、ユーザーの環境内の変更を追跡して識別します。 ファイルの整合性の監視を有効にすると、種類が**ソリューション**である **Change Tracking** リソースを使用できるようになります。 データ収集の頻度の詳細については、Azure Change Tracking の「[変更の追跡データ収集の詳細](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details)」を参照してください。

> [!NOTE]
> **Change Tracking** リソースを削除すると、Security Center のファイルの整合性の監視機能も無効になります。

## <a name="which-files-should-i-monitor"></a>どのファイルを監視する必要があるか
監視するファイルを選択するときは、システムとアプリケーションにとって重要なファイルを考慮する必要があります。 計画なしで変更されることがないファイルを選択することを検討してください。 アプリケーションまたはオペレーティング システムによって頻繁に変更されるファイル (ログ ファイルやテキスト ファイルなど) を選択すると、多数のノイズの発生によって攻撃が識別が困難になります。

Security Center では、ファイルとレジストリの変更を伴う既知の攻撃パターンに従って、監視対象としてお勧めするファイルを既定値として設定しています。

## <a name="using-file-integrity-monitoring"></a>ファイルの整合性の監視の使用
1. **[Security Center]** ダッシュボードを開きます。
2. 左側のウィンドウで、 **[高度なクラウド防御]** の下の **[ファイルの整合性の監視]** を選択します。
![Security Center ダッシュボード][1]

**[ファイルの整合性の監視]** が開きます。
  ![Security Center ダッシュボード][2]

各ワークスペースの次の情報が提供されます。

- 過去 1 週間に発生した変更の合計数 (ワークスペースで FIM が有効になっていない場合は、ダッシュ "-" が表示されることがあります)
- ワークスペースに対して報告を行っているコンピューターと VM の合計数
- ワークスペースの地理的な場所
- ワークスペースが存在する Azure サブスクリプション

ワークスペースに次のボタンが表示されることもあります。

- ![[有効化] アイコン][3] ワークスペースの FIM が有効ではないことを示します。 ワークスペースを選択すると、そのワークスペースのすべてのマシンで FIM を有効にすることができます。
- ![[アップグレード プラン] アイコン][4] ワークスペースまたはサブスクリプションが、Security Center の Standard レベルで実行されていないことを示します。 FIM 機能を使用するには、サブスクリプションが Standard で実行されている必要があります。  ワークスペースを選択することで、Standard にアップグレードできます。 Standard プランとアップグレード方法の詳細については、[Azure Security Center の Standard レベルへのアップグレードによるセキュリティの強化](security-center-pricing.md)に関する記事を参照してください。
- 空白 (ボタン表示なし) は、ワークスペースで FIM が既に有効になっていることを意味します。

**[ファイルの整合性の監視]** では、FIM を有効にするワークスペースの選択、ワークスペースのファイルの整合性の監視ダッシュボードの表示、またはワークスペースの Standard への[アップグレード](security-center-pricing.md)を実行できます。

## <a name="enable-fim"></a>FIM を有効にする
ワークスペースで FIM を有効にするには:

1. **[ファイルの整合性の監視]** で、 **[有効化]** ボタンを使用してワークスペースを選択します。
2. **[ファイルの整合性の監視を有効にする]** が開き、そのワークスペース下にある Windows マシンと Linux マシンの数が表示されます。

   ![ファイルの整合性の監視の有効化][5]

   Windows と Linux の推奨設定も表示されます。  **[Windows ファイル]** 、 **[レジストリ]** 、および **[Linux ファイル]** を展開して、推奨される項目の完全な一覧を表示します。

3. FIM を適用しないエンティティをオフにします。
4. FIM を有効にするには、 **[ファイルの整合性の監視を適用する]** を選択します。

> [!NOTE]
> 設定はいつでも変更できます。 詳細については、以降の「監視対象エンティティを編集する」を参照してください。
>
>

## <a name="view-the-fim-dashboard"></a>FIM ダッシュボードを表示する
**ファイルの整合性の監視**ダッシュボードには、FIM が有効になっているワークスペースが表示されます。 FIM ダッシュボードは、ワークスペースの FIM を有効にした後、または **[ファイルの整合性の監視]** ウィンドウで FIM が既に有効になっているワークスペースを選択したときに開きます。

![ファイルの整合性の監視ダッシュボード][6]

ワークスペースの FIM ダッシュボードには、次の情報が表示されます。

- ワークスペースに接続されているマシンの合計数
- 選択した期間中に発生した変更の合計数
- 変更の種類の内訳 (ファイル、レジストリ)
- 変更のカテゴリの内訳 (変更、追加、削除)

ダッシュボードの上部にあるフィルターを選択すると、変更を表示する期間を適用できます。

![期間フィルター][7]

**[コンピューター]** タブ (前図参照) には、このワークスペースに対して報告を行っているすべてのコンピューターが一覧表示されます。 ダッシュボードには、コンピューターごとに以下が一覧表示されます。

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

1. **ファイルの整合性の監視ダッシュ ボード**に戻り、 **[設定]** を選択します。

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
1. **ファイルの整合性の監視ダッシュボード**に戻り、上部にある **[設定]** を選択します。 **[ワークスペースの構成]** が開きます。
2. **[ワークスペースの構成]** で、追加するエンティティの種類のタブを選択します:[Windows レジストリ]、[Windows ファイル]、または [Linux ファイル]。 この例では、 **[Linux ファイル]** が選択されています。

   ![監視する新しい項目を追加する][14]

3. **[追加]** を選択します。 **[Edit for Change Tracking]\(変更追跡を行うための追加\)** が開きます。

   ![要求された情報の入力][15]

4. **[追加]** ページで、要求された情報を入力し、 **[保存]** を選択します。

## <a name="disable-monitored-entities"></a>監視対象エンティティを無効にする
1. **ファイルの整合性の監視**ダッシュボードに戻ります。
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

1. FIM を無効にするには、**ファイルの整合性の監視**ダッシュボードに戻ります。
2. ワークスペースを選択します。
3. **[ファイルの整合性の監視]** で、 **[無効化]** を選択します。

   ![FIM を無効にする][20]

4. 無効にするには、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順
この記事では、Security Center のファイルの整合性の監視 (FIM) を使用する方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [セキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
* [セキュリティに関する推奨事項の管理](security-center-recommendations.md) -- 推奨事項に従って Azure リソースを保護する方法について説明します。
* [セキュリティ正常性の監視](security-center-monitoring.md) -- Azure リソースの正常性を監視する方法について説明します。
* [セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティの警告の管理と対応の方法について説明します。
* [パートナー ソリューションの監視](security-center-partner-solutions.md) -- パートナー ソリューションの正常性状態を監視する方法について説明します。
* [Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
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
