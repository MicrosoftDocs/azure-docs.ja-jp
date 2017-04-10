---
title: "Azure RemoteApp のクラウド コレクションの作成方法 | Microsoft Docs"
description: "Azure クラウドにデータを保存する Azure RemoteApp のデプロイを作成する方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 4d7c6956-7e4a-4a41-b7f2-7e5832bf01e3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 59619d78959f9fcc3144a30af883021a8f5f3044


---
# <a name="how-to-create-a-cloud-collection-of-azure-remoteapp"></a>Azure RemoteApp のクラウド コレクションの作成方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

[Azure RemoteApp のコレクション](remoteapp-collections.md)には、次の 2 種類があります。 

* クラウド: Azure に完全に常駐します。 クラウドにすべてのデータを保存するか (したがって、クラウドのみのコレクション)、コレクションを VNET に接続して、そこにデータを保存するかを選択できます。   
* ハイブリッド: オンプレミス アクセス用の仮想ネットワークを含みます。これには、Azure AD およびオンプレミスの Active Directory 環境を使用する必要があります。

このチュートリアルでは、クラウド コレクションを作成する手順について説明します。 次の 4 つのステップがあります。 

1. Azure RemoteApp コレクションを作成する。
2. オプションでディレクトリの同期を構成する。 Azure AD と Active Directory を使用している場合、オンプレミスの Active Directory のユーザー、連絡先、およびパスワードを Azure AD テナントと同期する必要があります。
3. アプリを発行する。
4. ユーザー アクセスを構成する。

**開始する前に**

コレクションを作成する前に以下の操作が必要です:

