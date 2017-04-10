---
title: "Azure RemoteApp とその先にあるリソースへのアクセスのセキュリティ保護 | Microsoft Docs"
description: "Azure Active Directory で条件付きアクセスを使用して、Azure RemoteApp へのアクセスをセキュリティで保護する方法について説明します。"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: a19b0b09-ab26-4beb-80bb-33a46da39887
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 3cd588751346517f9359f760561839339b0f9edb


---
# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Azure RemoteApp とその先にあるリソースへのアクセスのセキュリティ保護
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

この記事では、管理者が、エンド ユーザーから始まりセキュリティで保護されたリソース (SQL Database や他のアプリケーション バックエンドなど) で終わる、Azure RemoteApp 経由のセキュリティで保護されたアクセス チャネルをセットアップする方法の概要を示します。 その目的は、必要な条件を満たす許可ユーザーのみがリモート アプリケーションにアクセスできるようにすることと、セキュリティで保護されたバックエンドに制御された Azure RemoteApp 環境からのみアクセスできるようにし、他の場所からはアクセスできないようにすることです。

管理者が確認する必要がある 3 つの主な領域を以下に示します。

![Azure RemoteApp の条件付きアクセスに関する考慮事項](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

詳細およびこれらの質問に対する回答については、以下で説明します。

## <a name="who-can-access-the-collection"></a>コレクションにアクセスできるのは誰ですか?
管理者は、コレクション内のリモート アプリケーションにアクセスできるユーザーを選択します。 Azure Active Directory (Azure AD) の職場または学校のアカウント (旧称、"組織アカウント") または Microsoft アカウント (例: @outlook.com). を使用することができます。ほとんどのエンタープライズ シナリオでは Azure AD アカウントが使用されます。このアカウントでは、後で説明する条件付きアクセス機能を使用できます。また、ドメイン参加コレクションに対して選択できるのはこのアカウントのみです。 記事の後半では、Azure RemoteApp で Azure AD アカウントを使用することを前提としています。

**実現内容:**

Azure RemoteApp へのアクセスを制御するために Azure AD アカウントを使用すると、次の&2; つのことが実現できます。

1. 発行されたアプリケーションと、それらのアプリケーションの接続先となるバックエンドにアクセスできるユーザーを常に把握する。
2. ユーザー アカウントの作成と削除、パスワード ポリシーの設定、多要素認証の使用などができるように、基になる Azure AD を制御する。 

## <a name="how-is-the-collection-accessed-from-where"></a>コレクションにはどのようにアクセスしますか? また、どこからアクセスしますか?
一般的に、管理者は Azure RemoteApp など、インターネットに接続されたパブリック環境へのアクセスに関するポリシーを定義します。 たとえば、管理者は、企業ネットワーク外部からその環境にアクセスするユーザーは多要素認証 (MFA) を使用してアクセスしなければならない (または完全にブロックする) ようにします。

Azure RemoteApp 管理者は、Azure AD Premium で使用可能な機能を用いて、Azure RemoteApp 環境に対して条件付きアクセス ポリシーを設定できます。 また、豊富なレポートおよびアラート機能を使用して、環境へのアクセス方法を監視することもできます。

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Azure RemoteApp に対する条件付きアクセスの設定方法
ここでは、ユーザーが企業ネットワークの外部にいる場合、Azure RemoteApp 管理者は環境へのアクセスをブロックするシナリオ例について説明します。

> [!NOTE]
> Azure AD が Premium レベルにアップグレード済みで、1 つ以上の Azure RemoteApp コレクションを作成済みであることを前提とします。
> 
> 

1. Azure ポータルで、 **[Active Directory]** タブをクリックします。 次に、構成するディレクトリをクリックします。
   
   注: 条件付きアクセスは、Azure RemoteApp ではなく、ご使用のディレクトリのプロパティであるため、すべての構成はディレクトリ レベルで行われます。 これは、変更を加えるにはディレクトリ管理者でなければならないことも意味します。
2. **[アプリケーション]** をクリックしてから、**[Microsoft Azure RemoteApp]** をクリックして条件付きアクセスを設定します。 ディレクトリ内の "サービスとしてのソフトウェア" アプリケーションごとに個別に条件付きアクセスを設定できることに注意してください。
   ![Azure RemoteApp に対する条件付きアクセスの設定](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
3. **[構成]** タブで、**[アクセス規則を有効にする]** を [オン] に設定します。
   ![Azure RemoteApp のアクセス規則を有効にする](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
4. ここで、さまざまな規則を構成し、適用するユーザーを選択できます。
   
   1. **[作業中でない場合、アクセスをブロック]** を選択し、指定したネットワーク環境外からのユーザーによる Azure RemoteApp へのアクセスを完全に防ぎます。
   2. 下にあるオプションをクリックし、"信頼されたネットワーク" を構成する IP アドレス範囲を定義します。 それ以外のものはすべて拒否されます。
5. 指定した範囲外の IP アドレスから Azure RemoteApp クライアントを起動して、構成をテストします。 Azure AD の資格情報でサインインした後、次のようなメッセージが表示されます。

![Azure RemoteApp へのアクセス拒否](./media/remoteapp-secureaccess/ra-accessdenied.png)

### <a name="future-conditional-access-features"></a>将来の条件付きアクセスの機能
Azure Active Directory チームは、条件付きアクセスの新機能に取り組んでいます。 管理者は、ネットワークの場所ベースの規則のほかに、新しい種類の規則を作成できるようになります。 パブリック プレビューの新機能は間もなく利用可能になります。

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Azure RemoteApp へのアクセスの監視方法
条件付きアクセスと共に使用する優れた機能として、Azure Active Directory Premium のレポート作成機能があります。 レポートを使用することで、自分の環境にアクセスしているユーザーを監視し、疑わしいアクティビティを検出できます。

たとえば、Azure RemoteApp にアクセスしたユーザーの名前、アクセスの回数および時刻を確認できます。

1. Azure ポータルで、 **[Active Directory]**をクリックしてから使用するディレクトリをクリックします。
2. **[レポート]** タブに移動します。
3. レポートの一覧から、**[統合アプリケーション]** の **[アプリケーションの使用状況]** を選択します。
   
   Azure RemoteApp の集計された統計がいくつか表示されます。 
   ![集計された Azure RemoteApp アクセスの統計](./media/remoteapp-secureaccess/ra-accessstats.png)
4. アプリケーションをクリックして、Azure RemoteApp にアクセスしているユーザーに関する情報を表示します。
   ![Azure RemoteApp のユーザー アクセスの統計](./media/remoteapp-secureaccess/ra-userstats.png)

### <a name="summary"></a>概要
Azure Active Directory Premium では、Azure RemoteApp (および Azure AD を介して使用可能な他のサービスとしてのソフトウェア アプリケーション) に対するアクセス規則を設定できます。 現時点では、規則はネットワークの場所ベースのポリシーに制限されていますが、将来的には、エンタープライズ管理の他の部分に拡張されます。

また、Azure AD Premium では、管理者による Azure RemoteApp 環境に対する制御範囲をさらに拡大するレポートと監視機能も提供されます。

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>セキュリティで保護されたリソースに Azure RemoteApp 環境からのみアクセスできるようにするにはどうすればよいですか?
この記事の前のセクションでは、Azure RemoteApp 環境へのアクセスをセキュリティで保護することに重点を置きました。 これは、アクセスを許可するユーザーを選択し、サービスの使用方法をさらに細かく制御するためのアクセス規則を設定して行いました。

Azure RemoteApp デプロイメントの一般的なシナリオは、リモート アプリケーションが SQL Database などのバックエンド リソースと通信を行う必要があるのいうものです。 このリソースは、オンプレミス (企業ネットワークなど) またはクラウド (Azure IaaS など) でホストされます。 管理者は、ユーザーの PC 上で直接実行されており、パブリック インターネット経由でアクセスするアプリケーションなどではなく、Azure RemoteApp を使用してデプロイされたアプリケーションからのみ、バックエンド リソースにアクセスできるようにする場合がよくあります。 多くの場合、Azure RemoteApp が一元的に管理され、セキュリティで保護された環境と見なされるため、ユーザーは Azure RemoteApp を経由しないとバックエンド リソースと対話することはできません。

このソリューションでは、同じ Azure Virtual Network (VNET) に Azure RemoteApp 環境とセキュリティで保護されたリソースの両方を配置します。 リソースが別のサイトにある場合は、Azure データ センターとお客様のオンプレミス環境にまたがる VNet を作成する場合などに、サイト間 VPN 接続を確立できます。

Azure RemoteApp では、ユーザーが独自の VNET を指定できる以下の&2; 種類のコレクション デプロイメントがサポートされています。

* ドメイン不参加: アプリケーションから、VNET 内の他のリソースを見通すことができます。 たとえば、これを使用して、SQL 認証を使用する SQL Database にアプリケーションを接続することができます (アプリケーションがデータベースに対して直接ユーザーを認証)。
* ドメイン参加: Azure RemoteApp で使用される仮想マシンは、VNET 内のドメイン コント ローラーに参加しています。 これは、アプリケーションがバックエンド リソースにアクセスするために、Windows ドメイン コントローラーに対して認証する必要がある場合に便利です。
  ![Azure RemoteApp のドメイン参加コレクション](./media/remoteapp-secureaccess/ra-domainjoined.png)

### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Azure とオンプレミス環境間のセキュリティで保護された接続を作成する方法
Azure とオンプレミス環境を接続するためのいくつかの構成オプションがあります。 ここでは、オプションの概要を示します。

Azure RemoteApp では、最初に VNet を構成してから、コレクションの作成プロセスでそれを使用する必要があります。 

## <a name="the-complete-solution"></a>完全なソリューション
以下の図は、エンド ユーザーからバックエンド リソースへの Azure RemoteApp (ARA) 経由のセキュリティで保護されたアクセス チャネルが構築された完全なソリューションを示しています。
![セキュリティで保護された Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) ステージ 1 では、ユーザーを選択し、ARA へのアクセス方法を制御するアクセス規則を作成しました。 この例では、企業ネットワークから作業を行っているユーザーに対してのみアクセスを許可しています。 非準拠ユーザーは、ARA 環境にはまったくアクセスすることはできません。
"ステージ 2" では、制御下の VNet/VPN 構成でのみバックエンド リソースを公開しました。 Azure RemoteApp は同じ VNet 内に配置されています。 最終的に、リソースは ARA 環境を介してのみアクセスできます。




<!--HONumber=Dec16_HO2-->


