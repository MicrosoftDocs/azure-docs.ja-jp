---
title: Azure Stack 統合システムの Azure 登録 | Microsoft Docs
description: マルチノード Azure Stack の Azure に接続されたデプロイのための Azure 登録プロセスについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 70408f11c8656fb62c8613777d1837d934f67074
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487568"
---
# <a name="register-azure-stack-with-azure"></a>Azure を使用した Azure Stack の登録

Azure Stack の Azure への登録により、Azure からマーケットプレース項目をダウンロードしたり、Microsoft に返送するコマース データを設定したりできます。 Azure Stack を登録した後は、Azure コマースに使用状況が報告され、登録に使用した Azure 課金サブスクリプション ID で確認できます。

この記事では、Azure を使用して Azure Stack 統合システムを登録する方法について説明します。 Azure を使用して ASDK を登録する方法については、ASDK ドキュメントの「[Azure Stack の登録](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register)」を参照してください。

> [!IMPORTANT]  
> マーケットプレースのオファー項目を含む、Azure Stack のすべての機能をサポートするには、登録が必要です。 さらに、従量制課金モデルを使用している場合、登録しないと、Azure Stack のライセンス条項違反になります。 Azure Stack のライセンス モデルに関する詳細は、[購入方法のページ](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)を参照してください。

## <a name="prerequisites"></a>前提条件

登録する前に、次の操作が完了している必要があります。

 - 資格情報を確認する
 - PowerShell 言語モードを設定する
 - PowerShell for Azure Stack をインストールする
 - Azure Stack ツールをダウンロードする
 - 登録シナリオを決定する

### <a name="verify-your-credentials"></a>資格情報を確認する

Azure を使用して Azure Stack を登録する前に、以下のものが必要です。

- Azure サブスクリプションのサブスクリプション ID。 登録には、EA、CSP、CSP 共有サービス サブスクリプションだけがサポートされています。 CSP では、[CSP サブスクリプションまたは APSS サブスクリプションのどちらを使用するか](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)を決める必要があります。<br><br>ID を取得するには、Azure にサインインして、**[すべてのサービス]** をクリックします。 次に、**[一般]** カテゴリの下で **[サブスクリプション]** を選び、使用するサブスクリプションをクリックすると、**[要点]** の下にサブスクリプション ID が表示されます。

  > [!Note]  
  > ドイツのクラウド サブスクリプションは現在サポートされていません。

- サブスクリプションの所有者であるアカウントのユーザー名とパスワード。

- ユーザー アカウントは Azure サブスクリプションにアクセスできなければならず、そのサブスクリプションに関連付けられているディレクトリで ID アプリケーションとサービス プリンシパルを作成するアクセス許可を持つ必要があります。 最低限の特権による管理を使用し、Azure Stack を Azure に登録することをお勧めします。 登録のためのサブスクリプションへのアクセスを制限するカスタム ロール定義を作成する方法について詳しくは、「[ Azure Stack の登録ロールを作成する](azure-stack-registration-role.md)」を参照してください。

- Azure Stack リソース プロバイダーを登録しました (詳細については、「Azure Stack リソース プロバイダーを登録する」セクションを参照してください)。

登録の後、Azure Active Directory の全体管理者のアクセス許可は必要ありません。 ただし、一部の操作では、全体管理者の資格情報が必要です。 たとえば、リソース プロバイダーのインストーラー スクリプトや、アクセス許可を付与する必要のある新機能などがあります。 アカウントの全体管理者のアクセス許可を一時的に復元するか、*既定のプロバイダー サブスクリプション*の所有者である別の全体管理者アカウントを使用します。

Azure Stack を登録するユーザーは、Azure Active Directory のサービス プリンシパルの所有者です。 Azure Stack の登録を変更できるのは、Azure Stack を登録したユーザーのみです。 登録サービス プリンシパルの所有者ではない管理者以外のユーザーが Azure Stack を登録または再登録しようとすると、403 応答が発生する可能性があります。 403 応答は、操作を完了するための十分な権限がユーザーにないことを示します。

