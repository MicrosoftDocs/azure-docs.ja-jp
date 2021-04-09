---
title: Azure AD Domain Services 管理の概念 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインとユーザー アカウントとパスワードの動作を管理する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: justinha
ms.openlocfilehash: 5473ef46751d64fdbbf1d52f39c66f49d707e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631389"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services でのユーザー アカウント、パスワード、および管理の管理の概念

Azure Active Directory Domain Services (AD DS) のマネージド ドメインを作成して実行すると、従来のオンプレミス AD DS 環境とは動作に違いがあります。 Azure AD DS でも自己管理型のドメインと同じ管理ツールを使用しますが、ドメイン コントローラー (DC) に直接アクセスすることはできません。 また、ユーザー アカウントの作成元よって、パスワード ポリシーとパスワード ハッシュの動作に違いがあります。

この概念に関する記事では、マネージド ドメインの管理方法と、ユーザー アカウントの作成方法に応じたさまざまな動作について説明します。

## <a name="domain-management"></a>ドメインの管理

マネージド ドメインは、DNS 名前空間および対応するディレクトリです。 マネージド ドメインでは、ユーザーとグループ、資格情報、ポリシーなどのすべてのリソースを含むドメイン コントローラー (DC) は、マネージド サービスの一部です。 冗長性を確保するために、マネージド ドメインの一部として 2 つの DC が作成されます。 これらの DC にサインインして管理タスクを実行することはできません。 代わりに、マネージド ドメインに参加している管理 VM を作成してから、通常の AD DS 管理ツールをインストールします。 たとえば、DNS やグループ ポリシー オブジェクトなどの Active Directory 管理センターまたは Microsoft 管理コンソール (MMC) スナップインを使用できます。

## <a name="user-account-creation"></a>ユーザー アカウントの作成

ユーザー アカウントは、複数の方法でマネージド ドメインに作成できます。 ほとんどのユーザー アカウントは Azure AD から同期されます。これには、オンプレミスの AD DS 環境から同期されたユーザー アカウントも含まれます。 マネージド ドメインにアカウントを手動で直接作成することもできます。 初期パスワードの同期やパスワード ポリシーなどの一部の機能は、ユーザー アカウントの作成方法と作成場所に応じて異なる動作をします。

* ユーザー アカウントは Azure AD から同期できます。 これには、Azure AD で直接作成されたクラウド専用のユーザー アカウントと、Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたハイブリッド ユーザー アカウントが含まれます。
    * マネージド ドメインのほぼすべてのユーザー アカウントは、Azure AD からの同期プロセスを通じて作成されています。
* ユーザー アカウントはマネージド ドメインに手動で作成することができ、Azure AD には存在しません。
    * マネージド ドメインでのみ実行されるアプリケーションのサービス アカウントを作成する必要がある場合は、マネージド ドメインに手動で作成できます。 同期は Azure AD から一方向で行われるため、マネージド ドメインに作成されたユーザー アカウントは Azure AD には同期されません。

## <a name="password-policy"></a>パスワード ポリシー

Azure AD DS には、アカウントのロックアウト、パスワードの最大有効期間、パスワードの複雑さなどの設定を定義する既定のパスワード ポリシーが含まれています。 アカウントのロックアウト ポリシーなどの設定は、前のセクションで説明したように、ユーザーの作成方法に関係なく、マネージド ドメイン内のすべてのユーザーに適用されます。 パスワードの最小長やパスワードの複雑さなどの一部の設定は、マネージド ドメインに直接作成されたユーザーにのみ適用されます。

独自のカスタム パスワード ポリシーを作成して、マネージド ドメインの既定のポリシーを上書きすることができます。 これらのカスタム ポリシーは、必要に応じて特定のユーザー グループに適用できます。

ユーザーの作成元で異なるパスワード ポリシーの適用方法の詳細については、「[マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー][password-policy]」を参照してください。

## <a name="password-hashes"></a>パスワード ハッシュ

