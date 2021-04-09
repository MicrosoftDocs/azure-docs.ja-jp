---
title: Azure の Active Directory ベースのサービスを比較する | Microsoft Docs
description: この概要では、Active Directory Domain Services、Azure Active Directory、Azure Active Directory Domain Services 向けのさまざまな ID サービスを比較します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: justinha
ms.openlocfilehash: 48ce982a6d6d7a3ed12d6e3b4711d111dec4d6f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174633"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>自己管理型の Active Directory Domain Services、Azure Active Directory、およびマネージド Azure Active Directory Domain Services の比較

アプリケーション、サービス、またはデバイスから、集中管理された ID にアクセスできるようにするには、Azure の Active Directory ベースのサービスを使用する一般的な方法が 3 つあります。 この中から ID ソリューションを選択すると、組織のニーズに最も適したディレクトリを柔軟に使用できます。 たとえば、モバイル デバイスを実行するクラウドのみを使用するユーザーを主に管理している場合、ご自分の Active Directory Domain Services (AD DS) の ID ソリューションを独自に構築して実行しても意味がない可能性があります。 代わりに、Azure Active Directory を使用することができます。

Active Directory ベースのこの 3 つの ID ソリューションは、共通の名前とテクノロジを共有しますが、顧客の異なる要求に対応するサービスが提供されるように設計されています。 これらの ID ソリューションと機能セットの概要は、次のとおりです。

* **Active Directory Domain Services (AD DS)** - ID と認証、コンピューター オブジェクトの管理、グループ ポリシー、信頼などの主要な機能が提供されるエンタープライズ対応のライトウェイト ディレクトリ アクセス プロトコル (LDAP) サーバー。
    * AD DS は、オンプレミスの IT 環境を使用する多くの組織で中心的なコンポーネントであり、主要なユーザー アカウント認証機能とコンピューター管理機能が提供されます。
    * 詳細については、[Windows Server ドキュメントの Active Directory Domain Services の概要][overview-adds]に関するページを参照してください。
* **Azure Active Directory (Azure AD)** - Microsoft 365、Azure portal、SaaS アプリケーションなどのリソースに対して、ユーザー アカウント サービスと認証サービスが提供されるクラウドベースの ID およびモバイル デバイス管理。
    * Azure AD をオンプレミスの AD DS 環境と同期させて、クラウドでネイティブに機能する単一の ID をユーザーに提供できます。
    * Azure AD の詳細については、「[Azure Active Directory とは][whatis-azuread]」を参照してください。
* **Azure Active Directory Domain Services (Azure AD DS)** - 完全に互換性のある従来の AD DS 機能のサブセット (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) を使用してマネージド ドメイン サービスが提供されます。
    * Azure AD DS は Azure AD と統合され、それ自体でオンプレミスの AD DS 環境と同期することができます。 この機能により、ID の一元管理のユース ケースを、リフト アンド シフト戦略の一部として Azure で実行される従来の Web アプリケーションに拡張することができます。
    * Azure AD およびオンプレミスとの同期の詳細については、[マネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ][synchronization]に関するページを参照してください。

この概要記事では、組織のニーズに応じて、これらの ID ソリューションを連携させる場合と個別に使用する場合を比較対比します。

