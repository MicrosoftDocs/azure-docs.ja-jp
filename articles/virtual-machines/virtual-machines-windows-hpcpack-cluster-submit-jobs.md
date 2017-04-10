---
title: "Azure の HPC Pack クラスターにジョブを送信する | Microsoft Docs"
description: "Azure の HPC Pack クラスターに HPC ジョブを送信するようにオンプレミス コンピューターを設定する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 4f5ae7c1750d697e7dae3f03ea5d3936c9fee52b
ms.lasthandoff: 03/25/2017


---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure の [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) クラスターにジョブを送信するようにオンプレミス コンピューターを構成します。 この記事では、クライアント ツールを使用して、HTTPS を介して Azure のクラスターにジョブを送信するようにローカル コンピューターを設定する方法について説明します。 これにより、各クラスター ユーザーは、ヘッド ノード VM に直接接続したり Azure サブスクリプションにアクセスしたりすることなく、クラウド ベースの HPC Pack クラスターにジョブを送信できます。

![Azure のクラスターにジョブを送信する][jobsubmit]

## <a name="prerequisites"></a>前提条件
* **Azure VM にデプロイされた HPC Pack ヘッド ノード** - [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)や [Azure PowerShell スクリプト](windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)などの自動ツールを使用して、ヘッド ノードとクラスターをデプロイすることをお勧めします。 この記事の手順を完了するには、ヘッド ノードの DNS 名とクラスター管理者の資格情報が必要です。
* **クライアント コンピューター** - HPC Pack クライアント ユーティリティを実行する Windows または Windows Server クライアント コンピューターが必要になります ([システム要件](https://technet.microsoft.com/library/dn535781.aspx)に関するページを参照)。 ジョブを送信する目的だけで HPC Pack Web ポータルまたは REST API を使用する場合、自分で選んだクライアント コンピューターを利用できます。
* **HPC Pack インストール メディア** - HPC Pack クライアント ユーティリティをインストールするには、HPC Pack (HPC Pack 2012 R2) の最新バージョンの無料インストール パッケージを [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=328024)から入手できます。 必ず、ヘッド ノード VM にインストールされている HPC Pack と同じバージョンをダウンロードしてください。

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>手順 1: ヘッド ノードに Web コンポーネントをインストールし、構成する
REST インターフェイスを有効にして HTTPS でクラスターにジョブを送信するために、HPC Pack ヘッド ノードに HPC Pack Web コンポーネントが構成されていることを確認します。 Web コンポーネントがインストールされていない場合は、最初に HpcWebComponents.msi インストール ファイルを実行して Web コンポーネントをインストールします。 次に、HPC PowerShell スクリプト **Set-HPCWebComponents.ps1**を実行し、コンポーネントを構成します。

詳細な手順については、「 [Microsoft HPC Pack Web コンポーネントのインストール](http://technet.microsoft.com/library/hh314627.aspx)」を参照してください。

> [!TIP]
> 一部の HPC Pack 用 Azure クイックスタート テンプレートでは、Web コンポーネントが自動的にインストールされ、構成されます。 [HPC Pack IaaS デプロイ スクリプト](windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)を利用してクラスターを作成する場合、デプロイの一環として任意で Web コンポーネントをインストールし、構成します。
> 
> 

**Web コンポーネントをインストールするには**

1. クラスター管理者の資格情報を使用し、ヘッド ノード VM に接続します。
2. HPC Pack セットアップ フォルダーから、ヘッド ノードで HpcWebComponents.msi を実行します。
3. ウィザードの手順に従い、Web コンポーネントをインストールします

**Web コンポーネントを構成するには**

1. ヘッド ノードで、管理者として HPC PowerShell を起動します。
2. 構成スクリプトの場所にディレクトリを変更するには、次のコマンドを入力します。
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. REST インターフェイスを構成し、HPC Web Service を起動し、次のコマンドを入力します。
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. 証明書の選択を求められたら、ヘッド ノードのパブリック DNS 名に対応する証明書を選択します。 たとえば、クラシック デプロイメント モデルを使用してヘッド ノード VM をデプロイする場合、証明書名は CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net 形式になります。 Resource Manager デプロイメント モデルを使用する場合、証明書名は、CN=&lt;*HeadNodeDnsName*&gt;.&lt;*region*&gt;.cloudapp.azure.com 形式になります。
   
   > [!NOTE]
   > この証明書は、後でオンプレミス コンピューターからヘッド ノードにジョブを送信するときに選択します。 Active Directory ドメインのヘッド ノードのコンピューター名に一致する証明書を選択したり、構成したりしないでください (CN=*MyHPCHeadNode.HpcAzure.local*など)。
   > 
   > 
5. Web ポータルのジョブ送信を構成するには、次のコマンドを入力します。
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. スクリプトが完了したら、次のコマンドを入力して HPC Job Scheduler Service を停止し、再起動します。
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>手順 2: HPC Pack クライアント ユーティリティをオンプレミス コンピューターにインストールする
HPC Pack クライアント ユーティリティを自分のコンピューターにインストールする場合は、[Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=328024)から HPC Pack セットアップ ファイル (完全インストール) をダウンロードします。 インストールを開始するとき、**HPC Pack クライアント ユーティリティ**のセットアップ オプションを選択します。

HPC Pack クライアント ツールを使用し、ジョブをヘッド ノード VM に送信するには、ヘッド ノードから証明書をエクスポートし、それをクライアント コンピューターにインストールする必要もあります。 証明書は .CER 形式である必要があります。

**ヘッド ノードから証明書をエクスポートするには**

1. ヘッド ノードで、ローカル コンピューター アカウントに対して Microsoft 管理コンソールに証明書スナップインを追加します。 スナップインを追加する手順については、「 [証明書スナップインを MMC に追加する](https://technet.microsoft.com/library/cc754431.aspx)」を参照してください。
2. コンソール ツリーで、**[証明書 - ローカル コンピューター]** > **[個人]** の順に展開し、**[証明書]** をクリックします。
3. 「[手順 1: ヘッド ノードに Web コンポーネントをインストールし、構成する](#step-1:-install-and-configure-the-web-components-on-the-head-node)」で HPC Pack Web コンポーネントに構成した証明書を見つけます (例: CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net)。
4. 証明書を右クリックし、**[すべてのタスク]** > **[エクスポート]** の順にクリックします。
5. 証明書のエクスポート ウィザードで、**[次へ]** をクリックしてから **[いいえ、秘密キーをエクスポートしません]** をクリックします。
6. ウィザードの残りの手順に従い、DER エンコード バイナリ X.509 (.CER) 形式で証明書をエクスポートします。

**クライアント コンピューターに証明書をインポートするには**

1. ヘッド ノードからクライアント コンピューターのフォルダーにエクスポートした証明書をコピーします。
2. クライアント コンピューターで、certmgr.msc を実行します。
3. 証明書マネージャーで、**[証明書 - 現在のユーザー]** > **[信頼されたルート証明機関]** の順に展開し、**[証明書]** を右クリックし、**[すべてのタスク]** > **[インポート]** をクリックします。
4. 証明書インポート ウィザードで、 **[次へ]** をクリックし、手順に従って、ヘッド ノードからエクスポートした証明書を信頼されたルート証明機関ストアにインポートします。

> [!TIP]
> クライアント コンピューターがヘッド ノードの証明機関を認識しないため、セキュリティ警告が表示されることがあります。 テスト目的であるため、この警告を無視し、証明書のインポートを完了します。
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>手順 3: クラスターでテスト ジョブを実行する
構成を検証する目的で、オンプレミス コンピューターから Azure のクラスターでジョブを実行してみます。 たとえば、HPC Pack GUI ツールまたはコマンドライン コマンドを利用し、ジョブをクラスターに送信できます。 Web ベース ポータルを利用してジョブを送信することもできます。

**クライアント コンピューターでジョブ送信コマンドを実行するには**

1. HPC Pack クライアント ユーティリティがインストールされているクライアント コンピューターで、コマンド プロンプトを起動します。
2. サンプル コマンドを入力します。 たとえば、クラスターのすべてのジョブのリストを表示するには、ヘッド ノードの完全 DNS 名に応じて、次のいずれかと同様のコマンドを入力します。
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    または
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > スケジューラ URL には、IP アドレスではなく、ヘッド ノードの完全 DNS 名を使用します。 IP アドレスを指定した場合、"サーバー証明書に有効な信頼チェーンがあるか、証明書を信頼されたルート ストアに配置する必要があります" のようなエラーが表示されます。
   > 
   > 
3. 入力を求められたら、HPC クラスター管理者または構成済みの別のクラスター ユーザーのユーザー名 (&lt;DomainName&gt;\\&lt;UserName&gt; 形式) とパスワードを入力します。 ジョブ操作が多ければ、資格情報をローカルに保存できます。
   
    ジョブの一覧が表示されます。

**クライアント コンピューターで HPC ジョブ マネージャーを使用するには**

1. ジョブの送信時にクラスター ユーザーのドメイン資格情報を保存しなかった場合は、資格情報マネージャーで資格情報を追加できます。
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 クライアント コンピューターのコントロール パネルで、資格情報マネージャーを起動します。
   
    b. **[Windows 資格情報]** > **[汎用資格情報の追加]** をクリックします。
   
    c. インターネット アドレス (たとえば、https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler、https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com/HpcScheduler) と、クラスター管理者または構成済みの別のクラスター ユーザーのユーザー名 (&lt;DomainName&gt;\\&lt;UserName&gt; 形式) とパスワードを指定します。
2. クライアント コンピューターで、HPC ジョブ マネージャーを起動します。
3. **[ヘッド ノードの選択]** ダイアログ ボックスで、Azure のヘッド ノードへの URL (たとえば、https://&lt;HeadNodeDnsName&gt;.cloudapp.net、https://&lt;HeadNodeDnsName&gt;.&lt;region&gt;.cloudapp.azure.com) を入力します。
   
    HPC ジョブ マネージャーが開き、ヘッド ノードでジョブの一覧が表示されます。

**ヘッド ノードで実行されている Web ポータルを使用するには**

1. クライアント コンピューターで Web ブラウザーを起動し、ヘッド ノードの完全 DNS 名に応じて、次のいずれかのアドレスを入力します。
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    または
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. セキュリティ ダイアログ ボックスが表示されたら、HPC クラスター管理者のドメイン資格情報を入力します。 (さまざまなロールで他のクラスター ユーザーを追加することもできます。 「[クラスター ユーザーの管理](https://technet.microsoft.com/library/ff919335.aspx)」を参照してください)。
   
    Web ポータルが開き、ジョブの一覧が表示されます。
3. クラスターから「Hello World」という文字列を返すサンプル ジョブを送信するには、左のナビゲーションで **[新しいジョブ]** をクリックします。
4. **[新しいジョブ]** ページの **[送信ページから]** で、**[HelloWorld]** をクリックします。 ジョブの送信ページが表示されます。
5. **[Submit]**をクリックします。 入力が求められたら、HPC クラスター管理者のドメインの資格情報を入力します。 ジョブが送信され、ジョブ ID が **[自分のジョブ]** ページに表示されます。
6. 送信したジョブの結果を表示するには、ジョブ ID をクリックし、**[タスクの表示]** をクリックしてコマンドの出力を表示します (**[出力]** の下で)。

## <a name="next-steps"></a>次のステップ
* [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)を使用し、Azure クラスターにジョブを送信することもできます。
* Linux クライアントからクラスター ジョブを送信する場合は、 [HPC Pack 2012 R2 SDK およびサンプル コード](https://www.microsoft.com/download/details.aspx?id=41633)の Python サンプルをご覧ください。

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png

