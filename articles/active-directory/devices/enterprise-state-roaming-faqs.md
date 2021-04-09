---
title: Enterprise State Roaming のよくあるご質問 - Azure Active Directory
description: ESR に関してよく寄せられる質問
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173126"
---
# <a name="settings-and-data-roaming-faq"></a>設定とデータのローミングに関する FAQ

この記事では、IT 管理者が設定やアプリ データの同期に関して抱く可能性のあるいくつかの疑問に答えます。

## <a name="what-data-roams"></a>ローミングの対象データは

**Windows 設定**: Windows オペレーティング システムに組み込まれている PC 設定。 主に、お使いの PC をカスタマイズする設定が該当し、大きく次のカテゴリに分けられます。

* *テーマ*: デスクトップ テーマやタスク バーの設定などの機能が含まれています。
* *Internet Explorer の設定*: 最近開いたタブ、お気に入りなどが含まれています。
* *Microsoft Edge ブラウザーの設定*: お気に入り、リーディング リストなどがあります。
* *パスワード*: インターネット パスワード、Wi-Fi プロファイルなどが含まれています。
* *言語設定*: キーボード レイアウト、システム言語、日付と時刻などの設定が含まれています。
* *簡単操作機能*: ハイ コントラスト テーマ、ナレーター、拡大鏡などがあります。
* *その他の Windows 設定*: マウス設定など。

> [!NOTE]
> この記事は、2015年7月に Windows 10 で起動された Microsoft Edge レガシ HTML ベースのブラウザーに適用されます。 この記事は、2020 年 1 月 15 日にリリースされた新しい Microsoft Edge Chromium ベースのブラウザーには適用されません。 新しい Microsoft Edge の同期動作の詳細については、「[Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)」を参照してください。

**アプリケーション データ**: ユニバーサル Windows アプリは、設定データを Roaming フォルダーに書き込むことができます。このフォルダーに書き込まれたデータはすべて自動的に同期されます。 この機能を利用するようにアプリを設計するかどうかは、アプリ開発者ごとの判断となります。 ローミングを使用するユニバーサル Windows アプリを開発する方法の詳細については、[アプリ データ ストレージ API](/windows/uwp/design/app-settings/store-and-retrieve-app-data) と[アプリ データのローミングに関する Windows 8 アプリ開発者ブログ](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/)を参照してください。

## <a name="what-account-is-used-for-settings-sync"></a>設定の同期に使用されるアカウントは

Windows 8.1 では常に、コンシューマーの Microsoft アカウントが設定の同期に使用されていました。 企業ユーザーは、その Active Directory ドメイン アカウントに Microsoft アカウントを接続することによって設定の同期を利用することができました。Windows 10 では、この Microsoft アカウントの接続機能が、プライマリ/セカンダリ アカウント フレームワークによって置き換えられています。

プライマリ アカウントは、Windows へのサインインに使用するアカウントとして定義されています。 Microsoft アカウントや Azure Active Directory (Azure AD) アカウント、オンプレミスの Active Directory アカウント、ローカル アカウントが該当します。 Windows 10 のユーザーは、プライマリ アカウントに加え、1 つまたは複数のセカンダリ クラウド アカウントを各自のデバイスに追加することができます。 一般に、セカンダリ アカウントとは、Microsoft アカウントや Azure AD アカウントのほか、Gmail や Facebook といったアカウントのことを指します。 セカンダリ アカウントで、シングル サインオンや Windows ストアなど追加のサービスにアクセスすることはできますが、設定の同期機能にアクセスすることはできません。

Windows 10 における設定の同期機能に使用できるのは、デバイスのプライマリ アカウントだけです (「[Windows 8 における Microsoft アカウントでの設定の同期を Windows 10 における Azure AD での設定の同期にアップグレードするとどうなりますか](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)」を参照)。

デバイス上の異なるユーザー アカウントのデータがミックスされることはありません。 設定の同期には次の 2 つの規則があります。

