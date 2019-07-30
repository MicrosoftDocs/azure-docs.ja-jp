---
title: Azure マネージド ワークステーションをデプロイする- Azure Active Directory
description: セキュリティで保護された Azure マネージド ワークステーションをデプロイして、間違った構成やセキュリティ侵害による違反のリスクを軽減する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550481"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>セキュリティで保護された Azure マネージド ワークステーションのデプロイ

[セキュリティで保護されたワークステーションについて理解](concept-azure-managed-workstation.md)したら、デプロイのプロセスを開始します。 このガイダンスでは、定義済みのプロファイルを使用して、より安全なワークステーションを最初から作成します。

![セキュリティで保護されたワークステーションのデプロイ](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

ソリューションをデプロイするには、プロファイルを選択しておく必要があります。 デプロイで複数のプロファイルを同時に使用し、タグまたはグループを使用してそれらを割り当てることができます。
> [!NOTE]
> 要件に応じて、プロファイルのいずれかを適用します。 Intune で別のプロファイルを割り当てることで、そのプロファイルに移行できます。

| プロファイル | 低 | 拡張 | 高 | 専用イメージ | セキュリティで保護 | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD のユーザー | はい | はい | はい | はい | はい | はい |
| Intune マネージド | はい | はい | はい | はい | はい | はい |
| デバイス - Azure AD 登録済み | はい |  |  |  |  | |   |
| デバイス - Azure AD 参加済み |   | はい | はい | はい | はい | はい |
| Intune セキュリティ ベースライン適用 |   | はい <br> (拡張) | はい <br> (高セキュリティ) | はい <br> (NCSC) | はい <br> (セキュリティで保護) |  NA |
| ハードウェアがセキュリティで保護された Windows 10 の標準を満たしている |   | はい | はい | はい | はい | はい |
| Microsoft Defender ATP が有効になっている |   | はい  | はい | はい | はい | はい |
| 管理者権限の削除 |   |   | はい  | はい | はい | はい |
| Microsoft Autopilot を使用するデプロイ |   |   | はい  | はい | はい | はい |
| Intune でのみインストールされるアプリ |   |   |   | はい | はい |はい |
| URL を承認済みリストに制限 |   |   |   | はい | はい |はい |
| インターネットのブロック (受信/送信) |   |   |   |  |  |はい |

## <a name="license-requirements"></a>ライセンスの要件

このガイドで説明する概念は、Microsoft 365 Enterprise E5 または同等の SKU を使用していることを前提としています。 このガイドの一部の推奨事項は、以前の SKU で実装することができます。 詳細については、[Microsoft 365 Enterprise のライセンス](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)に関するページをご覧ください。

ライセンスのプロビジョニングを自動化するために、ユーザーの[グループベースのライセンス](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)を検討してください。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory の構成

Azure Active Directory (Azure AD) では、管理者ワークステーションのユーザー、グループ、デバイスを管理します。 [管理者アカウント](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)で ID サービスおよび機能を有効にする必要があります。

セキュリティで保護されたワークステーションの管理者アカウントを作成するときに、現在のワークステーションにアカウントを公開します。 この初期構成とすべてのグローバル構成を行うときに、既知の安全なデバイスを使用する必要があります。 初めて構成する際に攻撃にさらされるリスクを軽減するために、[マルウェア感染を防ぐためのガイダンス](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)に従うことを検討してください。

また、少なくとも管理者には多要素認証を要求する必要があります。 実装のガイダンスについては、[クラウドベースの MFA のデプロイ](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)に関するページを参照してください。

### <a name="azure-ad-users-and-groups"></a>Azure AD のユーザーとグループ

1. Azure portal から、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[新しいユーザー]** の順に移動します。
1. [ユーザーの作成に関するチュートリアル](https://docs.microsoft.com/Intune/quickstart-create-user)の手順に従って、デバイス管理者を作成します。
1. 次のように入力します。
   * **名前** - セキュリティで保護されたワークステーションの管理者
   * **ユーザー名** - `secure-ws-admin@identityitpro.com`
   * **ディレクトリ ロール** - **制限付き管理者**。**Intune 管理者**ロールを選択します。
1. **作成** を選択します。

次に、ワークステーション ユーザーとワークステーション デバイスの 2 つのグループを作成します。

Azure portal から、 **[Azure Active Directory]**  >  **[グループ]**  >  **[新しいグループ]** に移動します。

1. ワークステーション ユーザー グループの場合、[グループベースのライセンス](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)を構成して、ユーザーへのライセンスのプロビジョニングを自動化できます。
1. ワークステーション ユーザー グループでは、次のように入力します。
   * **グループの種類** - セキュリティ
   * **グループの名前** - セキュリティで保護されたワークステーションのユーザー
   * **メンバーシップの種類**: 割り当て済み
1. セキュリティで保護されたワークステーションの管理者ユーザー (`secure-ws-admin@identityitpro.com`) を追加します。
1. セキュリティで保護されたワークステーションを管理する他のすべてのユーザーを追加できます。
1. **作成** を選択します。

1. ワークステーション デバイス グループでは、次のように入力します。
   * **グループの種類** - セキュリティ
   * **グループ名** - セキュリティで保護されたワークステーション
   * **メンバーシップの種類**: 割り当て済み
1. **作成** を選択します。

### <a name="azure-ad-device-configuration"></a>Azure AD デバイスの構成

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>デバイスを Azure AD に参加させることができるユーザーを指定する

管理セキュリティ グループがデバイスをドメインに参加させることができるように、Active Directory でデバイスの設定を構成します。 Azure portal からこの設定を構成するには、次の手順を実行します。

1. **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** に移動します。
1. **[ユーザーはデバイスを Azure AD に参加させることができます]** で **[選択済み]** を選択し、"セキュリティで保護されたワークステーションのユーザー" グループを選択します。

#### <a name="removal-of-local-admin-rights"></a>ローカル管理者権限の削除

この方法では、VIP、DevOps、およびセキュア レベルのワークステーションのユーザーには、自分のコンピューターに対する管理者権限を付与しないようにする必要があります。 Azure portal からこの設定を構成するには、次の手順を実行します。

1. **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** に移動します。
1. **[Azure AD 参加済みデバイス上の追加のローカル管理者]** で、 **[なし]** を選択します。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>デバイスを参加させるには多要素認証が必要

Azure AD にデバイスを参加させるプロセスをさらに強化するには、次の手順を実行します。

1. **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** に移動します。
1. **[デバイスを参加させるには多要素認証が必要]** で **[はい]** を選択します。
1. **[保存]** を選択します。

#### <a name="configure-mdm"></a>MDM を構成する

Azure ポータルで次の手順を実行します。

1. **[Azure Active Directory]**  >  **[モビリティ (MDM および MAM)]**  >  **[Microsoft Intune]** に移動します。
1. **[MDM ユーザー スコープ]** 設定を **[すべて]** に変更します。
1. **[保存]** を選択します。

これらの手順により、Intune でデバイスを管理できるようになります。 詳細については、「[クイック スタート: Windows 10 デバイスの自動登録を設定する](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)」を参照してください。 Intune の構成とコンプライアンス ポリシーは以降の手順で作成します。

#### <a name="azure-ad-conditional-access"></a>Azure AD の条件付きアクセス

Azure AD の条件付きアクセスを使用すると、特権管理タスクを準拠デバイスに制限できます。 **セキュリティで保護されたワークステーションのユーザー** グループの定義済みのメンバーは、クラウド アプリケーションにサインインするときに多要素認証を実行する必要があります。 ベスト プラクティスとして、ポリシーから緊急アクセス アカウントを除外します。 詳細については、[Azure AD での緊急アクセス アカウントの管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)に関する記事をご覧ください。

Azure portal から条件付きアクセスを構成するには、次の手順を実行します。

1. **[Azure Active Directory]**  >  **[条件付きアクセス]**  >  **[新しいポリシー]** に移動します。
1. 次のように入力します。
   * **名前** - セキュリティで保護されたデバイスにはポリシーが必要
   * 割り当て
     * **ユーザーとグループ**
       * 含める - **ユーザーとグループ** - 先ほど作成された**セキュリティで保護されたワークステーションのユーザー** グループを選択します。
       * 除外する - **ユーザーとグループ** - 組織の緊急アクセス アカウントを選択します。
     * **クラウド アプリ** - **すべてのクラウド アプリ**を含めます。
    * アクセス制御
      * **許可** - **[アクセス権の付与]** ラジオ ボタンを選択します。
        * **[多要素認証が必要です]** 。
        * **[デバイスは準拠としてマーク済みである必要があります]** 。
        * 複数のコントロールの場合 - **[選択したコントロールすべてが必要]** 。
    * ポリシーを有効にする - **[オン]**

ユーザーが会社のリソースにアクセスしない国をブロックするポリシーを作成することもできます。 IP の場所ベースの条件付きアクセス ポリシーの詳細については、[Azure Active Directory 条件付きアクセスの場所の条件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)に関する記事をご覧ください。

## <a name="intune-configuration"></a>Intune の構成

### <a name="configure-enrollment-status"></a>登録ステータスを構成する

セキュリティで保護されたワークステーションが信頼できるクリーンなデバイスであることを確認することが重要です。 新しいデバイスの購入時に、出荷時の設定を [S モードの Windows 10 Pro](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode) にするよう要求できます。これにより、サプライ チェーンの管理時に脆弱性にさらされるリスクを抑えることができます。 デバイスをサプライヤーから受け取った後に、Autopilot を使用して S モードから変更できます。 以下のガイダンスでは、変換プロセスの適用に関する詳細を提供します。

使用する前に、デバイスが完全に構成されていることを確認するために、Intune には、**すべてのアプリとプロファイルがインストールされるまでデバイスの使用をブロックする**手段が用意されています。

**Azure portal** から次の手順を実行します。
1. **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[登録ステータス ページ]**  >  **[既定]**  >  **[設定]** に移動します。
1. **[アプリとプロファイルのインストール進行状況を表示する]** を **[はい]** に設定します。
1. **[すべてのアプリとプロファイルがインストールされるまでデバイスの使用をブロックする]** を **[はい]** に設定します。

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot デプロイ プロファイルを作成する

デバイス グループの作成後、デプロイ プロファイルを作成して、Autopilot デバイスを構成する必要があります。

Azure portal の Intune で次の手順を実行します。

1. **[デバイスの登録]**  >  **[Windows の登録]**  >  **[デプロイ プロファイル]**  >  **[プロファイルの作成]** を選択します。
1. 次のように入力します。
   * 名前 - **セキュリティで保護されたワークステーションのデプロイ プロファイル**
   * 説明 - **セキュリティで保護されたワークステーションのデプロイ**
   * **[すべての対象デバイスを Autopilot に変換する]** を **[はい]** に設定します。 この設定により、リスト内のすべてのデバイスが確実に Autopilot デプロイ サービスに登録されます。 登録の処理を 48 時間できるようにします。
1. **[次へ]** を選択します。
   * **[配置モード]** で **[自己展開 (プレビュー)]** を選択します。 このプロファイルが割り当てられたデバイスは、デバイスを登録するユーザーに関連付けられます。 デバイスを登録するには、ユーザーの資格情報が必要です。
   * **[Azure AD への参加の種類]** ボックスに **[Azure AD 参加済み]** と表示され、淡色表示されている必要があります。
   * 言語 (リージョン) を選択し、ユーザー アカウントの種類として **[Standard]** を選択します。 
1. **[次へ]** を選択します。
   * スコープのタグを選択します (構成済みのタグがある場合)。
1. **[次へ]** を選択します。
1. **[割り当て]**  >  **[割当先]**  >  **[選択したグループ]** を選択します。 **[含めるグループを選択]** で、 **[セキュリティで保護されたワークステーションのユーザー]** を選択します。
1. **[次へ]** を選択します。
1. **[作成]** を選択してプロファイルを作成します。 これで、Autopilot デプロイ プロファイルをデバイスに割り当てられるようになりました。

### <a name="configure-windows-update"></a>Windows Update を構成する

Windows 10 を最新の状態に保つことは、実行できる最も重要なことの 1 つです。 Windows を安全な状態に維持するには、更新プログラムがワークステーションに適用されるペースを管理する更新プログラム リングをデプロイします。 

このガイダンスでは、新しい更新プログラム リングを作成し、次の既定の設定を変更することをお勧めします。

Azure Portal で次の操作を行います。

1. **[Microsoft Intune]**  >  **[ソフトウェア更新プログラム]**  >  **[Windows 10 更新プログラムのリング]** に移動します。
1. 次のように入力します。
   * 名前 - **Azure マネージド ワークステーションの更新プログラム**
   * サービス チャネル - **Windows Insider - 高速**
   * Quality update deferral (days)\(品質更新プログラムの延期 (日数)\) - **3**
   * 機能更新プログラムの延期期間 (日数) - **3**
   * 自動更新の動作 - **エンド ユーザーによる制御なしで自動的にインストールおよび再起動する**
   * ユーザーによる Windows Update の一時停止をブロックする - **ブロック**
   * 作業時間外に再起動するにはユーザーの承認が必要です - **必要**
   * ユーザーに再起動を許可する (再起動猶予期間) - **必要**
   * 自動再起動後にユーザーを再起動猶予期間に切り替え (日数) - **3**
   * 再起動猶予期間リマインダーの一時停止 (日数) - **3**
   * 保留中の再起動の期限を設定する (日数) - **3**

1. **作成** を選択します。
1. **[割り当て]** タブで、**セキュリティで保護されたワークステーション** グループを追加します。

Windows Update のポリシーに関する追加情報については、「[Policy CSP - Update (ポリシー CSP - 更新プログラム)](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)」をご覧ください。

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 統合

Windows Defender ATP と Microsoft Intune が連携することで、セキュリティ侵害を防ぐことができます。 また、侵害の影響を抑えることもできます。 ATP 機能により、リアルタイムの脅威検出が提供され、エンドポイント デバイスの広範な監査とログが有効化されます。

Windows Defender ATP と Intune の統合を構成するには、Azure portal に移動します。

1. **[Microsoft Intune]**  >  **[デバイスのポリシー準拠]**  >  **[Windows Defender ATP]** に移動します。
1. **[Configuring Windows Defender ATP]\(Windows Defender ATP の構成\)** の手順 1 で、 **[Connect Windows Defender ATP to Microsoft Intune in the Windows Defender Security Center​]\(Windows Defender セキュリティ センターで Windows Defender ATP を Microsoft Intune に接続する\)** を選択します。
1. Windows Defender セキュリティ センターで、次のようにします。
   1. **[設定]**  >  **[高度な機能]** を選択します。
   1. **[Microsoft Intune connection]\(Microsoft Intune の接続\)** で、 **[オン]** を選択します。
   1. **[Save preferences]\(ユーザー設定の保存\)** を選択します。
1. 接続が確立されたら、Intune に戻り、上部にある **[更新]** を選択します。
1. **[Connect Windows devices version 10.0.15063 and above to Windows Defender ATP]\(バージョン 10.0.15063 以上の Windows デバイスを Windows Defender ATP に接続します\)** を **[オン]** に設定します。
1. **[保存]** を選択します。

詳細については、[Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) に関する記事をご覧ください。

### <a name="finish-workstation-profile-hardening"></a>ワークステーション プロファイルのセキュリティ強化を完了する

ソリューションのセキュリティ強化を正常に完了するには、適切なスクリプトをダウンロードして実行します。 必要な**プロファイル レベル**のダウンロード リンクを確認します。

| プロファイル | ダウンロード場所 | ファイル名 |
| --- | --- | --- |
| 低セキュリティ | 該当なし |  該当なし |
| 強化されたセキュリティ | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| 高セキュリティ  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 専用イメージ | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 特殊なコンプライアンス* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| セキュリティで保護 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* 特殊なコンプライアンスは、NCSC Windows 10 セキュリティ ベースラインで提供される特殊な構成を適用するスクリプトです。

このスクリプトが正常に実行されたら、Intune でプロファイルとポリシーを更新できます。 強化されたプロファイルとセキュリティで保護されたプロファイルのスクリプトによって、ポリシーとプロファイルが自動的に作成されますが、**セキュリティで保護されたワークステーション** グループにポリシーを割り当てる必要があります。

* スクリプトによって作成された Intune デバイス構成プロファイルは、**Azure portal** >  **[Microsoft Intune]**  >  **[デバイスの構成]**  >  **[プロファイル]** で確認できます。
* スクリプトによって作成された Intune デバイス コンプライアンス ポリシーは、**Azure portal** >  **[Microsoft Intune]**  >  **[デバイスのポリシー準拠]**  >  **[ポリシー]** で確認できます。

スクリプトによって行われた変更を確認するために、プロファイルをエクスポートできます。 これにより、SECCON のドキュメントで概説されているように、必要になる可能性のある追加のセキュリティ強化を確認できます。

[DeviceConfiguration GiuHub リポジトリ](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)の Intune データ エクスポート スクリプト `DeviceConfiguration_Export.ps1` を実行して、現在のすべての Intune プロファイルをエクスポートします。

## <a name="additional-configurations-and-hardening-to-consider"></a>考慮すべき追加の構成とセキュリティ強化

ここでのガイダンスに従って、セキュリティで保護されたワークステーションをデプロイしました。 ただし、追加の制御も検討する必要があります。 例:

* 代替ブラウザーへのアクセスの制限
* 送信 HTTP の許可
* 一部の Web サイトのブロック
* カスタム PowerShell スクリプトを実行するためのアクセス許可の設定

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>ファイアウォールの構成サービス プロバイダー (CSP) で規則を設定する

許可されたエンドポイントとブロックされたエンドポイントでの必要に応じて、インバウンド規則とアウトバウンド規則の管理に変更を加えることができます。 セキュリティで保護されたワークステーションのセキュリティ強化を続けていく中で、受信トラフィックと送信トラフィックをすべて拒否する制限を緩和できます。 送信が許可されたサイトを追加して、共通の信頼できる Web サイトを含めます。 詳細については、[ファイアウォールの構成サービス](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)に関する記事をご覧ください。

既定の制限されている推奨事項は次のとおりです。

* 受信をすべて拒否
* 送信をすべて拒否

Spamhaus Project では、[ドメイン ブロック リスト (DBL)](https://www.spamhaus.org/dbl/) を管理しています。これは、サイトをブロックする際の出発点として使用できる優れたリソースです。

### <a name="manage-local-applications"></a>ローカル アプリケーションを管理する

生産性アプリケーションなど、ローカル アプリケーションが削除されると、セキュリティで保護されたワークステーションが完全にセキュリティ強化された状態に移行します。 ここでは、既定のブラウザーとして Chrome を追加し、Intune を使用してユーザーによるブラウザーとそのプラグインの変更を制限します。

#### <a name="deploy-applications-using-intune"></a>Intune を使用してアプリケーションをデプロイする

場合によっては、セキュリティで保護されたワークステーションで Google Chrome ブラウザーのようなアプリケーションが必要になります。 次の例では、**セキュリティで保護されたワークステーション** セキュリティ グループ内のデバイスに Chrome をインストールする手順を示します。

1. オフライン インストーラーである [Windows 64 ビット版 Chrome バンドル](https://cloud.google.com/chrome-enterprise/browser/download/)をダウンロードします。
1. ファイルを抽出し、`GoogleChromeStandaloneEnterprise64.msi` ファイルの場所を書き留めておきます。
1. **Azure portal** で、 **[Microsoft Intune]**  >  **[クライアント アプリ]**  >  **[アプリ]**  >  **[追加]** に移動します。
1. **[アプリの種類]** で、 **[基幹業務]** を選択します。
1. **[アプリのパッケージ ファイル]** で、抽出された場所から `GoogleChromeStandaloneEnterprise64.msi` ファイルを選択し、 **[OK]** をクリックします。
1. **[アプリ情報]** で、説明と発行元を入力します。 **[OK]** を選択します。
1. **[追加]** を選択します。
1. **[割り当て]** タブの **[割り当ての種類]** で、 **[登録済みデバイスで使用可能]** を選択します。
1. **[組み込まれたグループ]** で、**セキュリティで保護されたワークステーション** グループを追加します。
1. **[OK]** を選択し、 **[保存]** を選択します。

Chrome の設定の構成に関するその他のガイダンスについては、「[Manage Chrome Browser with Microsoft Intune (Microsoft Intune で Chrome ブラウザーを管理する)](https://support.google.com/chrome/a/answer/9102677)」をご覧ください。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>カスタム アプリ用のポータル サイトの構成

セキュリティで保護されたモードでは、アプリケーションのインストールが Intune ポータル サイトに制限されます。 しかし、ポータルをインストールするには、Microsoft Store へのアクセスが必要です。 セキュリティで保護されたソリューションでは、オフライン モードを使用して、すべてのデバイスでポータル サイトを利用できるようにすることが可能です。

[ポータル サイト](https://docs.microsoft.com/Intune/store-apps-company-portal-app)の Intune で管理されているコピーを使用すると、セキュリティで保護されたワークステーションのユーザーにプッシュ ダウンできる追加のツールにオンデマンドでアクセスできます。

デプロイに特別な準備を必要とする Windows 32 ビット アプリや他のアプリをインストールすることが必要な場合があります。 このような場合、[Microsoft win32 コンテンツ準備ツール](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)で、インストールにすぐに使用できる `.intunewin` 形式のファイルを提供できます。

### <a name="use-powershell-to-create-custom-settings"></a>PowerShell を使用してカスタム設定を作成する

PowerShell を使用して、ホスト管理機能を拡張することもできます。 このサンプル スクリプトでは、ホストで既定の背景を設定します。 これは、Azure portal とプロファイルを通じて利用することもできる機能です。 このサンプル スクリプトは、PowerShell の機能を説明する目的でのみ提供されています。

セキュリティで保護されたワークステーションで、カスタム コントロールを設定したり、いくつかの設定を行うことが必要な場合があります。 この例では、すぐに使えるセキュリティで保護されたワークステーションとしてデバイスを簡単に識別できる Powershell の機能を使用して、ワークステーションの背景を変更します。

Microsoft スクリプト センターの [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) スクリプトを使用すると、Windows で[こちらの無料の汎用背景イメージ](https://i.imgur.com/OAJ28zO.png)を起動時に読み込むことができます。

1. ローカル デバイスにスクリプトをダウンロードします。
1. customerXXXX と背景イメージのダウンロード先を更新します。 この例では、customerXXXX を背景に置き換えます。  
1. **Azure portal** >  **[Microsoft Intune]**  >  **[デバイスの構成]**  >  **[PowerShell スクリプト]**  >  **[追加]** に移動します。
1. スクリプトの**名前**を入力し、**スクリプトの場所**を指定します。
1. **[構成]** をクリックします。
   1. **[このスクリプトをログオンしたユーザーの資格情報を使用して実行する]** を **[はい]** に設定します。
   1. **[OK]** を選択します。
1. **作成** を選択します。
1. **[割り当て]**  >  **[グループの選択]** を選択します。
   1. **セキュリティで保護されたワークステーション** セキュリティ グループを追加します。
   1. **[保存]** を選択します。

## <a name="enroll-and-validate-your-first-device"></a>最初のデバイスを登録して確認する

1. デバイスを登録するには、次の情報が必要です。
   * **シリアル番号** - デバイス シャーシにあります。
   * **Windows 製品 ID** - Windows 設定メニューの **[システム]**  >  **[バージョン情報]** にあります。
   * [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) を実行すると、デバイスの登録に必要なすべての情報が含まれた CSV ハッシュ ファイルを取得できます。
   
     `Get-WindowsAutoPilotInfo – outputfile device1.csv` を実行して、Intune にインポートできる CSV ファイルとして情報を出力します。

     > [!NOTE]
     > スクリプトには管理者特権が必要です。 リモートの署名済みスクリプトとして実行されます。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` コマンドを使用すると、スクリプトを正しく実行できます。

   * この情報は、Windows 10 バージョン 1809 以降のデバイスにサインインすることで収集できます。 また、ハードウェア リセラーもこの情報を提供できます。
1. **Azure portal** で、 **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[デバイス - Windows AutoPilot デバイスを管理する]** に移動します。
1. **[インポート]** を選択し、CSV ファイルを選択します。
1. **セキュリティで保護されたワークステーション** セキュリティ グループにデバイスを追加します。
1. 構成する Windows 10 デバイスで、 **[Windows の設定]**  >  **[更新とセキュリティ]**  >  **[回復]** に移動します。
   1. **[この PC を初期状態に戻す]** で **[開始する]** を選択します。
   1. プロンプトに従ってデバイスをリセットし、構成済みのプロファイルとコンプライアンス ポリシーを使用して再構成します。

デバイスが構成されたら、構成を確認します。 デプロイを続行する前に、最初のデバイスが正しく構成されていることを確認します。

## <a name="assign-and-monitor"></a>割り当てと監視

デバイスとユーザーを割り当てるには、[選択されたプロファイル](https://docs.microsoft.com/intune/device-profile-assign)をセキュリティ グループにマップする必要があります。 サービスに対するアクセス許可を必要とするすべての新しいユーザーもセキュリティ グループに追加する必要があります。

プロファイルは、[Intune のプロファイル監視機能](https://docs.microsoft.com/intune/device-profile-monitor)を使用して監視できます。

## <a name="next-steps"></a>次の手順

* [Microsoft Intune](https://docs.microsoft.com/intune/index) の詳細を確認します。
* [Azure AD](https://docs.microsoft.com/azure/active-directory/index) について理解します。