これらの要件を満たす Azure サブスクリプションがない場合は、[ここで無料の Azure アカウントを作成](https://azure.microsoft.com/free/?b=17.06)できます。 Azure Stack を登録しても、Azure サブスクリプションに課金されることはありません。

> [!NOTE]
> 複数の Azure Stack がある場合のベスト プラクティスは、各 Azure Stack を専用のサブスクリプションに登録することです。 これにより、使用状況の追跡が容易になります。

### <a name="powershell-language-mode"></a>PowerShell 言語モード

Azure Stack を正常に登録するには、PowerShell 言語モードを **FullLanguageMode** に設定する必要があります。  現在の言語モードが完全に設定されていることを確認するには、PowerShell ウィンドウを管理者特権で開いて、次の PowerShell コマンドレットを実行します。

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

出力で **FullLanguageMode** が返されていることを確認します。 その他の言語モードが返されている場合は、別のマシン上で再登録を実行するか、言語モードを **FullLanguageMode** に設定してから作業を続行する必要があります。

### <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack をインストールする

Azure に登録するために、最新の PowerShell for Azure Stack を使用します。

最新バージョンがまだインストールされていない場合は、「[PowerShell for Azure Stack をインストールする](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)」を参照してください。

### <a name="download-the-azure-stack-tools"></a>Azure Stack ツールをダウンロードする

Azure Stack ツールの GitHub リポジトリには、Azure Stack 機能 (登録機能を含む) をサポートする PowerShell モジュールが含まれています。 登録プロセス中に、Azure Stack ツール リポジトリにある **RegisterWithAzure.psm1** PowerShell モジュールをインポートおよび利用して、使用する Azure Stack インスタンスを Azure に登録する必要があります。

最新バージョンを使用していることを確認するには、Azure に登録する前に、既存のバージョンの Azure Stack ツールをすべて削除し、[GitHub から最新バージョンをダウンロードする](azure-stack-powershell-download.md)必要があります。

### <a name="determine-your-registration-scenario"></a>登録シナリオを決定する

Azure Stack のデプロイは、"*接続*" デプロイまたは "*切断*" デプロイになります。

 - **接続中**  
 "接続" とは、インターネットと Azure に接続できるように Azure Stack がデプロイされたことを意味します。 ID ストアには、Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) のどちらかを使用します。 接続されたデプロイの場合、従量課金制と容量ベースの 2 つの課金モデルから選択できます。
    - [**従量課金制**課金モデルを使用して接続された Azure Stack を Azure に登録する](#register-connected-with-pay-as-you-go-billing)
    - [**容量**課金モデルを使用して接続された Azure Stack を Azure に登録する](#register-connected-with-capacity-billing)

 - **切断**  
 Azure からの切断デプロイ オプションを使用すると、インターネットへの接続なしで Azure Stack をデプロイして使用できます。 ただし、切断されたデプロイでは、AD FS ID ストアおよび容量ベースの課金モデルに制限されます。
    - [**容量**課金モデルを使用して切断された Azure Stack を登録する](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>使用する一意の登録名の判別 
Azure Stack を Azure に登録するときに、一意の登録名を指定する必要があります。 Azure の登録に Azure Stack サブスクリプションを関連付ける簡単な方法は、Azure Stack の**クラウド ID** を使用することです。 

> [!NOTE]
> 容量ベースの課金モデルを使用している Azure Stack 登録では、[期限切れの登録を削除](azure-stack-registration.md#change-the-subscription-you-use)して Azure に再登録するのでない限り、年単位のサブスクリプションの期限が切れた後の再登録で一意の名前を変更する必要があります。

Azure Stack デプロイのクラウド ID を調べるには、特権エンドポイントにアクセスできるコンピューターで管理者として PowerShell を開き、次のコマンドを実行して、**CloudID** の値を書き留めます。 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>従量課金制モデルを使用して接続された Azure Stack を登録する

従量制課金モデルを使用して Azure Stack を Azure に登録するには、次の手順を使用します。

> [!Note]  
> これらのすべての手順は、特権エンドポイント (PEP) にアクセスできるコンピューターから実行する必要があります。 PEP の詳細については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。

接続された環境では、インターネットと Azure にアクセスできます。 これらの環境では、Azure Stack リソース プロバイダーを Azure に登録してから、課金モデルを構成する必要があります。

1. Azure Stack リソース プロバイダーを Azure に登録するには、PowerShell ISE を管理者として起動し、**EnvironmentName** パラメーターを適切な Azure サブスクリプション タイプ (以下のパラメーターを参照) に設定して、次の PowerShell コマンドレットを使用します。

2. Azure Stack を登録するために使用する Azure アカウントを追加します。 アカウントを追加するには、**Add-AzureRmAccount** コマンドレットを実行します。 Azure アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | パラメーター | 説明 |  
   |-----|-----|
   | EnvironmentName | Azure クラウド サブスクリプション環境名。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure サブスクリプションを使用している場合は **AzureChinaCloud** です。  |

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、使用するものを選択します。  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure サブスクリプションで Azure Stack リソース プロバイダーを登録するには、次のコマンドを実行します。

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 管理者として PowerShell ISE を起動し、Azure Stack ツールをダウンロードしたときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 PowerShell を使用して **RegisterWithAzure.psm1** モジュールをインポートします。

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 次に、同じ PowerShell セッションで、正しい Azure PowerShell コンテキストにログインしていることを確認します。 これは、前に Azure Stack リソース プロバイダーの登録に使用された Azure アカウントです。 実行する PowerShell:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | パラメーター | 説明 |  
   |-----|-----|
   | EnvironmentName | Azure クラウド サブスクリプション環境名。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure サブスクリプションを使用している場合は **AzureChinaCloud** です。  |

7. 同じ PowerShell セッションで **Set-AzsRegistration** コマンドレットを実行します。 実行する PowerShell:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Set-AzsRegistration コマンドレットの詳細については、「[登録に関するリファレンス](#registration-reference)」を参照してください。

   このプロセスには 10 - 15 分かかります。 コマンドが完了すると、「**Your environment is now registered and activated using the provided parameters. (提供されたパラメーターを使用して環境が登録され、アクティブ化されました。)**」というメッセージが表示されます。

## <a name="register-connected-with-capacity-billing"></a>容量課金モデルを使用して接続された Azure Stack を登録する

従量制課金モデルを使用して Azure Stack を Azure に登録するには、次の手順を使用します。

> [!Note]  
> これらのすべての手順は、特権エンドポイント (PEP) にアクセスできるコンピューターから実行する必要があります。 PEP の詳細については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。

接続された環境では、インターネットと Azure にアクセスできます。 これらの環境では、Azure Stack リソース プロバイダーを Azure に登録してから、課金モデルを構成する必要があります。

1. Azure Stack リソース プロバイダーを Azure に登録するには、PowerShell ISE を管理者として起動し、**EnvironmentName** パラメーターを適切な Azure サブスクリプション タイプ (以下のパラメーターを参照) に設定して、次の PowerShell コマンドレットを使用します。

2. Azure Stack を登録するために使用する Azure アカウントを追加します。 アカウントを追加するには、**Add-AzureRmAccount** コマンドレットを実行します。 Azure アカウント資格情報の入力を求められ、お使いのアカウントの構成によっては 2 要素認証を使用する必要があります。

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | パラメーター | 説明 |  
   |-----|-----|
   | EnvironmentName | Azure クラウド サブスクリプション環境名。 サポートされている環境名は **AzureCloud**、**AzureUSGovernment**、または中国の Azure サブスクリプションを使用している場合は **AzureChinaCloud** です。  |

3. 複数のサブスクリプションがある場合は、次のコマンドを実行して、使用するものを選択します。  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Azure サブスクリプションで Azure Stack リソース プロバイダーを登録するには、次のコマンドを実行します。

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 管理者として PowerShell ISE を起動し、Azure Stack ツールをダウンロードしたときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 PowerShell を使用して **RegisterWithAzure.psm1** モジュールをインポートします。

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > **Set-AzsRegistration** コマンドレットの UsageReportingEnabled パラメーターを false に設定することで、使用状況レポートを無効にすることができます。 
   
   Set-AzsRegistration コマンドレットの詳細については、「[登録に関するリファレンス](#registration-reference)」を参照してください。

## <a name="register-disconnected-with-capacity-billing"></a>容量課金モデルを使用して切断された Azure Stack を登録する

切断された (インターネット接続のない) 環境で Azure Stack を登録している場合は、Azure Stack 環境から登録トークンを取得してから、Azure に接続でき、かつ PowerShell for Azure Stack がインストールされたコンピューター上でそのトークンを使用する必要があります。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Azure Stack 環境から登録トークンを取得する

1. 管理者として PowerShell ISE を起動し、Azure Stack ツールをダウンロードしたときに作成された **AzureStack-Tools-master** ディレクトリ内の **Registration** フォルダーに移動します。 **RegisterWithAzure.psm1** モジュールをインポートします。  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 登録トークンを取得するには、次の PowerShell コマンドレットを実行します。  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Get-AzsRegistrationToken コマンドレットの詳細については、「[登録に関するリファレンス](#registration-reference)」を参照してください。

   > [!Tip]  
   > 登録トークンは、*$FilePathForRegistrationToken* に指定されたファイルに保存されます。 ファイル パスまたはファイル名は任意に変更できます。

3. 接続された Azure マシンで使用するためにこの登録トークンを保存します。 $FilePathForRegistrationToken からファイルまたはテキストをコピーできます。

### <a name="connect-to-azure-and-register"></a>Azure に接続して登録する

インターネットに接続されたコンピューターで同じ手順を実行し、RegisterWithAzure.psm1 モジュールをインポートし、正しい Azure PowerShell コンテキストにサインインします。 次に、Register-AzsEnvironment を呼び出します。 Azure に登録する登録トークンを指定します。 同じ Azure サブスクリプション ID を利用して複数の Azure Stack インスタンスを登録している場合、一意の登録名を指定します。 次のコマンドレットを実行します。

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```powershell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > 後で参照できるように登録リソース名および登録トークンを保存します。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Azure の登録リソースからアクティブ化キーを取得する

次に、Register-AzsEnvironment 中に Azure で作成された登録リソースからアクティブ化キーを取得する必要があります。

アクティブ化キーを取得するには、次の PowerShell コマンドレットを実行します。  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > アクティブ化キーは *$KeyOutputFilePath*　に指定されたファイルに保存されます。 ファイル パスまたはファイル名は任意に変更できます。

### <a name="create-an-activation-resource-in-azure-stack"></a>Azure Stack にアクティブ化リソースを作成する

Get-AzsActivationKey から作成されたアクティブ化キーのファイルまたはテキストを使用して Azure Stack 環境に戻ります。 次に、アクティブ化キーを使用して Azure Stack にアクティブ化リソースを作成します。 アクティブ化リソースを作成するには、次の PowerShell コマンドレットを実行します。 

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

オプションで、Get-Content コマンドレットを使用して、登録トークンが含まれているファイルを示すことができます。

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Azure Stack の登録を検証する

Azure Stack の登録に成功したことは、**[Region management]\(リージョン管理\)** タイルを使用して確認できます。 このタイルは、管理者ポータルの既定のダッシュボードにあります。 状態には、登録済みと未登録とがあります。 登録済みである場合は、Azure Stack の登録に使用した Azure サブスクリプション ID が、登録のリソース グループおよび名前と共に表示されます。

1. [Azure Stack 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. ダッシュボードで、**[Region management]\(リージョン管理\)** を選択します。

3. **[プロパティ]** を選択します。 このブレードには、環境の状態と詳細が表示されます。 **[登録済み]** 状態と **[未登録]** 状態とがあります。

    [![[Region management]\(リージョン管理\) タイル](media/azure-stack-registration/admin1sm.png "[Region management]\(リージョン管理\) タイル")](media/azure-stack-registration/admin1.png#lightbox)

    登録した場合、プロパティには以下が含まれます。
    
    - **登録サブスクリプション ID**:登録され、Azure Stack に関連付けられた Azure サブスクリプション ID
    - **登録リソース グループ**:Azure Stack リソースを含む関連付けられているサブスクリプション内の Azure リソース グループ。

4. Azure portal を使用して、Azure Stack のアプリの登録を表示します。 Azure Stack の登録に使用したサブスクリプションに関連付けられているアカウントを使用して、Azure portal にサインインします。 Azure Stack に関連付けられているテナントに切り替えます。
5. **[Azure Active Directory] > [アプリの登録] > [アプリケーションをすべて表示]** に移動します。

    ![アプリの登録](media/azure-stack-registration/app-registrations.png)

    Azure Stack のアプリの登録には、プレフィックス **Azure Stack** が付加されます。

また、登録が成功したかどうかは、Marketplace の管理機能を使用して確認することもできます。 [Marketplace Management]\(Marketplace の管理\) ブレードに Marketplace アイテムの一覧が表示された場合、登録は成功しています。 一方、非接続環境では、[Marketplace management]\(Marketplace の管理\) に Marketplace アイテムを表示することができません。

> [!NOTE]
> 登録が完了すると、登録されていないことを示すアクティブな警告は表示されなくなります。 非接続のシナリオでは、正常に登録が完了しても、Azure Stack を登録してアクティブ化するよう求めるメッセージが [Marketplace management]\(Marketplace の管理\) に表示されます。

## <a name="renew-or-change-registration"></a>登録を更新または変更する

### <a name="renew-or-change-registration-in-connected-environments"></a>接続された環境で登録を更新または変更する

次の状況では、登録を更新または変更する必要があります。

- 容量ベースの年単位サブスクリプションを更新した後。
- 課金モデルを変更したとき。
- 容量ベースの課金のために変更を調整したとき (ノードの追加/削除)。

#### <a name="change-the-subscription-you-use"></a>使用するサブスクリプションを変更する

使用するサブスクリプションを変更する場合は、まず **Remove-AzsRegistration** コマンドレットを実行してから、正しい Azure PowerShell コンテキストにログインしていることを確認し、最後に \<課金モデル\> を含む変更されたパラメーターを指定して **Set-AzsRegistration** を実行する必要があります。

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>課金モデルまたは機能の提供方法を変更する

インストールの課金モデルまたは機能の提供方法を変更する場合は、登録機能を呼び出して新しい値を設定できます。 最初に現在の登録を削除する必要はありません。

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>切断された環境で登録を更新または変更する

次の状況では、登録を更新または変更する必要があります。

- 容量ベースの年単位サブスクリプションを更新した後。
- 課金モデルを変更したとき。
- 容量ベースの課金のために変更を調整したとき (ノードの追加/削除)。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Azure Stack からアクティブ化リソースを削除する

最初に Azure Stack からアクティブ化リソースを削除してから、Azure の登録リソースを削除する必要があります。  

Azure Stack のアクティブ化リソースを削除するには、Azure Stack 環境で次の PowerShell コマンドレットを実行します。  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

次に、Azure の登録リソースを削除するには、Azure に接続されたコンピューターを使用していることを確認して、正しい Azure PowerShell コンテキストにサインインし、以下の説明に従って、適切な PowerShell コマンドレットを実行します。

リソースの作成に使用した登録トークンを使用できます。  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

または登録名を使用することもできます。

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>切り離された手順を使用して再登録する

これで、接続が切断されたシナリオで登録が完全に解除されました。接続が切断されたシナリオで Azure Stack 環境を登録する手順を繰り返す必要があります。

### <a name="disable-or-enable-usage-reporting"></a>使用状況レポートを無効または有効にする

容量課金モデルを使用する Azure Stack 環境では、**Set-AzsRegistration** または **Get-AzsRegistrationToken** コマンドレットのいずれかで **UsageReportingEnabled** パラメーターを使用して使用状況レポートをオフにします。 Azure Stack では、既定で使用状況メトリックがレポートされます。 容量モデルを使用するオペレーターまたは切断された環境をサポートするオペレーターは、使用状況レポートをオフにする必要があります。

#### <a name="with-a-connected-azure-stack"></a>接続された Azure Stack

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>切断された Azure Stack

1. 登録トークンを変更するには、次の PowerShell コマンドレットを実行します。  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 登録トークンは、*$FilePathForRegistrationToken* に指定されたファイルに保存されます。 ファイル パスまたはファイル名は任意に変更できます。

2. 接続された Azure マシンで使用するためにこの登録トークンを保存します。 $FilePathForRegistrationToken からファイルまたはテキストをコピーできます。

## <a name="move-a-registration-resource"></a>登録リソースを移動する
同じサブスクリプションのリソース グループ間における登録リソースの移動は、すべての環境で**サポートされています**。 ただし、サブスクリプションをまたがる登録リソースの移動がサポートされるのは、両方のサブスクリプションで同じパートナー ID が解決される場合の CSP のみです。 新しいリソース グループへのリソースの移動については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)」を参照してください。

## <a name="registration-reference"></a>登録に関するリファレンス

### <a name="set-azsregistration"></a>Set-AzsRegistration

Set-AzsRegistration を使用すると、Azure Stack を Azure に登録し、マーケットプレースでの項目のオファーと使用状況レポートを有効または無効にすることができます。

コマンドレットを実行するには、以下が必要です。
- 任意の種類のグローバルな Azure サブスクリプション。
- そのサブスクリプションの所有者または共同作成者であるアカウントを使用して Azure PowerShell にログインしている必要があります。

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| パラメーター | Type | 説明 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | [特権エンドポイントへのアクセス](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)に使用する資格情報。 ユーザー名は、**AzureStackDomain\CloudAdmin** の形式です。 |
| PrivilegedEndpoint | String | ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 詳細については、[特権エンドポイントの使用](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)の記事を参照してください。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 自分のサブスクリプションで使用する請求モデル。 このパラメーターの有効値は、Capacity、PayAsYouUse、および Development です。 |
| MarketplaceSyndicationEnabled | True または False | ポータル内でマーケットプレース管理機能を使用できるようにするかどうかを決定します。 インターネット接続を使用して登録している場合は true に設定します。 切断された環境で登録している場合は false に設定します。 切断された環境で登録した場合は、[オフライン シンジケーション ツール](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)を使用して、マーケットプレースの項目をダウンロードできます。 |
| UsageReportingEnabled | True または False | Azure Stack では、既定で使用状況メトリックがレポートされます。 容量モデルを使用するオペレーターまたは切断された環境をサポートするオペレーターは、使用状況レポートをオフにする必要があります。 このパラメーターの有効値は、True、False です。 |
| AgreementNumber | String |  |
| RegistrationName | String | 同じ Azure Subscription ID を利用し、複数の Azure Stack インスタンスで登録スクリプトを実行している場合、登録に一意の名前を設定します。 このパラメーターの既定値は **AzureStackRegistration** です。 ただし、複数の Azure Stack インスタンスに同じ名前を使用すると、スクリプトは失敗します。 |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken は、入力パラメーターから登録トークンを生成します。

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| パラメーター | Type | 説明 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | [特権エンドポイントへのアクセス](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)に使用する資格情報。 ユーザー名は、**AzureStackDomain\CloudAdmin** の形式です。 |
| PrivilegedEndpoint | String |  ログ収集およびその他のデプロイ後タスクのような機能を提供する、あらかじめ構成されたリモート PowerShell コンソール。 詳細については、[特権エンドポイントの使用](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)の記事を参照してください。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 自分のサブスクリプションで使用する請求モデル。 このパラメーターの有効値は、Capacity、PayAsYouUse、および Development です。 |
| MarketplaceSyndicationEnabled | True または False |  |
| UsageReportingEnabled | True または False | Azure Stack では、既定で使用状況メトリックがレポートされます。 容量モデルを使用するオペレーターまたは切断された環境をサポートするオペレーターは、使用状況レポートをオフにする必要があります。 このパラメーターの有効値は、True、False です。 |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>登録エラー

Azure Stack の登録を試みている間に、次のエラーのいずれかが発生する可能性があります。
1. $hostName の必須ハードウェア情報を取得できませんでした。 物理ホストと接続を確認してから、登録を再実行してください。

2. ハードウェア情報を取得するために $hostName に接続できません。物理ホストと接続を確認してから、登録を再実行してください。

> 原因: これは通常、アクティブ化を試みるためにホストから UUID、BIOS、CPU などのハードウェア詳細を取得しようとしたが、物理ホストに接続できないことでハードウェア詳細を取得不能だったことが原因です。

Marketplace の管理にアクセスしして、製品を配信しようとするとエラーが発生します。 
> 原因: これは通常、Azure Stack が登録リソースにアクセスできない場合に発生します。 これに関する一般的な理由の 1 つとして、Azure サブスクリプションのディレクトリ テナントが変更されると登録がリセットされることがあげられます。 サブスクリプションのディレクトリ テナントを変更した場合、Azure Stack Marketplace へのアクセスおよび使用状況のレポートを行うことはできません。 この問題を解決するには再登録する必要があります。

切断されたプロセスを使用して既にスタンプを登録している場合でも、Marketplace の管理から、Azure Stack の登録とアクティブ化を要求されます。 
> 原因: これは切断された環境における既知の問題です。 [これらの手順](azure-stack-registration.md#verify-azure-stack-registration)を実行することで登録状態を確認できます。 Marketplace の管理を使用するには、[オフライン ツール](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)を使用する必要があります。 

## <a name="next-steps"></a>次の手順

[Azure から Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)
