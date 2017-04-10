---
title: "Azure デプロイ用にリモート アクセスを Eclipse で有効にする方法"
description: "Azure Toolkit for Eclipse を使用して Azure デプロイのリモート アクセスを有効にする方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: b6150006-9a7f-4d83-be18-d35ec780c7c5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 7d547ee39c4fe2bd52dd1fedce9c61d63b4e3011


---
# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Azure デプロイ用にリモート アクセスを Eclipse で有効にする方法
デプロイの問題を解消するとき、リモート アクセスを有効にして利用し、デプロイをホストする仮想マシンに接続できます。 リモート アクセス機能は、リモート デスクトップ プロトコル (RDP) に依存しています。 Azure に公開した後にデプロイメントのリモート アクセスを構成できます。あるいは、Windows オペレーティング システムで Eclipse を使用している場合、Azure に公開する前にリモート アクセスを構成できます。 Azure でデプロイの仮想マシンに接続するには、ご利用のオペレーティング システムとの間に互換性があるリモート デスクトップ クライアントが必要になることにご注意ください。

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Azure にデプロイする前にリモート アクセスを有効にする方法
> [!NOTE]
> Azure にアプリケーションをデプロイする前にリモート アクセスを有効にするには、Windows で Eclipse を実行する必要があります。
> 
> 

次の図は、リモート アクセスを有効にするために使用する **[リモート アクセス]** プロパティ ダイアログを示しています。

![][ic719494]

**[リモート アクセス]** プロパティ ダイアログは&2; つの方法で表示できます。

* **[Publish to Azure (Azure に発行)]** の **[Remote Access (リモート アクセス)]** セクションにある **[Advanced (詳細)]** リンクをクリックします。
* Azure プロジェクトの **[プロパティ]** ダイアログを開きます。

新しい Azure デプロイメント プロジェクトを作成すると、プロジェクトのリモート アクセスは既定では有効になりません。 ただし、 **[Azure に発行]** ダイアログにユーザー名とパスワードを指定することでリモート アクセスを簡単に有効にできます。 リモート アクセスのパスワードは X.509 証明書で暗号化されます。 自分の証明書を利用しない場合、Azure Plugin for Eclipse に付属する自己署名証明書が暗号化で利用されます。 この自己署名証明書は Azure プロジェクトの **cert** フォルダーにあり、パブリック証明書ファイル (SampleRemoteAccessPublic.cer) と PFX (Personal Information Exchange) 証明書ファイル (SampleRemoteAccessPrivate.pfx) の両方として保存されています。 後者には証明書の秘密鍵と既定のパスワードである **Password1**が含まれています。 ただし、このパスワードは公開されているため、既定の証明書は学習目的にのみ利用し、本稼働デプロイには利用できません。 そのため、学習目的以外では、デプロイのリモート セッションを有効にするとき、**[Publish to Azure (Azure に発行)]** ダイアログの **[Advanced (詳細)]** リンクをクリックし、自分の証明書を指定する必要があります。 Azure でユーザー パスワードを復号化できるように、Microsoft Azure 管理ポータル内でホストされているサービスに PFX 版の証明書をアップロードする必要があります。

このチュートリアルの残りの部分では、最初にリモート アクセスを無効にして作成した Azure デプロイメント プロジェクトのリモート アクセスを有効にする方法について紹介します。 このチュートリアルでは、新しい自己署名証明書を作成します。選択したパスワードがその .pfx ファイルに入ります。 証明書機関が発行した証明書を利用することもできます。

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Azure にデプロイした後、リモート アクセスを有効にする方法
Azure にデプロイした後にリモート アクセスを有効にするには、次の手順を使用します。

1. Azure アカウントを使用して Microsoft Azure 管理ポータルにログインします。
2. **[Cloud Services]**の一覧で、デプロイしたクラウド サービスを選択します。
3. クラウド サービス Web ページで、 **[構成]** リンクをクリックします。
4. 構成ページの下部で、 **[リモート]** リンクをクリックします。
5. ポップアップ ダイアログ ボックスが表示されたら、次の操作を行います。
   
   * リモート アクセスを有効にするロールを指定します。
   * クリックして **[リモート デスクトップを有効にする]** チェックボックスを選択します。
   * リモート アクセスに使用するユーザー名とパスワードを指定します。
   * 使用する証明書を選択します。
6. **[OK]** 

構成変更が進行中であり、完了に数分かかることを示すメッセージが表示されます。 構成変更が完了したら、この記事の後半にある「 **リモートでログインするには** 」セクションの手順に従います。

