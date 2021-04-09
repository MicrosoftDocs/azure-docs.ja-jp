---
title: OS、マシンの種類、クラウドに応じた Azure Security Center の機能
description: OS、種類、クラウドのデプロイに応じて使用できる Azure Security Center の機能について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 070f35dc3efea4a3f05ec36cc3860fa589aa87e3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437912"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

以下の 2 つのタブは、Windows および Linux の仮想マシンとサーバーで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows マシン**](#tab/features-windows)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 対応マシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[仮想マシン行動分析 (およびセキュリティ アラート)](alerts-reference.md)|✔|✔|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|-|-|-|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|✔|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|


### <a name="linux-machines"></a>[**Linux マシン**](#tab/features-linux)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 対応マシン**|**Azure Defender が必要**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint 統合](security-center-wdatp.md)|-|-|-|-|
|[仮想マシン行動分析 (およびセキュリティ アラート)](./azure-defender.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|はい|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|-|-|-|はい|
|[ネットワーク ベースのセキュリティ アラート](other-threat-protections.md#network-layer)|✔|✔|-|はい|
|[Just In Time VM アクセス](security-center-just-in-time.md)|✔|-|-|はい|
|[ネイティブ脆弱性評価](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|はい|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|はい|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|はい|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|はい|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|はい|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|はい|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|✔|✔|✔|はい|
|足りない OS パッチの評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: いいえ<br><br>Arc 対応:はい|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|いいえ|
|ディスク暗号化の評価|✔</br>([サポートされるシナリオ](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)の場合)|✔|-|いいえ|
|サードパーティの脆弱性評価|✔|-|✔|いいえ|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|いいえ|

--- 


> [!TIP]
>Azure Defender でのみ利用可能な機能を試すために、30 日間の試用版に登録できます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。


## <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 この一覧からのエンドポイント保護ソリューションが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Microsoft Defender ウイルス対策| Windows Server 2016 以降| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – Deep Security | Windows Server ファミリ  | いいえ | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい |

> [!NOTE]
> Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) を検出するには、PowerShell (v3.0 またはそれ以降) の後で SCEP をインストールする必要があります。



## <a name="feature-support-in-government-clouds"></a>Government クラウドでの機能のサポート

| サービスまたは機能 | US Gov | China Gov |
|------|:----:|:----:|
|[Just-In-Time VM アクセス](security-center-just-in-time.md) (1)|✔|✔|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[適応型アプリケーション制御](security-center-adaptive-application.md) (1)|✔|✔|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker ホストのセキュリティ強化機能](harden-docker-hosts.md) (1)|✔|✔|
|[マシンに対する統合された脆弱性評価](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender for Endpoint](harden-docker-hosts.md) (1)|✔|-|
|[AWS アカウントを接続する](quickstart-onboard-aws.md) (1)|-|-|
|[GCP アカウントを接続する](quickstart-onboard-gcp.md) (1)|-|-|
|[連続エクスポート](continuous-export.md)|✔|✔|
|[ワークフローの自動化](workflow-automation.md)|✔|✔|
|[推奨事項の除外規則](exempt-resource.md)|-|-|
|[アラートの抑制ルール](alerts-suppression-rules.md)|✔|✔|
|[セキュリティ アラートのメール通知](security-center-provide-security-contact-details.md)|✔|✔|
|[資産インベントリ](asset-inventory.md)|✔|✔|
|[Azure Defender for App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender for Storage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender for SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender for Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender for DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender for container registries](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Kubernetes ワークロード保護](kubernetes-workload-protections.md)|✔|✔|
|||

(1) **Azure Defender for servers** が必要

(2) 部分的


## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。