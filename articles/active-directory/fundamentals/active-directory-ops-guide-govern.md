---
title: Azure Active Directory ガバナンス運用リファレンス ガイド
description: この運用リファレンス ガイドでは、ガバナンス管理をセキュリティで保護するために実行する必要がある検査とアクションについて説明します。
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
ms.openlocfilehash: ba3ca140abe36a31ffa03422420ea537bbe4f39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935752"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory ガバナンス運用リファレンス ガイド

[Azure AD 運用リファレンス ガイド](active-directory-ops-guide-intro.md)のこのセクションでは、非特権および特権 ID が付与されたアクセスを評価および証明し、環境への変更を監査および管理するために行う必要があるチェックとアクションについて説明します。

> [!NOTE]
> これらの推奨事項は公開日の時点で最新ですが、時間の経過と共に変化する可能性があります。 Microsoft の製品とサービスは時間の経過と共に進化するため、継続的にガバナンス プラクティスを評価する必要があります。

## <a name="key-operational-processes"></a>主要な運用プロセス

### <a name="assign-owners-to-key-tasks"></a>主要タスクに所有者を割り当てる

Azure Active Directory を管理するには、ロールアウト プロジェクトに含まれていない主要な運用タスクとプロセスを継続的に実行する必要があります。 環境を最適化するために、これらのタスクを設定することも重要です。 主なタスクと推奨される所有者は次のとおりです。

| タスク | 所有者 |
| :- | :- |
| SIEM システムで Azure AD 監査ログをアーカイブする | InfoSec 運用チーム |
| 準拠していないマネージド アプリケーションを検出する | IAM 運用チーム |
| アプリケーションへのアクセスを定期的に確認する | InfoSec アーキテクチャ チーム |
| 外部 ID へのアクセスを定期的に確認する | InfoSec アーキテクチャ チーム |
| 特権ロールを持つユーザーを定期的に確認する | InfoSec アーキテクチャ チーム |
| 特権ロールをアクティブ化するためのセキュリティ ゲートを定義する | InfoSec アーキテクチャ チーム |
| 同意の付与を定期的に確認する | InfoSec アーキテクチャ チーム |
| 組織の従業員に基づいてアプリケーションとリソースのカタログとアクセス パッケージを設計する | アプリ所有者 |
| セキュリティ ポリシーを定義して、パッケージにアクセスするユーザーを割り当てます | InfoSec チーム + アプリ所有者 |
| ポリシーに承認ワークフローが含まれる場合、ワークフローの承認を定期的に確認する | アプリ所有者 |
| アクセス レビューを使用して、条件付きアクセス ポリシーなどのセキュリティ ポリシーの例外を確認する | InfoSec 運用チーム |

リストを確認する際には、所有者が空のタスクに所有者を割り当てるか、上記の推奨事項に一致しない所有者を持つタスクの所有権を調整する必要がある場合があります。

#### <a name="owner-recommended-reading"></a>所有者に関する推奨資料