Azure AD DS でマネージド ドメインのユーザーを認証するためには、NT LAN Manager (NTLM) 認証および Kerberos 認証に適した形式のパスワード ハッシュが必要となります。 NTLM 認証と Kerberos 認証に必要な形式のパスワード ハッシュは、ご利用のテナントに対して Azure AD DS を有効にするまで、Azure AD で生成または保存されることはありません。 また、セキュリティ上の理由から、クリアテキスト形式のパスワード資格情報が Azure AD に保存されることもありません。 そのため、こうした NTLM または Kerberos のパスワード ハッシュをユーザーの既存の資格情報に基づいて Azure AD が自動的に生成することはできません。

クラウド専用ユーザー アカウントの場合、ユーザーはマネージド ドメインを使用する前に各自のパスワードを変更する必要があります。 このパスワード変更プロセスによって、Kerberos 認証と NTLM 認証に使用されるパスワード ハッシュが Azure AD に生成されて保存されます。 パスワードが変更されるまで、アカウントは Azure AD から Azure AD DS に同期されません。

Azure AD Connect を使用してオンプレミスの AD DS 環境から同期されたユーザーについては、[パスワード ハッシュの同期を有効にします][hybrid-phs]。

> [!IMPORTANT]
> Azure AD Connect によってレガシ パスワード ハッシュが同期されるのは、Azure AD DS を Azure AD テナントに対して有効にしたときだけです。 Azure AD Connect を使用してオンプレミス AD DS 環境と Azure AD の同期しか行わない場合、レガシ パスワード ハッシュは使用されません。
>
> レガシ アプリケーションが NTLM 認証または LDAP simple bind を使用していない場合は、Azure AD DS の NTLM パスワード ハッシュ同期を無効にすることをお勧めします。 詳しくは、「[弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にする][secure-domain]」をご覧ください。

適切に構成されれば、使用可能なパスワード ハッシュがマネージド ドメインに保存されます。 マネージド ドメインを削除した場合、その時点で保存されていたパスワード ハッシュがあればすべて削除されます。 別のマネージド ドメインを後から作成した場合、Azure AD にある同期済みの資格情報は再利用できません。パスワード ハッシュを再度保存するには、パスワード ハッシュ同期を再構成する必要があります。 既にドメイン参加済みの VM またはユーザーがすぐに認証を行うことはできません。Azure AD が、新しいマネージド ドメインにパスワード ハッシュを生成して保存する必要があります。 詳細については、[Azure AD DS と Azure AD Connect のパスワード ハッシュ同期プロセス][azure-ad-password-sync]に関するセクションを参照してください。

> [!IMPORTANT]
> Azure AD Connect は、オンプレミスの AD DS 環境との同期のためにのみインストールおよび構成する必要があります。 マネージド ドメインに Azure AD Connect をインストールして元の Azure AD にオブジェクトを同期することはサポートされていません。

## <a name="forests-and-trusts"></a>フォレストと信頼

"*フォレスト*" は、Active Directory Domain Services (AD DS) で 1 つまたは複数の "*ドメイン*" をグループ化するために使われる論理構造です。 ドメインには、ユーザーまたはグループのオブジェクトが格納され、認証サービスが提供されます。

Azure AD DS では、フォレストにはドメインが 1 つだけ含まれます。 多くの場合、オンプレミスの AD DS フォレストには多数のドメインが含まれます。 大規模な組織では、特に合併や買収の後に、複数のオンプレミス フォレストが存在し、各フォレストにそれぞれ複数のドメインが含まれている場合があります。

既定では、マネージド ドメインは "*ユーザー*" フォレストとして作成されます。 このタイプのフォレストでは、オンプレミスの AD DS 環境で作成されたユーザー アカウントも含め、Azure AD 内のすべてのオブジェクトが同期されます。 ドメインに参加している VM へのサインインなどのために、マネージド ドメインに対してユーザー アカウントを直接認証できます。 ユーザー フォレストが機能するのは、パスワード ハッシュの同期が可能で、ユーザーがスマート カード認証などの専用サインイン方法を使用していない場合です。

