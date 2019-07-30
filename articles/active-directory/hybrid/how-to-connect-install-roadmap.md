---
title: Azure AD Connect と Azure AD Connect Health のインストール ロードマップ。 | Microsoft Docs
description: このドキュメントでは、Azure AD Connect および Connect Health のインストールに使用できるインストール オプションとパスの概要について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d6cce859aa26935f0b6007fe8e5ff2c2239b210
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109445"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Azure AD Connect と Azure AD Connect Health のインストール ロードマップ

## <a name="install-azure-ad-connect"></a>Azure AD Connect のインストール

> [!IMPORTANT]
> 公式に文書化されているアクションを除き、Microsoft は Azure AD Connect Sync の変更や操作をサポートしません。 サポートされていないアクションを行うと、Azure AD Connect Sync が不整合な状態になったり、サポートされていない状態になったりする可能性があります。結果的に、Microsoft ではこのようなデプロイについてテクニカル サポートを提供できなくなります。

Azure AD Connect は [Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?LinkId=615771)からダウンロードできます。

| 解決策 | シナリオ |
| --- | --- |
| 開始する前に - [ハードウェアと前提条件](how-to-connect-install-prerequisites.md) |<li>Azure AD Connect のインストールを開始する前に実行する手順です。</li> |
| [簡単設定](how-to-connect-install-express.md) |<li>シングル フォレストの AD が存在する場合、この方法をお勧めします。</li> <li>ユーザーは、パスワード同期により、同じパスワードを使ってサインインします。</li> |
| [カスタマイズした設定](how-to-connect-install-custom.md) |<li>複数のフォレストがある場合に使用されます。 多くのオンプレミス [トポロジ](plan-connect-topologies.md)がサポートされます。</li> <li>パススルー認証、フェデレーション用の ADFS などのサインイン オプションをカスタマイズするか、サード パーティの ID プロバイダーを使います。</li> <li>フィルター処理やライトバックなどの同期機能をカスタマイズします。</li> |
| [DirSync からのアップグレード](how-to-dirsync-upgrade-get-started.md) |<li>既存の DirSync サーバーが既に実行されている場合に使用します。</li> |
| [Azure AD Sync または Azure AD Connect からのアップグレード](how-to-upgrade-previous-version.md) |<li>複数の方法から自由に選択できます。</li> |

