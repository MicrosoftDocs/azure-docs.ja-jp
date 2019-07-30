---
title: サンプル - PCI-DSS v3.2.1 ブループリント - コントロール マッピング
description: Payment Card Industry Data Security Standard v3.2.1 ブループリント サンプルの Azure Policy と RBAC へのコントロール マッピング。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540947"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI-DSS v3.2.1 ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints PCI-DSS v3.2.1 のブループリント サンプルが PCI-DSS v3.2.1 のコントロールにどのようにマップされているかを説明したものです。 そのコントロールについて詳しくは、[PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) に関するページをご覧ください。

以下のマッピングは、**PCI-DSS v3.2.1: 2018** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。 次に、 **[[プレビュー] PCI v3.2.1:2018 コントロールを監査し、特定の VM 拡張機能をデプロイして監査要件をサポートする]** 組み込みポリシー イニシアティブを見つけて選択します。

## <a name="132-and-134-boundary-protection"></a>1.3.2 および 1.3.4 境界保護

このブループリントでは、寛容なルールが適用されているネットワーク セキュリティ グループを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、ネットワークの管理と制御を強化することができます。 ルールが寛容すぎると、意図しないネットワーク アクセスが許可される恐れがあるため、そのようなルールがないか確認する必要があります。 このブルー プリントでは、保護されていないエンドポイント、アプリケーション、およびストレージ アカウントを監視する 1 つの Azure Policy 定義を割り当てることができます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- ストレージ アカウントに対する制限のないネットワーク アクセスの監査
- インターネットに接続するエンドポイント経由のアクセスを制限する必要がある

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a、4.1、4.1.g、4.1.h、および 6.5.3 暗号化による保護

このブループリントでは、特定の暗号化コントロールを適用し、脆弱な暗号化設定の使用を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、暗号化コントロールの使用に関するポリシーの適用を支援します。 最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 具体的には、このブループリントによって割り当てられたポリシーでは、SQL データベース上で透過的なデータ暗号化が必要になるほか、ストレージ アカウントの暗号化の漏れの監査、および Automation アカウント変数が必要になります。 ストレージ アカウント、Function Apps、WebApp、API Apps、および Redis Cache への安全でない接続の監査に対処し、暗号化されていない Service Fabric 通信を監査するポリシーもあります。

- Function App には HTTPS 経由でのみアクセスできるようにする
- Web アプリケーションには HTTPS を介してのみアクセスできるようにする
- API アプリには HTTPS を介してのみアクセスできるようにする
- 暗号化されていない SQL データベースの Azure Security Center での監視
- 仮想マシンでディスク暗号化を適用する必要がある
- Automation アカウント変数は、暗号化する必要がある
- Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある
- ストレージ アカウントへの安全な転送を有効にする必要がある
- Service Fabric クラスターでは、ClusterProtectionLevel プロパティを EncryptAndSign に設定する必要がある
- SQL データベースで Transparent Data Encryption を有効にする必要がある
- SQL DB Transparent Data Encryption のデプロイ

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、6.2、6.6、および 11.2.1 脆弱性スキャンとシステム更新

このブループリントでは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、および仮想マシンの脆弱性を Azure Security Center で監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、情報システムの脆弱性管理を改善できます。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。

- Endpoint Protection の欠落の Azure Security Center での監視
- Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ
- SQL Server での脅威検出のデプロイ
- システム更新プログラムをマシンにインストールする必要がある
- 使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある
- SQL データベースの脆弱性を修復する必要がある
- 脆弱性評価ソリューションによって脆弱性を修復する必要がある

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1、 7.1.2、および 7.1.3 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 サブスクリプション所有者のアクセス許可を管理することで、職務を適切に分離することができます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 最大 3 人の所有者をサブスクリプションに対して指定する必要がある 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、7.2.1、8.3.1.a、および 8.3.1.b アクセス特権の管理

このブループリントでは、所有者アクセス許可、書き込みアクセス許可、または読み取りアクセス許可を持つ外部アカウントと、所有者アクセス許可や書き込みアクセス許可を持つ、多要素認証が有効になっていない従業員アカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、アクセス特権を制限および制御することができます。 Azure では、Azure リソースにアクセスするユーザーを管理するために、ロールベースのアクセス制御 (RBAC) が実装されています。 カスタム RBAC ルールの実装状況を把握することで、それらの実装ニーズや実装の適切性を確認することができます (カスタム RBAC ルールはエラーを起こしやすいので、これは非常に重要です)。 このブループリントでは、SQL Server に対する Azure Active Directory 認証の使用を監査する [Azure Policy](../../../policy/overview.md) 定義も割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます  
。
 
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある
- SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある
- カスタム RBAC 規則の使用監査

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 および 8.1.5 最小特権とユーザー アクセス権の確認

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、ロールベースのアクセス制御 (RBAC) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントでは、優先的にレビューする必要があるアカウント (非推奨のアカウントや、管理者特権のアクセス許可を持つ外部アカウントなど) を監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある
- 読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 アクセス権の削除または調整

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、ロールベースのアクセス制御 (RBAC) が実装されています。 Azure Active Directory と RBAC を使用すれば、ユーザー ロールを更新して組織の変更を反映することができます。 必要な場合は、サインインしようとしているアカウントをブロック (または削除) して、Azure リソースへのアクセス権を直ちに削除することもできます。 このブループリントでは、削除を検討する必要がある非推奨アカウントを監査するための [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- 非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a、b、8.2.4.a、b、および 8.2.5 パスワードベースの認証

このブループリントでは、最低限の強度や他のパスワード要件が適用されていない Windows VM を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、強力なパスワードの適用を支援します。 パスワード強度のポリシーに違反している VM を把握できるようになるので、適切な是正措置を実施し、すべての VM ユーザー アカウントに対して、パスワード ポリシーへの準拠を徹底させることができます。

- [プレビュー]: Audit Windows VMs that do not have a maximum password age of 70 days
- [プレビュー]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [プレビュー]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [プレビュー]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [プレビュー]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [プレビュー]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 および 10.5.4 監査の生成

このブループリントでは、Azure リソースのログ設定を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システム イベントのログ記録の徹底を支援します。
診断ログは、Azure リソース内で実行された操作の分析情報を提供します。 Azure のログでは、リソース全体にまたがるイベントの時間相関レコードを作成するために、同期された内部時計が使用されます。

- 未監査の SQL サーバーを Azure Security Center で監視する
- 診断設定の監査
- SQL サーバー レベルの監査設定の監査
- SQL Server での監査のデプロイ
- ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある
- 仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある

## <a name="1236-and-1237-information-security"></a>12.3.6 および 12.3.7 情報セキュリティ

このブループリントでは、許容できるネットワークの場所とその環境で許可されている承認済みの企業製品を監査する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、ネットワークの管理と制御を強化することができます。 これらは、これらの各ポリシー内のポリシー パラメーターを通じて各企業によってカスタマイズ可能です。

- 許可される場所
- リソース グループが許可される場所

## <a name="next-steps"></a>次の手順

PCI-DSS v3.2.1 のブループリントのコントロール マッピングを確認しました。次に、以下の記事で概要およびこのサンプルをデプロイする方法を確認します。

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 ブループリント - 概要](./index.md)
> [PCI-DSS v3.2.1 ブループリント - デプロイ手順](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
