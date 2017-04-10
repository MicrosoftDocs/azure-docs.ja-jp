---
title: "HPC Pack クラスターと Azure Active Directory | Microsoft Docs"
description: "Azure の HPC Pack 2016 クラスターを Azure Active Directory と統合する方法について説明します"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: bdc23f0e54c8bd30f3c082e9038d005d3595d429
ms.openlocfilehash: dd789f76a0fee69aabc894c33b4682253f7c617d


---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Azure Active Directory を使用して Azure の HPC Pack クラスターを管理する
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) では、Azure の HPC クラスターをデプロイする管理者向けに [Azure Active Directory](../active-directory/index.md) (Azure AD) との統合をサポートしています。



次のタスクの概要については、この記事の手順に従ってください。 
* HPC Pack クラスターを Azure AD テナントに手動で統合する
* Azure の HPC Pack クラスターでジョブのスケジュールを設定して管理する 

HPC Pack クラスター ソリューションを Azure AD に統合するには、他のアプリケーションやサービスを統合する標準的な手順に従います。 この記事は、Azure AD での基本的なユーザー管理について理解していることを前提としています。 詳細と背景については、「[Azure Active Directory のドキュメント](../active-directory/index.md)」と以降のセクションをご覧ください。

## <a name="benefits-of-integration"></a>統合の利点


Azure Active Directory (Azure AD) は、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスで、クラウド ソリューションへのシングル サインオン (SSO) アクセスを提供します。

HPC Pack クラスターを Azure AD と統合することで、次の目標の達成をサポートします。

