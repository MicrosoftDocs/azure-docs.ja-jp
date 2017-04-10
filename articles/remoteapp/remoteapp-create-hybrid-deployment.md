---
title: "Azure RemoteApp のハイブリッド コレクションの作成方法 | Microsoft Docs"
description: "内部ネットワークに接続する RemoteApp のデプロイを作成する方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 08ea0ce3-3a2c-4ddf-9394-6d75c8030cb1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 15430904979108cbebe4a64a8ae07f2f01b82690


---
# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Azure RemoteApp のハイブリッド コレクションの作成方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp のコレクションには、次の 2 種類があります。

* クラウド: Azure に完全に常駐します。 クラウドにすべてのデータを保存するか (したがって、クラウドのみのコレクション)、コレクションを VNET に接続して、そこにデータを保存するかを選択できます。   
* ハイブリッド: オンプレミス アクセス用の仮想ネットワークを含みます。これには、Azure AD およびオンプレミスの Active Directory 環境を使用する必要があります。

何が必要かわかりませんか。 「 [Azure RemoteApp にはどのような種類のコレクションが必要ですか](remoteapp-collections.md)」を参考にしてください。

このチュートリアルでは、ハイブリッド コレクションを作成する手順について説明します。 次の 8 つの手順があります。

1. コレクションに使用する [イメージ](remoteapp-imageoptions.md) を決定する。 これには、カスタム イメージを作成するか、サブスクリプションに含まれているいずれかの Microsoft イメージを使用します。
2. 仮想ネットワークをセットアップする。 [VNET 計画](remoteapp-planvnet.md)と[サイジング](remoteapp-vnetsizing.md)情報を確認する。
3. コレクションを作成する。
4. コレクションをローカル ドメインに参加させます。
5. テンプレート イメージをコレクションに追加する。
6. ディレクトリ同期を構成する。 Azure RemoteApp を使用するには、次のいずれかの方法で Azure Active Directory と統合する必要があります。1) パスワード同期オプションを使用して Azure Active Directory 同期を構成する。2) パスワード同期オプションを使用しないで Azure Active Directory 同期を構成する。ただし、この場合は AD FS にフェデレーションされたドメインを使用する。 [Azure RemoteApp の Active Directory の構成情報](remoteapp-ad.md)を参照してください。
7. RemoteApp のアプリを発行する。
8. ユーザー アクセスを構成する。

**開始する前に**

コレクションを作成する前に以下の操作が必要です:

