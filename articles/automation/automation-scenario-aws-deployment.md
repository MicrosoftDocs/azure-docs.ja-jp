---
title: "Amazon Web Services への VM のデプロイの自動化 | Microsoft Docs"
description: "この記事は、Azure Automation を使用して、Amazon Web Service VM の作成を自動化する方法を示します"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: tiandert; bwren
translationtype: Human Translation
ms.sourcegitcommit: ff1acafaacc40dd8a04b008df7cd479c811a7af0
ms.openlocfilehash: 62b6c09f749aa36307206e23d36bd86b5b8ce455


---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation シナリオ - AWS 仮想マシンのプロビジョニング
この記事では、Azure Automation を利用して仮想マシンを Amazon Web Service (AWS) サブスクリプションにプロビジョニングし、その VM に特別な名前を付ける方法 (AWS では "タグ付け" と呼びます) について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Azure Automation アカウントと AWS サブスクリプションが必要です。 Azure Automation アカウントの設定と、AWS サブスクリプションの資格情報による構成については、「[アマゾン ウェブ サービスによる Runbook の認証](automation-sec-configure-aws-account.md)」を参照してください。  先に進む前に、このアカウントを作成するか、AWS サブスクリプションの資格情報で更新してください。このアカウントは以降の手順で参照します。

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell モジュールをデプロイする
VM プロビジョニング Runbook は、AWS PowerShell モジュールを利用して処理を実行します。 次の手順を実行して、AWS サブスクリプションの資格情報で構成した Automation アカウントにモジュールを追加してください。  

1. Web ブラウザーを開き、[PowerShell ギャラリー](http://www.powershellgallery.com/packages/AWSPowerShell/)に移動し、**[Deploy to Azure Automation (Azure Automation にデプロイする)]** ボタンをクリックします。<br><br> ![AWS PS モジュールのインポート](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure ログイン ページが表示され、認証が終わると Azure Portal に移動し、次のブレードが表示されます。<br><br> ![[モジュールのインポート] ブレード](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. **[リソース グループ]** ボックスの一覧からリソース グループを選択し、[パラメーター] ブレードで次の情報を指定します。
   
   * **[New or Existing Automation Account (string) (新規または既存の Automation アカウント (文字列))]** ボックスの一覧から **[既存]** を選択します。  
   * **[Automation アカウント名 (文字列)]** ボックスに、AWS サブスクリプションの資格情報を含む Automation アカウントの名前を正確に入力します。  たとえば、 **AWSAutomation**という名前の専用アカウントを作成した場合、このボックスにはその名前を入力します。
   * **[Automation Account Location (Automation アカウントの場所)]** ボックスの一覧から適切なリージョンを選択します。
4. 必要な情報の入力が完了したら、 **[作成]**をクリックします。
   
   > [!NOTE]
   > PowerShell モジュールを Azure Automation にインポートしているときに、コマンドレットも抽出されます。これらのアクティビティは、モジュールのインポートとコマンドレットの抽出が完全に終了するまでは表示されません。 このプロセスには数分かかることがあります。  
   > <br>
   > 
   > 
5. Azure ポータルで、手順 3 で参照した Automation アカウントを開きます。
6. **[資産]** タイルをクリックし、**[資産]** ブレードで **[モジュール]** タイルを選択します。
7. **[モジュール]** ブレードで、一覧に **AWSPowerShell** モジュールが表示されます。

## <a name="create-aws-deploy-vm-runbook"></a>AWS デプロイ VM Runbook を作成する
AWS PowerShell モジュールのデプロイが終わったら、PowerShell スクリプトを使用して AWS への仮想マシンのプロビジョニングを自動化する Runbook を作成できます。 次の手順は、Azure Automation でネイティブ PowerShell スクリプトを利用する方法を示しています。  

> [!NOTE]
> このスクリプトに関する他のオプションと情報については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript)を参照してください。
> 

