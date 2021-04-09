---
title: Azure Active Directory 一般的な運用ガイド リファレンス
description: この運用リファレンス ガイドでは、一般的な運用をセキュリティで保護するために実行する必要がある検査とアクションについて説明します。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836837"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 一般的な運用ガイド リファレンス

[Azure AD 運用リファレンス ガイド](active-directory-ops-guide-intro.md)のこのセクションでは、Azure Active Directory (Azure AD) の一般的な運用を最適化するために実行する必要がある検査とアクションについて説明します。

> [!NOTE]
> これらの推奨事項は公開日の時点で最新ですが、時間の経過と共に変化する可能性があります。 Microsoft の製品とサービスは時間の経過と共に進化するため、継続的に運用プラクティスを評価する必要があります。

## <a name="key-operational-processes"></a>主要な運用プロセス

### <a name="assign-owners-to-key-tasks"></a>主要タスクに所有者を割り当てる

Azure Active Directory を管理するには、ロールアウト プロジェクトに含まれていない主要な運用タスクとプロセスを継続的に実行する必要があります。 環境を最適化するために、これらのタスクを設定することも重要です。 主なタスクと推奨される所有者は次のとおりです。

| タスク | 所有者 |
| :- | :- |
| ID セキュリティ スコアの向上を促進する | InfoSec 運用チーム |
| Azure AD Connect サーバーの管理 | IAM 運用チーム |
| IdFix レポートを定期的に実行してトリアージする | IAM 運用チーム |
| 同期と AD FS のため Azure AD Connect Health アラートをトリアージする | IAM 運用チーム |
| Azure AD Connect Health を使用しない場合、カスタム インフラストラクチャを監視するためのプロセスとツールが同等になります | IAM 運用チーム |
| AD FS を使用しない場合、カスタム インフラストラクチャを監視するためのプロセスとツールが同等になります | IAM 運用チーム |
| ハイブリッド ログの監視:Azure AD アプリのプロキシ コネクタ | IAM 運用チーム |
| ハイブリッド ログの監視:パススルー認証エージェント | IAM 運用チーム |
| ハイブリッド ログの監視:パスワード ライトバック サービス | IAM 運用チーム |
| ハイブリッド ログの監視:オンプレミスのパスワード保護ゲートウェイ | IAM 運用チーム |
| ハイブリッド ログの監視:Azure AD MFA NPS 拡張機能 (該当する場合) | IAM 運用チーム |

リストを確認する際には、所有者が空のタスクに所有者を割り当てるか、上記の推奨事項に一致しない所有者を持つタスクの所有権を調整する必要がある場合があります。

#### <a name="owners-recommended-reading"></a>所有者向けの推奨資料

- [Azure Active Directory での管理者ロールの割り当て](../roles/permissions-reference.md)
- [Azure でのガバナンス](../../governance/index.yml)

## <a name="hybrid-management"></a>ハイブリッド管理

### <a name="recent-versions-of-on-premises-components"></a>最新バージョンのオンプレミス コンポーネント

オンプレミスのコンポーネントの最新バージョンを使用すると、最新のセキュリティ更新プログラム、パフォーマンスの向上、および環境をさらに簡素化するのに役立つ機能を顧客に提供できます。 ほとんどのコンポーネントには、アップグレード プロセスを自動化する自動アップグレード設定があります。

コンポーネントには、次が含まれます。

- Azure AD Connect
- Azure AD アプリケーション プロキシ コネクタ
- Azure AD パススルー認証エージェント
- Azure AD Connect Health エージェント

確立済みでない限り、これらのコンポーネントをアップグレードするプロセスを定義し、可能な限り自動アップグレード機能を利用する必要があります。 6 か月以上経過しているコンポーネントが見つかった場合は、できるだけ早くアップグレードする必要があります。

#### <a name="hybrid-management-recommended-reading"></a>ハイブリッド管理に関する推奨資料