* Windows の設定は常にプライマリ アカウントでローミングされます。
* アプリのデータは、そのアプリを取得する際に使われたアカウントでタグ付けされます。 同期されるのは、プライマリ アカウントでタグ付けされたアプリだけです。アプリの所有権タグは、Windows ストアやモバイル デバイス管理 (MDM) 経由でアプリをサイド ローディングしたときに決定されます。

アプリの所有者を特定できない場合は、プライマリ アカウントでローミングされます。 デバイスが Windows 8 や Windows 8.1 から Windows 10 にアップグレードされると、すべてのアプリが Microsoft アカウントで取得されたようにタグ付けされます。 これは、ほとんどのユーザーがアプリを Windows ストアを通じて取得し、Windows 10 以前の Azure AD アカウントでは Windows ストアのサポートがなかったために起こります。 オフライン ライセンスでインストールされたアプリは、そのデバイス上のプライマリ アカウントでタグ付けされます。

> [!NOTE]
> 企業の所有の下で Azure AD に接続された Windows 10 デバイスの Microsoft アカウントは、今後ドメイン アカウントに接続できません。 Microsoft アカウントをドメイン アカウントに接続してそのユーザーの全データを Microsoft アカウントに同期させる機能 (Microsoft アカウントと Active Directory との接続による Microsoft アカウントでのローミング機能) は、Active Directory と Azure AD とが接続された環境に参加している Windows 10 デバイスからは削除されます。

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Windows 8 における Microsoft アカウントでの設定の同期を Windows 10 における Azure AD での設定の同期にアップグレードするとどうなりますか

Active Directory に接続されている Microsoft アカウントで Active Directory ドメインに参加している Windows 8.1 のユーザーが設定を同期する際には、その Microsoft アカウントが使用されます。 Windows 10 へのアップグレード後も、ドメインに参加しているユーザーで Active Directory ドメインと Azure AD が接続されていない場合は、引き続き Microsoft アカウントでユーザー設定を同期します。

一方、オンプレミスの Active Directory ドメインが Azure AD と接続されている場合、お使いのデバイスは、接続されている Azure AD アカウントを使用して設定を同期します。 Azure AD 管理者によって Enterprise State Roaming が有効にされていない場合、接続されている Azure AD アカウントは設定の同期を中止します。 Windows 10 ユーザーで Azure AD の ID でサインインしている場合、管理者が Azure AD を介した設定の同期を有効にするとすぐに Windows の設定の同期が開始されます。

個人データを会社のデバイスに保存している場合、Windows OS とアプリケーションのデータが Azure AD と同期されることになるため注意してください。 これには、次のような意味があります。

* 個人の Microsoft アカウントの設定が、職場または学校の Azure AD アカウントの設定から乖離します。 これは、Microsoft アカウントと Azure AD の設定の同期が別のアカウントを使用するようになるためです。
* 従来、Active Directory に接続された Microsoft アカウント経由で同期されていた個人データ (Wi-Fi のパスワード、Web の資格情報、Internet Explorer のお気に入りなど) は今後、Azure AD 経由で同期されます。

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Microsoft アカウントと Azure AD の Enterprise State Roaming の相互運用性について教えてください

November 2015 以降のリリースの Windows 10 では、Enterprise State Roaming が一度に 1 つのアカウントでのみサポートされます。 職場や学校の Azure AD アカウントで Windows にサインインした場合は、すべてのデータが Azure AD 経由で同期されます。 個人用 Microsoft アカウントで Windows にサインインした場合は、すべてのデータが Microsoft アカウント経由で同期されます。 ユニバーサル アプリのデータはデバイスのプライマリ サインイン アカウントのみを使用してローミングされ、アプリのライセンスがプライマリ アカウントによって所有されている場合にのみローミングされます。 セカンダリ アカウントによって所有されているアプリのユニバーサル アプリ データは同期されません。

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>複数テナントの Azure AD アカウントが存在する場合、設定は同期されますか

異なる Azure AD テナントの複数の Azure AD アカウントが同じデバイス上に存在する場合は、そのデバイスのレジストリを、Azure AD テナントごとに Azure Rights Management サービスと通信するように更新する必要があります。  

