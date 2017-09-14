---
title: "Azure Security Center でサポートされているプラットフォーム | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center でサポートされている Windows と Linux のオペレーティング システムの一覧を示します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c33e132037d95fa92fd59a8243a9a8a351ae0224
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="supported-platforms-in-azure-security-center"></a>Azure Security Center でサポートされているプラットフォーム
セキュリティ状態の監視と推奨事項は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルのどちらで作成された仮想マシン (VM) でも利用できます。

> [!NOTE]
> Azure リソースの詳細については、 [クラシック デプロイメント モデルと Resource Manager デプロイメント モデル](../azure-classic-rm.md) に関するページを参照してください。
>
>

## <a name="supported-platforms-for-windows-vms"></a>Windows VM でサポートされているプラットフォーム
サポートされている Windows オペレーティング システム:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

> [!NOTE]
>
* OS の脆弱性評価は、Windows Server 2016 ではまだ利用できません。
* クラッシュ分析とクラッシュ検出は Windows Server 2012 と Windows Server 2012 R2 でのみサポートされています。
>
>

## <a name="supported-platforms-for-linux-vms"></a>Linux VM でサポートされているプラットフォーム
サポートされている Linux オペレーティング システム:

* Ubuntu バージョン 12.04、14.04、16.04、16.10
* Debian バージョン 7、8
* CentOS バージョン 6.\*、7.*
* Red Hat Enterprise Linux (RHEL) バージョン 6.\*、7.*
* SUSE Linux Enterprise Server (SLES) バージョン 11 SP4 以降、12.*
* Oracle Linux バージョン 6\*、7.*

> [!NOTE]
> 仮想マシンの動作分析は、Linux オペレーティング システムではまだ利用できません。
>
>

## <a name="vms-and-cloud-services"></a>VM とクラウド サービス
クラウド サービスで実行されている VM もサポートされます。 監視されるのは、運用スロットで実行されているクラウド サービスの Web ロールと worker ロールだけです。 クラウド サービスの詳細については、 [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- 「[Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)」 - Azure Security Center を導入するための設計上の考慮事項を計画および理解する方法について説明します。
- [Azure Security Center のタイプ別のセキュリティ アラート](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) - Security Center における仮想マシンの動作分析とクラッシュ ダンプ メモリ分析について説明します。
- [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