- [Azure AD Connect:自動アップグレード](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Azure AD アプリケーション プロキシ コネクタを理解する | 自動アップデート](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health のアラートのベースライン

Azure AD Connect および AD FS を監視するために組織は [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) をデプロイする必要があります。 Azure AD Connect と AD FS は、ライフサイクル管理と認証を壊して、結果的に停止を引き起こす可能性があるクリティカルなコンポーネントです。 Azure AD Connect Health は、オンプレミスの ID インフラストラクチャを監視して把握し、環境の信頼性を確保するのに役立ちます。

![Azure AD Connect Health のアーキテクチャ](./media/active-directory-ops-guide/active-directory-ops-img16.png)

環境の正常性を監視するときは、重要度の高いアラートにすぐに対処し、その後に重要度の低いアラートに対処します。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health に関する推奨資料

- [Azure AD Connect Health エージェントのインストール](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>オンプレミスのエージェント ログ

一部の ID 管理とアクセス管理サービスでは、ハイブリッドシナリオを有効にするためにオンプレミスのエージェントが必要です。 例としては、パスワードのリセット、パススルー認証 (PTA)、Azure AD アプリケーション プロキシ、Azure AD MFA NPS 拡張機能などがあります。 運用チームが、System Center Operations Manager や SIEM などのソリューションを使用して、コンポーネント エージェントのログをアーカイブおよび分析することで、これらのコンポーネントのベースラインを設定して正常性を監視することが重要です。 また、エラーのパターンをトラブルシューティングする方法については、Infosec 運用チームまたはヘルプデスクにとっても重要です。

#### <a name="on-premises-agents-logs-recommended-reading"></a>オンプレミスのエージェント ログに関する推奨資料

- [アプリケーション プロキシのトラブルシューティング](../manage-apps/application-proxy-troubleshoot.md)
- [セルフサービス パスワード リセット のトラブルシューティング - Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Azure AD アプリケーション プロキシ コネクタについて](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect:パススルー認証のトラブルシューティング](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Azure AD MFA NPS 拡張機能のエラー コードのトラブルシューティング](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>オンプレミスのエージェント管理

ベスト プラクティスを採用することは、エージェントをオンプレミスで最適に運用する上で役立ちます。 次のベスト プラクティスを検討してください。

- プロキシ アプリケーションにアクセスする際の単一障害点を回避し、シームレスな負荷分散と高可用性を実現するために、コネクタ グループごとに複数の Azure AD アプリケーション プロキシ コネクタを使用することをお勧めします。 現在、運用環境でアプリケーションを処理するコネクタグループにコネクタが 1 つしかない場合は、冗長性を確保するために少なくとも 2 つのコネクタをデプロイする必要があります。
- デバッグのためにアプリ プロキシ コネクタ グループを作成して使用すると、トラブルシューティングのシナリオや、新しいオンプレミス アプリケーションのオンボードに役立ちます。 また、コネクタ コンピューターに、Message Analyzer や Fiddler などのネットワーク ツールをインストールすることもお勧めします。
- 認証フロー中の単一障害点を回避し、シームレスな負荷分散と高可用性を実現するために、複数のパススルー認証エージェントを使用することをお勧めします。 冗長性を確保するために、少なくとも 2 つのパススルー認証エージェントをデプロイしてください。

#### <a name="on-premises-agents-management-recommended-reading"></a>オンプレミスのエージェント管理に関する推奨資料

- [Azure AD アプリケーション プロキシ コネクタについて](../manage-apps/application-proxy-connectors.md)
- [Azure AD パススルー認証 - クイックスタート](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>大規模な管理

### <a name="identity-secure-score"></a>ID セキュリティ スコア

[ID セキュリティ スコア](./identity-secure-score.md)は、組織のセキュリティ対策を定量化して計測します。 報告された結果を絶えず確認して対処し、最高のスコアを得ることが重要です。 このスコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する
- ID セキュリティの強化を計画する
- 強化の成功を確認する

![セキュリティ スコア](./media/active-directory-ops-guide/active-directory-ops-img17.png)

現在、ID セキュリティ スコアの変更を監視するためのプログラムが組織に存在しない場合は、計画を実装し、改善アクションを監視および促進する所有者を割り当てることをお勧めします。 組織は、スコアの影響が 30 を超えるものには、可能な限り素早く改善アクションを修復する必要があります。

### <a name="notifications"></a>通知

Microsoft は、管理者に電子メール通知を送信して、サービスのさまざまな変更、必要な構成の更新、および管理者の介入が必要なエラーを通知します。 お客様は、通知の電子メールアドレスを設定して、すべての通知を確認して対処することができる適切なチームメンバーに通知を送信することが重要です。 [メッセージ センター](/office365/admin/manage/message-center) に複数の受信者を追加し、通知 (Azure AD Connect Health 通知を含む) を配布リストまたは共有メールボックスに送信するように要求することをお勧めします。 メール アドレスを持つグローバル管理者アカウントが 1 つしかない場合は、メールに対応しているアカウントを少なくとも 2 つ構成してください。

Azure AD によって使用される "差出人" アドレスは 2 つあります。<o365mc@email2.microsoft.com> は、メッセージ センターの通知に使用され、<azure-noreply@microsoft.com> 以下に関連する通知を送信します。

- [Azure AD アクセス レビュー](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [エンタープライズ アプリの証明書期限切れ通知](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- エンタープライズ アプリのサービス プロビジョニング通知

送信される通知の種類と確認する場所については、次の表を参照してください。

| 通知の送信元 | 送信内容 | 確認する場所 |
|:-|:-|:-|
| 技術部連絡先 | 同期エラー | Azure portal - プロパティ ブレード |
| メッセージ センター | Identity Services および Microsoft 365 バックエンド サービスのインシデントと低下の通知 | Office ポータル |
| Identity Protection の毎週のダイジェスト | Identity Protection のダイジェスト | Azure AD Identity Protection ブレード |
| Azure AD Connect Health | アラート通知 | Azure Portal - Azure AD Connect Health ブレード |
| エンタープライズ アプリケーションの通知 | 証明書の有効期限が近い場合、およびプロビジョニング エラーが発生したときの通知 | Azure portal - エンタープライズ アプリケーション ブレード (各アプリには独自のメール アドレス設定があります) |

#### <a name="notifications-recommended-reading"></a>通知に関する推奨資料

- [組織の住所、技術部連絡先、およびその他の情報を変更する](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>運用における外部からのアクセス

### <a name="ad-fs-lockdown"></a>AD FS のロックダウン

Azure AD で直接認証されるようアプリケーションを構成している組織は、[Azure AD スマート ロックアウト](../authentication/concept-sspr-howitworks.md)を利用できます。 Windows Server 2012 R2 で AD FS を使っている場合は、AD FS の[エクストラネット ロックアウト保護](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)を実装します。 Windows Server 2016 以降で AD FS を使っている場合は、[エクストラネット スマート ロックアウト](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)を実装します。 少なくとも、オンプレミスの Active Directory へのブルート フォース攻撃のリスクを含むように、エクストラネット ロックアウトを有効にすることをお勧めします。 ただし、Windows 2016 以降で AD FS を利用している場合は、[パスワード スプレー](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻撃を軽減するのに役立つ、エクストラネットのスマート ロックアウトも有効にする必要があります。

AD FS が Azure AD フェデレーションにのみ使用される場合、攻撃対象を最小限に抑えるために無効にできるエンドポイントがいくつかあります。 たとえば、AD FS が Azure AD にのみ使用される場合、**usernamemixed** および **windowstransport** に対して有効になっているエンドポイント以外の WS-Trust エンドポイントを無効にする必要があります。

### <a name="access-to-machines-with-on-premises-identity-components"></a>オンプレミスの ID コンポーネントを使用したマシンへのアクセス

オンプレミスのハイブリッド コンポーネントでは、オンプレミスのドメインと同じ方法でコンピューターへのアクセスをロックダウンする必要があります。 たとえば、バックアップ オペレーターまたは Hyper-V の管理者は、Azure AD Connect サーバーにログインしてルールを変更することはできません。

Active Directory 管理階層モデルは、環境のフル コントロール (階層 0) と攻撃者が頻繁に侵害するリスクが高いワークステーション資産の間で、一連のバッファー ゾーンを使用して ID システムを保護するよう設計されています。 ![階層モデルの 3 つの階層を示す図](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[階層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)は 3 つのレベルで構成され、管理者アカウントのみが含まれ、標準ユーザー アカウントは含まれません。

- **階層 0**: 環境内のエンタープライズ ID を直接管理します。 階層 0 には、Active Directory フォレスト、ドメイン、ドメイン コントローラー、それに含まれるすべての資産を直接的または間接的に管理するアカウント、グループ、その他の資産が含まれます。 階層 0 のすべての資産は、事実上相互に管理しあっているため、セキュリティの機密性は同じです。
- **階層 1**: エンタープライズ サーバーとアプリケーションを管理します。 階層 1 の資産には、サーバーのオペレーティング システム、クラウド サービス、エンタープライズ アプリケーションが含まれます。 階層 1 の管理者アカウントによって、これらの資産でホストされている多くのビジネス価値を管理します。 一般的なロールの例として、すべてのエンタープライズ サービスに影響する機能を持つこれらのオペレーティング システムを管理しているサーバー管理者があります。
- **階層 2**: - ユーザーのワークステーションとデバイスを管理します。 階層 2 の管理者アカウントによって、ユーザーのワークステーションやデバイスでホストされている多くのビジネス価値を管理します。 例としては、ヘルプ デスクやコンピューター サポートの管理者が挙げられます。ほとんどのユーザー データの整合性に影響を及ぼす可能性があるためです。

ドメイン コントローラーの場合と同じ方法で、Azure AD Connect、AD FS、SQL サービスなどのオンプレミスの ID コンポーネントへのアクセスをロックダウンします。

## <a name="summary"></a>まとめ

セキュリティで保護された ID インフラストラクチャには、7 つの側面があります。 この一覧は、Azure Active Directory (Azure AD) の運用を最適化するために実行する必要があるアクションを見つけるのに役立ちます。

- 主要タスクに所有者を割り当てる。
- オンプレミスのハイブリッド コンポーネントのアップグレード プロセスを自動化します。
- Azure AD Connect および AD FS を監視するために Azure AD Connect Health をデプロイする必要があります。
- System Center Operations Manager または SIEM ソリューションを使用して、コンポーネント エージェントのログをアーカイブおよび分析することで、オンプレミスのハイブリッド コンポーネントの正常性を監視します。
- ID セキュリティ スコアでセキュリティ対策を測定して、セキュリティを強化します。
- AD FS をロックダウンします。
- オンプレミスの ID コンポーネントでマシンへのアクセスをロックダウンします。

## <a name="next-steps"></a>次のステップ

デプロイされていない機能の実装の詳細については、[Azure AD のデプロイ計画](active-directory-deployment-plans.md)を参照してください。