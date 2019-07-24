---
title: サンプル - ISO 27001 ASE/SQL ワークロード ブループリント - コントロール マッピング
description: Azure Policy と RBAC に対する ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルのコントロール マッピング。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7a760bfe70fa2a83c43a0b41b77ba9bf45e809ca
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258609"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>ISO 27001 ASE/SQL ワークロード ブループリント サンプルのコントロール マッピング

以下の記事は、Azure Blueprints ISO 27001 ASE/SQL Workload ブループリント サンプルが ISO 27001 コントロールにどのようにマップされているかを説明したものです。 コントロールについて詳しくは、[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html) をご覧ください。

以下のマッピングは、**ISO 27001:2013** コントロールに対するものです。 右側のナビゲーションを使用すると、特定のコントロール マッピングに直接ジャンプできます。 マップ コントロールの多くは、[Azure Policy](../../../policy/overview.md) イニシアチブを使用して実装されますす。 イニシアチブの詳細を確認するには、Azure portal で **[ポリシー]** を開き、**[定義]** ページを選択します。 その後、**[プレビュー] Audit ISO 27001:2013 コントロールを選択し、監査要件のビルトイン ポリシー イニシアチブをサポートするための VM 拡張機能をデプロイ**します。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 職務の分離

Azure サブスクリプションの所有者を 1 人しか設定しなかった場合、管理の冗長性は確保されません。 逆に、Azure サブスクリプションの所有者が多すぎると、所有者アカウントが侵害されてセキュリティ侵害が発生するリスクが高まります。 このブループリントでは、Azure サブスクリプションの所有者数を監査する 2 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サブスクリプションの所有者を適切な数に維持することができます。 サブスクリプション所有者のアクセス許可を管理することで、職務を適切に分離することができます。

- [プレビュー]: Audit minimum number of owners for subscription
- [プレビュー]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 ネットワークおよびネットワーク サービスへのアクセス

Azure では、Azure リソースにアクセスするユーザーを管理するために、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 このブループリントでは、7 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure リソースへのアクセスを制御できます。 これらのポリシーは、リソースの種類や構成の使用状況を監査することで、リソースへのアクセスをより厳格に制限するためのものです。
これらのポリシーに違反しているリソースを把握することで、適切な是正措置を実施し、承認済みのユーザーだけが Azure リソースにアクセスできるようにすることができます。

- [プレビュー]: Deploy VM extension to audit Linux VM accounts with no passwords
- [プレビュー]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  パスワードs
- [プレビュー]: Audit Linux VM accounts with no passwords
- [プレビュー]: Audit Linux VM allowing remote connections from accounts with no passwords
- 従来のストレージ アカウントの使用に関する監査
- 従来の仮想マシンの使用の監査
- マネージド ディスクを使用していない VM の監査

## <a name="a922-user-access-provisioning"></a>A.9.2.2 ユーザー アクセスのプロビジョニング

