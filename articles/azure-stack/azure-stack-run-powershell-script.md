---
title: "Azure Stack Development Kit のデプロイ | Microsoft Docs"
description: "Azure Stack Development Kit を準備し、PowerShell スクリプトを実行してそれをデプロイする方法について説明します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 530a9558df2323e1aa49d9f4b974c142ee5ecf37
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-the-azure-stack-development-kit"></a>Azure Stack Development Kit のデプロイ

*適用先: Azure Stack Development Kit*

[Azure Stack Development Kit](azure-stack-poc.md) をデプロイするには、次の手順を完了する必要があります。

1. [デプロイ パッケージをダウンロード](https://azure.microsoft.com/overview/azure-stack/try/?v=try)して、Cloudbuilder.vhdx を取得します。
2. 開発キットをインストールするコンピューター (開発キットのホスト) を構成するために、asdk-installer.ps1 スクリプトを実行し、[cloudbuilder.vhdx を準備](#prepare-the-development-kit-host)します。 この手順の後に、開発キットのホストは、Cloudbuilder.vhdx から起動します。
3. 開発キットのホストに[開発キットをデプロイ](#deploy-the-development-kit)します。

> [!NOTE]
> インターネット接続されていない Azure Stack 環境を使用する場合でも、最適な結果を得るには、接続中にデプロイすることが最良です。 そうすることにより、Windows Server 2016 の評価バージョンがデプロイ時にアクティブ化されます。 Windows Server 2016 の評価版は、10 日以内にアクティブ化されない場合、シャットダウンされます。
> 
> 

## <a name="download-and-extract-the-development-kit"></a>開発キットのダウンロードと抽出
1. ダウンロードを開始する前に、コンピューターが次の前提条件を満たしていることを確認します。

   * コンピューターには、最低 60 GB のディスク空き領域があります。
   * [.NET framework 4.6 (またはそれ以降のバージョン)](https://aka.ms/r6mkiy) がインストールされています。

2. [[はじめに] ページに移動して](https://azure.microsoft.com/overview/azure-stack/try/?v=try)、詳細を入力し、**[送信]** をクリックします。
3. **[Download the software]\(ソフトウェアをダウンロード\)** の **[Azure Stack Development Kit]** をクリックします。
4. ダウンロードした AzureStackDownloader.exe ファイルを実行します。
5. **Azure Stack Development Kit のダウンローダー**ウィンドウで、手順 1 から 5 を実行します。
6. ダウンロードが完了したら **[実行]** をクリックして、MicrosoftAzureStackPOC.exe を起動します。
7. [使用許諾契約書] 画面および自己解凍ウィザードの情報を確認し、**[次へ]** をクリックします。
8. [プライバシーに関する声明] 画面と自己解凍ウィザードの情報を確認し、**[次へ]** をクリックします。
9. ファイルの抽出先をクリックして、**[次へ]** をクリックします。
   * 既定は、<drive letter>:\<current folder>\Microsoft Azure Stack です。
10. [送信先] 画面と自己解凍ウィザードの情報を確認し、**[抽出]** をクリックして CloudBuilder.vhdx (最大 25 GB) と ThirdPartyLicenses.rtf ファイルを抽出します。 この処理には、完了までに時間がかかる場合があります。

> [!NOTE]
> ファイルを抽出したら、exe ファイルと bin ファイルを削除して、コンピューターの場所を復元できます。 または、これらのファイルを別の場所に移動すると、再度デプロイするときにファイルを再度ダウンロードする必要がありません。
> 
> 

## <a name="prepare-the-development-kit-host"></a>開発キットのホストの準備
1. 開発キットのホストに物理的に接続できること、および (KVM などの) 物理コンソールにアクセスできることを確認します。 このアクセスは、以下の手順 13 で開発キットのホストを再起動した後で必要になります。
2. 開発キットのホストが[最小要件](azure-stack-deploy.md)を満たしていることを確認します。 要件を確認するには、「[Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)」 (Azure Stack のデプロイ チェッカー) を使用してください。
3. 開発キットのホストにローカル管理者としてサインインします。
4. CloudBuilder.vhdx ファイルを C:\ ドライブ (C:\CloudBuilder.vhdx) のルートにコピーまたは移動します。
5. 次のスクリプトをダウンロードして、開発キットのインストーラー ファイル (asdk installer.ps1) を開発キットのホストの c:\AzureStack_Installer フォルダーにダウンロードします。
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. 管理者特権で PowerShell コンソールを開き、C:\AzureStack_Installer\asdk-installer.ps1 スクリプトを実行し、**[Prepare vhdx]\(vhdx の準備\)** をクリックします。
7. インストーラーの **[Select Cloudbuilder vhdx]\(Cloudbuilder vhdx の選択\)** ページで、前の手順でダウンロードした cloudbuilder.vhdx ファイルを探して選択します。
8. 省略可能: **[ドライバーの追加]** ボックスをオンにすると、ホストで使用する追加のドライバーが含まれるフォルダーを指定できます。
9. **[オプション設定]** ページで、開発キットのホストのローカル管理者アカウントを指定します。 これらの資格情報を指定しない場合、以下のインストール時に、ホストへの KVM アクセスが必要になります。
10. また、**[オプション設定]** ページには、次を設定するオプションもあります。
    - **[コンピューター名]**: このオプションでは、開発キットのホスト名が設定されます。 名前は FQDN の要件に準拠している必要があり、長さは 15 文字以下にする必要があります。 既定は、Windows によって生成されたランダムなコンピューター名です。
    - **[タイム ゾーン]**: 開発キットのホストのタイム ゾーンが設定されます。 既定は、(UTC-8:00) 太平洋標準時 (米国およびカナダ) です。
    - **[Static IP configuration]\(静的な IP 構成\)**: デプロイが静的 IP アドレスを使用するよう設定されます。 それ以外の場合、インストーラーが cloudbuilder.vhx から再起動されるときに、ネットワーク インターフェイスが DHCP と構成されます。
11. **[次へ]** をクリックします。
12. 前の手順で静的 IP を構成した場合、次を実行する必要があります。
    - ネットワーク アダプターを選択します。 **[次へ]** をクリックする前に、アダプターに接続できることを確認します。
    - **[IP アドレス]**、**[ゲートウェイ]**、および **[DNS]** 値が正しいことを確認し、**[次へ]** をクリックします。
13. **[次へ]** をクリックして、準備プロセスを開始します。
14. 準備が **[完了]** と示されたら、**[次へ]** をクリックします。
15. **[今すぐ再起動する]** をクリックして cloudbuilder.vhdx から起動し、デプロイ処理を続行します。

## <a name="deploy-the-development-kit"></a>開発キットのデプロイ
1. 開発キットのホストにローカル管理者としてサインインします。 前の手順で指定した資格情報を使用します。

    > [!IMPORTANT]
    > Azure Active Directory のデプロイの場合、Azure Stack ではインターネットへのアクセスを、直接または透過プロキシを介してのいずれかで必要とします。 このデプロイでは、ネットワーク用に NIC は 1 つのみサポートされます。 NIC が複数ある場合、次のセクションで開発スクリプトを実行する前に、(他はすべて無効にし) 1 つのみが有効になっていることを確認します。
    
2. 管理者特権で PowerShell コンソールを開き、(Cloudbuilder.vhdx の別のドライブにある場合がある) \AzureStack_Installer\asdk-installer.ps1 スクリプトを実行し、**[インストール]** をクリックします。
3. **[タイプ]** ボックスで、**[Azure クラウド]** または **[ADFS]** を選択します。
    - **[Azure クラウド]**: Azure Active Directory は ID プロバイダーです。 このパラメーターは、AAD アカウントにグローバル管理者アクセス許可がある特定のディレクトリを指定するために使用します。 .onmicrosoft.com の形式の AAD ディレクトリ テナントの完全名です。 
    - **[ADFS]**: 既定のスタンプ ディレクトリ サービスは、ID プロバイダーであり、サインインに使用する既定のアカウントは azurestackadmin@azurestack.local であり、使用するパスワードは、セットアップの一部として提供したものです。
4. **[Local administrator password]\(ローカル管理者のパスワード\)** の **[パスワード]** ボックスに、(現在の構成済みのローカル管理者パスワードと同じ) ローカル管理者パスワードを入力し、**[次へ]** をクリックします。
5. 開発キットに使用するネットワーク アダプターを選択して、**[次へ]** をクリックします。
6. BGPNAT01 仮想マシン用に DHCP または静的なネットワーク構成を選択します。
    - **[DHCP]** (既定): 仮想マシンが DHCP サーバーから IP ネットワークの構成を取得します。
    - **[静的]**: DHCP が Azure Stack に、インターネットにアクセスするための有効な IP アドレスを割り当てることができない場合にのみこのオプションは使用されます。 静的な IP アドレスは、サブネット マスク長を使用して指定する必要があります (例: 10.0.0.5/24)。
7. 任意で次の値を設定します。
    - **[VLAN ID]**: VLAN ID を設定します。 このオプションは、ホストと AzS-BGPNAT01 が物理ネットワーク (およびインターネット) にアクセスするために VLAN ID を構成する必要がある場合にのみ使用されます。 
    - **[DNS Forwarder]\(DNS フォワーダ\)**: DNS サーバーは Azure Stack のデプロイの一部として作成されます。 ソリューション内のコンピューターにスタンプ外の名前解決を許可するには、既存のインフラストラクチャの DNS サーバーを提供します。 スタンプ内の DNS サーバーが、このサーバーに不明な名前解決の要求を送信します。
    - **[タイム サーバー]**: 特定のタイム サーバーを設定します。 
8. **[次へ]** をクリックします。 
9. **[Verifying network interface card properties]\(ネットワーク インターフェイス カードのプロパティを確認しています\)** ページに、進行状況バーが表示されます。 
    - **[An update cannot be downloaded]\(更新プログラムをダウンロードできませんでした\)** と書かれている場合、ページの指示に従います。
    - **[完了]** と書かれている場合、**[次へ]** をクリックします。
10. **[概要] ページで** [デプロイ] **をクリックします。**
11. Azure Active Directory のデプロイを使用している場合、Azure Active Directory のグローバル管理者アカウントの資格情報を入力するよう求められます。
12. デプロイには場合によって数時間かかります。その間システムは自動的に 1 回再起動されます。
   
   > [!IMPORTANT]
   > デプロイの進行状況を監視する場合は、azurestack\AzureStackAdmin としてサインインします。 コンピューターがドメインに参加した後に、ローカル管理者としてサインインした場合、デプロイの進行状況は表示されません。 デプロイは再実行せず、代わりに azurestack\AzureStackAdmin としてサインインし、それが実行中であることを確認します。
   > 
   > 
   
    デプロイが成功した場合、PowerShell コンソールには、**[COMPLETE: Action ‘Deployment’]\(完了: アクション ‘デプロイ’\)** と表示されます。
   
デプロイが失敗した場合は、同じ管理者特権の PowerShell ウィンドウから次の PowerShell 再実行スクリプトを使用できます。

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

このスクリプトでは、成功した最後の手順からデプロイを再開します。

または、最初から[再デプロイ](azure-stack-redeploy.md)することも可能です。


## <a name="reset-the-password-expiration-to-180-days"></a>パスワードの有効期限を 180 日間にリセットする

開発キットのホストのパスワードがすぐに期限切れにならないようにするには、デプロイ後に次の手順を実行します。

1. 開発キットのホストで、**[グループ ポリシーの管理]** を開き、**[グループ ポリシーの管理]**、**[フォレスト: azurestack.local]**、**[ドメイン]**、**[azurestack.local]** に移動します。
2. **[MemberServer]** を右クリックして、**[編集]** をクリックします。
3. グループ ポリシー管理エディターで、**[コンピューターの構成]**、**[ポリシー]**、**[Windows の設定]**、**[セキュリティの設定]**、**[アカウント ポリシー]**、**[パスワード ポリシー]** の順に移動します。
4. 右側のウィンドウの **[パスワードの有効期間]** をダブルクリックします。
5. **[Maximum password age Properties]\(パスワードの有効期間プロパティ\)** ダイアログ ボックスで、**[パスワードの有効期限]** の値を 180 に変更し、**[OK]** をクリックします。


## <a name="next-steps"></a>次のステップ
[Azure のサブスクリプションを使用した Azure Stack の登録](azure-stack-register.md)

[Azure Stack への接続](azure-stack-connect-azure-stack.md)