1. 各 Azure AD テナントの GUID を見つけます。 Azure Portal を開いて Azure AD テナントを選択します。 テナントの GUID は、選択されたテナントの [プロパティ] ページ (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) ) にあり、 **[ディレクトリ ID]** というラベルが付いています。 
2. GUID を特定したら、レジストリ キー **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>** を追加する必要があります。
   **テナント ID の GUID** キーから、**AllowedRMSServerUrls** という名前の新しい複数行文字列値 (REG-MULTI-SZ) を作成します。 そのデータに対して、デバイスがアクセスする他の Azure テナントのライセンス配布ポイントの URL を指定します。
3. ライセンス配布ポイントの URL は、AADRM モジュールから **Get-AadrmConfiguration** コマンドレットを実行することによって見つけることができます。 **LicensingIntranetDistributionPointUrl** の値と **LicensingExtranetDistributionPointUrl** の値とが異なる場合は、両方の値を指定します。 同じ値である場合は、その値を一度だけ指定してください。

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>既存の Windows デスクトップ アプリケーションに使用されるローミングの設定に関しては、どのような選択肢がありますか

ローミングが利用できるのは、ユニバーサル Windows アプリに限られます。 既存の Windows デスクトップ アプリケーションでローミングを利用する方法としては、次の 2 つの選択肢があります。

