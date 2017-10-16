---
title: "Azure Stack の管理の基本 | Microsoft Docs"
description: "Azure Stack を管理するために知っておく必要があることについて説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 04dea8f055eb562455b568c43553a6fefe749467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-administration-basics"></a>Azure Stack の管理の基本

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack の管理経験がない場合は、知っておく必要があることがいくつかあります。 このガイドでは、Azure Stack オペレーターとしての役割の概要と、ユーザーがすばやく生産的になれるようにユーザーに伝える必要があることを説明します。

## <a name="understand-the-builds"></a>ビルドを理解する

### <a name="integrated-systems"></a>統合システム

Azure Stack 統合システムを使用している場合、Azure Stack の更新バージョンは更新プログラム パッケージを通して配布されます。 これらのパッケージをインポートして、管理者ポータルの [更新] タイルでパッケージを適用できます。
 
### <a name="development-kit"></a>Development kit

Azure Stack Development Kit を使用している場合は、「[What is Azure Stack? (Azure Stack とは)](azure-stack-poc.md)」の記事を確認し、Development Kit の目的と制限事項について確実に理解しておいてください。 開発キットは "サンド ボックス" として使用する必要があります。その中では、Azure Stack を評価し、非運用環境でアプリの開発とテストを行うことができます。 (デプロイの情報については、[Azure Stack Development Kit](azure-stack-deploy-overview.md) のデプロイ クイックスタートを参照してください。)

Azure のように、Microsoft は迅速にイノベーションを進めています。 新しいビルドは定期的にリリースされます。 Development Kit を実行しており、最新のビルドに移行する場合は、[Azure Stack を再デプロイ](azure-stack-redeploy.md)する必要があります。 更新プログラム パッケージは適用できません。 このプロセスは時間がかかりますが、最新の機能を試すことができるというメリットがあります。 Microsoft Web サイトの Development Kit ドキュメントには、最新のリリース ビルドが反映されています。

## <a name="learn-about-available-services"></a>利用できるサービスの詳細

ユーザーにどのようなサービスを提供できるかを認識しておく必要があります。 Azure Stack では Azure サービスのサブセットがサポートされます。 サポートされるサービスは引き続き増加してゆきます。

**基礎となるサービス**

Azure Stack をデプロイすると、Azure Stack には既定で以下の "基礎となるサービス" が含まれています。

- コンピューティング
- ストレージ
- ネットワーク
- Key Vault

これらの基礎となるサービスによって、最小限の構成でユーザーにサービスとしてのインフラストラクチャ (IaaS) を提供できます。

**その他のサービス**

現時点では、その他に以下の、サービスとしてのプラットフォーム (PaaS) サービスをサポートしています。

- App Service
- Azure Functions
- SQL および MySQL データベース

これらのサービスをユーザーに提供する前には、追加の構成が必要です。 詳細については、Azure Stack オペレーター ドキュメントの "チュートリアル" や "ハウツー ガイド\サービスの提供" に関するセクションをご覧ください。

**サービスのロードマップ**

Azure Stack には、引き続き Azure サービスのサポートが追加される予定です。 計画されているロードマップについては、「[Azure Stack: An extension of Azure (Azure Stack: Azure の拡張機能)](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)」のホワイトペーパーをご覧ください。 新しい発表については、[Azure Stack のブログ投稿](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)をチェックすることもできます。

## <a name="what-tools-do-i-use-to-manage"></a>管理に使用するツールについて
 
[管理者ポータル](azure-stack-manage-portals.md)または PowerShell を使用して Azure Stack を管理できます。 基本的概念を学ぶためには、ポータルを介するのが最も容易な方法です。 PowerShell を使用する場合は、準備の手順があります。 PowerShell を[インストール](azure-stack-powershell-install.md)し、追加モジュールを[ダウンロード](azure-stack-powershell-download.md)して、PowerShell を[構成](azure-stack-powershell-configure-admin.md)する必要があります。

Azure Stack はデプロイ、管理、整理のための基礎となるメカニズムとして、Azure Resource Manager を使用します。 Azure Stack の管理とユーザーのサポートを行う予定であれば、Resource Manager について学習する必要があります。 「[Azure Resource Manager の概要](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)」ホワイトペーパーを参照してください。

## <a name="your-typical-responsibilities"></a>クラウド オペレーターの通常の担当範囲

ユーザーはサービスを使用する必要があります。 ユーザーの観点からは、クラウド オペレーターの主な役割は、ユーザーがこれらのサービスを使用できるようにすることです。 提供するサービスを決定し、プラン、オファー、クォータを作成することで、それらのサービスを使用できるようにする必要があります。 詳細については、「[Overview of offering services in Azure Stack (Azure の提供サービスの概要)](azure-stack-offer-services-overview.md)」をご覧ください。 

仮想マシンのイメージなど、[マーケットプレース](azure-stack-marketplace.md)に項目を追加する必要もあります。 [マーケットプレースの項目を Azure から Azure Stack にダウンロードする](azure-stack-download-azure-marketplace-item.md)のが最も容易な方法です。

> [!NOTE]
> プラン、オファー、およびサービスをテストする場合は、管理者ポータルではなく[ユーザー ポータル](azure-stack-manage-portals.md)を使用する必要があります。

