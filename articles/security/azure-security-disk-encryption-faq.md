---
title: "Azure Disk Encryption についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption についてよく寄せられる質問への回答を示します。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: c28604e3b7058f830c69eedc5d7f25d65e2448a8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="azure-disk-encryption-faq"></a>Azure Disk Encryption に関する FAQ

この記事では、Windows および Linux IaaS VM の Azure Disk Encryption に関してよく寄せられる質問 (FAQ) への回答を示します。 このサービスの詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)」をご覧ください。

## <a name="general-questions"></a>一般的な質問
**Q:** Azure Disk Encryption はどこで一般公開 (GA) されていますか。

**A:** Windows および Linux IaaS VM の Azure Disk Encryption は、すべての Azure パブリック リージョンで一般公開されています。

**Q:** Azure Disk Encryption ではどのようなユーザー エクスペリエンスを利用できますか。

**A:** Azure Disk Encryption の一般公開では、Azure Resource Manager テンプレート、Azure PowerShell、および Azure CLI がサポートされています。 これにより、柔軟性が高まっています。 IaaS VM のディスク暗号化を有効にするオプションは 3 つあります。 Azure Disk Encryption のユーザー エクスペリエンスと段階的なガイダンスの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

**Q:** Azure Disk Encryption のコストはどのくらいかかりますか。

**A:** Azure Disk Encryption による VM ディスクの暗号化には料金はかかりません。

**Q:** Azure Disk Encryption はどのレベルの仮想マシンをサポートしていますか。

**A:** Azure Disk Encryption は、[A、D、DS、G、GS、および F](https://azure.microsoft.com/pricing/details/virtual-machines/) シリーズの IaaS VM を含む Standard レベルの VM で使用できます。 Premium Storage がある VM でも利用できます。 Basic レベルの VM では使用できません。

**Q:** Azure Disk Encryption は、どの Linux ディストリビューションをサポートしていますか。

**A:** Azure Disk Encryption は、以下の Linux サーバーのディストリビューションおよびバージョンでサポートされます。

| Linux ディストリビューション | バージョン | 暗号化がサポートされているボリュームの種類|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS とデータ ディスク |
| Ubuntu | 14.04.5-DAILY-LTS | OS とデータ ディスク |
| RHEL | 7.3 | OS とデータ ディスク |
| RHEL | 7.2 | OS とデータ ディスク |
| RHEL | 6.8 | OS とデータ ディスク |
| RHEL | 6.7 | データ ディスク |
| CentOS | 7.3 | OS とデータ ディスク |
| CentOS | 7.2n | OS とデータ ディスク |
| CentOS | 6.8 | OS とデータ ディスク |
| CentOS | 7.1 | データ ディスク |
| CentOS | 7.0 | データ ディスク |
| CentOS | 6.7 | データ ディスク |
| CentOS | 6.6 | データ ディスク |
| CentOS | 6.5 | データ ディスク |
| openSUSE | 13.2 | データ ディスク |
| SLES | 12 SP1 | データ ディスク |
| SLES | 優先順位: 12-SP1 | データ ディスク |
| SLES | HPC 12 | データ ディスク |
| SLES | 優先順位: 11-SP4 | データ ディスク |
| SLES | 11 SP4 | データ ディスク |

**Q:** Azure Disk Encryption の使用を開始するにはどうすればよいですか。

**A**: 使用を開始するには、[Windows および Linux IaaS VM の Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) に関するホワイト ペーパーをご覧ください。

**Q:** Azure Disk Encryption でブート ボリュームとデータ ボリュームの両方を暗号化できますか。

**A:** はい、Windows および Linux IaaS VM のブート ボリュームとデータ ボリュームを暗号化できます。 Windows VM の場合は、データを暗号化する前に、OS ボリュームを暗号化する必要があります。 Linux VM の場合は、先に OS ボリュームを暗号化しなくても、データを暗号化できます。 Linux の OS ボリュームを暗号化した後で Linux IaaS VM の OS ボリュームの暗号化を無効にすることはできません。

**Q:** Azure Disk Encryption では、Bring Your Own Key (BYOK) 機能は有効ですか。

**A:** はい、独自のキー暗号化キーを指定できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

**Q:** Azure によって作成されたキー暗号化キーを使用できますか。

**A:** はい、Azure Key Vault を使用して、Azure Disk Encryption で使用するキー暗号化キーを生成できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

**Q:** オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護することはできますか。

**A:** Azure Disk Encryption で暗号化キーを保護するためにオンプレミスのキー管理サービスまたは HSM を使用することはできません。 暗号化キーを保護するために使用できるのは、Azure Key Vault サービスのみです。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

**Q:** Azure Disk Encryption を構成するための前提条件とは何ですか。

**A:** 前提条件となる PowerShell スクリプトがあります。 このスクリプトを使用して、Azure Active Directory アプリケーションを作成して新しいキー コンテナーを作成するか、既存のキー コンテナーをディスク暗号化アクセス用に設定してシークレットとキーの暗号化と保護を実行できるようにします。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption の前提条件とデプロイ シナリオおよびエクスペリエンスを参照してください。

**Q:** Azure Disk Encryption を構成するための PowerShell の使用方法に関する詳細情報はどこで入手できますか。

**A:** 基本的な Azure Disk Encryption タスクの実行方法に関連する優れた記事の他、より高度なシナリオが用意されています。 基本的なタスクについては、「[Explore Azure Disk Encryption with Azure PowerShell – Part 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)」(Azure PowerShell を使用する Azure Disk Encryption の探索 - パート 1) を参照してください。 より高度なシナリオについては、「[Explore Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)」(Azure PowerShell を使用する Azure Disk Encryption の探索 – パート 2) を参照してください。

**Q:** Azure Disk Encryption ではどのバージョンの Azure PowerShell がサポートされていますか。

**A:** Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用してください。 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードしてください。 Azure Disk Encryption は、Azure SDK Version 1.1.0 では*サポートされていません*。

> [!NOTE]
> Linux 用 Azure Disk Encryption プレビューの拡張機能は推奨されていません。 詳細については、「[Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)」(Linux IaaS VM 用 Azure Disk Encryption プレビューの拡張機能の非推奨化) を参照してください。

**Q:** カスタム Linux イメージに対して Azure Disk Encryption を適用できますか。

**A:** カスタム Linux イメージに対して Azure Disk Encryption を適用することはできません。 前述したサポート対象のディストリビューションのギャラリー Linux イメージのみがサポートされます。 カスタム Linux イメージは現時点ではサポートされていません。

**Q:** yum update を使用して Linux Red Hat VM に更新プログラムを適用できますか。

**A:**はい、Red Hat Linux VM の更新プログラムまたは修正プログラムを実行できます。 詳細については、「[Applying updates to a encrypted Azure IaaS Red Hat VM using Yum Update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)」(yum update を使用して暗号化された Azure IaaS Red Hat VM に更新プログラムを適用する) を参照してください。

**Q:** 質問やフィードバックはどこに投稿すればよいでしょうか。

**A:** ご質問やフィードバックは、[Azure Disk Encryption フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)に投稿してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Disk Encryption に関して最もよく寄せられるご質問について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure Virtual Machine を暗号化する](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [保存時の Azure データの暗号化](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

