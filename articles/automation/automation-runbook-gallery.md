---
title: "Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー | Microsoft Docs"
description: "Microsoft やコミュニティからの Runbook とモジュールを Azure Automation 環境にインストールして使用できます。  この記事では、これらのリソースにアクセスしたり、自分の Runbook をギャラリーに投稿したりする方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: e6033f089152c27713f98f12634c53012f5ff070
ms.openlocfilehash: 04cfafc9e7a037d915f63723fd0b67a07954460b


---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー
Azure Automation で独自の Runbook およびモジュールを作成するのではなく、マイクロソフトやコミュニティによって既に作成されているさまざまなシナリオにアクセスできます。  これらのシナリオを変更しないでそのまま使用することも、特定の要件に合うように編集することもできます。

Runbook は [Runbook](#runbooks-in-runbook-gallery) ギャラリーから、モジュールは [PowerShell ギャラリー](#modules-in-powerShell-gallery)から入手できます。  開発したシナリオを共有することにより、コミュニティに貢献することもできます。

## <a name="runbooks-in-runbook-gallery"></a>Runbook ギャラリーでの Runbook
[Runbook ギャラリー](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation)では、Microsoft やコミュニティからさまざまな Runbook が提供されており、それを Azure Automation にインポートできます。 [TechNet スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter/site/upload)でホストされているギャラリーから Runbook をダウンロードすることも、Azure クラシック ポータルまたは Azure ポータルでギャラリーから Runbook を直接インポートすることもできます。

Runbook ギャラリーから直接インポートできるのは、Azure クラシック ポータルまたは Azure ポータルを使用した場合のみです。 Windows PowerShell を使用してこの機能を実行することはできません。

> [!NOTE]
> Runbook ギャラリーから取得した Runbook は、内容を検証し、運用環境でインストールおよび実行するときは細心の注意を払う必要があります。
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-classic-portal"></a>Azure クラシック ポータルで Runbook ギャラリーから Runbook をインポートするには
1. Azure Portal で、**[新規]**、**[App Services]**、**[Automation]**、**[Runbook]**、**[ギャラリーから]** の順にクリックします。
2. カテゴリを選択して関連する Runbook を表示し、Runbook を選択して詳細を表示します。 目的の Runbook を選択するときは、右矢印ボタンをクリックします。
   
    ![Runbook ギャラリー](media/automation-runbook-gallery/runbook-gallery.png)
3. Runbook の内容を確認し、説明の要件に注意します。 終了したら右矢印ボタンをクリックします。
4. Runbook の詳細を入力し、チェック マーク ボタンをクリックします。 Runbook の名前は既に設定されています。
5. Runbook が Automation アカウントの **[Runbook]** タブに表示されます。

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Azure ポータルで Runbook ギャラリーから Runbook をインポートするには
1. Azure ポータルで、Automation アカウントを開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[ギャラリーの参照]** ボタンをクリックします。
   
    ![[ギャラリーの参照] ボタン](media/automation-runbook-gallery/browse-gallery-button.png)
4. 必要なギャラリー項目を探し、選択して詳細を表示します。
   
    ![[ギャラリーの参照]](media/automation-runbook-gallery/browse-gallery.png)
5. **[ソース プロジェクトの表示]** をクリックして、 [TechNet スクリプト センター](http://gallery.technet.microsoft.com/)の項目を表示します。
6. 項目をインポートするには、項目クリックして詳細を表示し、 **[インポート]** ボタンをクリックします。
   
    ![[インポート] ボタン](media/automation-runbook-gallery/gallery-item-detail.png)
7. 必要に応じて Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。
8. Runbook が Automation アカウントの **[Runbook]** タブに表示されます。

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Runbook ギャラリーへの Runbook の追加
他のユーザーにも役に立つと思われる Runbook を Runbook ギャラリーに追加してください。  [Runbook をスクリプト センターにアップロード](http://gallery.technet.microsoft.com/site/upload) して追加するときは、次の詳細を考慮してください。

* Runbook をウィザードに表示するには、**カテゴリ**として *Windows Azure* を指定し、**サブカテゴリ**として *Automation* を指定する必要があります。  
* アップロードは、単一の .ps1 ファイルまたは .graphrunbook ファイルである必要があります。  Runbook でモジュール、子 Runbook、または資産が必要な場合は、送信の説明および Runbook のコメント セクションで列記する必要があります。  複数の Runbook を必要とするシナリオがある場合は、各 Runbook を個別にアップロードし、それぞれの説明に関連する Runbook の名前を列記します。 同じカテゴリに表示されるように同じタグを使用していることを確認します。 シナリオが動作するためには他の Runbook が必要であることをユーザーが知るには、説明を読む必要があります。
* (グラフィカル ワークフローではなく) **グラフィック Runbook** を発行する場合は、"GraphicalPS" タグを追加します。 
* **[コード セクションの挿入]** アイコンを使用して、PowerShell または PowerShell ワークフローのコード スニペットを説明に挿入します。
* アップロードの概要が Runbook ギャラリーの結果に表示されるので、ユーザーが Runbook の機能を特定するのに役立つ詳細な情報を提供する必要があります。
* 次のタグから 1 ～ 3 個をアップロードに割り当てる必要があります。  ウィザードでは、Runbook はそのタグに一致するカテゴリの下に表示されます。  この一覧にないタグは、ウィザードによって無視されます。 一致するタグを指定しないと、Runbook はその他のカテゴリに表示されます。
  
  * バックアップ
  * 容量管理
  * Change Control
  * コンプライアンス
  * Dev / Test Environments
  * 障害復旧
  * 監視
  * Patching
  * プロビジョニング
  * 修復
  * VM Lifecycle Management
* Automation はギャラリーを 1 時間に 1 回更新するので、投稿物がすぐに表示されないことがあります。

## <a name="modules-in-powershell-gallery"></a>PowerShell ギャラリーのモジュール
PowerShell モジュールには Runbook で使用できるコマンドレットが含まれ、Azure Automation でインストールできる既存のモジュールを [PowerShell ギャラリー](http://www.powershellgallery.com)から入手できます。  このギャラリーは Azure ポータルから起動でき、Azure Automation に直接インストールするか、ダウンロードして手動でインストールすることができます。  Azure クラシック ポータルからは直接モジュールをインストールすることはできませんが、他のモジュールと同じように、ダウンロードしてインストールすることはできます。

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Azure ポータルで Automation モジュール ギャラリーからモジュールをインポートするには
1. Azure ポータルで、Automation アカウントを開きます。
2. **[アセット]** タイルをクリックして、資産の一覧を開きます。
3. **[モジュール]** タイルをクリックして、モジュールの一覧を開きます。
4. **[ギャラリーを参照]** ボタンをクリックして、ギャラリーの参照ブレードを起動します。
   
    ![モジュール ギャラリー](media/automation-runbook-gallery/modules-blade.png) <br>
5. ギャラリーの参照ブレードを起動した後は、次のフィールドで検索できます。
   
   * モジュール名
   * タグ
   * 作成者
   * コマンドレット/DSC リソース名
6. 目的のモジュールを探し、選択して詳細を表示します。  
   特定のモジュールにドリル ダウンする場合は、PowerShell ギャラリーに戻るリンク、必要な依存関係、およびモジュールが含むコマンドレット/DSC リソースのすべてを含むモジュールの詳細を表示できます。
   
    ![PowerShell モジュールの詳細](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. モジュールを Azure Automation に直接インストールするには、 **[インポート]** ボタンをクリックします。
   
    ![[モジュールのインポート] ボタン](media/automation-runbook-gallery/module-import-button.png)
8. [インポート] ボタンをクリックすると、インポートしようとしているモジュール名が表示されます。 すべての依存関係がインストールされている場合は、 **[OK]** ボタンがアクティブになります。 依存関係が存在しない場合は、このモジュールをインポートする前に、その依存関係をインポートする必要があります。
9. **[OK]** をクリックしてモジュールをインポートすると、モジュールのブレードが開きます。 Azure Automation がモジュールをアカウントにインポートする場合は、モジュールとコマンドレットについてのメタデータが抽出されます。
   
    ![[モジュールのインポート] ブレード](media/automation-runbook-gallery/module-import-blade.png)
   
    各アクティビティを抽出する必要があるため、数分かかる場合があります。
10. モジュールがデプロイされていることを示す通知、および完了したことを示す通知を受け取ります。
11. モジュールがインポートされた後は、使用可能なアクティビティが表示され、Runbook と Desired State Configuration でそのリソースを使用できます。

## <a name="requesting-a-runbook-or-module"></a>Runbook またはモジュールの要求
[ユーザーの声](https://feedback.azure.com/forums/246290-azure-automation/)に要求を送信できます。  Runbook の作成について支援が必要な場合、または PowerShell について質問がある場合は、 [フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)に質問を投稿してください。

## <a name="next-steps"></a>次のステップ
* Runbookの使用を開始するには、「 [Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)
* Runbook 用の PowerShell と PowerShell ワークフローとの違いについては、「 [PowerShell ワークフローについて](automation-powershell-workflow.md)




<!--HONumber=Dec16_HO3-->