1. PowerShell セッションを開き、次のように入力して、PowerShell ギャラリーから PowerShell スクリプト New-AwsVM をダウンロードします。<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure Portal で Automation アカウントを開き、**[Runbook]** タイルをクリックします。  
3. **[Runbook]** ブレードで、**[Runbook の追加]** を選択します。
4. **[Runbook の追加]** ブレードで、**[簡易作成]** を選択します (新しい Runbook を作成します)。
5. **Runbook** のプロパティ ブレードで、[名前] ボックスに Runbook の名前を入力し、**[Runbook の種類]** ボックスの一覧から **[PowerShell]** を選択した後、**[作成]** をクリックします。<br><br> ![[モジュールのインポート] ブレード](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. [PowerShell Runbook の編集] ブレードが表示されたら、PowerShell スクリプトをコピーして Runbook 作成キャンバスに貼り付けます。<br><br> ![Runbook PowerShell スクリプト](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > このサンプル PowerShell スクリプトを使用するときは、次の点に注意してください。
    > 
    > * この Runbook には、パラメーターの既定値が多数含まれています。 すべての既定値に目を通して適宜変更してください。
    > * AWS 資格情報を **AWScred**以外の資格情報資産の名前で保存している場合は、スクリプトの 57 行をその名前で更新する必要があります。  
    > * PowerShell の AWS CLI コマンドを使用する場合、特にこのサンプル Runbook では、AWS リージョンを指定する必要があります。 指定しなかった場合、コマンドレットは失敗します。  詳細については、AWS Tools for PowerShell ドキュメントの AWS トピック「 [Specify AWS Region (AWS リージョンを指定する)](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 」を参照してください。  
    >

7. AWS サブスクリプションからイメージ名の一覧を取得するため、PowerShell ISE を起動し、AWS PowerShell モジュールをインポートします。  ISE 環境の **Get-AutomationPSCredential** を **AWScred = Get-Credential** に置き換えることで、AWS に対する認証を実行します。  これにより、資格情報の入力を求めるプロンプトが表示され、ユーザー名として**アクセス キー ID** を、パスワードとして**シークレット アクセス キー**を指定できます。  次の例を見てください。  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    次の出力が返されます。<br><br>
   ![AWS イメージの取得](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Runbook で **$InstanceType**として参照される Automation 変数に、いずれかのイメージ名をコピーして貼り付けます。 この例では、無料の AWS 階層型サブスクリプションを使用しているため、サンプル Runbook では **t2.micro** を使用します。  
9. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

### <a name="testing-the-aws-vm-runbook"></a>AWS VM Runbook のテスト
Runbook のテストに進む前に、確認する必要がある事柄がいくつかあります。 具体的には次の処理が行われます。  

* AWS に対して認証される **AWScred** という資産を作成したこと、または使用中の資格情報資産の名前を参照するようにスクリプトを更新したこと。    
* AWS PowerShell モジュールを Azure Automation にインポートしたこと。  
* 新しい Runbook を作成し、パラメーターの値を確認して適宜更新したこと。  
* Runbook 設定の **[ログ記録とトレース]** で **[詳細レコードの記録]** と (必要に応じて) **[進行状況レコードの記録]** を **[オン]** に設定したこと。<br><br> ![Runbook のログ記録とトレース](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Runbook を開始するために **[開始]** をクリックし、[Runbook の開始] ブレードが開いたら **[OK]** をクリックします。
2. [Runbook の開始] ブレードで、**[VMname]** を指定します。  スクリプトに事前構成してある他のパラメーターの既定値をそのまま使用します。  **[OK]** をクリックして Runbook ジョブを開始します。<br><br> ![New-AwsVM runbook の開始](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 作成した Runbook ジョブのジョブ ウィンドウが開かれます。 このウィンドウを閉じます。
4. Runbook ジョブ ブレードで **[すべてのログ]** タイルを選択することで、ジョブの進行状況と出力**ストリーム**を確認できます。<br><br> ![Stream output](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. VM がプロビジョニングされたことを確認するために AWS Management Console にログインします (まだログインしていない場合)。<br><br> ![AWS console deployed VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>次のステップ
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)
* PowerShell Workflow Runbook を初めて利用するときは、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
* PowerShell スクリプトのサポート機能の詳細については、「 [Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)




<!--HONumber=Nov16_HO3-->