* [サインアップ](https://azure.microsoft.com/services/remoteapp/) します。
* Azure RemoteApp サービス アカウントとして使用するためのユーザー アカウントを Active Directory に作成します。 ドメインへのマシンの参加のみが実行可能になるように、このアカウントのアクセス許可を制限します。
* オンプレミスのネットワークに関する情報、つまり IP アドレス情報と VPN デバイスの詳細情報を収集します。
* [Azure PowerShell](/powershell/azureps-cmdlets-docs) モジュールをインストールします。
* アクセス権を付与するユーザーに関する情報を集めます。 ユーザーごとに Azure Active Directory のユーザー プリンシパル名 (たとえば、name@contoso.com) が必要です。 Azure AD と Active Directory 間で UPN が一致していることを確認します。
* テンプレート イメージを選択します。 Azure RemoteApp テンプレート イメージには、ユーザーに発行するアプリケーションとプログラムが含まれます。 詳細については、 [Azure RemoteApp イメージのオプション](remoteapp-imageoptions.md) に関するページを参照してください。
* Office 365 ProPlus イメージを使用する必要がありますか。 [こちら](remoteapp-officesubscription.md)で詳細を確認できます。
* [RemoteApp を使用するために Active Directory を構成します](remoteapp-ad.md)。

## <a name="step-1-set-up-your-virtual-network"></a>手順 1. 仮想ネットワークをセットアップする
既存の Azure 仮想ネットワークを使用するハイブリッド コレクションをデプロイすることも、新しい仮想ネットワークを作成することもできます。 ユーザーは仮想ネットワークを利用することで、ローカル ネットワーク上のデータに RemoteApp リモート リソースを介してアクセスします。 Azure 仮想ネットワークを使用すると、コレクションは、他の Azure サービスおよびその仮想ネットワークにデプロイされている仮想マシンへの直接ネットワーク アクセスが可能になります。

VNET を作成する前に、[VNET 計画](remoteapp-planvnet.md)と [VNET サイズ](remoteapp-vnetsizing.md)に関する情報を確認してください。

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Azure VNET を作成して Active Directory のデプロイに参加させる
まず [仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)の作成から開始します。 これは Azure ポータルの **[ネットワーク]** タブで実行します。 仮想ネットワークを、Azure Active Directory テナントと同期している Active Directory デプロイに接続する必要があります。

詳細については、「 [Azure ポータルを使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) 」をご覧ください。

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>仮想ネットワークで Azure RemoteApp を使用する準備ができていることを確認します。
コレクションを作成する前に、新しい仮想ネットワークで準備ができていることを確認します。 これは次の手順を実行することで検証できます。

1. RemoteApp 用に作成した仮想ネットワークのサブネット内に Azure 仮想マシンを作成します。
2. リモート デスクトップを使用して仮想マシンに接続します (**[接続]** をクリックします)。
3. RemoteApp 用に使用するのと同じ Active Directory デプロイに参加します。

これで動作するかを確認します。 動作すれば、仮想ネットワークとサブネットは Azure RemoteApp を使用する準備ができています。

リモート デスクトップでの Azure 仮想マシンの作成と接続についての詳細は、 [ここ](https://msdn.microsoft.com/library/azure/jj156003.aspx)を参照してください。

## <a name="step-2-create-an-azure-remoteapp-collection"></a>手順 2: Azure RemoteApp コレクションを作成する
1. [Azure ポータル](http://manage.windowsazure.com)で、[Azure RemoteApp] ページに移動します。
2. **[新規] > [VNET で作成]** の順にクリックします。
3. コレクションの名前を入力します。
4. Standard または Basic から、使用するプランを選択します。
5. ドロップダウン リストから VNET を選択し、サブネットを選択します。
6. それがドメインに参加するよう選択します。
7. **[RemoteApp コレクションの作成]**をクリックします。

Azure RemoteApp コレクションが作成されたら、コレクションの名前をダブルクリックします。 これにより、**[クイック スタート]** ページが表示されます。このページで、コレクションの構成を完了します。

何か問題がありますか。 [ハイブリッド コレクションのトラブルシューティング情報](remoteapp-hybridtrouble.md)を参照してください。

## <a name="step-3-link-your-collection-to-the-local-domain"></a>手順 3. コレクションをローカル ドメインに関連付ける
1. **[クイック スタート]** ページで、**[ローカル ドメインに参加]** をクリックします。
2. Azure RemoteApp サービス アカウントをローカル Active Directory ドメインに追加します。 追加するには、ドメイン名、組織単位、サービス アカウントのユーザー名とパスワードが必要になります。
   
    これは、「 [Azure RemoteApp の Active Directory を構成する](remoteapp-ad.md)」の手順に従った場合に収集される情報です。

## <a name="step-4-link-to-an-azure-remoteapp-image"></a>手順 4: Azure RemoteApp イメージのリンクを作成する
Azure RemoteApp テンプレート イメージにはユーザーと共有するプログラムが含まれています。 新しい [テンプレート イメージ](remoteapp-imageoptions.md) を作成することも、既存のイメージ (既に Azure RemoteApp にインポート済みまたはアップロード済みのもの) に関連付けることもできます。 Office 365 または Office 2013 (評価版) プログラムが含まれている、いずれかの Azure RemoteApp [テンプレート イメージ](remoteapp-images.md) に関連付けることもできます。

新しいイメージをアップロードする場合は、イメージの名前を入力して場所を選択する必要があります。 ウィザードの次のページに PowerShell コマンドレットのセットが表示されます。特定のイメージをアップロードするために、このコマンドレットをコピーして管理者特権の Windows PowerShell プロンプトから実行します。

既存のテンプレート イメージを関連付ける場合は、そのイメージ名、保管場所、関連先の Azure のサブスクリプションを指定するだけで実行できます。

## <a name="step-5-configure-active-directory-directory-synchronization"></a>手順 5. Active Directory ディレクトリの同期を構成する
Azure RemoteApp を使用するには、次のいずれかの方法で Azure Active Directory と統合する必要があります。1) パスワード同期オプションを使用して Azure Active Directory 同期を構成する。2) パスワード同期オプションを使用しないで Azure Active Directory 同期を構成する。ただし、この場合は AD FS にフェデレーションされたドメインを使用する。

[AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) に関するページを参照してください。この記事では、4 つの手順でディレクトリ統合を設定する方法を説明します。

計画に関する情報、および詳しい手順については、「 [ディレクトリ同期のロードマップ](http://msdn.microsoft.com//library/azure/hh967642.aspx) 」を参照してください。

## <a name="step-6-publish-apps"></a>手順 6: アプリを発行する
Azure RemoteApp アプリケーションは、ユーザーに提供するアプリケーションまたはプログラムのことです。 このプログラムは、コレクション用にアップロードしたテンプレート イメージに置かれています。 ユーザーがアプリケーションにアクセスすると、アプリケーションはユーザーのローカル環境で実行しているように見えますが、実際には Azure で実行しています。

ユーザーがアプリケーションにアクセスするには、アプリケーションを発行しておく必要があります。これにより、ユーザーはリモート デスクトップ クライアントからアプリケーションにアクセスできます。

コレクションには複数のアプリケーションを発行できます。 発行ページから、 **[発行]** をクリックしてアプリを追加します。 プログラムは、テンプレート イメージの **[スタート]** メニューから発行することも、テンプレート イメージのパスをアプリケーションに指定して発行することもできます。 **[スタート]** メニューから追加する場合は、発行するアプリケーションを選択します。 アプリケーションのパスを指定して発行する場合は、アプリケーションの名前と、アプリケーションがインストールされるテンプレート イメージ上の保存場所までのパスを指定します。

## <a name="step-7-configure-user-access"></a>手順 7. ユーザー アクセスを構成する
これでコレクションが作成できたので、リモート リソースを使用可能にするユーザーを追加する必要があります。 アクセス権を付与するユーザーは、この Azure RemoteApp コレクションを作成するために使用したサブスクリプションに関連付けられた Active Directory テナントに存在している必要があります。

1. [クイック スタート] ページの **[ユーザー アクセスの構成]**をクリックします。
2. アクセス権を付与する (Active Directory の) 仕事用アカウントか、または Microsoft アカウントを入力します。
   
   **注:**
   
   必ず *user@domain.com* 形式を使用してください。
   
   コレクションで Office 365 ProPlus を使用している場合は、ユーザーの Active Directory ID を使用する必要があります。 これにより,ライセンスを検証できます。
3. ユーザーが検証されたら、 **[保存]**をクリックします。

## <a name="next-steps"></a>次のステップ
これで、Azure RemoteApp ハイブリッド コレクションの作成とデプロイが正常に完了しました。 次のステップは、ユーザーによるリモート デスクトップ クライアントのダウンロードとインストールです。 このクライアントの URL は、Azure RemoteApp の [クイック スタート] ページにあります。 次に、ユーザーがクライアントにログインし、発行したアプリケーションにアクセスします。

### <a name="help-us-help-you"></a>サポートのお願い
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上へスクロールし、**[GitHub で編集]** をクリックすると変更を加えることができます。その後は Microsoft で確認が行われ、承認されると変更点や改善点がこちらのページに反映されます。




<!--HONumber=Dec16_HO2-->


