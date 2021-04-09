---
title: Azure Automation Runbook を使用してアマゾン ウェブ サービスの VM をデプロイする
description: この記事では、アマゾン ウェブ サービスの VM の作成を自動化する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 03f1f1659d120a2aa1b827063cb2bc84138f3655
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896072"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Runbook を使用してアマゾン ウェブ サービスの VM をデプロイする

この記事では、Azure Automation を利用して仮想マシンを Amazon Web Service (AWS) サブスクリプションにプロビジョニングし、その VM に特別な名前を付ける方法 (AWS では "タグ付け" と呼びます) について説明します。

## <a name="prerequisites"></a>前提条件

Azure Automation アカウントと Amazon Web Services (AWS) サブスクリプションが必要です。 Azure Automation アカウントの設定と、AWS サブスクリプションの資格情報による構成については、「[アマゾン ウェブ サービスによる Runbook の認証](automation-config-aws-account.md)」を参照してください。 先に進む前に、このアカウントを作成するか、AWS サブスクリプションの資格情報で更新してください。このアカウントは以降のセクションで参照します。

## <a name="deploy-amazon-web-services-powershell-module"></a>Amazon Web Services PowerShell モジュールをデプロイする

VM プロビジョニング Runbook は、AWS PowerShell モジュールを使用して処理を実行します。 次の手順を使用して、AWS サブスクリプションの資格情報で構成した Automation アカウントにモジュールを追加してください。  

1. Web ブラウザーを開き、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AWSPowerShell/)に移動し、 **[Deploy to Azure Automation (Azure Automation にデプロイする)]** ボタンをクリックします。<br><br> ![AWS PS モジュールのインポート](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Azure ログイン ページが表示され、認証が終わると Azure Portal に移動し、次のページが表示されます。<br><br> ![[モジュールのインポート] ページ](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. 使用する Automation アカウントを選択し、 **[OK]** をクリックしてデプロイを開始します。

   > [!NOTE]
   > Azure Automation は、PowerShell モジュールをインポートするときに、コマンドレットを抽出します。 これらのアクティビティは、Automation でのモジュールのインポートが完全に完了し、コマンドレットが抽出されるまで表示されません。 このプロセスには数分かかることがあります。  
   > <br>

1. Azure ポータルで、Automation アカウントを開きます。
2. **[資産]** タイルをクリックし、[資産] ウィンドウで **[モジュール]** を選択します。
3. [モジュール] ページの一覧に **AWSPowerShell** モジュールが表示されます。

## <a name="create-aws-deploy-vm-runbook"></a>AWS デプロイ VM Runbook を作成する

AWS PowerShell モジュールのデプロイが終わったら、PowerShell スクリプトを使用して AWS への仮想マシンのプロビジョニングを自動化する Runbook を作成できます。 次の手順は、Azure Automation でネイティブ PowerShell スクリプトを使用する方法を示しています。  

> [!NOTE]
> このスクリプトに関する他のオプションと情報については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-AwsVM/)を参照してください。
> 

1. PowerShell セッションを開き、次のコマンドを入力して、PowerShell ギャラリーから PowerShell スクリプト New-AwsVM をダウンロードします。<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Azure Portal から Automation アカウントを開き、 **[プロセス オートメーション]** の **[Runbook]** を選択します。  
3. [Runbook] ページで、 **[Runbook の追加]** を選択します。
4. [Runbook の追加] ウィンドウで、 **[簡易作成]** を選択して新しい Runbook を作成します。
5. [Runbook のプロパティ] ウィンドウで、Runbook の名前を入力します。
6. **[Runbook の種類]** ドロップダウン リストから、 **[PowerShell]** を選択し、 **[作成]** をクリックします。<br><br> ![Runbook の作成ウィンドウ](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. [PowerShell Runbook の編集] ページが表示されたら、PowerShell スクリプトをコピーして Runbook 作成キャンバスに貼り付けます。<br><br> ![Runbook PowerShell スクリプト](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    このサンプル PowerShell スクリプトを使用するときは、次の点に注意してください。

    * この Runbook には、パラメーターの既定値が多数含まれています。 すべての既定値に目を通して適宜変更してください。
    * AWS 資格情報を `AWScred` とは異なる資格情報資産の名前で保存している場合は、スクリプトの 57 行をその名前で更新する必要があります。  
    * PowerShell の AWS CLI コマンドを使用する場合、特にこのサンプル Runbook では、AWS リージョンを指定する必要があります。 指定しなかった場合、コマンドレットは失敗します。 詳細については、AWS Tools for PowerShell ドキュメントの AWS トピック「 [Specify AWS Region (AWS リージョンを指定する)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) 」を参照してください。  

8. AWS サブスクリプションからイメージ名の一覧を取得するため、PowerShell ISE を起動し、AWS PowerShell モジュールをインポートします。 ISE 環境内の `Get-AutomationPSCredential` を `AWScred = Get-Credential` に置き換えることで、AWS に対する認証を行います。 このステートメントにより、資格情報の入力を求めるプロンプトが表示され、ユーザー名としてアクセス キー ID を、パスワードとしてシークレット アクセス キーを指定できます。 

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
9. Runbook で `$InstanceType` として参照される Automation 変数に、いずれかのイメージ名をコピーして貼り付けます。 この例では、無料の AWS 階層型サブスクリプションを使用しているため、サンプル Runbook では **t2.micro** を使用します。  
10. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。

### <a name="test-the-aws-vm-runbook"></a>AWS VM Runbook をテストする

1. AWS に対して認証を行うための `AWScred` という資産が Runbook によって作成されることを確認するか、ご使用の資格情報の資産名を参照するようにスクリプトを更新します。    
2. 新しい Runbook を確認し、すべてのパラメーターの値が適宜更新されていることを確認します。
AWS PowerShell モジュールが Azure Automation にインポートされていることを確認します。  
3. Azure Automation で、Runbook 操作の **[ログとトレース]** に表示される **[詳細レコードの記録]** と (必要に応じて) **[進行状況レコードの記録]** を **[オン]** に設定します。<br><br> ![Runbook のログ記録とトレース](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)。  
4. **[開始]** をクリックして Runbook を開始し、[Runbook の開始] ウィンドウが開いたら **[OK]** をクリックします。
5. [Runbook の開始] ウィンドウで、VM の名前を指定します。 スクリプトに事前構成してある他のパラメーターの既定値をそのまま使用します。 **[OK]** をクリックして Runbook ジョブを開始します。<br><br> ![New-AwsVM runbook の開始](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. 作成した Runbook ジョブのジョブ ウィンドウが開きます。 このウィンドウを閉じます。
7. Runbook ジョブ ページで **[すべてのログ]** を選択することで、ジョブの進行状況と出力ストリームを確認できます。<br><br> ![Stream output](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. VM がプロビジョニングされたことを確認するために AWS Management Console にログインします (まだログインしていない場合)。<br><br> ![AWS console deployed VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>次のステップ
 
* サポートされる Runbook については、「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。
* Runbook を使った作業については、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* スクリプトのサポートについては、[Azure Automation でのネイティブ PowerShell スクリプトのサポート](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)に関するページを参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
