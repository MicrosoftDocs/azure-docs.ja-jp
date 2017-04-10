---
title: "クラウド内のオンプレミスの ID インフラストラクチャの監視"
description: "これは、Azure AD Connect Health の内容と使用する理由を説明するページです。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: d995918ef51f345bf76a25f9b6908cf5f4aac6c9
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視
Azure Active Directory (Azure AD) Connect Health では、オンプレミスの ID インフラストラクチャと同期サービスを監視、分析できます。 これを使用すると、Active Directory フェデレーション サービス (AD FS) サーバー、Azure AD Connect サーバー (同期エンジン)、Active Directory ドメイン コントローラーなど、お使いの主要な ID コンポーネントの監視機能が用意されているため、Office 365 と Microsoft Online Services への信頼性の高い接続を維持できます。また、これらのコンポーネントの主要なデータ ポイントにアクセスしやすくなるため、使用状況やその他の重要な分析を取得して、しかるべき情報に基づく判断ができるようになります。

この情報は、[Azure AD Connect Health ポータル](https://aka.ms/aadconnecthealth)に表示されます。 Azure AD Connect Health ポータルでは、アラート、パフォーマンスの監視、使用状況の分析、その他の情報を表示できます。 Azure AD Connect Health では、1 つのレンズで主要な ID コンポーネントの正常性をまとめて確認できます。

![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Azure AD Connect Health の機能の増加に伴い、ID というレンズを使用した単一のダッシュボードがポータルに用意されました。 より強固で正常な統合環境が提供され、お客様のユーザーの生産性が向上します。

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health を使用する理由
オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミスの両方のリソースに共通の ID でアクセスできるため、ユーザーの生産性が向上します。 ただし、この統合には課題があります。ユーザーが任意のデバイスからオンプレミスとクラウド両方のリソースに安全にアクセスできるように、この環境の正常性を確保する必要があります。 Azure AD Connect Health では、Office 365 または他の Azure AD アプリケーションにアクセスするときに使用するオンプレミスの ID インフラストラクチャを監視して分析することができます。 オンプレミスの各 ID サーバーにエージェントをインストールするぐらいにシンプルです。

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health for AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health for AD FS では、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 上の AD FS 2.0 がサポートされています。 また、エクストラネット アクセスの認証をサポートする AD FS プロキシまたは Web アプリケーション プロキシ サーバーの監視もサポートの対象に含まれます。 Health エージェントを簡単に低コストでインストールできる Azure AD Connect Health for AD FS の主な機能は次のとおりです。

* AD FS と AD FS プロキシ サーバーが正常に動作していない場合に通知されるアラートを使用して監視する
* 重大なアラートの電子メール通知を送信する
* AD FS の容量計画に役立つ、パフォーマンス データの傾向を表示する
* AD FS の利用方法を把握するのに役立つように、(アプリ、ユーザー、ネットワークの場所などの) ピボットで AD FS サインインの使用状況を分析する
* 無効なユーザー名とパスワードによる試行を行った上位 50 人のユーザーと直近の IP アドレスなど、AD FS に関するレポートを作成する

次のビデオでは、Azure AD Connect Health for AD FS の概要について説明します。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health for Sync](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health for Sync は、オンプレミスの Active Directory と Azure AD の間で実行された同期を監視し、情報を提供します。 Azure AD Connect Health for Sync の主な機能は次のとおりです。

* Azure AD Connect サーバー (同期エンジン) が正常に動作していない場合に通知されるアラートを使用して監視する
* 重大なアラートの電子メール通知を送信する
* 同期操作の待ち時間を示すグラフと各種操作 (追加、更新、削除など) の傾向を含む、同期操作の分析を行う
* 同期プロパティ、最後に正常に行われた Azure AD へのエクスポートについての概要情報を表示する
* オブジェクトレベルの同期エラーについてレポートするのに \(Azure AD Premium は不要\)

次のビデオでは、Azure AD Connect Health for Sync の概要について説明します。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health for AD DS](active-directory-aadconnect-health-adds.md)
Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 にインストールされているドメイン コントローラーは、Azure AD Connect Health for Active Directory Domain Services (AD DS) で監視することができます。 Health エージェントをインストールすることによって、クラウドからオンプレミスの AD DS 環境を監視できるようになります。 Azure AD Connect Health for AD DS の主な機能は次のとおりです。

* ドメイン コントローラーの異常を検出する監視アラートと重大なアラートを知らせる電子メール通知
* [ドメイン コントローラー] ダッシュボード。ドメイン コントローラーの正常性と運用状態を把握できるクイック ビューアーが用意されています
* [レプリケーションの状態] ダッシュボード。レプリケーションに関する直近の情報のほか、エラーが検出された場合はトラブルシューティング ガイドへのリンクが表示されます
* 使用頻度の高いパフォーマンス カウンターのパフォーマンス データ グラフへの迅速なアクセス。トラブルシューティングと監視を行ううえで必要不可欠です

次のビデオでは、Azure AD Connect Health for AD DS の概要について説明します。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health の使用開始
Azure AD Connect Health の使用を開始するには、次の手順に従います。

1. [Azure AD Premium](../active-directory-get-started-premium.md) を入手するか、[試用版の利用を開始](https://azure.microsoft.com/trial/get-started-active-directory/)します。
2. [Azure AD Connect Health エージェントをダウンロードし、ID サーバーにインストール](#download-and-install-azure-ad-connect-health-agent)します。
3. [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) で Azure AD Connect Health ダッシュボードを表示します。

> [!NOTE]
> Azure AD Connect Health ダッシュボードでデータを表示するためには、あらかじめ対象サーバーに Azure AD Connect Health エージェントをインストールしておく必要があります。
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Azure AD Connect Health エージェントのダウンロードとインストール
* Azure AD Connect Health の[要件を必ず満たし](active-directory-aadconnect-health-agent-install.md#requirements)てください。
* Azure AD Connect Health for AD FS の使用を開始します
    * [Azure AD Connect Health for AD FS エージェントをダウンロードします](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [インストール手順を参照します](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* Azure AD Connect Health for sync の使用を開始します
    * [最新バージョンの Azure AD Connect をダウンロードしてインストールします](http://go.microsoft.com/fwlink/?linkid=615771)。 同期用の Health エージェントは、Azure AD Connect のインストールの一環としてインストールされます (バージョン 1.0.9125.0 以上)。
* Azure AD Connect Health for AD DS の使用を開始します
    * [Azure AD Connect Health for AD DS エージェントをダウンロードします](http://go.microsoft.com/fwlink/?LinkID=820540)。
    * [インストール手順を参照します](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)。

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health ポータル
Azure AD Connect Health ポータルでは、アラート、パフォーマンスの監視、使用状況の分析に関するビューが表示されます。 https://aka.ms/aadconnecthealth という URL から Azure AD Connect Health のメイン ブレードに移動できます。 ブレードは、ウィンドウと考えることができます。 メイン ブレードでは、**[クイック スタート]**、Azure AD Connect Health で提供されるサービス、その他の構成オプションが表示されます。 次のスクリーンショットとそれに続く簡単な説明をご覧ください。 エージェントのデプロイ後、Azure AD Connect Health で監視されているサービスが、Health サービスによって自動的に識別されます。

> [!NOTE]
> ライセンスの情報については、[Azure AD Connect の FAQ](active-directory-aadconnect-health-faq.md) または [Azure AD の価格に関するページ](https://aka.ms/aadpricing)を参照してください。
    
![Azure AD Connect Health ポータル](./media/active-directory-aadconnect-health/portal4.png)

* **[クイック スタート]**: このオプションを選択すると、**[クイック スタート]** ブレードが開きます。 **[ツールの入手]** を選択することで、Azure AD Connect Health エージェントをダウンロードできます。 ドキュメントの利用とフィードバックの提供もできます。
* **[Active Directory フェデレーション サービス]**: このオプションを選択すると、Azure AD Connect Health が現在監視しているすべての AD FS サービスが表示されます。 インスタンスを選択すると、ブレードが開いてそのサービス インスタンスに関する情報が表示されます。 この情報には、概要、プロパティ、アラート、監視、使用状況の分析などが含まれます。 [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)に関するページで各種機能を参照してください。
* **[Azure Active Directory Connect (Sync)]**: このオプションを選択すると、Azure AD Connect Health が現在監視している Azure AD Connect サーバーが表示されます。 エントリを選択すると、ブレードが開いて Azure AD Connect サーバーに関する情報が表示されます。 [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)に関するページで各種機能を参照してください。
* **[Active Directory Domain Services]**: このオプションを選択すると、Azure AD Connect Health が現在監視しているすべての AD DS フォレストが表示されます。 フォレストを選択すると、ブレードが開いてそのフォレストに関する情報が表示されます。 この情報には、重要度のきわめて高い情報、ドメイン コントローラーのダッシュボード、レプリケーションの状態のダッシュボード、アラート、監視の概要が含まれます。 「[AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)」で各種機能を参照してください。
* **[構成]**: このセクションには、次の機能をオンまたはオフに切り替えるオプションがあります。

  - Azure AD Connect Health エージェントを最新バージョンに自動更新する: Azure AD Connect Health エージェントの最新バージョンが使用可能になると、自動的に更新されます。 この機能は、既定では有効になっています。
  - Microsoft がトラブルシューティングの目的でのみ Azure AD ディレクトリの正常性データにアクセスすることを許可する: この機能が有効になっていると、ユーザーに表示されているのと同じデータを Microsoft でも表示できます。 この情報は問題のトラブルシューティングと支援に役立ちます。 この機能は、既定では無効になっています。

## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health の FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health のバージョン履歴](active-directory-aadconnect-health-version-history.md)

