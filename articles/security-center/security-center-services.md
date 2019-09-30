---
title: Azure Security Center でサポートされている使用可能な機能 | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるサービスの一覧を示します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0d2b417dd01e26f0f93722be66d82972b52aca83
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234570"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure Security Center でサポートされている使用可能な機能

> [!NOTE]
>一部の機能は、Standard レベルでのみ使用できます。 Security Center の Standard レベルにまだサインアップしていない場合は、無料試用期間を利用できます。 詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」ページをご覧ください。

以下のセクションでは、[サポートされているプラットフォーム](security-center-os-coverage.md)で使用できる Security Center の機能を示します。

* [仮想マシン/サーバー](#vm-server-features)
* [PaaS サービス](#paas-services)


## <a name="vm-server-features"></a>仮想マシン/サーバーでサポートされる機能

> [!div class="mx-tableFixed"]

|サーバー|Windows|||Linux|||価格|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Azure 以外**|**Azure**||**Azure 以外**||
||**仮想マシン**|**Virtual Machine Scale Set**||**仮想マシン**|**Virtual Machine Scale Set**|
|VMBA 脅威検出アラート|✔|✔|✔|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|推奨事項 (無料) 脅威の検出 (Standard)|
|ネットワーク経由の脅威検出アラート|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP の統合|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|X|X|X|Standard|
|不足しているパッチ|✔|✔|✔|✔|✔|✔|無料|
|セキュリティ構成|✔|✔|✔|✔|✔|✔|無料|
|エンドポイント保護の評価|✔|✔|✔|X|X|X|無料|
|JIT VM アクセス|✔|X|X|✔|X|X|Standard|
|アダプティブ アプリケーション制御|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|ディスク暗号化の評価|✔|✔|X|✔|✔|X|無料|
|サード パーティ デプロイ|✔|X|X|✔|X|X|無料|
|NSG の評価|✔|✔|X|✔|✔|X|無料|
|ファイルレス脅威の検出|✔|✔|✔|X|X|X|Standard|
|ネットワーク マップ|✔|✔|X|✔|✔|X|Standard|
|アダプティブ ネットワーク制御|✔|✔|X|✔|✔|X|Standard|
|規制へのコンプライアンス ダッシュボードおよびレポート|✔|✔|✔|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の検出|X|X|X|✔|✔|✔|Standard|

### サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 これらのエンドポイント保護ソリューションのいずれかが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – すべてのバージョン* | Windows Server ファミリ  | いいえ | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい **\*** |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい  **\***  |

 **\*** カバレッジの状態とサポート データは、現在、保護されているサブスクリプションに関連付けられている Log Analytics ワークスペース内でのみ使用でき、Azure Security Center ポータルには反映されません。

> [!NOTE]
>
> - Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) が検出されるには、PowerShell 3.0 (またはそれ以降) の後で SCEP をインストールする必要があります。
> - Trend Micro 保護の検出が Deep Security エージェント用にサポートされています。  OfficeScan エージェントはサポートされていません。


## PaaS サービスでサポートされる機能 <a name="paas-services"> </a>

Azure Security Center では、次の PaaS リソースがサポートされています。

|Service|推奨事項 (無料)|脅威検出 (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB*|X| ✔|
|BLOB ストレージ|✔| ✔|
|ストレージ アカウント|✔| NA|
|App Service|✔| ✔|
|Function|✔| X|
|クラウド サービス|✔| X|
|VNet|✔| NA|
|Subnet|✔| NA|
|NIC|✔| NA|
|NSG|✔| NA|
|Subscription|✔ **| ✔|
|Batch アカウント|✔| X|
|Service Fabric アカウント|✔| X|
|Automation アカウント|✔| X|
|Load Balancer|✔| X|
|Search|✔| X|
|Service Bus 名前空間|✔| X|
|Stream Analytics|✔| X|
|イベント ハブの名前空間|✔| X|
|ロジック アプリ|✔| X|
|Redis|✔| NA|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key Vault|✔| X|

\* 現在、これらの機能はパブリック プレビューでサポートされています。

\*\* Azure Active Directory (Azure AD) のレコメンデーションは、Standard サブスクリプションでのみ使用できます。

## <a name="next-steps"></a>次の手順

- [Security Center でデータが収集される方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。
- [Azure Security Center での VM と サーバーの脅威検出](security-center-alerts-iaas.md)の詳細を確認します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。