Azure では、Azure リソースにアクセスするユーザーを管理するために、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 このブループリントでは、SQL Server と [Service Fabric](../../../../service-fabric/service-fabric-overview.md) に対する [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 認証の使用状況を監査するために、3 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 Azure Active Directory 認証を使用すると、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。 また、このブルー プリントでは、カスタム RBAC ルールの使用状況を監査するための Azure Policy 定義も割り当てられます。 カスタム RBAC ルールの実装状況を把握することで、それらの実装ニーズや実装の適切性を確認することができます (カスタム RBAC ルールはエラーを起こしやすいので、これは非常に重要です)。

- SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングの監査
- Service Fabric でクライアント認証に Azure Active Directory を使用していることの監査
- カスタム RBAC 規則の使用監査

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 アクセス特権の管理

このブループリントでは、所有者アクセス許可や書き込みアクセス許可を持つ外部アカウントと、所有者アクセス許可や書き込みアクセス許可を持つ、多要素認証が有効になっていないアカウントを監査するための、4 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、アクセス特権を制限および制御することができます。

- [プレビュー]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [プレビュー]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [プレビュー]: Audit external accounts with owner permissions on a subscription
- [プレビュー]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 ユーザーのシークレット認証情報の管理

このブループリントでは、多要素認証が有効になっていないアカウントを監査するための、3 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。 多要素認証は、1 つの認証情報が侵害された場合でも、アカウントのセキュリティが維持されるようにするために役立ちます。 多要素認証が有効になっていないアカウントを監視することで、侵害される可能性が高いアカウントを特定することができます。 また、このブループリントでは、Linux VM のパスワード ファイルのアクセス許可を監査し、それらが正しく設定されていない場合にアラートを生成する、2 つの Azure Policy 定義も割り当てられます。 これを設定すれば、必要な是正措置を行って、認証が侵害されないようにすることができます。

- [プレビュー]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [プレビュー]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [プレビュー]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [プレビュー]: Deploy VM extension to audit Linux VM passwd file permissions
- [プレビュー]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 ユーザー アクセス権のレビュー

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 Azure リソースにできるユーザーとそのアクセス許可は、Azure portal を使用して確認できます。 このブループリントでは、優先的にレビューする必要があるアカウント (非推奨のアカウントや、管理者特権のアクセス許可を持つ外部アカウントなど) を監査するための、4 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- [プレビュー]: Audit deprecated accounts on a subscription
- [プレビュー]: Audit deprecated accounts with owner permissions on a subscription
- [プレビュー]: Audit external accounts with owner permissions on a subscription
- [プレビュー]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 アクセス権の削除や調整

Azure では、Azure のリソースにアクセスするユーザーを効果的に管理できるように、[ロールベースのアクセス制御](../../../../role-based-access-control/overview.md) (RBAC) が実装されています。 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) と RBAC を使用すれば、ユーザー ロールを更新して組織の変更を反映することができます。 必要な場合は、サインインしようとしているアカウントをブロック (または削除) して、Azure リソースへのアクセス権を直ちに削除することもできます。 このブループリントでは、削除を検討する必要がある非推奨アカウントを監査するための、2 つの [Azure Policy](../../../policy/overview.md) 定義が割り当てられます。

- [プレビュー]: Audit deprecated accounts on a subscription
- [プレビュー]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 パスワード管理システム

