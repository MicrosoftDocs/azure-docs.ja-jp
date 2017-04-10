---
title: "Azure Security Center のよく寄せられる質問 (FAQ) | Microsoft Docs"
description: "この FAQ は、Azure セキュリティ センターについて寄せられる質問とその回答です。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f07cd15658f7d367bdf364ece449dd5d09b2f9d3
ms.openlocfilehash: 38503e36f9c0ad981d0239d82a858dbe2c5263a4
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure セキュリティ センターのよく寄せられる質問 (FAQ)
この FAQ は、Azure Security Center について寄せられる質問とその回答です。Azure Security Center は、Microsoft Azure リソースのセキュリティの視覚化と制御の向上により、脅威を回避、検出、対応するのに役立つサービスです。

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-security-center"></a>Azure セキュリティ センターとは
Azure セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

### <a name="how-do-i-get-azure-security-center"></a>Azure セキュリティ センターはどうしたら取得できますか。
Azure Security Center は、Microsoft Azure サブスクリプションで有効化し、[Azure Portal](https://azure.microsoft.com/features/azure-portal/) からアクセスします  ([ポータルにサインイン](https://portal.azure.com)して、**[参照]** を選択し、**[セキュリティ センター]** までスクロールします)。  

## <a name="billing"></a>課金
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure セキュリティ センターの課金のしくみを教えてください。
Azure Security Center は Free と Standard の&2; つのプランで提供されます。

Free レベルでは、セキュリティ ポリシーを設定できるほか、必要なコントロールの構成プロセスを紹介するセキュリティ アラート、インシデント、推奨事項を受け取ることができます。 また、Free レベルでは、Azure リソースと、Azure サブスクリプションに統合済みのパートナー ソリューションのセキュリティ状態を監視することもできます。

Standard レベルでは、Free レベルの機能に加えて、高度な検出機能 (脅威インテリジェンス、行動分析、クラッシュ分析、異常検出) を使用できます。 Standard レベルは、最初の 60 日間は無料です。 60 日目以降もこのサービスを引き続き使用する選択をした場合は、サービスへの課金が自動的に開始されます。 アップグレードを行うには、 [セキュリティ ポリシー](security-center-policies.md#set-security-policies-for-subscriptions)で価格レベルを選択してください。 詳細については、[Security Center の価格](security-center-pricing.md)に関する記事を参照してください。

## <a name="permissions"></a>アクセス許可
Azure Security Center では、[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) が使用されています。RBAC が提供する[組み込みのロール](../active-directory/role-based-access-built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。

## <a name="data-collection"></a>データ収集
Security Center では、仮想マシンからデータを収集して、そのセキュリティ状態の評価、セキュリティ推奨事項の提供、脅威についての警告を行います。 最初にセキュリティ センターにアクセスするときは、サブスクリプション内のすべての仮想マシンに対してデータ収集が有効になっています。 データ収集は有効にしておくことをお勧めしますが、セキュリティ センター ポリシーで [データ収集を無効](#how-do-i-disable-data-collection) にして、オプトアウトすることもできます。

### <a name="how-do-i-disable-data-collection"></a>データ収集を無効にするにはどうしたらよいですか。
サブスクリプションの**データ収集**は、セキュリティ ポリシーでいつでも無効にできます  ([Azure Portal にサインイン](https://portal.azure.com)して、**[参照]**、**[セキュリティ センター]**、**[ポリシー]** の順に選択します)。サブスクリプションを選択すると、新しいブレードが開き、**データ収集**をオフにするオプションが表示されます。 Azure 監視エージェントは、データの収集をオフにすると自動的にサブスクリプションの既存の仮想マシンから削除されます。

> [!NOTE]
> セキュリティ ポリシーは Azure サブスクリプション レベルとリソース グループ レベルで設定できますが、データの収集をオフにするにはサブスクリプションを選択する必要があります。
>
>

### <a name="how-do-i-enable-data-collection"></a>データ収集を有効にするにはどうしたらよいですか。
Azure サブスクリプションのデータ収集の有効化は、セキュリティ ポリシーで行うことができます。 データ収集を有効にするには、[Azure Portal にサインイン](https://portal.azure.com)して、**[参照]**、**[セキュリティ センター]**、**[ポリシー]** の順に選択します。 **[データ収集]** を **[オン]** に設定し、収集するデータのストレージ アカウントを構成します (質問「[データはどこに格納されますか](#where-is-my-data-stored)」を参照)。 **データ収集** が有効になっていると、サブスクリプション内のすべてのサポートされている仮想マシンからセキュリティの構成とイベントの情報が自動的に収集されます。

> [!NOTE]
> セキュリティ ポリシーは Azure サブスクリプション レベルとリソース グループ レベルで設定できますが、データ収集の構成を行うことができるのはサブスクリプション レベルのみです。
>
>

### <a name="what-happens-when-data-collection-is-enabled"></a>データ収集を有効にするとどうなりますか。
データ収集は、Azure Monitoring Agent と Azure セキュリティの監視拡張機能を使用して有効にします。 Azure セキュリティの監視拡張機能では、さまざまなセキュリティ関連の構成をスキャンして、[Windows イベント トレーシング](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) トレースに送信します。 さらに、オペレーティング システムでは、イベント ログ エントリが作成されます。  Azure Monitoring Agent は、イベント ログ エントリと ETW トレースを読み取り、分析のためにそれらをストレージ アカウントにコピーします。  これは、セキュリティ ポリシーで構成したストレージ アカウントです。 ストレージ アカウントの詳細については、質問「[データはどこに格納されますか](#where-is-my-data-stored)」を参照してください。

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>Monitoring Agent やセキュリティの監視拡張機能は、サーバーのパフォーマンスに影響しますか。
エージェントと拡張機能は、システム リソースのわずかな量しか消費しないため、パフォーマンスにほとんど影響しません。 パフォーマンスの影響と、エージェントおよび拡張機能の詳細については、[計画および運用ガイド](security-center-planning-and-operations-guide.md#data-collection-and-storage)を参照してください。

### <a name="where-is-my-data-stored"></a>データはどこに格納されますか。
実行している仮想マシンがある各リージョンに対し、これらの仮想マシンから収集されたデータが格納されているストレージ アカウントを選択します。 これにより、プライバシーとデータ主権目的で、同じ地域にデータを保持することが容易になります。 サブスクリプションのストレージ アカウントはセキュリティ ポリシーで選択します ([Azure Portal にサインイン](https://portal.azure.com)して、**[参照]**、**[セキュリティ センター]**、**[ポリシー]** の順に選択します)。サブスクリプションを選択すると、新しいブレードが開きます。 リージョンを選択するには、**[Choose storage accounts (ストレージ アカウントの選択)]** を選択します。 各リージョンに対してストレージ アカウントを選ばないと、ストレージ アカウントが自動的に作成されて、securitydata リソース グループに追加されます。

> [!NOTE]
> セキュリティ ポリシーは Azure サブスクリプション レベルとリソース グループ レベルで設定可能ですが、ストレージ アカウントのリージョンの選択はサブスクリプション レベルでのみ行うことができます。
>
>

Azure Storage とストレージ アカウントの詳細については、「[Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)」と「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。

## <a name="using-azure-security-center"></a>Azure セキュリティ センターの使用
### <a name="what-is-a-security-policy"></a>セキュリティ ポリシーとは何ですか。
セキュリティ ポリシーは、指定されたサブスクリプションまたはリソース グループ内のリソースに推奨されるコントロールのセットを定義します。 Azure Security Center では、セキュリティに関する会社の要件、および各サブスクリプションでのアプリケーションのタイプやデータの機密度に合わせて、Azure サブスクリプションとリソース グループのポリシーを定義できます。

たとえば、開発またはテストに使用されるリソースには、実稼働アプリケーションで使われるものと異なるセキュリティ要件があります。 同様に、PII (個人情報) のような規制されたデータを持つアプリケーションには、より高いレベルのセキュリティが必要です。 Azure Security Center で有効になっているセキュリティ ポリシーが、セキュリティに関する推奨事項と監視を促進します。 セキュリティ ポリシーの詳細については、「 [Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」を参照してください。

> [!NOTE]
> サブスクリプション レベルのポリシーとリソース グループ レベルのポリシーとの間に競合がある場合は、リソース グループ レベルのポリシーが優先されます。
>
>

### <a name="who-can-modify-a-security-policy"></a>セキュリティ ポリシーを変更できるのは誰ですか。
セキュリティ ポリシーは、サブスクリプションまたはリソース グループごとに構成されます。 サブスクリプション レベルまたはリソース グループ レベルのセキュリティ ポリシーを変更するには、サブスクリプションの所有者または共同作成者であることが必要です。

セキュリティ ポリシーを構成する方法については、「 [Azure セキュリティ センターでのセキュリティ ポリシーの設定](security-center-policies.md)」を参照してください。

### <a name="what-is-a-security-recommendation"></a>セキュリティに関する推奨事項とは
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性が識別されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。 次に例をいくつか示します。

* 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする
* 仮想マシンへのトラフィックを制御するために [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) とルールを構成する
* Web アプリケーションを対象とする攻撃から保護するための Web アプリケーション ファイアウォールをプロビジョニングする
* 不足しているシステムの更新をデプロイする
* 推奨基準と一致しない OS 構成に対処する

ここにはセキュリティ ポリシーで有効な推奨事項のみが表示されています。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Azure リソースの現在のセキュリティ状態を確認する方法を教えてください。
**[セキュリティ センター]** ブレードの **[リソース ヘルス]** タイルには、ご使用の環境の全体的なセキュリティ対策が、仮想マシン、Web アプリケーション、およびその他のリソースごとに示されます。 各リソースには、潜在的なセキュリティの脆弱性が確認されたことを示すインジケーターがあります。 [リソース ヘルス] タイルをクリックすると、リソースが表示され、注意が必要な場所または問題が存在する可能性のある場所が識別されます。

### <a name="what-triggers-a-security-alert"></a>セキュリティの警告をトリガーするものは何ですか。
Azure Security Center は、Azure のリソース、ネットワーク、パートナー ソリューション (マルウェア対策やファイアウォールなど) から、自動的にログ データを収集して分析し、結合します。 脅威が検出されると、セキュリティの警告が作成されます。 例には次の検出が含まれます。

* 既知の悪意のある IP アドレスと通信する、セキュリティ侵害された仮想マシン
* Windows エラー報告を使用して検出された高度なマルウェア
* 仮想マシンに対するブルート フォース攻撃
* マルウェア対策や Web アプリケーション ファイアウォールなどのセキュリティ ソリューションの統合パートナーからのセキュリティの警告

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft Security Response Center と Azure Security Center によって検出され、警告される脅威の違いは何ですか。
Microsoft Security Response Center (MSRC) では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者から脅威インテリジェンスと不正使用の報告を受け取ります。 不正利用者や許可されていない利用者が顧客データにアクセスしたことや、顧客による Azure の利用方法が利用規約の条件に従っていないことを MSRC が検出すると、セキュリティ インシデント マネージャーが顧客に通知します。 通常、通知は、Azure Security Center で指定されたセキュリティ担当者または Azure サブスクリプション所有者 (セキュリティ担当者が指定されていない場合) に電子メールで送られます。

Security Center は、顧客の Azure 環境を継続的に監視し、分析を適用して悪意のある可能性があるさまざまなアクティビティを自動的に検出する Azure サービスです。 検出されたアクティビティは、Security Center のダッシュボードにセキュリティの警告として表示されます。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure Security Center の監視対象になる Azure リソースは何ですか。
Azure Security Center は、次の Azure リソースを監視します。

* 仮想マシン (VM) ( [Cloud Services](../cloud-services/cloud-services-choose-me.md)を含む)
* Azure 仮想ネットワーク
* Azure SQL サービス
* Azure サブスクリプションに統合済みのパートナー ソリューション (VM 上および [App Service](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>[Virtual Machines]
### <a name="what-types-of-virtual-machines-are-supported"></a>サポートされる仮想マシンのタイプは何ですか。
セキュリティ正常性の監視と推奨事項は、 [クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)のどちらで作成された仮想マシン (VM) でも利用できます。

サポートされる Windows VM:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

サポートされる Linux VM:

* Ubuntu バージョン 12.04、14.04、16.04、16.10
* Debian バージョン 7、8
* CentOS バージョン 6.\*、7.*
* Red Hat Enterprise Linux (RHEL) バージョン 6.\*、7.*
* SUSE Linux Enterprise Server (SLES) バージョン 11 SP4 以降、12.*
* Oracle Linux バージョン 6\*、7.*

クラウド サービスで実行されている VM もサポートされます。 監視されるのは、運用スロットで実行されているクラウド サービスの Web ロールと worker ロールだけです。 クラウド サービスの詳細については、 [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure VM で実行しているマルウェア対策ソリューションが、Azure Security Center で認識されないのはなぜですか。
Azure Security Center で可視化できるのは、Azure 拡張機能によりインストールされたマルウェア対策ソリューションのみです。 たとえば、Azure Security Center では、お客様のイメージにプレインストールされていたマルウェア対策ソリューションや、お客様が独自のプロセス (構成管理システムなど) で仮想マシンにインストールしたマルウェア対策ソリューションは検出できません。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM で "スキャン データがありません" というメッセージが表示されるのはなぜですか。
Azure Security Center でデータ収集が有効になると、スキャン データが取り込まれるまでにある程度の時間 (1 時間未満) がかかる場合があります。 停止状態の VM のスキャン データは取り込まれません。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM Agent is Missing? (VM エージェントが見つかりません)" というメッセージが表示されるのはなぜですか。
データ収集を有効にするには、VM エージェントが VM にインストールされている必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 他の VM への VM エージェントのインストール方法については、 [VM エージェントと拡張機能](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)に関するブログ投稿を参照してください。

