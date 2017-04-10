---
title: "Azure Automation の一般的な問題のトラブルシューティング | Microsoft Docs"
description: "この記事には、Azure Automation の一般的なエラーのトラブルシューティングを行って修正する際に役立つ情報が記載されています。"
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "Automation のエラー, トラブルシューティング, 問題"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2017
ms.author: sngun; v-reagie
translationtype: Human Translation
ms.sourcegitcommit: a8ecffbc5f9c7e2408708d59459a0d39e59d6e1e
ms.openlocfilehash: 3e4a4b431e26e58a0af1eee182fded51b6618fac


---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Azure Automation の一般的な問題のトラブルシューティング 
この記事では、Azure Automation で発生することがある一般的なエラーのトラブルシューティングのヘルプを提供し、それらのエラーの考えられる解決策を提案します。

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook の使用時に発生する認証エラー
### <a name="scenario-sign-in-to-azure-account-failed"></a>シナリオ: Azure アカウントにサインインできない
**エラー:** Add-AzureAccount コマンドレットまたは Login-AzureRmAccount コマンドレットの使用時に「Unknown_user_type: Unknown User Type (未知のユーザー タイプ)」というエラーが発生します。

**エラーの理由:** このエラーは、資格情報アセット名が無効な場合、または Automation 資格情報アセットのセットアップに使用したユーザー名とパスワードが無効な場合に発生します。

**トラブルシューティングのヒント:** 次の手順で原因を突き止めます。  