> [!div class="nextstepaction"]
> [使用を開始するには、Azure portal を使用して Azure AD DS マネージド ドメインを作成します][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS と自己管理型 AD DS

Kerberos や NTLM などの従来の認証メカニズムにアクセスする必要があるアプリケーションおよびサービスを使用している場合、クラウドで Active Directory Domain Services を提供するには、次の 2 つの方法があります。

* "*マネージド ドメイン*"。Azure Active Directory Domain Services (Azure AD DS) を使用して作成します。 Microsoft が、必要なリソースの作成と管理を行います。
* "*自己管理型*" ドメイン: 仮想マシン (VM)、Windows Server ゲスト OS、Active Directory Domain Services (Azure AD DS) などの従来のリソースを使用して、作成および構成します。 管理者が引き続き、これらのリソースを管理します。

Azure AD DS では、管理者に代わって Microsoft がコア サービス コンポーネントを "*マネージド*" ドメイン エクスペリエンスとしてデプロイし、管理します。 管理者は、VM、Windows Server OS、ドメイン コントローラー (DC) などのコンポーネント用 AD DS インフラストラクチャのデプロイ、管理、修正プログラムの適用、セキュリティ保護は行いません。

Azure AD DS では、機能のより小さいサブセットが従来の自己管理型 AD DS 環境に提供され、設計と管理の複雑さの一部が軽減されます。 たとえば、設計および保守すべき AD フォレスト、ドメイン、サイト、レプリケート リンクはありません。 それでも [Azure AD DS とオンプレミス環境の間にフォレストの信頼を作成する][create-forest-trust]ことができます。

アプリケーションおよびサービスがクラウドで実行され、Kerberos や NTLM などの従来の認証メカニズムにアクセスする必要がある場合、Azure AD DS では、管理オーバーヘッドを最小限に抑えたマネージド ドメイン エクスペリエンスが提供されます。 詳細については、[Azure AD DS でのユーザー アカウント、パスワード、および管理の概念][administration-concepts]に関するページを参照してください。

自己管理型 AD DS 環境をデプロイして実行する場合、関連するすべてのインフラストラクチャとディレクトリ コンポーネントを保守する必要があります。 自己管理型 AD DS 環境では、追加の保守オーバーヘッドが発生しますが、スキーマの拡張やフォレストの信頼の作成などの追加タスクを実行できます。

クラウド内のアプリケーションおよびサービスに ID を提供する自己管理型 AD DS 環境の一般的なデプロイ モデルには、次のものがあります。

* **スタンドアロンのクラウド専用 AD DS:** Azure VM はドメイン コントローラーとして構成され、個別のクラウド専用 AD DS 環境が作成されます。 この AD DS 環境は、オンプレミスの AD DS 環境と統合されません。 クラウド内の VM にサインインして管理するために、別の資格情報セットを使用します。
* **リソース フォレストのデプロイ:** Azure VM はドメイン コントローラーとして構成され、既存のフォレストの一部として AD DS ドメインが作成されます。 オンプレミスの AD DS 環境に対して信頼関係が構成されます。 クラウドのこのリソース フォレストに、他の Azure VM をドメイン参加させることができます。 ユーザー認証は、オンプレミスの AD DS 環境への VPN/ExpressRoute 接続経由で実行されます。
* **オンプレミスのドメインを Azure に拡張:** VPN/ExpressRoute 接続を使用して、オンプレミスのネットワークに Azure 仮想ネットワークを接続します。 Azure VM をこの Azure 仮想ネットワークに接続して、オンプレミスの AD DS 環境にドメイン参加させます。
    * 別の方法として、Azure VM を作成し、オンプレミスの AD DS ドメインからレプリカ ドメイン コントローラーとして昇格させます。 これらのドメイン コントローラーは、オンプレミスの AD DS 環境への VPN/ExpressRoute 接続経由でレプリケートされます。 オンプレミスの AD DS ドメインは、Azure に効果的に拡張されます。

次の表では、組織に必要となる可能性のある機能の一部、マネージド Azure AD DS ドメインまたは自己管理型 AD DS ドメインの違いについて概要を示します。

| **機能** | **Azure AD DS** | **自己管理型 AD DS** |
| ----------- |:---------------:|:----------------------:|
| **マネージド サービス**                               | **&#x2713;** | **&#x2715;** |
| **安全なデプロイ**                            | **&#x2713;** | 管理者がデプロイをセキュリティ保護 |
| **DNS サーバー**                                    | **&#x2713;** (管理されたサービス) |**&#x2713;** |
| **ドメインまたはエンタープライズ管理者の特権** | **&#x2715;** | **&#x2713;** |
| **ドメイン参加**                                   | **&#x2713;** | **&#x2713;** |
| **NTLM と Kerberos を使用するドメイン認証** | **&#x2713;** | **&#x2713;** |
| **Kerberos の制約付き委任**               | リソースベース | リソースベースとアカウントベース|
| **カスタムの OU 構造**                           | **&#x2713;** | **&#x2713;** |
| **グループ ポリシー**                                  | **&#x2713;** | **&#x2713;** |
| **スキーマの拡張機能**                             | **&#x2715;** | **&#x2713;** |
| **AD ドメイン/フォレストの信頼**                     | **&#x2713;** (一方向の出力方向フォレスト信頼のみ) | **&#x2713;** |
| **Secure LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP の読み取り**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP の書き込み**                                    | **&#x2713;** (マネージド ドメイン内) | **&#x2713;** |
| **地理的に分散したデプロイ**                   | **&#x2713;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS と Azure AD

Azure AD を使用することにより、組織で使用されているデバイスの ID を管理して、これらのデバイスから企業リソースへのアクセスを制御できます。 ユーザーは、個人のデバイスを Azure AD に登録 (Bring Your Own (BYO) モデル) することも可能です。これにより、デバイスに ID が提供されます。 すると、ユーザーが Azure AD にサインインし、デバイスを使用してセキュリティで保護されたリソースにアクセスすると、Azure AD でデバイスは認証されます。 デバイスは、Microsoft Intune などのモバイル デバイス管理 (MDM) ソフトウェアを使用して管理できます。 この管理機能を使用すると、機密性の高いリソースへのアクセスを、管理対象デバイスとポリシーに準拠しているデバイスに制限できます。

従来のコンピューターおよびノート PC も Azure AD に参加できます。 このメカニズムには、個人用デバイスを Azure AD に登録する場合と同じメリットがあります。たとえば、ユーザーは会社の資格情報を使用してデバイスにサインインできます。

Azure AD に参加しているデバイスには、次のような利点があります。

* Azure AD によってセキュリティ保護されたアプリケーションに対するシングル サインオン (SSO)
* デバイス間でのユーザー設定の企業ポリシーに準拠しているローミング
* 会社の資格情報を使用したビジネス向け Windows ストアへのアクセス
* Windows Hello for Business
* 企業のポリシーに準拠しているデバイスからアプリおよびリソースへのアクセス制限

オンプレミスの AD DS 環境を含むハイブリッド デプロイの有無に関係なく、デバイスを Azure AD に参加させることができます。 次の表は、一般的なデバイスの所有権モデルと、それらをドメインに参加させる通常の方法について概要を示します。

| **デバイスの種類**                                        | **デバイス プラットフォーム**             | **メカニズム**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| 個人用デバイス                                          | Windows 10、iOS、Android、macOS | Azure AD 登録済み    |
| オンプレミスの AD DS に参加していない組織所有デバイス | Windows 10                       | Azure AD 参加済み        |
| オンプレミスの AD DS に参加している組織所有デバイス  | Windows 10                       | ハイブリッド Azure AD 参加済み |

Azure AD に参加または登録しているデバイスでは、最新の OAuth/OpenID 接続ベースのプロトコルを使用して、ユーザー認証が行われます。 これらのプロトコルは、インターネット経由で動作するように設計されているため、ユーザーが会社のリソースにどこからでもアクセスするモバイル シナリオに最適です。

Azure AD DS に参加しているデバイスでは、アプリケーションは認証に Kerberos プロトコルおよび NTLM プロトコルを使用できます。このため、リフト アンド シフト戦略の一環として Azure VM に移行され、そこで実行されるレガシ アプリケーションをサポートできます。 次の表は、デバイスの表現方法の違いと、ディレクトリに対してデバイス自体を認証できる方法の違いについて概要を示します。

| **特徴**                      | **Azure AD 参加済み**                                 | **Azure AD DS 参加済み**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| デバイスの制御            | Azure AD                                            | Azure AD DS マネージド ドメイン                                                |
| ディレクトリ内の表現 | Azure AD ディレクトリ内のデバイス オブジェクト            | Azure AD DS マネージド ドメイン内にあるコンピューター オブジェクト                        |
| 認証                  | OAuth/OpenID Connect ベースのプロトコル              | Kerberos および NTLM プロトコル                                               |
| 管理                      | Intune などのモバイル デバイス管理 (MDM) ソフトウェア | グループ ポリシー                                                              |
| ネットワーク                      | インターネット経由で動作                             | マネージド ドメインがデプロイされている仮想ネットワークに接続されているか、ピアリングされている必要があります |
| 最適な対象                    | エンドユーザーのモバイルまたはデスクトップ デバイス                  | Azure にデプロイされるサーバー VM                                              |


ADFS を使用したフェデレーション認証のために、オンプレミスの AD DS と Azure AD が構成されている場合、Azure DS で使用できる (現在の、または有効な) パスワード ハッシュはありません。 フェデレーション認証が実装される前に作成された Azure AD ユーザー アカウントには古いパスワード ハッシュがある可能性がありますが、これはオンプレミスのパスワードのハッシュと一致しないと考えられます。 そのため、Azure AD DS はユーザーの資格情報を検証できません。

## <a name="next-steps"></a>次のステップ

Azure AD DS の使用を開始するには、[Microsoft Azure portal を使用して Azure AD DS マネージド ドメインを作成][tutorial-create]します。

また、[Azure AD DS でのユーザー アカウント、パスワード、および管理の概念][administration-concepts]および[マネージド ドメインでのオブジェクトと資格情報の同期のしくみ][synchronization]についても学習できます。

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