[インストール後に](how-to-connect-post-installation.md) 想定どおりに動作していることをことを確認し、ユーザーにライセンスを割り当てる必要があります。

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect のインストールの次のステップ
|トピック |Link|  
| --- | --- |
|Azure AD Connect のダウンロード | [Azure AD Connect のダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 設定を使用したインストール | [Azure AD Connect の高速インストール](./how-to-connect-install-express.md)|
|カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](./how-to-connect-install-custom.md)|
|DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](./how-to-dirsync-upgrade-get-started.md)|
|インストール後に | [インストールの確認とライセンスの割り当て](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Azure AD Connect のインストールの詳細
[運用](how-to-connect-sync-operations.md) 上の問題への備えも必要になることがあります。 [障害](how-to-connect-sync-staging-server.md#disaster-recovery)が発生したときにフェールオーバーできるように、スタンバイ サーバーを用意するという方法もあります。 頻繁に構成を変更する予定がある場合は、 [ステージング モード](how-to-connect-sync-staging-server.md) サーバーについて計画してください。

|トピック |リンク|  
| --- | --- |
|サポートされているトポロジ | [Azure AD Connect のトポロジ](plan-connect-topologies.md)|
|設計概念 | [Azure AD Connect の設計概念](plan-connect-design-concepts.md)|
|インストールで使用するアカウント | [Azure AD Connect の資格情報とアクセス許可の詳細](reference-connect-accounts-permissions.md)|
|運用計画 | [Azure AD Connect 同期:操作タスクおよび考慮事項](how-to-connect-sync-operations.md)|
|ユーザーのサインイン オプション | [Azure AD Connect ユーザーのサインイン オプション](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>同期機能を構成する
Azure AD Connect には、必要に応じて有効にすることができる機能や、既定で有効になっている機能があります。 ただし一部の機能は、特定のシナリオやトポロジを実現するために、特別な構成が必要となります。

[フィルター処理](how-to-connect-sync-configure-filtering.md) は、Azure AD に同期するオブジェクトを制限する場合に使用します。 既定では、すべてのユーザー、連絡先、グループ、Windows 10 コンピューターが同期の対象となります。 フィルター処理は、ドメインや OU、属性に基づいて変更することができます。

[パスワード ハッシュ同期](how-to-connect-password-hash-synchronization.md)は、Active Directory のパスワード ハッシュを Azure AD と同期させる機能です。 エンド ユーザーがオンプレミスとクラウドで同じパスワードを使用でき、しかもそれを 1 か所で管理することができます。 オンプレミスの Active Directory が認証機関として使用されているため、独自のパスワード ポリシーを使用することもできます。

[パスワード ライトバック](../authentication/quickstart-sspr.md) により、ユーザーはクラウドでパスワードを変更およびリセットし、オンプレミスのパスワード ポリシーを適用できます。

[デバイスの書き戻し](how-to-connect-device-writeback.md)により、Azure AD に登録されているデバイスをオンプレミスの Active Directory にライトバックできます。これにより、そのデバイスを条件付きアクセスに使用できるようになります。

[誤って削除されないように保護する](how-to-connect-sync-feature-prevent-accidental-deletes.md) 機能は既定で有効になっており、多数のクラウド ディレクトリが同時に削除されるのを防ぐことができます。 1 回の実行で削除できるディレクトリは、既定では 500 個です。 この設定は、組織の規模に応じて変更できます。

[自動アップグレード](how-to-connect-install-automatic-upgrade.md) は、簡単設定を使用したインストールでは既定で有効になっており、Azure AD Connect が最新のリリースで常に最新の状態になるようにします。

### <a name="next-steps-to-configure-sync-features"></a>同期機能を構成する次のステップ
|トピック |Link|  
| --- | --- |
|フィルター処理の構成 | [Azure AD Connect 同期:フィルター処理の構成](how-to-connect-sync-configure-filtering.md)|
|パスワード ハッシュの同期 | [パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)|
|パススルー認証 | [パススルー認証](how-to-connect-pta.md)
|パスワードの書き戻し | [パスワード管理の概要](../authentication/quickstart-sspr.md)|
|デバイスの書き戻し | [Azure AD Connect でのデバイスの書き戻しの有効化](how-to-connect-device-writeback.md)|
|誤って削除されないように保護する | [Azure AD Connect 同期:誤って削除されないように保護する](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|自動アップグレード | [Azure AD Connect:自動アップグレード](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Azure AD Connect Sync のカスタマイズ
Azure AD Connect Sync には、ほとんどのお客様とトポロジに対応した既定の構成が設定されています。 とはいえ、既定の構成ではうまくいかず、調整が必要な場面も必ず存在します。 このセクションとリンク先のトピックにまとめられているように、構成は変更できます。

これまでに同期トポロジを扱った経験がない場合は、 [技術的概念](how-to-connect-sync-technical-concepts.md)で説明されている基本情報と用語を確認してください。 Azure AD Connect は、MIIS2003、ILM2007、FIM2010 が進化したものです。 同じ要素もあるものの、多数の変更が加えられています。

この [既定の構成](concept-azure-ad-connect-sync-default-configuration.md) は、複数のフォレストが存在する可能性があることを前提としています。 これらのトポロジでは、ユーザー オブジェクトが別のフォレスト内の連絡先として表されることがあります。 ユーザーは、別のリソース フォレストにリンクされたメールボックスを持っている場合もあります。 既定の構成の動作については、 [ユーザーと連絡先](concept-azure-ad-connect-sync-user-and-contacts.md)に関するページを参照してください。

同期の構成モデルは、 [宣言型のプロビジョニング](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)と呼ばれています。 高度な属性のフローでは、 [関数](reference-connect-sync-functions-reference.md) を使って属性の変換を表現します。 Azure AD Connect に付属するツールを使って、構成全体を確認、検証できます。 構成を変更する必要がある場合は、新しいリリースを採用しやすいように、 [ベスト プラクティス](how-to-connect-sync-best-practices-changing-default-configuration.md) に従ってください。

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Azure AD Connect Sync のカスタマイズの次のステップ
|トピック |Link|  
| --- | --- |
|Azure AD Connect Sync に関するすべての記事 | [Azure AD Connect Sync](how-to-connect-sync-whatis.md)|
|技術的概念 | [Azure AD Connect 同期:技術的概念](how-to-connect-sync-technical-concepts.md)|
|既定の構成について | [Azure AD Connect 同期:既定の構成について](concept-azure-ad-connect-sync-default-configuration.md)|
|ユーザーと連絡先について | [Azure AD Connect 同期:ユーザーと連絡先について](concept-azure-ad-connect-sync-user-and-contacts.md)|
|宣言型のプロビジョニング | [Azure AD Connect Sync: 宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|既定の構成の変更 | [既定の構成の変更するためのベスト プラクティス](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>フェデレーション機能を構成する

Azure AD Connect には、Azure AD との認証連携を AD FS の使用とフェデレーション信頼の管理を通じて省力化するさまざまな機能が備わっています。 Azure AD Connect では、Windows Server 2012R2 以降の AD FS がサポートされます。

フェデレーション信頼の管理に Azure AD Connect を使っていない場合でも、[AD FS ファームの SSL 証明書を更新](how-to-connect-fed-ssl-update.md)することができます。

ファームに [AD FS サーバーを追加](how-to-connect-fed-management.md#addadfsserver)することで必要に応じてファームを拡張できます。

たった数回のクリック操作で Azure AD との[信頼を修復](how-to-connect-fed-management.md#repairthetrust)できます。

ADFS は [複数のドメイン](how-to-connect-install-multiple-domains.md)をサポートするように構成できます。 たとえば、フェデレーションに利用する複数の上位ドメインが必要になることがあります。

Azure AD から証明書を自動更新するように ADFS サーバーを更新していない場合、または非 ADFS ソリューションを使用している場合、[証明書の更新](how-to-connect-fed-o365-certs.md)が必要になったときに通知されます。

### <a name="next-steps-to-configure-federation-features"></a>フェデレーション機能を構成する次のステップ
|トピック |Link|  
| --- | --- |
|AD FS に関するすべての記事 | [Azure AD Connect とフェデレーション](how-to-connect-fed-whatis.md)|
|サブドメインで ADFS を構成する | [Azure AD とのフェデレーションに使用する複数ドメインのサポート](how-to-connect-install-multiple-domains.md)|
|AD FS ファームを管理する | [Azure AD Connect を使用した AD FS の管理とカスタマイズ](how-to-connect-fed-management.md)|
|フェデレーション証明書を手動で更新する | [Office 365 および Azure AD 用のフェデレーション証明書の更新](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Azure AD Connect Health の使用開始
Azure AD Connect Health の使用を開始するには、次の手順に従います。

1. [Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) を入手するか、[試用版の利用を開始](https://azure.microsoft.com/trial/get-started-active-directory/)します。
2. [Azure AD Connect Health エージェントをダウンロードし、ID サーバーにインストール](#download-and-install-azure-ad-connect-health-agent)します。
3. [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) で Azure AD Connect Health ダッシュボードを表示します。

> [!NOTE]
> Azure AD Connect Health ダッシュボードでデータを表示するためには、あらかじめ対象サーバーに Azure AD Connect Health エージェントをインストールしておく必要があります。
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Azure AD Connect Health エージェントのダウンロードとインストール
* Azure AD Connect Health の[要件を必ず満たし](how-to-connect-health-agent-install.md#requirements)てください。
* Azure AD Connect Health for AD FS の使用を開始します
    * [Azure AD Connect Health for AD FS エージェントをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [インストール手順を参照します](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* Azure AD Connect Health for sync の使用を開始します
    * [最新バージョンの Azure AD Connect をダウンロードしてインストールします](https://go.microsoft.com/fwlink/?linkid=615771)。 同期用の Health エージェントは、Azure AD Connect のインストールの一環としてインストールされます (バージョン 1.0.9125.0 以上)。
* Azure AD Connect Health for AD DS の使用を開始します
    * [Azure AD Connect Health for AD DS エージェントをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=820540)。
    * [インストール手順を参照します](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)。


## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health ポータル
Azure AD Connect Health ポータルでは、アラート、パフォーマンスの監視、使用状況の分析に関するビューが表示されます。 https://aka.ms/aadconnecthealth URL で Azure AD Connect Health のメイン ブレードに移動することができます。 ブレードは、ウィンドウと考えることができます。 メイン ブレードでは、 **[クイック スタート]** 、Azure AD Connect Health で提供されるサービス、その他の構成オプションが表示されます。 次のスクリーンショットとそれに続く簡単な説明をご覧ください。 エージェントのデプロイ後、Azure AD Connect Health で監視されているサービスが、Health サービスによって自動的に識別されます。

> [!NOTE]
> ライセンスの情報については、[Azure AD Connect Health の FAQ](reference-connect-health-faq.md) または [Azure AD の価格に関するページ](https://aka.ms/aadpricing)を参照してください。
    
![Azure AD Connect Health ポータル](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **[クイック スタート]** :このオプションを選択すると、 **[クイック スタート]** ブレードが開きます。 **[ツールの入手]** を選択することで、Azure AD Connect Health エージェントをダウンロードできます。 ドキュメントの利用とフィードバックの提供もできます。
* **[Azure Active Directory Connect (Sync)]** : このオプションを選択すると、Azure AD Connect Health が現在監視している Azure AD Connect サーバーが表示されます。 **[同期エラー]** エントリは、カテゴリごとに、最初のオンボード同期サービスの基本的な同期エラーを示します。 **[同期サービス]** エントリを選択すると、ブレードが開いて Azure AD Connect サーバーに関する情報が表示されます。 [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)に関するページで各種機能を参照してください。
* **[Active Directory フェデレーション サービス]** : このオプションを選択すると、Azure AD Connect Health が現在監視しているすべての AD FS サービスが表示されます。 インスタンスを選択すると、ブレードが開いてそのサービス インスタンスに関する情報が表示されます。 この情報には、概要、プロパティ、アラート、監視、使用状況の分析などが含まれます。 [AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)に関するページで各種機能を参照してください。
* **[Active Directory Domain Services]** : このオプションを選択すると、Azure AD Connect Health が現在監視しているすべての AD DS フォレストが表示されます。 フォレストを選択すると、ブレードが開いてそのフォレストに関する情報が表示されます。 この情報には、重要度のきわめて高い情報、ドメイン コントローラーのダッシュボード、レプリケーションの状態のダッシュボード、アラート、監視の概要が含まれます。 「[AD DS での Azure AD Connect Health の使用](how-to-connect-health-adds.md)」で各種機能を参照してください。
* **構成する**:このセクションには、次の機能をオンまたはオフに切り替えるオプションがあります。

  - **[設定]** エントリには、エージェントの基本的な構成が含まれています。 自動アップグレード設定は、Azure AD Connect Health エージェントの最新バージョンへの自動更新を可能にします。Azure AD Connect Health Agent の最新バージョンが利用可能になると、自動的に更新されます。 この機能は、既定では有効になっています。 Microsoft がトラブルシューティングの目的でのみ Azure AD ディレクトリの正常性データにアクセスすることを許可する: この機能が有効になっていると、ユーザーに表示されているのと同じデータを Microsoft でも表示できます。 この情報は問題のトラブルシューティングと支援に役立ちます。 この機能は、既定では無効になっています。
* **[ロール ベースのアクセス制御 (IAM)]** は、ロール ベースの Connect Health データへのアクセスを管理するためのセクションです。 

## <a name="next-steps"></a>次の手順

- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)
- [パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)|
- [パススルー認証](how-to-connect-pta.md)
- [Azure AD Connect とフェデレーション](how-to-connect-fed-whatis.md)
- [Azure AD Connect Health エージェントをインストールする](how-to-connect-health-agent-install.md) 
- [Azure AD Connect Sync](how-to-connect-sync-whatis.md)