Azure AD DS の "*リソース*" フォレストでは、ユーザーは自身のオンプレミス AD DS からの、一方向のフォレストの "*信頼*" を介して認証されます。 この方法では、ユーザー オブジェクトとパスワード ハッシュが Azure AD DS に同期されません。 ユーザー オブジェクトと資格情報は、オンプレミスの AD DS にのみ存在します。 このアプローチを使用すると、企業は、LDAPS、Kerberos、NTLM などの従来の認証に依存するリソースとアプリケーション プラットフォームを Azure でホストでき、一方で認証に関する問題や懸念事項が取り除かれます。

Azure AD DS 内のフォレストの種類の詳細については、[リソース フォレストとは何か][concepts-forest]および[フォレストの信頼が Azure AD DS で機能する方法][concepts-trust]に関するページを参照してください。

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU

Azure AD DS では、使用可能なパフォーマンスと機能は SKU に基づいています。 マネージド ドメインを作成するときに SKU を選択する必要があり、マネージド ドメインのデプロイ後にビジネス要件が変更されたときに SKU を切り替えることができます。 次の表に、使用可能な SKU とそれらの違いを示します。

| SKU 名   | オブジェクトの最大数 | バックアップ頻度 | 送信フォレストの信頼の最大数 |
|------------|----------------------|------------------|----|
| Standard   | 無制限            | 5 日ごと     | 0  |
| Enterprise | 無制限            | 3 日ごと     | 5  |
| Premium    | 無制限            | 毎日            | 10 |

これらの Azure AD DS SKU の前は、マネージド ドメイン内のオブジェクト (ユーザー アカウントとコンピューター アカウント) の数に基づく課金モデルが使用されていました。 ドメイン ドメイン内のオブジェクトの数に基づいて変化する価格設定はもう存在しません。

詳細については、「[Azure AD DS の価格][pricing]」ページをご覧ください。

### <a name="managed-domain-performance"></a>マネージド ドメインのパフォーマンス

ドメインのパフォーマンスは、アプリケーションに対する認証の実装方法によって異なります。 追加のコンピューティング リソースにより、クエリの応答時間が短縮され、同期操作にかかる時間が短縮される場合があります。 SKU レベルが増加すると、マネージド ドメインで使用できるコンピューティング リソースが増加します。 アプリケーションのパフォーマンスを監視し、必要なリソースを計画してください。

ビジネスまたはアプリケーションの需要が変化し、マネージド ドメイン用の追加のコンピューティング パワーが必要な場合は、別の SKU に切り替えることができます。

### <a name="backup-frequency"></a>バックアップ頻度

バックアップの頻度によって、マネージド ドメインのスナップショットが取得される頻度が決まります。 バックアップは、Azure プラットフォームによって管理される自動化されたプロセスです。 マネージド ドメインに問題が発生した場合は、Azure サポートによるバックアップからの復元の支援を受けることができます。 同期は Azure AD "*から*" 一方向で行われるため、マネージド ドメインで問題が発生しても Azure AD およびオンプレミスの AD DS 環境や機能には影響しません。

SKU レベルが増加するにつれて、それらのバックアップ スナップショットの頻度が増加します。 ビジネス要件と目標復旧時点 (RPO) を確認して、マネージド ドメインで必要なバックアップの頻度を決定します。 ビジネス要件やアプリケーション要件が変更され、頻繁にバックアップが必要になった場合は、別の SKU に切り替えることができます。

### <a name="outbound-forest-trusts"></a>送信フォレストの信頼

前のセクションでは、マネージド ドメインからオンプレミスの AD DS 環境 への一方向の送信フォレストの信頼について詳しく説明しました。 マネージド ドメインのために作成できるフォレストの信頼の最大数は、SKU によって決まります。 ビジネスとアプリケーションの要件を確認して、実際に必要な信頼の数を決定し、適切な Azure AD DS SKU を選択します。 ここでも、ビジネス要件が変更され、追加のフォレストの信頼を作成する必要がある場合は、別の SKU に切り替えることができます。

## <a name="next-steps"></a>次のステップ

まず初めに、[Azure AD DS マネージド ドメインを作成しましょう][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
