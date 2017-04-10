---
title: "職場での Windows 10 デバイスの使用 | Microsoft Docs"
description: "企業が Windows 10 でデバイスをプロビジョニングして使用する際のさまざまな方法を比較して、ユーザーおよび IT 管理者が利用できる機能を簡潔に評価します。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 94ccc8fd-b17b-4fda-8d56-9d87aa37a9f9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 71e8944fc56420ddd7fd42bbfbfdf6d145a1cf3b
ms.lasthandoff: 03/21/2017


---
# <a name="using-windows-10-devices-in-your-workplace"></a>職場での Windows 10 デバイスの使用
適用対象: Windows 10 PC

Windows 10 には、ユーザーが安全かつ便利な方法で職場のリソースにアクセスできるようにする、組織向けの 3 つのモデルが用意されています。

* **Azure Active Directory Join** (Azure AD Join)。Office 365 などの主にクラウドのリソースにアクセスする作業者を対象としています。 Azure AD Join は、Windows 10 からの新機能である、セルフサービスの作業プロビジョニング エクスペリエンスです。
* **ドメイン参加**。オンプレミスのアプリとリソースに投資している組織を対象としています。 ドメイン参加により、Windows 10 では Azure AD への接続時のエクスペリエンスが向上します。
* **新しい単純化された BYOD エクスペリエンス**。職場または学校アカウントを Windows に追加して個人のデバイス上でリソースに簡単にアクセスしたいユーザーを対象としています。

次の表では、企業が Windows 10 でデバイスをプロビジョニングして使用する際のさまざまな方法を比較して、ユーザーおよび IT 管理者向けの機能を簡潔に評価します。

|  | Domain Join | Azure AD Join | 個人デバイス |
| --- | --- | --- | --- |
| 職場または学校アカウント用の Windows デバイスでのサインイン。 |はい |あり |いいえ |
| ユーザーによる Office 365 と Azure AD アプリへのシングル サインオン (SSO)。 SSO は、1 回サインインするだけで組織のリソースにアクセスできる機能です。 |はい |あり |はい |
| ユーザーによる Kerberos または NTLM アプリへの SSO。 |はい |制限あり |はい (VPN 経由の場合) |
| Microsoft Passport と Windows Hello による、職場または学校アカウント用の強力な承認と便利なサインイン。 |はい |あり |はい |
| 職場または学校アカウントを使用した、企業向けの Windows ストアへのアクセス (Microsoft アカウントは不要)。 |はい |あり |はい |
| 職場または学校アカウントを使用した、企業に準拠した、デバイス間でのユーザー設定のローミング。 |はい |あり |はい |
| 組織のアプリへのアクセスを、組織のデバイス ポリシーに準拠したデバイスに限定する機能。 |はい |あり |はい |
| "任意の場所から作業する" ためのユーザーによるデバイスのセルフサービス プロビジョニング。 |いいえ |はい |はい |
| デバイスを管理する機能。 |はい (GP/SCCM 経由の場合) |はい |はい |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Windows 10 の Azure AD Join やドメイン参加で職場所有のデバイスを使用する
Windows 10 には、職場所有のデバイスから職場のリソースにアクセスする方法として次の 2 つが用意されています。

* Azure AD Join
* Domain Join
  
  いずれも組織のニーズと要件に応じた有効なオプションです。 組織は、場合によっては両方のデプロイメント方法を使用した方がメリットがあることがあります。

## <a name="when-to-use-azure-active-directory-join"></a>どのようなときに Azure Active Directory Join を使用するか
Azure AD Join は、Windows 10 の新しいセルフサービスの作業プロビジョニング エクスペリエンスです。  主にクラウドの Office 365 などの職場のリソースにアクセスする作業員を対象としています。 これは、企業のコンピューター、タブレット、携帯電話を構成するための簡単な方法です。 デバイスは、Windows プラットフォーム間で一貫性のあるコントロールを使用することで、モバイル デバイス管理を介して管理されます。

**Azure AD Join は次のいずれかの理由で使用されます。**

* デバイスを携帯する作業員用に、セルフサービスによるデバイスのプロビジョニングを有効にしたい場合。
* タブレットや携帯電話など職場所有のモバイル デバイスをユーザーに提供したい場合。
* 季節労働者、請負業者、学生などの一連のユーザーを Active Directory ではなく Azure AD で管理したい場合。
* リモートの支店の作業員に制限のあるオンプレミスのインフラに対する参加機能を提供したい場合。
* オンプレミスの Active Directory を所有していない場合。

一部の組織は、特にクラウドにリソースの多くまたはすべてを移行する際に、職場所有のデバイスをデプロイする主な手段として Azure AD Join を使用します。 Active Directory と Azure AD の両方を使用するハイブリッド組織では、ユーザーや部門に応じていずれかの手段のデプロイを選択することもできます。

