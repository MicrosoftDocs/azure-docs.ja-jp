---
title: "Azure RemoteApp で Office 365 サブスクリプションを使用する方法 | Microsoft Docs"
description: "Azure RemoteApp で Office 365 サブスクリプションを使用して Office アプリを共有する方法を説明します。"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: f82b6e23-2b71-47be-846d-fd93f2652f3c
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 4ad7970b94949bf105ddf549279d4d1bcbad5f40


---
# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Azure RemoteApp で Office 365 サブスクリプションを使用する方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

既存の Office 365 サブスクリプションを Azure RemoteApp で使用して、Office アプリをクラウドから共有できます。 ここでは Office 365 + Azure RemoteApp のオプションについて説明し、サブスクリプションを活用するのに役立つ Office 365 についての記事へのリンクを示します。

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Azure RemoteApp で Office 365 アカウントを使用する方法
詳細については、「 [Office 365 のユーザー アカウントで Azure RemoteApp を使用する方法](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Office 365 サブスクリプションを使用して Azure RemoteApp で Office アプリケーションを実行できますか?
はい。 実際、Azure RemoteApp で Office アプリケーションを実行する唯一の方法は、Office 365 サブスクリプションを使用することです。

(注: Azure RemoteApp のデプロイがホスト側パートナーによって実行されている場合は、パートナーが[サービス プロバイダー ライセンス契約](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx)に基づいて Office ライセンスを提供できる場合があります)。

Office 365 サブスクリプションがすばらしい点は、Azure Cloud を含む多くのさまざまなプラットフォームおよび環境で同じユーザー ライセンスを使用できることです。 Azure RemoteApp で Office アプリケーションを使用する場合、追加のライセンスを購入したり、特別な方法で既存のライセンスを構成したりする必要はありません。 [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx)を含む Office 365 サブスクリプションがあれば十分です。

Office 365 ProPlus は[共有コンピューターのアクティブ化](https://technet.microsoft.com/library/Dn782860.aspx)を可能にします。この機能を使用すると、Azure RemoteApp (およびリモート デスクトップ サービス) のような仮想環境とクラウド環境で Office を一時ユーザーがアクティブ化できます。

Office 365 ProPlus を含む Office 365 プランについては、 「[それぞれの Office&365; プランのサービスの可用性](https://technet.microsoft.com/library/office-365-plan-options.aspx)」の表をご覧ください。 Office 365 ProPlus を含まないプランがあることに注意してください (たとえば、Office 365 Business プラン)。 お使いのプランに Office 365 ProPlus が含まれない場合は、含むプラン (Office 365 Education E3 など) へのアップグレードを検討してください。

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>Azure RemoteApp で Office 365 ProPlus ライセンスを使用するにはどうすればよいですか?
Office 365 ProPlus の各ユーザー ライセンスでは、1 人のユーザーが最大 5 台のコンピューターとタブレットおよび携帯電話で Office アプリケーションをアクティブ化できます。 デバイスで Office を非アクティブ化するまで、各アクティブ化はユーザーに登録されています (ユーザーは、[Office 365 ポータル](https://portal.office365.com/)で自分のデバイスを管理できます)。

Azure RemoteApp では、1 人のユーザーが、自覚なしで同じ日に複数台のコンピューターにログインできます。 これは、サービスがクラウドのリソースを自動的に管理してスケーリングするためであり、ユーザーは共有するアプリとプログラムだけを認識します。 このシナリオでは、Office 365 ProPlus は、コンピューターのアクティブ化モードの共有を提供します。つまり、ユーザーはリソースにアクセスするためにライセンス管理を行う必要がなく、個々のコンピューターは 5 台というアクティブ化制限の対象としてカウントされることがなくなります。

管理者が Office 365 ProPlus ライセンスをユーザーに割り当てると、ユーザーは個人のデバイスで、および Azure RemoteApp コレクションから、Office を使用できます。

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Office 365 と Azure RemoteApp ではどの Office アプリケーションを使用できますか?
Office 365 サブスクリプションを使用すると、Azure RemoteApp デプロイメントでは Office 2013 をアクティブ化して共有できます。 現在、Azure RemoteApp では他のバージョンの Office の使用はサポートされていません。 これには、Office 2003、Office 2007、Office 2010 および Office 2016 が含まれます。

## <a name="what-about-visio-pro-or-project-pro"></a>Visio Pro または Project Pro はどうですか?
RemoteApp サブスクリプションに含まれる Office 365 ProPlus のイメージには、Visio Pro と Project Pro の両方が含まれます。 ただし、Office 365 ProPlus サブスクリプションを使用してこれらのプログラムをアクティブ化することはできません。それぞれに専用のライセンスがあります。 それらは [Office 365 ポータル](https://portal.office365.com/)でアクティブ化できます。 

これらのプログラムを使用しない場合は、ライセンスを取得する必要はありません。 使用するプログラムだけをアクティブ化し、他はスキップしてください。 イメージにはまだ表示されますが、それらを使用することはできません。 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Office 365 と Azure RemoteApp を使い始めるにはどうすればよいですか?
Office 365 ライセンスの詳細がわかったら、Azure RemoteApp で使ってみてください。とても簡単です。

Azure RemoteApp コレクションを作成するときは、 **[Office 365 ProPlus (サブスクリプションが必要)]** イメージを使用します。

![Azure RemoteApp イメージと Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)

このイメージには、最新バージョンの Windows Server と Office 365 ProPlus が含まれます。 コレクション (発行アプリを含む) を構成した後、ユーザーは (RemoteApp クライアントを使用して) Azure RemoteApp にログインし、Office アプリに Office 365 資格情報を提供するだけです。 ライセンスは自動的に提供され、どのようなセットアップも管理も必要ありません。

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Office 365 ProPlus でカスタム イメージを作成できますか?
Office 365 ProPlus を含むコレクションのカスタム イメージを作成できます。 2 つの選択肢があり、提供される Azure ギャラリー イメージを使用するか、または独自のカスタム イメージを作成してそこに Office 365 ProPlus をインストールします。

### <a name="use-the-azure-gallery-image"></a>Azure ギャラリー イメージを使用する
コレクションに Office 365 ProPlus をデプロイする最も簡単な方法は、Azure RemoteApp サブスクリプションに含まれる [Azure ギャラリー イメージのいずれかで開始する](remoteapp-image-on-azurevm.md) ことです。 **[Office 365 ProPlus がプレインストールされている Windows Server リモート デスクトップ セッション ホスト]** イメージを必ず選択します。 次に、そのイメージに必要な他のアプリをインストールすれば、準備完了です。

### <a name="use-a-custom-image"></a>カスタム イメージを使用する
いつでもカスタム イメージを作成できます。[Azure VM](remoteapp-image-on-azurevm.md) を作成するか、[ローカルにイメージを作成](remoteapp-create-custom-image.md)してそれを Azure にアップロードします。 どちらの場合も、共有コンピューター アクティブ化ノードを使用して Office 365 ProPlus をインストールしてください。 [Office 展開ツール](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx)を使用し、インストールの[手順](https://technet.microsoft.com/library/Dn782858.aspx)に従います。  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>カスタム イメージで Office 365 ProPlus の自動更新を無効にする - 重要
カスタム イメージは、ユーザーの要求が増えたときに新しいリソースを追加するためテンプレートとして、Azure RemoteApp によって使用されます。 遅延や接続の問題を回避するため、イメージでは Office の自動更新を無効にしてください。 無効にしないと、そのテンプレートで作成されたすべてのリソースが、開始時に自動的に更新されます。 代わりに、カスタム イメージの更新には標準の Azure RemoteApp プロセスを使用します。 テンプレート イメージで Office アプリケーションを&1; 回更新した後、Azure RemoteApp でユーザーを更新します。

自動更新を無効にするには、Office 展開ツールの構成ファイルに以下を追加します。

        <Updates Enabled="FALSE" />

構成ファイルには以下の行が含まれるようになります。

        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Office 365 ProPlus でイメージを更新するにどうすればよいですか?
コレクション内のイメージを更新する多くの理由があります。 次に、いくつか示します。

* Windows の最新の更新プログラムを取得する 
* Office 365 ProPlus アプリケーションの最新の更新プログラムを取得する
* カスタム アプリを更新する
* イメージ自体の他の構成設定を変更する

更新したイメージを使用するようにコレクションを更新する手順については、 [こちら](remoteapp-update.md)をご覧ください。 ただし、イメージおよび Office 365 ProPlus を更新する方法については、以下の情報を確認してください。

イメージを更新するには&2; つのオプションがあります。イメージを完全に新しいものに置き換える方法と、既存イメージを手動で更新する方法です。

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>最新の Azure ギャラリー イメージでイメージを置き換え、カスタマイズを追加する
このオプションでは、Microsoft に Windows Server および Office 365 ProPlus を更新させます。 既存のイメージを更新する代わりに、最新のギャラリー イメージを基にしてまったく新しいイメージを作成します。 次に、イメージをカスタマイズしたときの手順を繰り返し、カスタム アプリのインストールや、イメージ構成の変更などを行います。

ギャラリーのイメージは定期的に更新されるので、Windows Server および Office 365 ProPlus アプリも更新されます。 もちろん欠点もあり、新しいイメージを取得するたびに、カスタマイズを適用する必要があります。 カスタマイズの設定を自動化するスクリプトを作成できます。

### <a name="manually-update-your-existing-image"></a>既存のイメージを手動で更新する
このオプションでは、標準の Windows ツールを使用して、イメージに更新を適用します。 Office 365 ProPlus の場合、Office デプロイメント ツールを使用して、Office 365 ProPlus の最新の更新プログラムまたはバージョンをダウンロードしてインストールします。

> [!IMPORTANT]
> 注意: Office 365 ProPlus の自動更新を無効にしてください。
> 
> 

Office 展開ツールを使用した更新の詳細については以下をご覧ください。

* [Office 展開ツールを使用して Office 365 製品のクイック実行を展開する](https://technet.microsoft.com/library/JJ219423.aspx)
* [Office 展開ツールを使用して Office 365 ProPlus を展開および更新する](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (ビデオ)
* [Office 365 ProPlus の更新設定を構成する](https://technet.microsoft.com/library/dn761708.aspx)




<!--HONumber=Dec16_HO2-->


