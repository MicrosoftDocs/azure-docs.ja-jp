---
title: "Azure RemoteApp の FAQ | Microsoft Docs"
description: "Azure RemoteApp についてよく寄せられる質問の回答を確認する。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: swadhwa
editor: 
ms.assetid: bad66603-91f9-437f-8a70-236405d2a27f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9d80640a58171e94440ca02f44a73866f6412997
ms.openlocfilehash: 296dfaaa05b89cae265a9926fad08495ea02a647


---
# <a name="azure-remoteapp-faq"></a>Azure RemoteApp よく寄せられる質問
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp について次のような質問が寄せられています。 他にもありますか? [RemoteApp フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) にアクセスして必要な情報についてお知らせいただくか、下のコメント欄にご記入ください。

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>お探しの情報がない場合、 またはまだ回答のない質問がある場合
必要な情報が見つからない場合や、ここでは取り上げられていない不明な点がある場合は、 [Azure RemoteApp フォーラム](http://aka.ms/araforum) にアクセスして質問を投稿してください。 不明点への回答は随時こちらに追加していきます。

## <a name="what-is-azure-remoteapp"></a>Azure RemoteApp とは
* **Azure RemoteApp とは** RemoteApp とは、多くのさまざまなユーザー デバイスからアプリケーションへのセキュリティで保護されたリモート アクセスを提供する際に役立つ Azure サービスです。 詳細については、「[Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
* **デプロイメント オプションにはどのようなものがありますか。** RemoteApp のコレクションには、クラウドとハイブリッドの&amp;2; つの種類があります。 ドメインへの参加の要不要など、さまざまな要素によって必要なコレクションが決まります。 この決定については、 [こちらのページ](remoteapp-collections.md)で詳しく説明しています。

## <a name="quick-tips-on-using-azure-remoteapp"></a>Azure RemoteApp の使用に関するクイック ヒント
* **切断されるまでの時間はどのくらいですか。どれくらいアイドル状態が続くとサインアウトされますか。** 4 時間です。 ユーザーのアイドル状態が 4 時間続くと、自動的に Azure RemoteApp からサインアウトされます。 その他の既定の設定については、「[Azure Subscription and Service Limits, Quotas, and Constraints (Azure サブスクリプションとサービスの制限、クォータ、制約)](../azure-subscription-service-limits.md)」を参照してください。
* **このサービスは無料で試すことができますか。** はい。 30 日間使える無料試用版があります。 試行期間が終了すると、(運用環境で使用可能な) 有料アカウントに切り替えるか、サービスの使用を終了することができます。 [portal.azure.com](http://portal.azure.com) にアクセスして無料試用版を開始し、RemoteApp の新しいインスタンスを作成してください。 無料試用版では、2 つの RemoteApp インスタンスを作成でき、インスタンスあたり 10 ユーザーをサポートできます。 試行期間は、30 日であることに注意してください。
  
  ## <a name="azure-remoteapp-subscription-details"></a>Azure RemoteApp サブスクリプションの詳細
* **サービスの制限とは何ですか。** Azure RemoteApp の既定の設定とサービスの制限については、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。 他に質問がある場合は、お知らせください。
* **必要なユーザー数は、どの程度でしょうか。** 少なくとも 20 ユーザーが必要です。 明確にするために繰り返しますと、最低ユーザー数は 20 となっています。 つまり、20 ユーザー分の代金が課金されることになります。 
* **RemoteApp の料金についてはどうですか。** 「 [Azure RemoteApp の料金体系について ](https://azure.microsoft.com/pricing/details/remoteapp/)」を参照してください。
* **コレクションの種類によってはコストがかかるものはありますか。** コレクションに対する要件によってはそうなる場合があります。 ハイブリッド コレクションの場合、Azure RemoteApp からオンプレミス ネットワークへの接続が必要です。 既存の VNET と Express のルートを使用する場合、追加コストはありません。 ただし、新しい Azure VNET と、ゲートウェイまたは ExpressRoute のいずれかを使用する場合は、[VPN ゲートウェイ](https://azure.microsoft.com/pricing/details/vpn-gateway)または [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/) に対して課金されます。 このコスト (詳細はリンクを参照) が、毎月の Azure の RemoteApp のコストに追加されます。

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>サポートされているコレクションや使用すべきコレクションなど
* **カスタム基幹業務 (LOB) アプリケーションはサポートされていますか。** はい。 Azure RemoteApp でカスタム アプリケーションを使用するには、 [カスタム テンプレート イメージ](remoteapp-create-custom-image.md)を作成し、RemoteApp コレクションにアップロードします。
* **自社のカスタム LOB アプリケーションは、Azure RemoteApp で動作しますか。** この質問の回答を見つけ出す最善の方法は、テストすることです。 [RD 互換性センター](http://www.rdcompatibility.com/compatibility/default.aspx)をご確認ください。
* **組織にとって最適なデプロイメント方法は、クラウドとハイブリッドのどちらですか。** ハイブリッド コレクションは、シングル サインオン (SSO) との完全な統合、およびセキュリティで保護されたオンプレミスのネットワーク接続を必要としている場合に、最も包括的なエクスペリエンスを提供します。 クラウド コレクションでは、複数の認証方法を使用することにより、アジャイルかつ簡単な方法を使用して、分離されたデプロイメントを構築できます。 デプロイメント オプションの詳細については、「 [Azure RemoteApp とは](remoteapp-whatis.md)」を参照してください。
* **SQL または別のデータベースが、オンプレミスまたは Azure にあります。どちらの種類のデプロイメントを使用すべきでしょうか。** これは SQL またはバックエンドのデータベースの場所によります。 データベースがプライベート ネットワークにある場合は、ハイブリッド コレクションを使用します。 データベースがインターネットに表示され、クライアント接続で接続できる場合は、クラウド コレクションを使用できます。
* **ドライブの割り当て、USB とシリアル ポート、クリップボードの共有、およびプリンターのリダイレクトについてはどうでしょうか。** Azure RemoteApp は、これらのすべての機能についてサポートしています。 クリップボードの共有とプリンターのリダイレクトは、既定で有効になります。 リダイレクトの詳細については、「 [Using redirection in Azure RemoteApp](remoteapp-redirection.md)」を参照してください。 

## <a name="template-images"></a>テンプレート イメージ
* **クラウドまたは既存の仮想マシンを、RemoteApp コレクションのテンプレートとして使用できますか。** はい。 Azure VM に基づくイメージを作成することも、サブスクリプションに含まれるイメージのいずれかを使用することも、カスタム イメージを作成することもできます。 [RemoteApp のイメージ オプション](remoteapp-imageoptions.md)を参照してみてください。

## <a name="network-options"></a>ネットワーク オプション
* **ハイブリッド コレクションは、VNET を必要とします。既存の VNET を使用できますか。** 既存の VNET が Azure VNET であれば、使用できます。 詳細については、[ハイブリッド コレクションの手順](remoteapp-create-hybrid-deployment.md)に関するページの「手順 1. 仮想ネットワークをセットアップする」を参照してください。
* **クラウドのコレクションで VNET を使用できますか。** はい、できます。 詳細については、「 [クラウド コレクションを作成する](remoteapp-create-cloud-deployment.md)」の特に手順 1 を参照してください。

## <a name="authentication-options"></a>認証オプション
* **認証については、どの方法がサポートされていますか。** クラウド コレクションは、Office 365 アカウントでもある Microsoft アカウントと Azure Active Directory アカウントをサポートしています。 ハイブリッド コレクションは、([Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx) のようなツールを使用して) Windows Server Active Directory のデプロイから同期されている Azure Active Directory アカウント、特にパスワード同期オプションまたは Active Directory フェデレーション サービス (AD FS) の構成済みフェデレーションのいずれかで同期されているアカウントのみをサポートしています。 [Multi-Factor Authentication (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/) を構成することもできます。

> [!NOTE]
> Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントに属している必要があります (サブスクリプションは、ポータルの **[設定]** タブで表示、変更できます。 詳細については、[RemoteApp を使用した Azure Active Directory テナントの変更](remoteapp-changetenant.md)に関するページを参照してください)。
> 
> 

* **使用している Azure Active Directory アカウントにアクセス許可を付与できないのはなぜですか。** Azure Active Directory ユーザーは、サブスクリプションに関連付けられているディレクトリに属している必要があります。 このディレクトリは、ポータルの [設定] タブで表示および変更できます。 詳細については、「 [Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md) 」を参照してください。

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>クライアント - Azure RemoteApp へのアクセスにはどのようなデバイスを使用できますか?
さまざまなクライアントのインストール手順を含む、クライアントに関するよい情報は、「 [Azure RemoteApp でのアプリへのアクセス](remoteapp-clients.md)」を参照してください。

* **クライアント アプリケーションがサポートするデバイスとオペレーティング システムはどれでしょう。**
  コンピューターとタブレット: 
  
  * Windows 10 (クライアント プレビュー)
  * Windows 8.1 および Windows 8
  * Windows 7 Service Pack 1
  * Mac OS X
  * Windows RT
  * Android タブレット
  * iPad

    と電話:
  * iPhone
  * Android フォン
  * Windows Phone
    
    [ダウンロード](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) 。
* **Azure RemoteApp はシン クライアントをサポートしますか。** はい、次の Windows Embedded シン クライアントがサポートされます。
  
  * Windows Embedded Standard 7
  * Windows Embedded 8 Standard
  * Windows Embedded 8.1 Industry Pro
  * Windows 10 IoT Enterprise
* **リモート デスクトップ セッション ホスト (RDSH) 用にサポートされる Windows Server のバージョンはどれですか。** Windows Server 2012 R2 です。

## <a name="support-and-feedback"></a>サポートとフィードバック
* **RemoteApp のサポート プランについてはどうですか。** 課金とサブスクリプションの管理は無料でサポートされます。 テクニカル サポートが [Azure のサービス プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。 [Azure のディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)を通して、無料のコミュニティ サポートも得ることができます。 
* **フィードバックを発行する方法を教えてください。** [フィードバック フォーラム](https://feedback.azure.com/forums/247748-azure-remoteapp/)」を参照してください。
* **Azure RemoteApp についての問い合わせ先を教えてください。** 質問を投稿するのに最適な場所である[ディスカッション フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp)に加え、RemoteApp に関するあらゆる情報を毎週お届けする、[エキスパートへの質問ウェビナー](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html)にも参加できます。
* **RemoteApp に関するドキュメントはありますか。** ご問い合わせありがとうございます。 ポータルのヘルプ ドロアーのヘルプ コンテンツ (ポータルのいずれかのページの **?** をクリックするだけです) に加えて、RemoteApp のすべてについて詳細に説明する次の資料を参照できます。
  
  * **概要:**
    * [Azure RemoteApp とは](remoteapp-whatis.md)
    * [RemoteApp テンプレート イメージとは何ですか。](remoteapp-images.md)
    * [How does licensing work? (ライセンスはどのように機能しますか。)](remoteapp-licensing.md)
    * [RemoteApp と Office は、どうやって一緒に使うのですか?](remoteapp-o365.md)
    * [RemoteApp でリダイレクトはどのように使用できますか](remoteapp-redirection.md)?
  * **デプロイ:**
    * [RemoteApp のカスタム テンプレート イメージの作成方法](remoteapp-create-custom-image.md)
    * [RemoteApp のハイブリッド デプロイメントの作成方法](remoteapp-create-hybrid-deployment.md)
    * [RemoteApp のクラウド コレクションの作成方法](remoteapp-create-cloud-deployment.md)
    * [Configure Azure Active Directory for RemoteApp (RemoteApp に Azure Active Directory を構成する)](remoteapp-ad.md)
    * [RemoteApp のアプリを公開する](remoteapp-publish.md)
  * **管理:**
    
    * [ユーザーの追加](remoteapp-user.md)
    * [Best practices for configuring and using RemoteApp (Azure RemoteApp を構成し、使用するためのベスト プラクティス)](remoteapp-bestpractices.md)    
    
    ビデオ。 RemoteApp についての多くのビデオがあります。 概要を説明しているビデオ ([Azure RemoteApp の概要](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) もあれば、デプロイについてのチュートリアルを提供するビデオ ([クラウド デプロイ](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be)と[ハイブリッド デプロイ](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)) もあります。 確認してください。

### <a name="help-us-help-you"></a>サポートのお願い
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上へスクロールし、**[GitHub で編集]** をクリックすると変更を加えることができます。その後は Microsoft で確認が行われ、承認されると変更点や改善点がこちらのページに反映されます。




<!--HONumber=Jan17_HO4-->