## <a name="how-to-enable-remote-access-in-your-package"></a>パッケージのリモート アクセスを有効にする方法
1. Eclipse の [プロジェクト エクスプローラー] ウィンドウで、Azure プロジェクトを右クリックし、 **[プロパティ]**をクリックします。
2. **[Properties (プロパティ)]** ダイアログで、左側のウィンドウの **[Azure]** を展開し、**[Remote Access (リモート アクセス)]** をクリックします。
3. **[Remote Access (リモート アクセス)]** ダイアログで、**[Enable all roles to accept Remote Desktop Connections with these login credentials (すべてのロールでこれらのログイン資格情報によるリモート デスクトップ接続を承認できるようにする)]** が選択されていることを確認します。
4. リモート デスクトップ接続のユーザー名を指定します。
5. ユーザーのパスワードを指定し、確定しますこのダイアログに設定されているユーザー名とパスワードの値は、リモート デスクトップ接続時に使用されます。 (これは PFX パスワードとは別のパスワードであることに注意してください。 )
6. ユーザー アカウントの有効期限を指定します。
7. **[New (新規)]** をクリックし、新しい自己署名証明書を作成します  (代替的に、**[Workspace (ワークスペース)]** ボタンまたは **[FileSystem (ファイル システム)]** ボタンでワークスペースまたはシステムから証明書を選択できます。ただし、このチュートリアルでは、新しい証明書を作成します)。
   
   * **[新しい証明書]** ダイアログ ボックスで、PFX ファイルに使用するパスワードを指定し、確認します。
   * **[名前 (CN)]**に与えられた値をそのまま使用するか、自分で作成した名前を使用します。
   * パスとファイル名を指定します。その場所に新しい証明書が .cer 形式で保存されます。 この手順と次の手順で、Azure プロジェクトの **cert** フォルダーを使用できますが、別の場所を選択してもかまいません。 このチュートリアルでは、**c:\mycert\mycert.cer** を使用します  (続行する前に **c:\mycert** フォルダーを作成するか、必要に応じて既存のフォルダーを使用します)。
   * パスとファイル名を指定します。その場所に新しい証明書とその秘密鍵が .pfx 形式で保存されます。 このチュートリアルでは、**c:\mycert\mycert.pfx** を使用します。 **[New Certificate (新しい証明書)]** ダイアログ ボックスは次のようになります (**c:\mycert** を使用しない場合、フォルダー パスを更新します)。
     
       ![][ic712275]
   * **[OK]** をクリックし、**[New Certificate (新しい証明書)]** ダイアログ ボックスを閉じます。
8. **[Remote Access (リモート アクセス)]** ダイアログ ボックスは次のようになります。</p>
   
    ![][ic719495]
9. **[OK]** をクリックし、**[Remote Access (リモート アクセス)]** ダイアログ ボックスを閉じます。

クラウドにデプロイメントするためのビルド セットでアプリケーションを再構築します。

## <a name="to-log-in-remotely"></a>リモートでログインするには
ロール インスタンスの準備ができたら、アプリケーションをホストしている仮想マシンにリモートでログインできます。

* Windows で Eclipse を使用しているとき、Azure にデプロイメントする際、**[Start remote desktop on deploy (デプロイ時にリモート デスクトップを開始する)]** オプションを選択した場合、デプロイが開始されたときにリモート デスクトップ接続のログオン画面が表示されます。 ユーザー名とパスワードの入力が求められたら、リモート ユーザーに指定した値を入力します。それでログインできます。
* リモートでログインするもう&1; つの方法は <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Microsoft Azure 管理ポータル</a>を利用することです。
  
  * Azure 管理ポータルの **[Cloud Services]** ビュー内で、クラウド サービスをクリックし、**[インスタンス]** をクリックし、特定のインスタンスをクリックし、**[接続]** ボタンをクリックします。 **[接続]** ボタンはコマンド バーに次のように表示されます。
    
      ![][ic659273]
  * **[接続]** ボタンをクリックすると、RDP ファイルを開くように求められます。 ファイルを開き、指示に従います。 (または、このファイルをローカル コンピューターに保存し、それをダブルクリックすることでファイルを実行できます。先に管理ポータルに移動する必要がありません。)
  * ユーザー名とパスワードの入力が求められたら、リモート ユーザーに指定した値を入力します。それでログインできます。

> [!NOTE]
> Windows 以外のオペレーティング システムを使用している場合、そのオペレーティング システムと互換性のあるリモート デスクトップ クライアントを利用し、次の手順に従い、ダウンロードした RDP ファイルの設定でそのクライアントを構成する必要があります。
> 
> 

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure 向け Hello World アプリケーションを Eclipse で作成する][Creating a Hello World Application for Azure in Eclipse]

[Azure Toolkit for Eclipse のインストール][Installing the Azure Toolkit for Eclipse] 

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java Developer Center]を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->



<!--HONumber=Jan17_HO1-->