* [Desktop Bridge](/windows/msix/desktop/source-code-overview) を使用する。既存の Windows デスクトップ アプリをユニバーサル Windows プラットフォームに移行できます。 そこから、ごくわずかな変更をコードに加えることで、Azure AD のアプリ データ ローミングを利用できるようになります。 既存のデスクトップ アプリは、そのデータのローミングに必要なアプリ ID を Desktop Bridge から得ることができます。
* [User Experience Virtualization (UE-V)](/previous-versions//dn458947(v=vs.85)) を使用する。Win32 アプリについては、UE-V を使用して既存の Windows デスクトップ アプリ用のカスタム設定テンプレートを作成し、ローミングを有効にできます。 この方法を選んだ場合、アプリの開発者がそのコードを変更する必要はありません。 UE-V は、オンプレミスの Active Directory ローミングに限られ、Microsoft Desktop Optimization Pack を購入済みであることが必要となります。

管理者は、次の [UE-V グループ ポリシー](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)を使用して Windows OS の設定やユニバーサル アプリ データのローミングを変更することで、Windows デスクトップ アプリのデータをローミングするように UE-V を構成できます。

* Roam Windows settings (Windows 設定のローミング) グループ ポリシー
* Do not synchronize Windows Apps (Windows アプリを同期しない) グループ ポリシー
* アプリケーション セクションでの Internet Explorer ローミング

将来的にはマイクロソフトが、さらに深いレベルで UE-V を Windows に統合し、Azure AD クラウドを使って設定をローミングできるように UE-V を拡張する方法を模索する可能性はあります。

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>同期された設定とデータをオンプレミスに保存することはできますか？

Enterprise State Roaming は同期されたすべてのデータを Microsoft クラウドに格納します。 オンプレミスのローミング ソリューションとしては UE-V をご利用ください。

## <a name="who-owns-the-data-thats-being-roamed"></a>ローミングされるデータはだれが所有するのですか

Enterprise State Roaming でローミングされたデータは各企業の所有となります。 データは Azure データセンターに保存されます。 すべてのユーザー データが、Azure Information Protection の Azure Rights Management サービスを使用して、転送中とクラウド内の保存時のどちらの状態でも暗号化されます。 これが Microsoft アカウント ベースの設定の同期機能から改良された点であり、Microsoft アカウント ベースでは特定の機密データ (ユーザーの資格情報など) だけが、デバイスから送信される前に暗号化されていました。

マイクロソフトは、顧客データの保護に積極的に取り組んでいます。 企業ユーザーの設定データは、Windows 10 デバイスから送信される前に Azure Rights Management サービスによって自動的に暗号化されるため、他のユーザーがこのデータを読み取ることはできません。 組織に Azure Rights Management サービスの有料サブスクリプションがある場合は、他の保護機能 (ドキュメントの追跡や失効など) を使用したり、機密情報を含む電子メールを自動的に保護したり、独自のキー ("Bring Your Own Key" ソリューション、BYOK とも呼ばれます) を管理したりできます。 これらの機能およびこの保護サービスの動作の詳細については、「[Azure Active Directory Rights Management の概要](/azure/information-protection/what-is-information-protection)」を参照してください。

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>特定のアプリや設定の同期を管理することはできますか

Windows 10 の MDM やグループ ポリシーには、個々のアプリケーションを対象にローミングを無効にするための設定は存在しません。 テナントの管理者がそのマネージド デバイス上の全アプリを対象にアプリ データの同期を無効にすることはできますが、アプリ単位やアプリ内の階層単位で細かく制御することはできません。

## <a name="how-can-i-enable-or-disable-roaming"></a>ローミングはどのようにして有効または無効にできますか

**設定** アプリから **[アカウント]**  >  **[設定の同期]** の順に移動します。 このページから、設定のローミングに使用されているアカウントを確認したり、ローミング対象となる設定グループの有効と無効を個別に切り替えたりすることができます。

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Windows 10 におけるローミングの実現手段としてマイクロソフトが推奨する方法は

設定のローミングに関して、マイクロソフトでは、移動ユーザー プロファイル、UE-V、Enterprise State Roaming などいくつかのソリューションを提供しています。  マイクロソフトが将来の Windows バージョン向けとして積極的に開発に取り組んでいるのは Enterprise State Roaming です。 データをクラウドに移すことに関して勤務先が積極的でない、または準備が整っていない場合は、主要なローミング テクノロジとして UE-V を使用することをお勧めします。 既存の Windows デスクトップ アプリケーションのローミングに対応する必要がある一方で、クラウドへの移行を勤務先が積極的に推進している場合は、Enterprise State Roaming と UE-V を併用することをお勧めします。 UE-V と Enterprise State Roaming は非常に類似したテクノロジですが、相互に排他的ではありません。 組織がユーザーが必要とするローミング サービスを確実に提供できるように、互いに補完します。  

Enterprise State Roaming と UE-V を併用するときは、次の規則が当てはまります。

* Enterprise State Roaming がデバイス上の主要なローミング エージェントになります。 UE-V は、"Win32 とのギャップ" を補うために使用されます。
* Windows の設定や UWP のモダン アプリのデータに対する UE-V ローミングは、UE-V のグループ ポリシーを使用する際には無効にする必要があります。 これらは既に Enterprise State Roaming で補われています。

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>仮想デスクトップ インフラストラクチャ (VDI) に対する Enterprise State Roaming の対応について教えてください

Enterprise State Roaming は Windows 10 クライアント SKU でサポートされていますが、サーバー SKU ではサポートされません。 ハイパーバイザー コンピューターでクライアント VM がホストされており、その仮想マシンにリモートでサインインしている場合、データはローミングされます。 複数のユーザーが同じ OS を共有しており、ユーザーがリモートからサーバーにログインしてデスクトップとまったく同じようにその環境を利用している場合、ローミングは正常に機能しない場合があります。 後者のセッション ベースのシナリオは公式対応していません。

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>組織が、ローミングを使用した後に Azure Rights Management を含むサブスクリプションを購入するとどうなりますか

組織が既に Windows 10 および Azure Rights Management 使用制限付き無料サブスクリプションでローミングを使用している場合は、Azure Rights Management 保護サービスを含む[有料サブスクリプション](https://azure.microsoft.com/pricing/details/information-protection/)を購入してもローミングの機能には影響せず、IT 管理者による構成変更は必要ありません。

## <a name="known-issues"></a>既知の問題

既知の問題の一覧については、「[troubleshooting](enterprise-state-roaming-troubleshooting.md)」(トラブルシューティング) セクションのドキュメントを参照してください。 

## <a name="next-steps"></a>次のステップ 

概要については、「[Enterprise State Roaming の概要](enterprise-state-roaming-overview.md)」を参照してください