* HPC Pack クラスターから従来の Active Directory ドメイン コント ローラーを削除します。 これにより、ユーザーのビジネスに必要ないクラスターの維持にかかるコストを削減し、デプロイ プロセスを高速化するのに役立ちます。
* Azure AD との統合により、次の利点が得られます。
    *   シングル サインオン 
    *   Azure の HPC Pack クラスターに AD のローカル ID を使用する 

    ![Azure Active Directory の環境](./media/virtual-machines-windows-hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>前提条件
* **Azure Virtual Machines にデプロイされている HPC Pack 2016 クラスター** - 手順については、「[Azure に HPC Pack 2016 クラスターをデプロイする](virtual-machines-windows-hpcpack-2016-cluster.md)」をご覧ください。 この記事の手順を完了するには、ヘッド ノードの DNS 名とクラスター管理者の資格情報が必要です。

  > [!NOTE]
  > Azure Active Directory との統合は、HPC Pack 2016 より前のバージョンではサポートされていません。



* **クライアント コンピューター** - HPC Pack クライアント ユーティリティを実行する Windows または Windows Server クライアント コンピューターが必要です。 ジョブを送信する目的だけで HPC Pack Web ポータルまたは REST API を使用する場合、自分で選んだクライアント コンピューターを利用できます。

* **HPC Pack クライアント ユーティリティ** - HPC Pack クライアント ユーティリティをクライアント コンピューターにインストールします。インストール パッケージは、Microsoft ダウンロード センターから無料で入手できます。


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>手順 1: HPC クラスター サーバーを Azure AD テナントに登録する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のメニューの **[Active Directory]** をクリックし、サブスクリプション内の目的のディレクトリをクリックします。 ディレクトリ内のリソースにアクセスするには、そのディレクトリへのアクセス許可が必要です。
3. **[ユーザー]** をクリックし、既に作成または構成されているユーザー アカウントが存在することを確認します。
4. **[アプリケーション]** > 、**[追加]**、**[組織で開発中のアプリケーションを追加]** の順にクリックします。 ウィザードで次の情報を入力します。
    * **[名前]** - HPCPackClusterServer
    * **[種類]** - **[Web アプリケーションや Web API]** を選択
    * **[サインイン URL]** - サンプルのベース URL (既定では `https://hpcserver`)
    * **[アプリケーション ID/URI]** - `https://<Directory_name>/<application_name>`。 `<Directory_name`> を Azure AD テナントのフルネーム (`hpclocal.onmicrosoft.com` など) に置き換え、`<application_name>` を前に選択した名前に置き換えます。

5. アプリケーションが追加されたら、**[構成]** をクリックします。 次のプロパティを構成します。
    * **[アプリケーションはマルチテナントです]** で **[はい]** を選択
    * **[アプリにアクセスするにはユーザー割り当てが必要]** で **[はい]** を選択

6. **[保存]**をクリックします。 保存が完了したら、**[マニフェストの管理]** をクリックします。 この操作により、アプリケーションのマニフェスト JavaScript Object Notation (JSON) ファイルがダウンロードされます。 ダウンロードしたマニフェストを編集します。`appRoles` を探し、次のアプリケーション ロールを追加します。
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. ファイルを保存します。 ポータルで、**[マニフェストの管理]** > **[マニフェストのアップロード]** をクリックします。 これで編集したマニフェストをアップロードできます。
8. **[ユーザー]** をクリックしてユーザーを選択し、**[割り当て]** をクリックします。 利用可能な役割 (HpcUsers または HpcAdminMirror) のいずれかをユーザーに割り当てます。 ディレクトリ内の他のユーザーでこの手順を繰り返します。 クラスター ユーザーの背景情報については、「[クラスター ユーザーの管理](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx)」をご覧ください。

   > [!NOTE] 
   > ユーザーは、[Azure Portal](https://portal.azure.com)の Azure Active Directory プレビュー ブレードを使用して管理することをお勧めします。
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>手順 2: HPC クラスター クライアントを Azure AD テナントに登録する

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のメニューの **[Active Directory]** をクリックし、サブスクリプション内の目的のディレクトリをクリックします。 ディレクトリ内のリソースにアクセスするには、そのディレクトリへのアクセス許可が必要です。
3. **[アプリケーション]** > 、**[追加]**、**[組織で開発中のアプリケーションを追加]** の順にクリックします。 ウィザードで次の情報を入力します。

    * **[名前]** - HPCPackClusterClient
    * **[種類]** - **[ネイティブ クライアント アプリケーション]** を選択
    * **[リダイレクト URI]** - `http://hpcclient`

4. アプリケーションが追加されたら、**[構成]** をクリックします。 **[クライアント ID]** の値をコピーして保存します。 この値は後でアプリケーションを構成するときに必要です。

5. **[その他のアプリケーションに対するアクセス許可]** で、**[アプリケーションの追加]** をクリックします。 (手順 1 で作成した) HpcPackClusterServer アプリケーションを探して追加します。

6. **[委任されたアクセス許可]** ドロップダウンで、**[Access HpcClusterServer (HpcClusterServer にアクセス)]** を選択します。 その後、 **[保存]**をクリックします。


## <a name="step-3-configure-the-hpc-cluster"></a>手順 3: HPC クラスターを構成する

1. Azure で HPC Pack 2016 ヘッド ノードに接続します。

2. HPC PowerShell を起動します。

3. 次のコマンドを実行します。

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    各値の説明:

    * `AADTenant` は Azure AD のテナント名を指定します (`hpclocal.onmicrosoft.com` など)。
    * `AADClientAppId` は手順 2. で作成されたアプリケーションのクライアント ID を指定します。

4. HpcSchedulerStateful サービスを再起動します。

    複数のヘッド ノードがあるクラスターでは、ヘッド ノードで次の PowerShell コマンドを実行して、HpcSchedulerStateful サービスのプライマリ レプリカを切り替えることができます。

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>手順 4: ジョブを管理してクライアントからジョブを送信する

HPC Pack クライアント ユーティリティを自分のコンピューターにインストールするには、Microsoft ダウンロード センターから HPC Pack 2016 のセットアップ ファイル (完全インストール) をダウンロードします。 インストールを開始するとき、**HPC Pack クライアント ユーティリティ**のセットアップ オプションを選択します。

クライアント コンピューターを準備するには、クライアント コンピューターで [HPC クラスターをセットアップ](virtual-machines-windows-hpcpack-2016-cluster.md)する間に使用する証明書をインストールします。 Windows 標準の証明書管理手順を使用して、公開証明書を **[証明書 - 現在のユーザー]** >  の **[信頼されたルート証明機関]** ストアにインストールします。 

これで HPC Pack のコマンドを実行するか、HPC Pack のジョブ マネージャー GUI を使用して、Azure AD アカウントでクラスターのジョブを送信および管理できます。 ジョブの送信オプションについては、[Azure の HPC Pack クラスターに HPC のジョブを送信する方法](virtual-machines-windows-hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster)に関する記事をご覧ください。

> [!NOTE]
> 初めて Azure の HPC Pack クラスターに接続しようとすると、ポップアップ ウィンドウが表示されます。 Azure ADの資格情報を入力してログインします。 トークンがキャッシュされます。 その後 Azure のクラスターに接続する際には、認証情報が変更されるかキャッシュがクリアされない限り、キャッシュされたトークンが使用されます。
>
  
たとえば、前の手順を完了すると、次のようにしてオンプレミスのクライアントからジョブをクエリできます。

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Azure AD との統合により使用できるジョブ送信用の便利なコマンドレット 

### <a name="manage-the-local-token-cache"></a>ローカルのトークン キャッシュを管理する

HPC Pack 2016 には、ローカルのトークン キャッシュを管理する 2 つの新しい HPC PowerShell コマンドレットが用意されています。 これらのコマンドレットは、非対話形式でジョブを送信するのに便利です。 次の例を参照してください。

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Azure AD アカウントを使用してジョブを送信するための資格情報を設定する 

場合によっては、HPC クラスター ユーザーでのジョブの実行が必要になることがあります (ドメインに参加している HPC クラスターではドメイン ユーザーとして実行。ドメインに参加していない HPC クラスターではローカル ユーザーとしてヘッド ノードで実行)。

1. 次のコマンドを使用して、資格情報を設定します。

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. その後、次のようにジョブを送信します。 ジョブとタスクは、$localUser 計算ノードで実行されます。

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   `–Credential` が `Submit-HpcJob` で指定されていない場合、ジョブとタスクはローカルで Azure AD アカウントとしてマッピングされているユーザーの下で実行されます  (HPC クラスターが Azure AD アカウントと同じ名前のローカル ユーザーを作成してタスクを実行します)。
    
3. Azure AD アカウントの拡張データを設定します。 これは、Azure AD アカウントを使用して Linux ノードで MPI ジョブを実行する際に便利です。

   * Azure AD アカウント自体の拡張データを設定する

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * 拡張データを設定し HPC クラスター ユーザーとして実行する
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```




<!--HONumber=Dec16_HO2-->