このブループリントでは、最小強度やその他のパスワード要件が適用されていない Windows VM を監査する、10 個の [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、強力なパスワード ポリシーを実施できます。 パスワード強度のポリシーに違反している VM を把握できるようになるので、適切な是正措置を実施し、すべての VM ユーザー アカウントに対して、パスワード ポリシーへの準拠を徹底させることができます。

- [プレビュー]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [プレビュー]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [プレビュー]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [プレビュー]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [プレビュー]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [プレビュー]: Audit Windows VM enforces password complexity requirements
- [プレビュー]: Audit Windows VM maximum password age 70 days
- [プレビュー]: Audit Windows VM minimum password age 1 day
- [プレビュー]: Audit Windows VM passwords must be at least 14 characters
- [プレビュー]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 暗号化コントロールの使用に関するポリシー

このブループリントでは、特定の暗号化コントロールを実施し、脆弱な暗号化設定の使用を監査するための、13 個の [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、暗号化コントロールの使用に関するポリシーを徹底させることができます。
最適でない暗号化構成が Azure リソースのどこに存在しているかを把握することにより、適切な是正措置を実施し、リソースの構成を情報セキュリティ ポリシーに準拠させることができます。 このブループリントによって割り当てられるポリシーでは、次の要件が定義されています: BLOB ストレージ アカウントとデータ レイク ストレージの暗号化を実施する。SQL データベースで透過的データ暗号化を実施する。ストレージ アカウント、SQL データベース、仮想マシン ディスク、および自動化アカウント変数の中に、暗号化されていないものがないか監査する。ストレージ アカウント、Function App、Web App、API Apps、および Redis Cache に対する接続の中に、安全でないものがないか監査する。仮想マシンのパスワード暗号化に脆弱なものがないか監査する。暗号化されていない Service Fabric 通信がないか監査する。

- [プレビュー]: Audit HTTPS only access for a Function App
- [プレビュー]: Audit HTTPS only access for a Web Application
- [プレビュー]: Audit HTTPS only access for an API App
- [プレビュー]: Audit missing blob encryption for storage accounts
- [プレビュー]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  暗号化n
- [プレビュー]: Audit Windows VM should not store passwords using reversible encryption
- [プレビュー]: Monitor unencrypted SQL database in Azure Security Center
- [プレビュー]: Monitor unencrypted VM Disks in Azure Security Center
- Automation アカウント変数の暗号化が有効であることの監査
- Redis Cache に対してセキュリティで保護された接続のみが有効であることの監査
- ストレージ アカウントへの安全な転送の監査
- Service Fabric で ClusterProtectionLevel プロパティが EncryptAndSign に設定されていることの監査
- Transparent Data Encryption の状態の監査

## <a name="a1241-event-logging"></a>A.12.4.1 イベント ログ

このブルー プリントでは、Azure リソースのログ設定を監査する 7 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システム イベントのログ記録を徹底させることができます。 割り当てられたポリシーでは、指定された Log Analytics ワークスペースにログを送信していない仮想マシンがないかどうかも監査されます。

- [プレビュー]:Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS)
- [プレビュー]:VMSS の Dependency Agent デプロイの監査 - VM イメージ (OS) が一覧にない
- [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)
- [プレビュー]:VMSS の Log Analytics エージェントのデプロイの監査 - VM イメージ (OS) が一覧にない
- [プレビュー]: Monitor unaudited SQL database in Azure Security Center
- 診断設定の監査
- SQL サーバー レベルの監査設定の監査

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 技術的脆弱性の管理

このブループリントでは、未実行のシステム更新、オペレーティング システムの脆弱性、SQL の脆弱性、および仮想マシンの脆弱性を監視する 5 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、情報システムの脆弱性管理を改善できます。 これらの分析情報には、デプロイされたリソースのセキュリティ状態に関するリアルタイムな情報が含まれます。これらの情報は、修復アクションの優先度を決定するのに役立ちます。

- [プレビュー]: Monitor missing Endpoint Protection in Azure Security Center
- [プレビュー]: Monitor missing system updates in Azure Security Center
- [プレビュー]: Monitor OS vulnerabilities in Azure Security Center
- [プレビュー]: Monitor SQL vulnerability assessment results in Azure Security Center
- [プレビュー]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 ソフトウェアのインストールに関する制限

アダプティブ アプリケーション制御は、Azure Security Center から提供されるソリューションです。Azure 内に配置された VM 上で、どのアプリケーションを実行できようにするかを制御するのに役立ちます。 このブループリントでは、許可されたアプリケーション セットへの変更を監視する Azure Policy 定義が割り当てられます。 ソフトウェアのインストールに関する制限は、ソフトウェアの脆弱性が発生する可能性を減らすのに役立ちます。

- [プレビュー]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 ネットワーク制御

このブルー プリントでは、寛容なルールが適用されているネットワーク セキュリティ グループを監視する [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、ネットワークの管理と制御を強化することができます。 ルールが寛容すぎると、意図しないネットワーク アクセスが許可される恐れがあるため、そのようなルールがないか確認する必要があります。

- [プレビュー]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 情報転送のポリシーと手順

このブループリントでは、ストレージ アカウントや Redis Cache との接続の安全性を監査する 2 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、Azure サービスとの情報転送のセキュリティを強化できます。

- Redis Cache に対してセキュリティで保護された接続のみが有効であることの監査
- ストレージ アカウントへの安全な転送の監査

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 アプリケーション サービス トランザクションの保護

このブルー プリントでは、保護されていないエンドポイント、アプリケーション、およびストレージ アカウントを監視する 3 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、情報システム資産の保護を強化できます。 ファイアウォールで保護されていないエンドポイントやアプリケーションがあったり、アクセス制限のないストレージ アカウントがあると、情報システム内の情報に対する意図しないアクセスが許可される恐れがあります。

- [プレビュー]: Monitor unprotected network endpoints in Azure Security Center
- [プレビュー]: Monitor unprotected web application in Azure Security Center
- ストレージ アカウントに対する制限のないネットワーク アクセスの監査

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 情報セキュリティの脆弱性レポート

このブループリントでは、Azure Security Center 内の脆弱性、修正プログラムの状態、およびマルウェア アラートを監視する 5 つの [Azure Policy](../../../policy/overview.md) 定義を割り当てることで、システムの脆弱性の監視を強化できます。 Azure Security Center では、デプロイされた Azure リソースのセキュリティ状態をリアルタイムに分析するためのレポート機能が提供されます。

- [プレビュー]: Monitor missing Endpoint Protection in Azure Security Center
- [プレビュー]: Monitor missing system updates in Azure Security Center
- [プレビュー]: Monitor OS vulnerabilities in Azure Security Center
- [プレビュー]: Monitor SQL vulnerability assessment results in Azure Security Center
- [プレビュー]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>次の手順

ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルのコントロール マッピングが確認できたら、以下の記事に進んで、アーキテクチャの詳細と、このサンプルのデプロイ方法を確認してください。

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - 概要](./index.md)
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。