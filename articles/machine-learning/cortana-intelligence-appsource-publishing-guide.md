---
title: "Cortana Intelligence の AppSource 発行ガイド | Microsoft Docs"
description: "Microsoft パートナーとして、Cortana Intelligence ソリューションを AppSource に発行するために従う必要のあるすべての手順を紹介します。"
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

--- 
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Cortana Intelligence の AppSource 発行ガイド

## <a name="overview"></a>概要
AppSource では、パートナーが構築し、マイクロソフトが評価したビジネス ソリューション/アプリを、管理職意思決定者 (BDM) が 1 か所で検索してシームレスに試すことができます。 AppSource の機能については、[こちらのビデオ](https://youtu.be/hpq_Y9LuIB8)をご覧ください。 

Microsoft パートナーとして AppSource に発行することで実際に利点が得られるのは次のようなケースです。
- [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable) を使用してインテリジェント ソリューション/アプリを構築している。
- ソリューションやアプリがビジネスの具体的な問題に対処している。
- 構築したモジュールや知的財産を、お客様が比較的簡単かつ予測可能な方法で再利用できる。

AppSource で公開されている [Cortana Intelligence ソリューション](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1)をご覧ください。 

この記事では、パートナーの Cortana Intelligence ソリューションを AppSource に発行する手順について詳しく説明します。

## <a name="getting-started"></a>使用の開始
1. Advanced Analytics のパートナーとして掲載される方法については、[Partner Community Benefits Guide (パートナー コミュニティ ベネフィット ガイド)](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF) の 9 ページをご覧ください。
1. [[アプリの申請]](https://appsource.microsoft.com/en-us/partners/list-an-app) フォームに入力します。

    "*アプリがビルドされた製品を選択してください*" という質問で、**[その他]** チェックボックスをオンにして、編集コントロールに「Cortana Intelligence」を一覧表示します。
1. Cortana Intelligence アプリは、AppSource にオンボードされる前に Cortana Intelligence のパートナー ソリューション テクノロジー チームの認定を受ける必要があります。 プロセスを開始するには、[Cortana Intelligence solution evaluation for AppSource publishing](https://aka.ms/cisappsrceval) のアンケート フォームに入力し、アプリの情報を提供してください。 このサイトはパスワードで保護されており、サイトにはパスワードを取得する手順が用意されています。

## <a name="solution-evaluation-criteria"></a>ソリューションの評価基準
アプリが満たす必要がある条件の一覧を次に示します。
1. アプリは、短期間で実装可能な事前定義された値の割合に対する最小構成を使用し、繰り返し可能な方法で、一定の機能領域内におけるビジネス上の具体的なユース ケースに関する問題に対処する必要があります。
1. ソリューションは、次のコンポーネントの少なくとも 1 つを使用する必要があります。

    - HDInsight
    - 機械学習
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Microsoft R Server スタンドアロン
    - SQL 2016 または HDInsight Premium 上の R サービス
1. ソリューションは、DPOR/CSPを使用してお客様ごとに月に $1000 以上をもたらす必要があります。
1. ソリューションは、ユーザー認証およびリソース アクセス制御が有効になる同意の下で、Azure Active Directory フェデレーション シングル サインオン (AAD フェデレーション SSO) を使用する必要があります。 アプリをAppSource にオンボードする前に、対象のソリューションの AAD フェデレーション SSO が有効になっている必要があることを、評価チームに示す必要があります。

     「AAD フェデレーション SSO が有効になっている」の意味については、[AppSource trial experience walk through](https://aka.ms/trialexperienceforwebapps) (AppSource 試用エクスペリエンスのチュートリアル) のビデオの 02:35 辺りをご覧ください。 アプリの AAD フェデレーション SSO がまだ有効になっていない場合については、次のドキュメントで説明されています。
   1. [ワンクリック サインイン](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/)。
   1. [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)。
     
1. ソリューションで Power BI を使用します。任意ですが、より多くの潜在顧客を生み出すことが実証されているため、強くお勧めします。

## <a name="devcenter-account-setup"></a>デベロッパー センター アカウントのセットアップ
これは、マイクロソフトのパブリッシャーとして自社を登録するプロセスです。 既存のデベロッパー センター アカウントでパブリッシャーとしてすでに登録されている場合は、デベロッパー センター アカウントに関連付けられている電子メール ID を共有します。 アプリケーションの公開が承認されると、[Cloud Portal Manage パブリッシャー プロファイル](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)の完全ドキュメントにアクセスできます。

ない場合は、デベロッパー センター アカウントをセットアップします。以下に主な手順を示します。
1. [こちら](https://signup.live.com/signup.aspx)から Microsoft アカウントを作成します。
1. Microsoft デベロッパー センターの[登録ページ](http://go.microsoft.com/fwlink/?LinkId=615100)に移動して [サインアップ] をクリックします。
1. 支払いが要求されたら、マイクロソフトから提供されたコードを使用します。 コードを持っていない場合は、[appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup) にお問い合わせください。
1. 居住しているまたは会社がある[国/リージョン](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)を選択します。 **これは後で変更することはできません。**
1. [開発者アカウントの種類](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)を選択します。AppSource では、**[会社]** を選択します。 会社アカウントの場合は、こちらの[ガイドライン](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)を確認してください。
1. 開発者アカウントに使用する連絡先情報を入力します。
デベロッパー センター アカウントをセットアップする方法の詳しい手順については、[こちら](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)をご覧ください。

## <a name="provide-info-for-microsoft-sellers"></a>Microsoft 販売者の情報を提供する
パートナーにとっての AppSource の主なバリュー プロポジションの 1 つは、Microsoft 販売者と協力してパートナーのアプリを潜在顧客に提示することです。

[Partner Solution Info for Microsoft Sellers](https://aka.ms/aapartnerappinfo) (Microsoft 販売者用のパートナー ソリューション情報) に入力して [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs) に送信します。 これは、Cortana Intelligence アプリの発行が承認されるうえで必要な手順です。

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>AppSource で説得力のあるお客様向けのチュートリアルを作成する
まず、AppSource で [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) をご覧ください。 AppSource 内のすべてのアプリのエントリには、試用エクスペリエンス用のエントリ ポイント以外に、タイトル、概要 (最大で 100 文字)、説明 (最大で 1300 文字)、画像、ビデオ (オプション)、PDF ドキュメントなどの情報があります。 パートナーはこれらすべてを活用して、説得力のあるカスタマー エクスペリエンスを構築する必要があります。

パートナーは、AppSource に掲載するコンテンツを、エンドツーエンドのセールス オーケストレーションとして捉える必要があります。 お客様はタイトルや説明を読むことでそのバリュー プロポジションを理解し、画像やビデオを通じてそのソリューションの内容を把握します。 ケース スタディは、既存のお客様がどのようにして価値を得ているかを潜在顧客が確認するのに役立ちます。 

これらはすべてお客様の興味をかきたて、お客様にもっと知りたいと思わせます。 これらは、技術サービスを販売する優れたセールスマンが新しいお客様に説明するためのピッチ デッキ ベースのプレゼンテーションとして捉えてください。 説明として推奨される形式は、バリュー プロポジションに基づいてテキストをサブセクションに分割することです。各サブセクションはサブ見出しで強調します。 

閲覧者は通常「オファーの概要」フィールドとサブ見出しにざっと目を通すことで、アプリの目的について要点を理解し、なぜそのアプリの購入を検討するかをほんの一目で判断します。 このため、ユーザーの注意を引き、詳細を読む理由を与えることが重要です。

これらのパートナーの成果をご覧ください。
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure Retail Personalization](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

セールスの最終的な活動は、アプリやソリューションのデモを提示してバリュー プロポジションをどのようにして提供するかを説明することです。 これがお客様にとって AppSource の試用エクスペリエンスが意味するものです。 優れたデモでは次のことを行います。
- アプリのバリュー プロポジションを再度概略化し、お客様の企業内でソリューションの操作を担うペルソナを列挙する
- 具体的な問題に対処しているサンプルのお客様についてストーリーを構築し、コンテキストを設定する
- その状況が一般的にどのような負担や影響をお客様に与えるか (ソリューションを使用する前の状況) と、ソリューションを使用した場合はどうなるかについて説明する。 これは、PowerBI のダッシュボードなどを使用して提示できます。
- 特定の Machine Learning アルゴリズムなどを使用して、ソリューションがそれをどのよう実現するについて概要を提示する。

AppSource に追加されるコンテンツは、次のことを実現するように高品質かつ十分に仕立てられている必要があります。
- パートナーのテクニカル セールス担当者が自身のセールス オーケストレーションにそれを使用することをお勧めします。 セールス チームがそれを使用することは、MS のセールス担当者も同じように行うことが期待できるよい兆候です。 これにより、お客様視点を持った担当者が購入取引を行う前に、同じストーリーをチームの同僚や上層部に継続的に伝えることで、予算や承認を獲得できます。
- サイトを組織的に訪問するお客様はコンテンツを独力で調査し、パートナーに積極的に応答してやり取りを次の段階に進めます。

パートナーが AppSource に掲載するコンテンツをエンドツーエンドのセールス オーケストレーションとして捉えることをお勧めする理由はここにあります。 試用エクスペリエンスに表示されるあらすじや機能に基づいて、オファーの種類を決めることができます。

## <a name="publish-your-app-on-the-publishing-portal"></a>発行ポータルでアプリを発行する
前述の手順に従って対象のアプリケーションを評価したら、発行ポータルにアクセスします。次の手順について詳しくは、[Cloud Partner Portal を使用して Cortana Intelligence オファーを発行する方法](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app)に関するページをご覧ください。

フィールドに関する情報が必要な場合は、<appsourcecissupport@microsoft.com> まで電子メールでお問い合わせください。
## <a name="my-app-is-published-on-appsource---now-what"></a>アプリが AppSource に発行されました。次はどうすればよいでしょうか?
アプリが無事に発行されました。
AppSource にアプリを発行することで得られる成果のレベルは、対象ユーザーにどのように影響を与えるかに大きく依存します。 成果を最大化する方法について詳しくは、「[Growth-Hacking your Cortana Intelligence app on AppSource](http://aka.ms/aagrowthhackguide)」 (AppSource で Cortana Intelligence アプリをグロース ハッキングする) をご覧ください。

ご質問、ご提案につきましては、<appsourcecissupport@microsoft.com> までお問い合わせください。