1. Azure に接続するために使用する Automation 資格情報資産名で、 **@** 文字などの特殊文字が使用されていないことを確認します。  
2. ローカル PowerShell ISE エディターで、Azure Automation に保存されているユーザー名とパスワードを使用できることを確認します。 その際、PowerShell ISE で次のコマンドレットを実行します。  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. ローカルで認証に失敗した場合、Azure Active Directory 資格情報が正しく設定されていないことになります。 Azure Active Directory アカウントを正しく設定する方法については、「 [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 」 (Azure Active Directory を使用して Azure を認証する) というブログ投稿を参照してください。  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>シナリオ: Azure サブスクリプションが見つかりません
**エラー:** Select-AzureSubscription コマンドレットまたは AzureRmSubscription コマンドレットの使用時に「The subscription named ``<subscription name>`` cannot be found (<サブスクリプション名> という名前のサブスクリプションが見つかりません)」というエラーが発生します。

**エラーの理由:** このエラーは、サブスクリプション名が無効な場合、またはサブスクリプションの詳細を取得しようとしている Azure Active Directory ユーザーがサブスクリプションの管理者として構成されていない場合に発生します。

**トラブルシューティングのヒント:** Azure で正しく認証され、選択しようとしているサブスクリプションにアクセスできることを次の手順で確認します。  

1. **Select-AzureSubscription** コマンドレットを実行する前に必ず **Add-AzureAccount** を実行してください。  
2. それでもエラー メッセージが表示される場合は、**Add-AzureAccount** コマンドレットの後ろに **Get-AzureSubscription** コマンドレットを追加するというコードの変更を行った後、そのコードを実行します。  Get-AzureSubscription の出力にサブスクリプション詳細が含まれることを確認します。  

   * 出力にサブスクリプション詳細が含まれない場合、サブスクリプションが初期化されていません。  
   * 出力にサブスクリプション詳細が含まれる場合は、 **Select-AzureSubscription** コマンドレットで正しいサブスクリプション名または ID を使用していることを確認します。   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>シナリオ: 多要素認証が有効になっているために Azure に対する認証が失敗した
**エラー:** Azure のユーザー名とパスワードで Azure に対して認証するとき、「Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required (強力な認証記録 (確認) が必要です)」というエラーが発生します。

**エラーの理由:** Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。  代わりに、証明書またはサービス プリンシパルを利用して Azure に対して認証する必要があります。

**トラブルシューティングのヒント:** Azure Service Management コマンドレットで証明書を使用する方法については、[証明書を作成し、追加して Azure サービスを管理する](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)方法に関するページを参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する方法については、[Azure ポータルでサービス プリンシパルを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)方法に関する記事と [Azure Resource Manager でサービス プリンシパルを認証する](../azure-resource-manager/resource-group-authenticate-service-principal.md)方法に関する記事を参照してください。

## <a name="common-errors-when-working-with-runbooks"></a>Runbook の使用時に発生する一般的なエラー
### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>シナリオ: 逆シリアル化されたオブジェクトであるため、Runbook が失敗する
**エラー:** Runbook が失敗し、「Cannot bind parameter ``<ParameterName>``. Cannot convert the ``<ParameterType>`` value of type Deserialized ``<ParameterType>`` to type ``<ParameterType>`` (パラメーター <パラメーター名> をバインドできません。逆シリアル化型 <パラメーター型> の値 <パラメーター値> を型 <パラメーター型> に変換できません)」というエラーが発生します。

**エラーの理由:** Runbook が PowerShell ワークフローの場合、ワークフローが中断された場合に Runbook の状態を維持できるように、複雑なオブジェクトが逆シリアル化形式で保存されます。  

**トラブルシューティングのヒント:**  
次の&amp;3; つの解決策のいずれでもこの問題は解決されます。

1. コマンドレット間で複雑なオブジェクトをパイプ処理する場合、これらのコマンドレットを InlineScript でラップします。  
2. オブジェクト全体を渡すのではなく、複雑なオブジェクトから、必要な名前または値を渡します。  
3. PowerShell ワークフロー Runbook ではなく PowerShell Runbook を使用します。  

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>シナリオ: 割り当てられたクォータを超えているために Runbook ジョブが失敗した
**エラー:** Runbook ジョブが失敗し、「The quota for the monthly total job run time has been reached for this subscription (このサブスクリプションの毎月の合計ジョブ実行時間のクォータに到達しました)」というエラーが発生します。

**エラーの理由:** ジョブの実行がアカウントの 500 分の無料クォータを超えるとこのエラーが発生します。 このクォータは、ジョブをテストする、ポータルからジョブを開始する、Webhook でジョブを実行する、Azure ポータルまたはデータセンターを利用して実行するジョブをスケジュールするなど、あらゆる種類のジョブ実行タスクに適用されます。 Automation の料金については、「 [Automation の料金](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

**トラブルシューティングのヒント:** 毎月 500 分以上処理する場合、サブスクリプション レベルを Free から Basic に変更する必要があります。 次の手順で Basic レベルにアップグレードできます。  

1. Azure サブスクリプションにサインインします。  
2. アップグレードする Automation アカウントを選択します。  
3. **[設定]** > 、**[価格レベルと使用状況]** > 、**[価格レベル]** の順に選択します。  
4. **[価格レベルの選択]** ブレードで、**[Basic]** を選択します。    

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>シナリオ: Runbook の実行時にコマンドレットが認識されない
**エラー:** Runbook ジョブが失敗し、「``<cmdlet name>``: The term ``<cmdlet name>`` is not recognized as the name of a cmdlet, function, script file, or operable program (<コマンドレット名> という用語はコマンドレット、関数、スクリプト ファイル、操作可能プログラムとして認識されません)」というエラーが発生します。

**エラーの理由:** このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。  この原因としては、コマンドレットが含まれるモジュールがアカウントにない、Runbook 名に名前の競合がある、コマンドレットが別のモジュールにも存在し、Automation が名前を解決できないなどが考えられます。

**トラブルシューティングのヒント:** 次の解決策のいずれでもこの問題は解決されます。  

* コマンドレット名を正しく入力していることを確認します。  
* Automation アカウントにコマンドレットが存在し、競合がないことを確認します。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索し、**Get-Command ``<CommandName>``** を実行します。  コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、それをキャンバスに追加し、Runbook に設定されている有効なパラメーターを使用していることを確認します。  
* 名前が競合し、コマンドレットが&2; つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで解決できます。 たとえば、**ModuleName\CmdletName** を使用できます。  
* ハイブリッド worker グループでオンプレミスの runbook を実行する場合は、モジュール/コマンドレットがハイブリッド worker をホストしているコンピューターにインストールされていることを確認します。

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>シナリオ: Runbook を長時間実行するといつも次の例外で失敗する: ジョブは同じチェックポイントから繰り返し削除されたため、実行を継続できません。
**エラーの理由:** これは Azure Automation 内のプロセスの "フェア シェア" 監視のための設計による動作です。3 時間以上実行している Runbook は自動的に中断されます。 ただし、返されるエラー メッセージでは "次" のオプションは提供されません。 さまざまな理由から Runbook は中断されることがあります。 ほとんどの場合、中断はエラーのために発生します。 たとえば、Runbook のキャッチされない例外、ネットワーク障害、Runbook を実行している Runbook Worker でのクラッシュなどはすべて、Runbook が中断する原因になり、再開時には最後のチェックポイントから開始します。

**トラブルシューティングのヒント:** この問題を回避するための解決策では、ワークフローでのチェックポイントを使用します。  詳細については、「[PowerShell ワークフローについての説明](automation-powershell-workflow.md#checkpoints)」を参照してください。  "フェア シェア" およびチェックポイントの詳細については、ブログ記事「[Using Checkpoints in Runbooks](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/)」(Runbook でのチェックポイントの使用) を参照してください。

## <a name="common-errors-when-importing-modules"></a>モジュールのインポート時に発生する一般的なエラー
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>シナリオ: モジュールがインポートに失敗するか、インポート後、コマンドレットを実行できない
**エラー:** モジュールがインポートに失敗するか、成功してもコマンドレットが抽出されません。

**エラーの理由:** モジュールが正常に Azure Automation にインポートできない一般的な理由には次が考えられます。  

* 構造が Automation で必要とされる構造と一致しません。  
* Automation アカウントにデプロイされていない別のモジュールにモジュールが依存しています。  
* モジュールのフォルダーにその依存関係がありません。  
* モジュールのアップロードに **New-AzureRmAutomationModule** コマンドレットを使用していますが、完全なストレージ パスを与えていないか、公共でアクセスできる URL でモジュールを読み込んでいません。  

**トラブルシューティングのヒント:**  
次の解決策のいずれでもこの問題は解決されます。  

* モジュールの形式が次のようになっていることを確認します。  
  ModuleName.Zip **->** モジュール名またはバージョン番号 **->** (ModuleName.psm1、ModuleName.psd1)
* .psd1 ファイルを開き、モジュールに依存関係があるかどうかを確認します。  依存関係がある場合、それらのモジュールを Automation アカウントにアップロードします。  
* 参照される .dll がモジュール フォルダーにあることを確認します。  

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Desired State Configuration (DSC) の使用時に発生する一般的なエラー
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>シナリオ: ノードが "失敗" 状態になり、"見つかりませんでした" というエラーが表示される
**エラー:** "有効な構成 ``<guid>`` が見つからないため、サーバー https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction から操作を取得できませんでした" というエラーが記載された "**失敗**" 状態のレポートがノードで生成されます。

**エラーの理由:** 通常、このエラーは、ノードがノード構成の名前 (ABC.WebServer など) ではなく構成名 (ABC など) に割り当てられている場合に発生します。  

**トラブルシューティングのヒント:**  

* ノードに "構成名" ではなく、"ノード構成名" が割り当てられていることを確認してください。  
* ノード構成は、Azure ポータルまたは PowerShell コマンドレットを使用してノードに割り当てることができます。

  * Azure ポータルを使用してノードにノード構成を割り当てるには、**[DSC ノード]** ブレードを開き、ノードを選択し、**[ノード構成の割り当て]** ボタンをクリックします。  
  * PowerShell コマンドレットを使用してノードにノード構成を割り当てるには、 **Set-AzureRmAutomationDscNode** コマンドレットを使用します。

### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>シナリオ: 構成のコンパイルを実行しても、ノード構成 (MOF ファイル) が生成されなかった
**エラー:** DSC のコンパイル ジョブが中断され、「Compilation completed successfully, but no node configuration .mofs were generated (コンパイルは正常に完了しましたが、ノード構成 .mof は生成されませんでした)」というエラーが表示されます。

**エラーの理由:** DSC 構成の **Node** キーワードに続く式の評価結果が $null の場合、ノード構成は生成されません。    

**トラブルシューティングのヒント:**  
次の解決策のいずれでもこの問題は解決されます。  

* 構成定義内の **Node** キーワードに続く式の評価結果が $null になっていないことを確認します。  
* 構成のコンパイル時に ConfigurationData を渡す場合は、 [ConfigurationData](automation-dsc-compile.md#configurationdata)から、構成に必要な期待値を渡すようにしてください。

### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>シナリオ: DSC ノードのレポートが "処理中" の状態で停止する
**エラー:** DSC エージェントによって、「指定されたプロパティ値のインスタンスが見つかりません」と出力されます。

**エラーの理由:** WMF のバージョンをアップグレードした結果、WMI が破損しています。  

**トラブルシューティングのヒント:** この問題を解決するには、[DSC の既知の問題と制限](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) に関する記事にある手順に従ってください。

### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>シナリオ: DSC 構成で資格情報が使用できない
**エラー:** DSC コンパイル ジョブが中断され、「型 ``<some resource name>`` のプロパティ 'Credential' の処理中に System.InvalidOperationException エラーが発生しました: 暗号化されたパスワードを変換してプレーンテキストとして格納することが許可されるのは、PSDscAllowPlainTextPassword が true に設定されている場合だけです」というエラーが表示されます。

**エラーの理由:** 構成に資格情報を使用したが、ノード構成ごとに **PSDscAllowPlainTextPassword** を true に設定するための適切な **ConfigurationData** を指定していませんでした。  

**トラブルシューティングのヒント:**  

* 上記の構成の各ノード構成について **PSDscAllowPlainTextPassword** を true に設定するために、適切な **ConfigurationData** を渡してください。 詳細については、 [Azure Automation DSC の資産](automation-dsc-compile.md#assets)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
上記のトラブルシューティング手順を実行しても解決しない場合は、以下の他のサポート オプションを確認してください。

* Azure エキスパートに支援を要請する。 [MSDN の Azure フォーラムまたは Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)に問題を投稿してください。
* Azure サポート インシデントを送信する。 [Azure サポート サイト](https://azure.microsoft.com/support/options/)にアクセスし、**[テクニカル/課金サポート]** の **[サポートの要求]** をクリックしてください。
* Azure Automation Runbook ソリューションや統合モジュールを探している場合は、 [スクリプト センター](https://azure.microsoft.com/documentation/scripts/) にスクリプトの要求を投稿することができます。
* Azure Automation に関するフィードバックや機能に関するご要望は、 [User Voice](https://feedback.azure.com/forums/34192--general-feedback)にお寄せください。



<!--HONumber=Jan17_HO4-->