サービスを提供することに加えて、オペレーターとして Azure Stack を稼働させ続けるという通常の業務をすべて実行する必要があります。 これには以下のような業務が含まれます。

- ユーザー アカウントを追加する ([Azure Active Directory](azure-stack-add-new-user-aad.md) のデプロイや [Active Directory フェデレーション サービス (AD FS) ](azure-stack-add-users-adfs.md)のデプロイのため)
- [ロールベースのアクセス制御 (RBAC) のロールを割り当てる](azure-stack-manage-permissions.md) (これは管理者に限られません)
- [インフラストラクチャの正常性を監視する](azure-stack-monitor-health.md)
- [ネットワーク](azure-stack-viewing-public-ip-address-consumption.md)と[ストレージ](azure-stack-manage-storage-accounts.md)のリソースを管理する
- 不具合のあるハードウェアを交換する、たとえば[障害が発生したディスクを交換する](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>ユーザーに伝えること

ユーザーには、Azure Stack 内のサービスを操作する方法、環境に接続する方法、オファーをサブスクライブする方法を知らせる必要があります。 ユーザーに提供するカスタム ドキュメントのほかに、ユーザーには Azure Stack ユーザー ドキュメント サイトを案内できます。

**Azure Stack 内のサービスを操作する方法について**

Azure Stack 内のサービスを使用してアプリを構築する前に、ユーザーが理解しておく必要のある情報があります。 たとえば、PowerShell と API については、特定のバージョン要件があります。 また、Azure のサービスと Azure Stack の同等のサービスに備わる機能には、一部違いがあります。 ユーザーには、以下の記事に必ず目を通してもらってください。

- [主な考慮事項: Azure Stack でのサービスの使用またはアプリの作成](user/azure-stack-considerations.md)
- [Azure Stack の仮想マシンに関する考慮事項](user/azure-stack-vm-considerations.md)
- [ストレージ: 相違点と考慮事項](user/azure-stack-acs-differences.md)

これらの記事には、Azure と Azure Stack でのサービスの相違点に関する情報がまとめられています。 これらは、グローバルな Azure ドキュメントで Azure サービスについて提供されている情報を補完するものです。

**ユーザーとしての Azure Stack への接続**

開発キット環境では、ユーザーが開発キット ホストに対するリモート デスクトップのアクセス権を持っていない場合は、Azure Stack にアクセスする前に仮想プライベート ネットワーク (VPN) 接続を構成する必要があります。 「[Azure Stack への接続](azure-stack-connect-azure-stack.md)」を参照してください。 

ユーザーは、[ユーザー ポータルへのアクセス方法](user/azure-stack-use-portal.md)や PowerShell を介した接続方法を知る必要があります。 統合システム環境では、ユーザーのポータル アドレスはデプロイごとに異なります。 ユーザーには正しい URL を提供する必要があります。

PowerShell を使用する場合、ユーザーはサービスを使用する前にリソース プロバイダーに登録する必要がある場合があります。 (リソース プロバイダーがサービスを管理しています。 たとえば、ネットワーク リソース プロバイダーは仮想ネットワーク、ネットワーク インターフェイス、ロード バランサーなどのリソースを管理しています。)ユーザーは PowerShell を[インストール](user/azure-stack-powershell-install.md)し、追加のモジュールを[ダウンロード](user/azure-stack-powershell-download.md)して、PowerShell を[構成](user/azure-stack-powershell-configure-user.md)する (これにはリソース プロバイダーの登録が含まれます) 必要があります。

**プランへのサブスクライブ**

ユーザーはサービスにアクセスする前に、オペレーターが作成した[オファーをサブスクライブする](azure-stack-subscribe-plan-provision-vm.md)必要があります。

## <a name="where-to-get-support"></a>サポートが受けられる場所

### <a name="integrated-systems"></a>統合システム

統合システムについては、Microsoft と Microsoft の OEM (original equipment manufacturer) ハードウェア パートナーとの間で、統合されたエスカレーションと解決のプロセスをご用意しています。

クラウド サービスに問題がある場合は、Microsoft カスタマー サポート サービス (CSS) を通じてサポートを提供いたします。 管理者ポータルの右上隅にあるヘルプとサポート アイコン (疑問符) をクリックして **[新しいサポート要求]** をクリックすると、サポート要求を直接展開できるサイトが開きます。

デプロイ、パッチ、更新プログラム、ハードウェア (現場交換可能ユニットを含む) や、ハードウェア ライフサイクル ホストで実行するソフトウェアなどのハードウェア ブランドのソフトウェアに問題がある場合は、はじめに OEM ハードウェア ベンダーにお問い合わせください。

その他の問題については、Microsoft CSS にお問い合わせください。

### <a name="development-kit"></a>Development kit

Development Kit については、[Microsoft フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)でサポート関連の質問をすることができます。 管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) をクリックし、**[新しいサポート要求]** をクリックすると、フォーラムのサイトが直接開かれます。 これらのフォーラムは定期的にチェックされています。 開発キットは評価環境であるため、Microsoft CSS を通した正式なサポートは提供されていません。

## <a name="next-steps"></a>次のステップ

- [Azure Stack でのリージョンの管理](azure-stack-region-management.md)


