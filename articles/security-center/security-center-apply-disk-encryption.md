---
title: Azure Security Center で Disk Encryption を適用する | Microsoft Docs
description: このドキュメントでは、**ディスク暗号化の適用**という Azure Security Center の推奨事項を実装する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0ae2b695a64e55b493453549627860872166ed25
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202493"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Azure Security Center で Disk Encryption を適用する
お使いの Windows または Linux VM ディスクが Azure Disk Encryption を使用して暗号化されていない場合、Azure Security Center により、ディスク暗号化を適用することが推奨されます。 Disk Encryption を使用すると、Windows および Linux IaaS VM ディスクを暗号化できます。  VM 上の OS とデータ ボリュームの両方を暗号化することをお勧めします。

Disk Encryption では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用します。 これらの機能により、OS とデータを暗号化を通じてデータの安全性が保護され、織のセキュリティ要件とコンプライアンス要件を達成することができます。 Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、[Azure Storage](https://azure.microsoft.com/documentation/services/storage/) で保存中の VM ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。

> [!NOTE]
> Azure Disk Encryption は、Windows Server オペレーティング システム (Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2) でサポートされています。 Disk Encryption は、Linux サーバー オペレーティング システム (Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES)) でサポートされています。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、 **[ディスク暗号化の適用]** を選択します。
2. **[ディスク暗号化の適用]** ブレードに、Disk Encryption が推奨される VM の一覧が表示されます。
3. 手順に従って、これらの VM に暗号化を適用します。

![][1]

Security Center で暗号化が必要と識別された Azure Virtual Machines を暗号化するには、次の手順をお勧めします。

* Azure PowerShell のインストールおよび構成。 これにより、Azure Virtual Machines の暗号化に必要な前提条件を設定するための PowerShell コマンドを実行できます。
* Azure Disk Encryption の前提条件となる Azure PowerShell スクリプトの取得と実行。
* 仮想マシンの暗号化。

これらの手順については、「[Azure PowerShell を使用して Windows IaaS VM を暗号化する](../security/azure-disk-encryption-linux-powershell-quickstart.md)」で具体的に説明しています。 このトピックでは、ディスク暗号化を構成するために Windows クライアント マシンを使用していると想定しています。

Azure Virtual Machines に使用できる方法はたくさんあります。 既に Azure PowerShell または Azure CLI についてよく知っている方は、他の方法を好む場合もあります。 こうした方法の詳細については、「[Azure Disk Encryption](../security/azure-security-disk-encryption.md)」を参照してください。

## <a name="see-also"></a>関連項目
このドキュメントでは、"ディスク暗号化の適用" というセキュリティ センターの推奨事項を実装する方法について説明しました。 ディスク暗号化の詳細については、以下を参照してください。

* [Encryption and key management with Azure Key Vault (Azure Key Vault を使用した暗号化とキー管理) (Azure Key Vault を使用した暗号化とキー管理)](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) 」(動画、36 分 39 秒) -- IaaS VM と Azure Key Vault のディスク暗号化管理を使用し、データを保護する方法について説明しています。
* [Azure Disk Encryption](../security/azure-security-disk-encryption-overview.md) 」(ドキュメント) -- Windows と Linux VM のディスク暗号化を有効にする方法について説明しています。

セキュリティ センターの詳細については、次を参照してください。

* [Azure セキュリティ センターでのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」 -- セキュリティ ポリシーの構成方法について説明しています。
* [Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure セキュリティ センターのよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