学区および大学では、Active Directory でスタッフを管理し、Azure AD で生徒を管理することができます。 企業によっては、リモートのオフィスや営業部門で Azure AD を使用することができます。 ハイブリッド組織内では、Azure AD Join とドメイン参加の両方の手段を使用できます。 Azure AD Join は、作業環境にデバイスをデプロイする際のドメイン参加に非常に役立つことがあります。

**企業リソースへの最も一般的なアクセスがクラウド経由である場合、組織には次のようなメリットがあります。**

* オンプレミスの ID インフラストラクチャの依存関係を削除できます。
* セルフサービス構成を可能にしてイメージング ソリューションから離れることにより、デバイスのデプロイ モデルを簡略化できます。
* モバイル デバイス管理を使用して、さまざまなプラットフォーム上のすべてのデバイスを管理できます。

Azure AD Join の詳細については、「 [Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」を参照してください。

## <a name="when-to-use-domain-join-or-keep-using-it"></a>どのようなときにドメイン参加を使用するか (または使用し続けるか)
過去 15 年間、多くの組織はドメイン参加を使用して職場のデバイスを接続してきました。 この方法を使用した場合、ユーザーは Active Directory の職場または学校アカウントを使用して自分のデバイスにサインインできます。 また、IT 部門はこのようなデバイスを一元的かつ全面的に管理できます。 組織は、通常、イメージ作成方法を利用してデバイスをプロビジョニングし、そのデバイスの管理には System Center Configuration Manager (SCCM) またはグループ ポリシーを使用することがよくあります。

**次のいずれかの理由により、企業ではドメイン参加を使用する (または使用し続ける) 必要があります。**

* NTLM または Kerberos を使用するデバイスに、Win32 アプリがデプロイされている場合。
* デバイスの管理に、GP または SCCM/DCM が必要な場合。
* 今後も社員のデバイスの構成にイメージング ソリューションを使用したい場合。

**Windows 10 のドメイン参加では、Azure AD に接続すると、次のようなメリットもあります。**

* Microsoft Passport と Windows Hello により、職場または学校アカウントのデバイスバインドの認証が強化され、サインインが便利になります。
* 職場または学校アカウントを使用するデバイスで、企業向けの Windows ストアにアクセスできます (Microsoft アカウントは不要)。
* 職場または学校アカウントを使用するデバイス間で、企業に準拠した、ユーザー設定のローミングが可能です (Microsoft アカウントは不要)。
* 組織のアプリへのアクセスを、組織のデバイス ポリシーに準拠したデバイスに限定する機能。

Azure AD Join の詳細については、「 [Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)」を参照してください。

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>職場または学校で個人所有のデバイスの参加を有効にする
Windows 10 では、企業の BYOD をサポートするために、ユーザーが職場または学校アカウントをコンピューター、タブレット、携帯電話に追加できるようになりました。 ユーザーが職場または学校アカウントを追加すると、デバイスは Azure AD に登録され、必要に応じて、組織が構成したモバイル デバイス管理システムに登録されます。 これらのデバイスは、ディレクトリに '登録済み' または'Azure AD Joined' と表示されます。 IT 管理者は、この情報を使用して、さまざまなポリシーを適用できます。これにより、必要に応じて職場所有のデバイスよりも個人所有のデバイスを使いやすくなります。

ユーザーは、個人所有のデバイスに職場または学校アカウントを非常に簡単に追加できます。 この操作は、初めて職場のアプリケーションにアクセスするときに実行することも、[設定] メニューを使用して手動で実行することもできます。 このアカウントから、組織のリソースへの SSO を実行できます。

Azure AD Join の詳細については、「 [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)」を参照してください。

## <a name="enable-domain-join-or-azure-ad-join"></a>ドメイン参加または Azure AD Join を有効にする
前述のすべての方法 (ドメイン参加、Azure AD Join、職場または学校アカウントを追加) には、Windows 10 のユーザー エクスペリエンスへのエントリ ポイントがあります。 ただし、すべては IT 管理者がインフラストラクチャで機能を有効にしないと、このエクスペリエンスは機能しません。

## <a name="requirements-for-deploying-azure-ad-join"></a>Azure AD Join をデプロイするための要件
一連の任意のユーザーに Azure AD Join をデプロイするには、以下が必要です。

* Azure AD サブスクリプション。
* さらに機能が必要な場合は An Azure AD Premium サブスクリプション (モバイル デバイス管理の自動登録など)。
* モバイル デバイス管理。 Microsoft Intune サブスクリプション、Office 365 のモバイル デバイス管理、Azure AD と統合されるパートナーのモバイル デバイス管理ベンダーなど (詳細については、この記事の最後の方にある「 [よく寄せられる質問](#frequently-asked-questions) 」を参照してください)。

ハイブリッドな環境では、Azure AD Connect をデプロイしてオンプレミス ディレクトリを Azure AD に拡張することを強くお勧めします。

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Azure AD でドメイン参加を使用するための要件
ドメイン参加は引き続きこれまでどおりに動作しますが、 Azure AD のメリットを享受するには、以下が必要になります。

* Azure AD サブスクリプション。
* オンプレミス ディレクトリを Azure AD に拡張するための Azure AD Connect のデプロイ。
* ドメイン参加済みデバイスから Azure AD への条件付きアクセスを許可するポリシー。
* "ドメイン参加済み" デバイスへのアクセスを許可するポリシー (一部のデバイスのアクセスを制限できるようにする場合)。
* 準拠デバイスを必要とする規則を有効にするための System Center Configuration Manager Version 1509 for Technical Preview  (TechNet のドキュメントとブログの投稿を参照してください)。

Windows 10 におけるドメイン参加の詳細については、「[Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)」を参照してください。

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>BYOD と "職場または学校アカウントを追加" を使用するための要件
職場または学校アカウントで "Bring Your Own Device" (BYOD) を有効にするには、以下が必要です。

* Azure AD サブスクリプション。
* さらに機能が必要な場合は An Azure AD Premium サブスクリプション (モバイル デバイス管理の自動登録など)。

## <a name="requirements-for-using-microsoft-passport"></a>Microsoft Passport を使用するための要件
Microsoft Passport を有効にするには、以下が必要です。

* Microsoft Passport を使用した証明書ベースの認証をサポートするための公開キー基盤 (PKI)。
* Azure AD Join と職場または学校アカウントに Microsoft Passport を使用する証明書ベース認証をサポートするための Intune サブスクリプション。
* ドメイン参加に Microsoft Passport を使用する証明書ベースの認証をサポートするための System Center Configuration Manager Version 1509 for Technical Preview (TechNet のドキュメントとブログの投稿を参照してください)。
* 組織で Microsoft Passport を有効にするためのポリシー。

PKI を使用する代わりに、次の手順を実行して、キーに基づく Microsoft Passport を有効にすることができます。

* Windows Server 2016 "Production Preview 1" DC のデプロイ (機能レベルがドメインまたはフォレストの場合は不要です。各 Active Directory サイトにサービスを提供する冗長性のためにいくつかの DC があれば十分です)。
* 組織で Microsoft Passport を有効にするためのポリシーの設定。

Windows 10 の Microsoft Passport および Windows Hello の詳細については、<link-to-MS-Passport-and-Windows-Hello-document> を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Azure AD と統合されるのはどのパートナーのモバイル デバイス管理製品ですか。
Azure AD には Windows 10 での統合登録および条件付きアクセスが可能な次のベンダー製品を組み込むことができます。

* AirWatch by VMware
* Citrix Xenmobile
* Lightspeed Mobile Manager
* SOTI オンプレミス モバイル デバイス管理
* MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Windows 10 の Workplace Join について説明してください。
Windows 8.1 では、BYOD を有効にするために社内参加が使用されていました。 Windows 10 では、このドキュメントで既に説明したように、"職場または学校アカウントを追加" を使用して BYOD を有効にします。 Azure AD とモバイル デバイス管理を統合しない組織では、ユーザーは、**[設定]**、 > **[アカウント]** > 、**[職場のアクセス]** の順に移動して、デバイスを手動で管理に登録できます。

### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>ユーザーは Windows 10 のドメイン アカウントに Microsoft アカウントを関連付けることができますか。
Windows 10 ではできません。 Windows 8.1 では、ドメイン参加済みデバイスのユーザーは、Microsoft アカウント (Hotmail、Live、Outlook、Xbox など) をドメイン アカウントに "関連付け"、Live サービスへの SSO、Windows ストアの使用、デバイス間でのユーザー設定のローミングなどの特定のエクスペリエンスを実現することができました。 Windows 10 では、Microsoft アカウントの "関連付け" 機能の提供が終了しています。 ユーザーは、Windows ストアなどのコンシューマー サービスへの SSO を有効にするために、追加のアカウントとして Microsoft アカウントを 1 つ以上追加できます。 これは、**[設定]**、 > **[アカウント]** > 、**[アカウント]** で実行します。

Windows 8.1 のドメイン参加済みデバイスからアップグレードするユーザーと、Microsoft アカウントが関連付けられているユーザーは、関連付けられている Microsoft アカウントが、使用する追加アカウントの一覧に自動的に追加されます。

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)


