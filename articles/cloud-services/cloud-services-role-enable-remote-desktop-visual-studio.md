---
title: Visual Studio を使用してロールのリモート デスクトップを有効にする (Azure Cloud Services クラシック)
description: Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ad95cefbdf839c28b0979b051e217a1dfec76eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743238"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-visual-studio"></a>Visual Studio を使用して、Azure Cloud Services (クラシック) でロールのリモート デスクトップ接続を有効にする

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。 リモート デスクトップ接続を使用すると、アプリケーションの実行中にそのアプリケーションの問題のトラブルシューティングや診断を行うことができます。

Visual Studio によってクラウド サービス向けに提供される発行ウィザードでは、発行プロセス中にユーザーが入力した資格情報を利用してリモート デスクトップを有効にできるオプションが含まれています。 このオプションは、Visual Studio 2017 バージョン 15.4 以前を使用するときに適しています。

しかしながら、Visual Studio 2017 バージョン 15.5 以降の場合、単独開発者としてのみ作業しているのでなければ、発行ウィザードを使ってリモート デスクトップを有効にする行為は避けることをお勧めします。 他の開発者がプロジェクトを開くような状況がある場合、代わりに、Azure Portal、PowerShell、または継続的配置ワークフローのリリース パイプラインからリモート デスクトップを有効にします。 このような推奨は、クラウド サービス VM における Visual Studio とリモート デスクトップの通信方法が変わったことによる影響です。これについてはこの記事で説明します。

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Visual Studio 2017 バージョン 15.4 以前でリモート デスクトップを構成する

Visual Studio 2017 バージョン 15.4 以前を使用しているとき、発行ウィザードの **[すべてのロールに対してリモート デスクトップを有効にする]** オプションを使用できます。 このウィザードは Visual Studio 2017 バージョン 15.5 以降で引き続き使用できますが、リモート デスクトップ オプションは使用しないでください。

1. Visual Studio のソリューション エクスプローラーでクラウド サービス プロジェクトを右クリックし、**[発行]** を選択し、発行ウィザードを開始します。

2. 必要に応じて Azure サブスクリプションにサインインし、**[次へ]** を選択します。

3. **[設定]** ページで **[すべてのロールに対してリモート デスクトップを有効にする]** を選択し、**[設定]** リンクを設定して **[リモート デスクトップ構成]** ダイアログ ボックスを開きます。

4. ダイアログ ボックスの一番下にある **[その他のオプション]** を選択します。 これで証明書を作成または選択できるドロップダウン リストが表示されます。リモート デスクトップを使用して接続するときに資格情報を暗号化できます。

   > [!Note]
   > リモート デスクトップ接続に必要な証明書は、他の Azure 操作で使用する証明書とは異なります。 リモート アクセス証明書には、秘密キーが必要です。

5. 一覧から証明書を選択するか、 **&lt;[作成]&gt;** を選択します。 新しい証明書を作成する場合、入力を求められたら新しい証明書にわかりやすい名前を付け、**[OK]** を選択します。 ドロップダウン リスト ボックスに新しい証明書が表示されます。

6. ユーザー名とパスワードを入力します。 既存のアカウントを使用することはできません。 新しいアカウントのユーザー名として "Administrator" を使用しないでください。

7. アカウントが期限切れになり、以後リモート デスクトップ接続がブロックされる日付を選択します。

8. 必要な情報をすべて入力したら、**[OK]** を選択します。 Visual Studio により、リモート デスクトップ設定がプロジェクトの `.cscfg` ファイルと `.csdef` ファイルに追加されます。選択した証明書を使用して暗号化されるパスワードが含まれています。

9. **[次へ]** ボタンを使用して残りの手順を完了し、クラウド サービスを発行する準備ができたら、**[発行]** を選択します。 発行する準備ができていない場合、**[キャンセル]** を選択し、変更内容の保存を求められたら **[はい]** で回答します。 後で、保存した設定でクラウド サービスを発行できます。

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 バージョン 15.5 以降でリモート デスクトップを構成する

Visual Studio 2017 バージョン 15.5 以降でも引き続き、クラウド サービス プロジェクトで発行ウィザードを使用できます。 単独開発者としてのみ作業している場合、**[すべてのロールに対してリモート デスクトップを有効にする]** オプションも使用できます。

