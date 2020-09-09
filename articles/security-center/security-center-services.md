---
title: Azure Security Center でサポートされている使用可能な機能 | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるサービスの一覧を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 489f0138caa54105cced9c9ef4e8846657d84717
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607132"
---
# <a name="feature-coverage-for-machines"></a>マシンを対象とする機能

以下の 2 つのタブは、Windows および Linux の仮想マシンとサーバーで使用できる Azure Security Center の機能を示しています。

## <a name="supported-features-for-virtual-machines-and-servers"></a>仮想マシンとサーバーでサポートされている機能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows マシン**](#tab/features-windows)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|Standard|
|[仮想マシン行動分析 (およびセキュリティ アラート)](threat-protection.md)|✔|✔|✔|Standard|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[ネットワーク ベースのセキュリティ アラート](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|-|-|-|Standard|
|足りない OS パッチの評価|✔|✔|✔|Azure: Free<br><br>Azure 以外: Standard|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: Free<br><br>Azure 以外: Standard|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Free<br><br>Azure 以外: Standard|
|ディスク暗号化の評価|✔|✔|-|Free|
|サードパーティの脆弱性評価|✔|-|-|Free|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[**Linux マシン**](#tab/features-linux)

|**機能**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure 以外のマシン**|**料金**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP の統合](security-center-wdatp.md)|-|-|-|Standard|
|[仮想マシン行動分析 (およびセキュリティ アラート)](security-center-alerts-iaas.md)|✔</br>(サポートされているバージョン)|✔</br>(サポートされているバージョン)|✔|Standard|
|[ファイルレスのセキュリティ アラート](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[ネットワーク ベースのセキュリティ アラート](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-In-Time VM アクセス](security-center-just-in-time.md)|✔|-|-|Standard|
|[ネイティブ脆弱性評価](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[ファイルの整合性の監視](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[アダプティブ アプリケーション制御](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[ネットワーク マップ](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[アダプティブ ネットワークのセキュリティ強化機能](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[規制へのコンプライアンス ダッシュボードおよびレポート](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の防止|✔|✔|✔|Standard|
|足りない OS パッチの評価|✔|✔|✔|Azure: Free<br><br>Azure 以外: Standard|
|セキュリティの誤った構成の評価|✔|✔|✔|Azure: Free<br><br>Azure 以外: Standard|
|[エンドポイント保護の評価](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|ディスク暗号化の評価|✔|✔|-|Free|
|サードパーティの脆弱性評価|✔|-|-|Free|
|[ネットワーク セキュリティ評価](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>Standard 価格レベルでのみ利用可能な機能を試すために、Free レベルのユーザーは 30 日間の試用版に登録できます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。


## <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション <a name="endpoint-supported"></a>

次の表に、次の項目のマトリックスを示します。

 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 この一覧からのエンドポイント保護ソリューションが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Microsoft Defender ウイルス対策| Windows Server 2016 以降| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – Deep Security | Windows Server ファミリ  | × | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | × | ○ |
| McAfee v10 以降 | Windows Server ファミリ  | × | ○ |
| McAfee v10 以降 | Linux Server ファミリ  | いいえ | はい **\*** |
| Sophos V9+| Linux Server ファミリ  | いいえ | はい  **\***  |

 **\*** カバレッジの状態とサポート データは、現在、保護されているサブスクリプションに関連付けられている Log Analytics ワークスペース内でのみ使用できます。 Azure Security Center ポータルには反映されません。

> [!NOTE]
> Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) を検出するには、PowerShell (v3.0 またはそれ以降) の後で SCEP をインストールする必要があります。


## <a name="next-steps"></a>次のステップ

- [Security Center によるデータの収集方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Security Center がサポートされているプラットフォーム](security-center-os-coverage.md)を確認します。