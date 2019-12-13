---
title: アマゾン ウェブ サービスへの VM のデプロイの自動化
description: この記事は、Azure Automation を使用して、Amazon Web Service VM の作成を自動化する方法を示します
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2a58d3e79301f277143d8c4b6e810a377a211b9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849634"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation シナリオ - AWS 仮想マシンのプロビジョニング
この記事では、Azure Automation を利用して仮想マシンを Amazon Web Service (AWS) サブスクリプションにプロビジョニングし、その VM に特別な名前を付ける方法 (AWS では "タグ付け" と呼びます) について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Azure Automation アカウントと AWS サブスクリプションが必要です。 Azure Automation アカウントの設定と、AWS サブスクリプションの資格情報による構成については、「[アマゾン ウェブ サービスによる Runbook の認証](automation-config-aws-account.md)」を参照してください。 先に進む前に、このアカウントを作成するか、AWS サブスクリプションの資格情報で更新してください。このアカウントは以降の手順で参照します。

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell モジュールをデプロイする
VM プロビジョニング Runbook は、AWS PowerShell モジュールを利用して処理を実行します。 次の手順を実行して、AWS サブスクリプションの資格情報で構成した Automation アカウントにモジュールを追加してください。  

1. Web ブラウザーを開き、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AWSPowerShell/)に移動し、 **[Deploy to Azure Automation (Azure Automation にデプロイする)]** ボタンをクリックします。<br><br> ![AWS PS モジュールのインポート](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure ログイン ページが表示され、認証が終わると Azure Portal に移動し、次のページが表示されます。<br><br> ![[モジュールのインポート] ページ](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 使用する Automation アカウントを選択し、 **[OK]** をクリックしてデプロイを開始します。

   > [!NOTE]
   > PowerShell モジュールを Azure Automation にインポートしているときに、コマンドレットも抽出されます。これらのアクティビティは、モジュールのインポートとコマンドレットの抽出が完全に終了するまでは表示されません。 このプロセスには数分かかることがあります。  
   > <br>

1. Azure Portal で、手順 3. で参照した Automation アカウントを開きます。
2. **[資産]** タイルをクリックし、 **[資産]** ウィンドウで **[モジュール]** タイルを選択します。
3. **[モジュール]** ページで、一覧に **AWSPowerShell** モジュールが表示されます。

## <a name="create-aws-deploy-vm-runbook"></a>AWS デプロイ VM Runbook を作成する
AWS PowerShell モジュールのデプロイが終わったら、PowerShell スクリプトを使用して AWS への仮想マシンのプロビジョニングを自動化する Runbook を作成できます。 次の手順は、Azure Automation でネイティブ PowerShell スクリプトを利用する方法を示しています。  

> [!NOTE]
> このスクリプトに関する他のオプションと情報については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-AwsVM/)を参照してください。
> 

1. PowerShell セッションを開き、次のように入力して、PowerShell ギャラリーから PowerShell スクリプト New-AwsVM をダウンロードします。<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure Portal から Automation アカウントを開き、左側にある **[プロセス オートメーション]** セクションの **[Runbook]** を選択します。  
3. **[Runbook]** ページで、 **[Runbook の追加]** を選択します。
4. **[Runbook の追加]** ウィンドウで、 **[簡易作成]** を選択します (新しい Runbook を作成します)。
5. **Runbook** のプロパティ ウィンドウで、[名前] ボックスに Runbook の名前を入力し、 **[Runbook の種類]** ボックスの一覧から **[PowerShell]** を選択した後、 **[作成]** をクリックします。<br><br> ![Runbook の作成ウィンドウ](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. [PowerShell Runbook の編集] ページが表示されたら、PowerShell スクリプトをコピーして Runbook 作成キャンバスに貼り付けます。<br><br> ![Runbook PowerShell スクリプト](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > このサンプル PowerShell スクリプトを使用するときは、次の点に注意してください。
    > 
    > * この Runbook には、パラメーターの既定値が多数含まれています。 すべての既定値に目を通して適宜変更してください。
    > * AWS 資格情報を **AWScred**以外の資格情報資産の名前で保存している場合は、スクリプトの 57 行をその名前で更新する必要があります。  
    > * PowerShell の AWS CLI コマンドを使用する場合、特にこのサンプル Runbook では、AWS リージョンを指定する必要があります。 指定しなかった場合、コマンドレットは失敗します。 詳細については、AWS Tools for PowerShell ドキュメントの AWS トピック「 [Specify AWS Region (AWS リージョンを指定する)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 」を参照してください。  
    >

7. AWS サブスクリプションからイメージ名の一覧を取得するため、PowerShell ISE を起動し、AWS PowerShell モジュールをインポートします。 ISE 環境の **Get-AutomationPSCredential** を **AWScred = Get-Credential** に置き換えることで、AWS に対する認証を実行します。 これにより、資格情報の入力を求めるプロンプトが表示され、ユーザー名として**アクセス キー ID** を、パスワードとして**シークレット アクセス キー**を指定できます。 次の例を見てください。  

        ```powershell
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
        ```
        
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

1. Runbook を開始するために **[開始]** をクリックし、[Runbook の開始] ウィンドウが開いたら **[OK]** をクリックします。
2. [Runbook の開始] ウィンドウで、 **[VMname]** を指定します。 スクリプトに事前構成してある他のパラメーターの既定値をそのまま使用します。 **[OK]** をクリックして Runbook ジョブを開始します。<br><br> ![New-AwsVM runbook の開始](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. 作成した Runbook ジョブのジョブ ウィンドウが開きます。 このウィンドウを閉じます。
4. Runbook ジョブ ページで **[すべてのログ]** タイルを選択することで、ジョブの進行状況と出力**ストリーム**を確認できます。<br><br> ![Stream output](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. VM がプロビジョニングされたことを確認するために AWS Management Console にログインします (まだログインしていない場合)。<br><br> ![AWS console deployed VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>次の手順
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)
* PowerShell Workflow Runbook を初めて利用するときは、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
* PowerShell スクリプトのサポート機能の詳細については、「 [Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