- [Azure Active Directory での管理者ロールの割り当て](../roles/permissions-reference.md)
- [Azure でのガバナンス](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>構成変更のテスト

ターゲットとなるユーザーのサブセットの展開などの単純な手法から、並列テスト テナントへの変更の展開まで、テスト時に特別な配慮が必要な変更があります。 テスト戦略を実装していない場合は、次の表のガイドラインに基づいてテスト アプローチを定義する必要があります。

| シナリオ| 推奨 |
|-|-|
|認証の種類をフェデレーションから PHS/PTA に、またはその逆に変更する| [段階的ロールアウト](../hybrid/how-to-connect-staged-rollout.md)を使用して、認証の種類を変更した場合の影響をテストします。|
|新しい条件付きアクセス (CA) ポリシーまたは ID 保護ポリシーを展開する|新しい条件付きアクセス ポリシーを作成し、テスト ユーザーに割り当てます。|
|アプリケーションのテスト環境のオンボード|アプリケーションを運用環境に追加し、MyApps パネルで非表示にして、品質保証 (QA) フェーズでテスト ユーザーに割り当てます。|
|同期規則の変更|現在運用環境にあるものと同じ構成 (ステージング モードとも呼ばれます) を使用して、テスト Azure AD Connect で変更を実行し、CSExport の結果を分析します。 問題がなく、準備ができたら運用環境にスワップします。|
|ブランド化の変更|別のテスト テナントでテストします。|
|新しい機能の展開|この機能がターゲットとなるユーザー セットへの展開をサポートしている場合は、パイロット ユーザーを特定して構築します。たとえば、セルフサービス パスワード リセットと多要素認証は、特定のユーザーまたはグループをターゲットにすることができます。|
|Active Directory などのオンプレミス ID プロバイダー (IdP) から Azure AD へのアプリケーションの切り替え|アプリケーションが複数の IdP 構成 (Salesforce など) をサポートしている場合、変更期間中に両方を構成し、Azure AD をテストします (アプリケーションで HRD ページを導入する場合)。 アプリケーションが複数の IdP をサポートしていない場合、変更管理期間とプログラムのダウンタイム中にテストをスケジュールします。|
|動的グループ規則の更新|新しい規則を使用して、並列動的グループを作成します。 計算された結果と比較します。たとえば、同じ条件で PowerShell を実行します。<br>テストに合格した場合、古いグループが使用されていた場所をスワップします (可能な場合)。|
|製品ライセンスの移行|「[Azure Active Directory のライセンス グループ内の 1 人のユーザーのライセンスを変更する](../enterprise-users/licensing-groups-change-licenses.md)」を参照してください。|
|承認、発行、MFA などの AD FS 規則の変更|グループ要求を使用して、ユーザーのサブセットをターゲットにします。|
|AD FS 認証エクスペリエンスまたは同様のファーム全体の変更|同じホスト名で並列ファームを作成し、構成の変更を実装し、HOSTS ファイル、NLB ルーティング規則、または同様のルーティングを使用してクライアントからテストします。<br>ターゲット プラットフォームが HOSTS ファイル (モバイル デバイスなど) をサポートしていない場合、変更を管理します。|

## <a name="access-reviews"></a>アクセス レビュー

### <a name="access-reviews-to-applications"></a>アプリケーションに対するアクセス レビュー

時間の経過と共に、ユーザーがチームや地位を異動すると、リソースへのアクセスが蓄積する可能性があります。 リソースの所有者は、定期的にアプリケーションへのアクセスを確認し、ユーザーのライフサイクル全体で不要になった特権を削除することが重要です。 Azure AD [アクセス レビュー](../governance/access-reviews-overview.md)を組織で使用することにより、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理できます。 リソース所有者は定期的にユーザーのアクセスを確認し、適切なユーザーのみがアクセスを継続できるようにします。 理想的には、このタスクに Azure AD のアクセス レビューを使用することをお勧めします。

![アクセス レビューの開始ページ](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> アクセス レビューを操作する各ユーザーには、有料の Azure AD Premium P2 ライセンスが必要です。

### <a name="access-reviews-to-external-identities"></a>外部 ID に対するアクセス レビュー

必要な時間内に、必要なリソースのみに制限された外部 ID へのアクセスを維持することが重要です。 Azure AD [アクセス レビュー](../governance/access-reviews-overview.md)を使用して、すべての外部 ID およびアプリケーション アクセスに対する定期的な自動アクセス レビュー プロセスを確立します。 プロセスが既にオンプレミスに存在する場合は、Azure AD アクセス レビューの使用を検討します。 アプリケーションがインベントリから削除されるか、使用されなくなったら、アプリケーションへのアクセス権を持っていたすべての外部 ID を削除します。

> [!NOTE]
> アクセス レビューを操作する各ユーザーには、有料の Azure AD Premium P2 ライセンスが必要です。

## <a name="privileged-account-management"></a>特権アカウントの管理

### <a name="privileged-account-usage"></a>特権アカウントの使用

ハッカーは、多くの場合、管理者アカウントや特権アクセスのその他の要素を標的にして、機密データやシステムにすばやくアクセスします。  特権ロールを持つユーザーは時間の経過と共に蓄積する傾向があるため、定期的に管理者アクセスを確認および管理し、Azure AD および Azure リソースへのジャストインタイム特権アクセスを提供することが重要です。

特権アカウントを管理するプロセスが組織に存在しない場合、または通常のユーザー アカウントを使用してサービスとリソースを管理する管理者が現在いる場合は、すぐに個別のアカウントの使用を開始することをお勧めします。たとえば、定期的な日常のアクティビティ用と、特権アクセス用で MFA を使用して構成されたものです。 さらに、組織に Azure AD Premium P2 サブスクリプションがある場合は、すぐに [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM) をデプロイするようにします。 同じトークン内で、その特権アカウントも確認し、必要に応じて[下位の特権ロールを割り当てます](../roles/security-planning.md)。

実装が推奨される特権アカウント管理のもう 1 つの側面は、そのようなアカウントに対して、手動で、または [PIM による自動的な方法](../privileged-identity-management/pim-how-to-perform-security-review.md)で[アクセス レビュー](../governance/access-reviews-overview.md)を定義することです。

#### <a name="privileged-account-management-recommended-reading"></a>特権アカウントの管理に関する推奨資料

- [Azure AD Privileged Identity Management におけるロール](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>緊急アクセス アカウント

組織は、次のような認証停止などの場合に備えて、[緊急アカウント](../roles/security-emergency-access.md)を作成して Azure AD を管理できるように準備する必要があります。

- 認証インフラストラクチャ (AD FS、オンプレミス AD、MFA サービス) の停止コンポーネント
- 管理スタッフの離職

管理者としてのサインインまたはアクティブ化に既存の個々のユーザーのアカウントを使用できないためにテナントから誤ってロックアウトされる問題を回避するには、2 つ以上の緊急アカウントを作成します。また、それらが実装され、[Microsoft のベスト プラクティス](../roles/security-planning.md)と ["非常時" の手順](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)に従っていることを確認します。

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA ポータルへの特権アクセス

[Azure マイクロソフト エンタープライズ契約 (Azure EA) ポータル](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)を使用すると、エンタープライズ内の強力なロールであるマスター マイクロソフト エンタープライズ契約に対して Azure サブスクリプションを作成できます。 Azure AD を適切に設定する前でも、このポータルの作成をブートストラップするのが一般的であるため、Azure AD ID を使用してそれをロックし、ポータルから個人アカウントを削除し、適切な委任が行われていることを確認し、ロックアウトのリスクを軽減する必要があります。

わかりやすく説明すると、EA ポータルの承認レベルが現在 "混合モード" に設定されている場合、EA ポータルのすべての特権アクセスからすべての [Microsoft アカウント](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)を削除し、Azure AD アカウントのみを使用するように EA ポータルを構成する必要があります。 EA ポータルの委任された役割が構成されていない場合は、部門とアカウントの委任された役割を見つけて実装する必要もあります。

#### <a name="privileged-access-recommended-reading"></a>特権アクセスに関する推奨資料

- [Azure Active Directory での管理者ロールのアクセス許可](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>エンタイトルメント管理

[エンタイトルメント管理 (EM)](../governance/entitlement-management-overview.md) を使用すると、アプリ所有者はリソースをバンドルし、組織内の特定のペルソナ (内部および外部) に割り当てることができます。 EM を使用すると、ビジネス オーナーのセルフサービスのサインアップと委任を許可し、さらにアクセスを許可し、アクセス期間を設定し、承認ワークフローを許可するガバナンス ポリシーを維持できます。 

> [!NOTE]
> Azure AD エンタイトルメント管理には Azure AD Premium P2 ライセンスが必要です。

## <a name="summary"></a>まとめ

セキュリティで保護された ID ガバナンスには 8 つの側面があります。 この一覧は、非特権および特権 ID に付与されたアクセスを評価および証明し、環境への変更を監査および管理するために実行する必要があるアクションを特定するために役立ちます。

- 主要タスクに所有者を割り当てる。
- テスト戦略を実装する。
- Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理する。
- すべての種類の外部 ID およびアプリケーション アクセスに対して、定期的な自動アクセス レビュー プロセスを確立する。
- アクセス レビュー プロセスを確立し、定期的に管理者アクセスを確認および管理し、Azure AD および Azure リソースへのジャストインタイムの特権アクセスを提供する。
- 予期しない停止に備えて Azure AD を管理するための緊急アカウントを準備する。
- Azure EA ポータルへのアクセスをロックする。
- エンタイトルメント管理を実装し、リソースのコレクションに対して管理されたアクセスを提供します。

## <a name="next-steps"></a>次のステップ

[Azure AD の運用のチェックとアクション](active-directory-ops-guide-ops.md)を開始します。