チームの一員として作業している場合、代わりに、[Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) か [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) を使用し、Azure クラウド サービスでリモート デスクトップを有効にしてください。

この推奨は、Visual Studio 2017 バージョン 15.5 以降とクラウド サービス VM の通信方法が変わったことに起因します。 発行ウィザードからリモート デスクトップを有効にすると、以前のバージョンの Visual Studio は、"RDP プラグイン" と呼ばれている機能を利用して VM と通信します。 Visual Studio 2017 バージョン 15.5 以降では、代わりに安全性と柔軟性が向上した "RDP 拡張" を使用して通信します。 これは、Azure Portal と PowerShell でリモート デスクトップを有効にする場合にも RDP 拡張が使用されることに伴う変更です。

Visual Studio と RDP 拡張が通信するとき、TLS を介してプレーンテキスト パスワードが送信されます。 ただし、プロジェクトの構成ファイルは暗号化されたパスワードのみを格納します。元々暗号化に使用されたローカル証明書でのみ、プレーンテキストに復号できます。

毎回同じ開発コンピューターからクラウド サービス プロジェクトを配置する場合、ローカル証明書が利用できます。 この場合も、発行ウィザードの **[すべてのロールに対してリモート デスクトップを有効にする]** オプションを使用できます。

しかし、ご自分や他の開発者が別のコンピューターからクラウド サービス プロジェクトを配置する場合、その別のコンピューターにはパスワードを復号するために必要な証明書が入っていないことになります。 結果として、次のエラー メッセージが表示されます。

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

クラウド サービスを配置するたびにパスワードを変更することもできますが、それではリモート デスクトップの利用者にとって不便です。

チーム内でプロジェクトを共有している場合、発行ウィザードでオプションを解除し、代わりに [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) または [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) から直接、リモート デスクトップを有効にすることをお勧めします。

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Visual Studio 2017 バージョン 15.5 以降でビルド サーバーから配置する

Visual Studio 2017 バージョン 15.5 以降がビルド エージェントにインストールされているビルド サーバー (たとえば、Azure DevOps Services で) からクラウド サービス プロジェクトを配置できます。 このように手配すると、暗号化証明書が利用できる同じコンピューターから配置されます。

Azure DevOps Services の RDP 拡張を使用するには、ビルド パイプラインに次の詳細を含めます。

1. RDP プラグインではなく RDP 拡張と配置が連動するように、MSBuild 引数に `/p:ForceRDPExtensionOverPlugin=true` を追加します。 次に例を示します。

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. ビルド手順の後で、「**Azure Cloud Service の配置**」手順を追加し、そのプロパティを設定します。

1. 配置手順の後、「**Azure Powershell**」手順を追加し、その **[表示名]** プロパティを "Azure Deployment: Enable RDP Extension" (あるいは別の適切な名前) に設定し、適切な Azure サブスクリプションを選択します。

1. **[スクリプトの種類]** を "インライン" に設定し、下のコードを **[インライン スクリプト]** フィールドに貼り付けます。 (このスクリプトでプロジェクトに `.ps1` ファイルを作成し、**[スクリプトの種類]** を "スクリプト ファイル パス" に設定し、ファイルを指すように **[スクリプト パス]** を設定することもできます。)

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>リモート デスクトップを使用して Azure ロールに接続する

Azure でクラウド サービスを発行し、リモート デスクトップを有効にしたら、Visual Studio サーバー エクスプローラーを使用し、クラウド サービス VM にログインできます。

1. サーバー エクスプローラーで、 **[Azure]** ノードを展開し、いずれかのクラウド サービスのノードとそのロールを 1 つ展開して、インスタンスの一覧を表示します。

2. インスタンス ノードを右クリックし、**[Connect Using Remote Desktop]\(リモート デスクトップを使用して接続\)** を選択します。

3. 以前作成したユーザー名とパスワードを入力します。 これでリモート セッションにログインした状態になります。

## <a name="additional-resources"></a>その他のリソース

[Cloud Services の構成方法](cloud-services-how-to-configure-portal.md)
