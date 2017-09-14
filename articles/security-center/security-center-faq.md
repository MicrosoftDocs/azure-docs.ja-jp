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
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 506c23179d09e2e22065c8ba2bc85a341bb0ea09
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure セキュリティ センターのよく寄せられる質問 (FAQ)
この FAQ は、Azure Security Center について寄せられる質問とその回答です。Azure Security Center は、Microsoft Azure リソースのセキュリティの視覚化と制御の向上により、脅威を回避、検出、対応するのに役立つサービスです。

> [!NOTE]
> 2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>
>

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-security-center"></a>Azure セキュリティ センターとは
Azure セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

### <a name="how-do-i-get-azure-security-center"></a>Azure セキュリティ センターはどうしたら取得できますか。
Azure Security Center は、Microsoft Azure サブスクリプションで有効化し、[Azure Portal](https://azure.microsoft.com/features/azure-portal/) からアクセスします  ([ポータルにサインイン](https://portal.azure.com)して、**[参照]** を選択し、**[セキュリティ センター]** までスクロールします)。  

## <a name="billing"></a>課金
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure セキュリティ センターの課金のしくみを教えてください。
Azure Security Center は 2 つのレベルで提供されます。

**Free レベル**では、Azure リソースのセキュリティ状態の表示、基本的なセキュリティ ポリシー、セキュリティに関する推奨事項、パートナーのセキュリティ製品やサービスとの統合が提供されます。

**Standard レベル**には、脅威インテリジェンス、行動分析、異常検出、セキュリティ インシデント、脅威評価レポートなどの高度な脅威検出機能が追加されます。 Standard レベルは、最初の 60 日間は無料です。 60 日目以降もこのサービスを引き続き使用することを選択した場合は、サービスへの課金が自動的に開始されます。  アップグレードを行うには、 [セキュリティ ポリシー](security-center-policies.md#set-security-policies)で価格レベルを選択してください。 詳細については、[Security Center の価格](security-center-pricing.md)に関する記事を参照してください。

## <a name="permissions"></a>アクセス許可
Azure Security Center では、[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) が使用されています。RBAC が提供する[組み込みのロール](../active-directory/role-based-access-built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。

## <a name="data-collection"></a>データ収集
Security Center では、仮想マシンからデータを収集して、そのセキュリティ状態の評価、セキュリティ推奨事項の提供、脅威についての警告を行います。 最初にセキュリティ センターにアクセスするときは、サブスクリプション内のすべての仮想マシンに対してデータ収集が有効になっています。 Security Center のポリシーでデータ収集を有効にすることもできます。

### <a name="how-do-i-disable-data-collection"></a>データ収集を無効にするにはどうしたらよいですか。
Azure Security Center の Free レベルを使用している場合は、仮想マシンからのデータ収集をいつでも無効にすることができます。 データ収集は、Standard レベルのサブスクリプションでは必須の機能です。 サブスクリプションのデータ収集は、セキュリティ ポリシーで無効にできます。 ([Azure Portal にサインイン](https://portal.azure.com)して、**[参照]**、**[セキュリティ センター]**、**[ポリシー]** の順に選択します)。サブスクリプションを選択すると、新しいブレードが開き、**データ収集**をオフにするオプションが表示されます。

### <a name="how-do-i-enable-data-collection"></a>データ収集を有効にするにはどうしたらよいですか。
Azure サブスクリプションのデータ収集の有効化は、セキュリティ ポリシーで行うことができます。 データ収集を有効にするには、 [Azure Portal にサインイン](https://portal.azure.com)して、**[参照]**、**[セキュリティ センター]**、**[ポリシー]** の順に選択します。 **[データ収集]** を **[オン]** に設定します。

### <a name="what-happens-when-data-collection-is-enabled"></a>データ収集を有効にするとどうなりますか。
データ収集を有効にすると、サブスクリプションにデプロイされる既存の仮想マシンと新しくサポートされる仮想マシンすべてで Microsoft Monitoring Agent が自動的にプロビジョニングされます。

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent はサーバーのパフォーマンスに影響しますか。
エージェントは、システム リソースのわずかな量しか消費しないため、パフォーマンスにほとんど影響しません。 パフォーマンスの影響と、エージェントおよび拡張機能の詳細については、[計画および運用ガイド](security-center-planning-and-operations-guide.md#data-collection-and-storage)を参照してください。

### <a name="where-is-my-data-stored"></a>データはどこに格納されますか。
このエージェントから収集されたデータは、サブスクリプションに関連付けられている既存の Log Analytics ワークスペースまたは新規のワークスペースのいずれかに格納されます。 詳細については、[データ セキュリティ](security-center-data-security.md)に関するページを参照してください。

## <a name="using-azure-security-center"></a>Azure セキュリティ センターの使用
### <a name="what-is-a-security-policy"></a>セキュリティ ポリシーとは何ですか。
セキュリティ ポリシーは、指定されたサブスクリプション内のリソースに対して推奨されるコントロールのセットを定義します。 Azure Security Center では、セキュリティに関する会社の要件、各サブスクリプションでのアプリケーションの種類やデータの機密度に合わせて Azure サブスクリプションのポリシーを定義します。

Azure Security Center で有効になっているセキュリティ ポリシーが、セキュリティに関する推奨事項と監視を促進します。 セキュリティ ポリシーの詳細については、「 [Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」を参照してください。

### <a name="who-can-modify-a-security-policy"></a>セキュリティ ポリシーを変更できるのは誰ですか。
セキュリティ ポリシーを変更するには、セキュリティ管理者であるか、そのサブスクリプションの所有者または共同作成者である必要があります。

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
**[Security Center Overview]\(Security Center の概要\)** ブレードでは、お使いの環境の全体的なセキュリティ体制が [コンピューティング]、[ネットワーク]、[ストレージおよびデータ]、[アプリケーション] に分けて詳しく示されます。 リソースの種類にはそれぞれ、潜在的なセキュリティの脆弱性が確認されたかどうかを示すインジケーターがあります。 各タイルをクリックすると、Security Center によって特定されたセキュリティの問題の一覧が、サブスクリプション内のリソースのインベントリと共に表示されます。

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
* Azure ストレージ アカウント
* ([App Service 環境](../app-service/app-service-app-service-environments-readme.md)にある) Azure Web アプリ
* Azure サブスクリプションに統合済みのパートナー ソリューション (VM 上および [App Service](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>[Virtual Machines]
### <a name="what-types-of-virtual-machines-are-supported"></a>サポートされる仮想マシンのタイプは何ですか。
監視と推奨事項は、[クラシック デプロイメント モデルと Resource Manager デプロイメント モデル](../azure-classic-rm.md)のどちらで作成された仮想マシン (VM) でも利用できます。

サポートされるプラットフォームの一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」を参照してください。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure VM で実行しているマルウェア対策ソリューションが、Azure Security Center で認識されないのはなぜですか。
Azure Security Center では、Azure 拡張機能によりインストールされたマルウェア対策ソリューションを可視化できます。 たとえば、Azure Security Center では、お客様のイメージにプレインストールされていたマルウェア対策ソリューションや、お客様が独自のプロセス (構成管理システムなど) で仮想マシンにインストールしたマルウェア対策ソリューションは検出できません。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM で "スキャン データがありません" というメッセージが表示されるのはなぜですか。
このメッセージが表示されるのは、VM のスキャン データがない場合です。 Azure Security Center でデータ収集が有効になると、スキャン データが取り込まれるまでにある程度の時間 (1 時間未満) がかかる場合があります。 スキャン データの初回作成以降でも、スキャン データが存在しなかったり、最近のスキャン データがなかったりする場合にはこのメッセージが表示される可能性があります。 停止状態の VM のスキャン データは取り込まれません。 スキャン データが最近取り込まれていない場合 (Windows エージェントの保持ポリシーに従い、既定値は 30 日間) にも、このメッセージが表示されることがあります。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM Agent is Missing? (VM エージェントが見つかりません)" というメッセージが表示されるのはなぜですか。
データ収集を有効にするには、VM エージェントが VM にインストールされている必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 他の VM への VM エージェントのインストール方法については、 [VM エージェントと拡張機能](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)に関するブログ投稿を参照してください。