* [サインアップ](https://azure.microsoft.com/services/remoteapp/) します。 
* アクセス権を付与するユーザーに関する情報を集めます。 この情報とは、ユーザーの Microsoft アカウントの情報または Active Directory の仕事用アカウントの情報です。
* この手順は、サブスクリプションの一部として提供されたテンプレートのイメージを使用しようとしているか、使用するテンプレートのイメージが既にアップロード済みであることが前提となっています。 別のテンプレート イメージをアップロードする必要がある場合は、[テンプレート イメージ] ページから実行できます。 **[テンプレート イメージのアップロード]** をクリックして、ウィザードで表示される手順に従います。 
* Office 365 ProPlus イメージを使用する必要がありますか。 [こちら](remoteapp-officesubscription.md)で詳細を確認できます。
* カスタム アプリケーションまたは LOB プログラムが必要な場合は、 新しい[イメージ](remoteapp-imageoptions.md)を作成して、クラウド コレクションで使用します。
* VNET に接続する必要があるかどうかを検討します。 VNET への接続を選択する場合は、[サイズのガイドライン](remoteapp-vnetsizing.md)を満たしていることと、[RemoteApp に接続できること](remoteapp-vnet.md)を確認します。 詳細については、 [VNET 計画に関する記事 ](remoteapp-planvnet.md)を確認してください。
* VNET を使用している場合は、ローカルの Active Directory ドメインに参加させるかどうかを決定します。

## <a name="step-1-create-a-cloud-collection---with-or-without-a-vnet"></a>手順 1: クラウド コレクションの作成 - VNET あり/なし
**クラウドのみのコレクション**を作成するには、次の手順に従います。

1. 管理ポータルで、[RemoteApp] ページに移動します。
2. **[新規] > [簡易作成]** をクリックします。
3. コレクションの名前を入力し、リージョンを選択します。
4. Standard または Basic から、使用するプランを選択します。
5. このコレクションに使用するテンプレートを選択します。 
   
    **ヒント:** RemoteApp のサブスクリプションには、Word などの公開されたプログラムおよび公開する準備の完了したプログラムを含む Office 365 または Office 2013 (評価版) プログラムが含まれている [テンプレート イメージ](remoteapp-images.md) が付属しています。 また、新しい [Custom Template Image (カスタム テンプレート イメージ)](remoteapp-imageoptions.md) を作成し、クラウド コレクションで使用することができます。
6. **[RemoteApp コレクションの作成]**をクリックします。
   
    **重要:** コレクションがプロビジョニングされるまで最大 30 分かかる場合があります。

Azure RemoteApp コレクションが作成されたら、コレクションの名前をダブルクリックします。 これにより、**[クイック スタート]** ページが表示されます。このページで、コレクションの構成を完了します。

**クラウドおよび VNET のコレクション**を作成するには、次の手順に従います。

1. 管理ポータルで、[Azure RemoteApp] ページに移動します。
2. **[新規]** > **[VNET で作成]** の順にクリックします。
3. コレクションの名前を入力します。
4. Standard または Basic から、使用するプランを選択します。
5. 既に作成した VNET を選択します。 その方法をご存じないでしょうか。 現在、その手順は [ハイブリッド](remoteapp-create-hybrid-deployment.md) に関するトピックに記載されています。
6. コレクションをドメインに参加させるかどうかを決定します。 参加させる場合、AD 接続を使用して、Azure AD と Active Directory 環境を統合する必要があります。 これは、後述の **手順 2.**で説明します。
7. **[RemoteApp コレクションの作成]**をクリックします。

## <a name="step-2-configure-active-directory-directory-synchronization-optional"></a>手順 2. Active Directory ディレクトリの同期を構成する (省略可能)
Active Directory を使用する場合、Azure RemoteApp では、Azure Active Directory とオンプレミスの Active Directory との間でディレクトリの同期が必要になります。これにより、ユーザー、連絡先、およびパスワードが Azure Active Directory テナントと同期します。 計画の詳細については、「[Azure RemoteApp の Active Directory の構成](remoteapp-ad.md)」を参照してください。 また、詳細について [AD 接続](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/)を直接参照することもできます。

## <a name="step-3-publish-apps"></a>手順 3: アプリを発行する
Azure RemoteApp アプリケーションは、ユーザーに提供するアプリケーションまたはプログラムのことです。 このプログラムは、コレクション用にアップロードしたテンプレート イメージに置かれています。 ユーザーがアプリケーションにアクセスすると、アプリケーションはローカル環境で実行されているように見えますが、実際には Azure の仮想マシンで実行されています。 

ユーザーがアプリケーションにアクセスするには、アプリケーションを発行しておく必要があります。アプリケーションを発行すると、ユーザーはリモート デスクトップ クライアントからアプリケーションにアクセスできます。

Azure RemoteApp コレクションには複数のアプリケーションを発行できます。 発行ページから、 **[発行]** をクリックしてプログラムを追加します。 プログラムは、テンプレート イメージの **[スタート]** メニューから発行することも、テンプレート イメージのパスをアプリケーションに指定して発行することもできます。 **[スタート]** メニューから追加する場合は、発行するアプリケーションを選択します。 アプリケーションのパスを指定して発行する場合は、アプリケーションの名前と、アプリケーションがインストールされるテンプレート イメージ上の保存場所までのパスを指定します。

## <a name="step-4-configure-user-access"></a>手順 4. ユーザー アクセスを構成する
これでコレクションが作成できたので、リモート リソースを使用可能にするユーザーを追加する必要があります。 Active Directory を使用している場合は、このコレクションを作成するために使用したサブスクリプションに関連付けられた Active Directory テナントに、アクセス権を付与するユーザーが存在している必要があります。

1. [クイック スタート] ページの **[ユーザー アクセスの構成]**をクリックします。 
2. アクセス権を付与する (Active Directory の) 仕事用アカウントか、または Microsoft アカウントを入力します。
   
   **注:** 
   
   必ず *user@domain.com* 形式を使用してください。
   
   コレクションで Office 365 ProPlus を使用している場合は、ユーザーの Active Directory ID を使用する必要があります。 これにより,ライセンスを検証できます。 
3. ユーザーが検証されたら、 **[保存]**をクリックします。

## <a name="next-steps"></a>次のステップ
これで、Azure RemoteApp クラウド コレクションの作成とデプロイが正常に完了しました。 次のステップは、ユーザーによるリモート デスクトップ クライアントのダウンロードとインストールです。 このクライアントの URL は、Azure RemoteApp の [クイック スタート] ページにあります。 次に、ユーザーがクライアントにログインし、発行したアプリケーションにアクセスします。

### <a name="help-us-help-you"></a>サポートのお願い
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上へスクロールし、**[GitHub で編集]** をクリックすると変更を加えることができます。その後は Microsoft で確認が行われ、承認されると変更点や改善点がこちらのページに反映されます。




<!--HONumber=Dec16_HO2-->


