---
title: Azure でのアウトバウンド SMTP 接続のトラブルシューティング | Microsoft Docs
description: 推奨されるメール送信方法と Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング方法について説明します。
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: c28790b2ef423a3d0f996d7c6030b04198756eb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607613"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング

2017 年 11 月 15 日以降、仮想マシン (VM) から外部ドメイン (outlook.com や gmail.com など) に直接送信されるアウトバウンド メール メッセージは、Azure の特定の種類のサブスクリプションでのみ使用できます。 TCP ポート 25 を使用するアウトバウンド SMTP 接続はブロックされました (ポート 25 は主に、認証されていないメール配信で使用されます)。

この動作の変更は、2017 年 11 月 15 日以降に作成されたサブスクリプションとデプロイにのみ適用されます。

## <a name="recommended-method-of-sending-email"></a>メール送信の推奨される方法

Azure VM から、または Azure App Service からメールを送信する場合は、認証済み SMTP リレー サービスを使用することをお勧めします。 (これらのリレー サービスは、通常は TCP ポート 587 を介して接続されますが、他のポートもサポートされます)。これらのサービスは、IP またはドメインの評価を維持して、サード パーティのメール プロバイダーでメッセージが拒否される可能性を最小限に抑えるために使用されます。 [SendGrid](https://sendgrid.com/partners/azure/) は、このような SMTP リレー サービスの 1 つですが、他にもあります。 オンプレミスで実行される安全な SMTP リレー サービスを使用できる場合もあります。

サブスクリプションの種類に関係なく、Azure ではこれらのメール配信サービスの使用は制限されません。

## <a name="enterprise-agreement"></a>Enterprise Agreement

Enterprise Agreement の Azure ユーザーの場合、認証済みリレーを使用せずにメールを送信する技術的能力は変わりません。 新規と既存の両方の Enterprise Agreement ユーザーは、Azure VM から外部のメール プロバイダーに直接アウトバウンド メール配信を試行することができます。Azure プラットフォームによる制限はありません。 メール プロバイダーが特定のユーザーからの受信メールを受け入れるという保証はありません。 ただし、Azure プラットフォームによって Enterprise Agreement サブスクリプション内の VM の配信試行がブロックされることはありません。 メール プロバイダーと直接協力して、特定のプロバイダーに関連するメッセージ配信やスパム フィルター処理の問題を修正する必要があります。

## <a name="pay-as-you-go"></a>従量課金制

2017 年 11 月 15 日より前に従量課金制のサブスクリプションにサインアップした場合、アウトバウンド メール配信を試行するための技術的能力は変わりません。 これらのサブスクリプション内の Azure VM から外部のメール プロバイダーへの直接のアウトバウンド メール配信は、引き続き試行できます。Azure プラットフォームによる制限はありません。 繰り返しますが、メール プロバイダーが特定のユーザーからの受信メールを受け入れるという保証はありません。 ユーザーは、メール プロバイダーと直接協力して、特定のプロバイダーに関連するメッセージ配信やスパム フィルター処理の問題を修正する必要があります。

2017 年 11 月 15 日以降に作成された従量課金制サブスクリプションの場合は、技術的な制限により、サブスクリプション内の VM から直接送信されるメールがブロックされます。 (認証済み SMTP リレーを使用せずに) Azure VM から外部のメール プロバイダーにメールを直接送信できるようにする必要があり、支払い履歴のある有効なアカウントをお持ちの場合は、制限を解除するようにリクエストできます。 これは、Azure portal の Azure 仮想ネットワーク リソース用の **[診断と解決]** ブレードの **[接続]** セクションで行うことができます。 リクエストが受け入れられると、サブスクリプションが有効になるか、次の手順に関する指示が表示されます。 

従量課金制サブスクリプションが除外され、VM が Azure portal で一旦停止された後に再開されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 除外されるのは、リクエストされたサブスクリプションのみであり、インターネットに直接ルーティングされる VM トラフィックのみです。

> [!NOTE]
> Microsoft は、サービス使用条件の違反が発生していると判断された場合に、これらの免除を取り消す権利を留保します。

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN、Azure Pass、Azure イン オープン プラン、Education、Azure for Students、Visual Studio、無料試用版

2017 年 11 月 15 日以降に、次のいずれかの種類のサブスクリプションを作成した場合は、技術的な制限により、サブスクリプション内の VM からメール プロバイダーに直接送信されるメールがブロックされます。
- MSDN
- Azure Pass
- Azure イン オープン プラン
- Education
- Microsoft Azure for Students
- 無料試用版
- Visual Studio サブスクリプション  

制限は不正使用を防ぐために実施されます。 これらの制限を解除するリクエストは許可されません。

これらの種類のサブスクリプションを使用する場合は、この記事の前半で説明されているように SMTP リレー サービスを使用するか、またはサブスクリプションの種類を変更することをお勧めします。

## <a name="cloud-solution-provider"></a>クラウド ソリューション プロバイダー

クラウド ソリューション プロバイダーを介して Azure リソースを使用する場合は、Azure portal の仮想ネットワーク リソース用の **[診断と解決]** ペインの **[接続]** セクションで制限を解除するようにリクエストできます。 リクエストが受け入れられると、サブスクリプションが有効になるか、次の手順に関する指示が表示されます。

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network、BizSpark Plus、または Azure スポンサー プラン

2017 年 11 月 15 日以降に作成された次の種類のサブスクリプションでは、技術的な制限により、サブスクリプション内の VM から直接送信されるメールがブロックされます。

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure スポンサー プラン

(認証済み SMTP リレーを使用せずに) Azure VM から外部のメール プロバイダーに直接メールを送信できるようにする必要がある場合は、次の問題の種類を使用してサポート ケースを開くことでリクエストできます。 **[Technical]\(技術\)**  >  **[仮想ネットワーク]**  >  **[接続]**  >  **[Cannot send email (SMTP/Port 25)]\(メールを送信できない (SMTP/ポート 25)\)** 。 現在のデプロイで、認証済みリレーを使用する代わりに、メール プロバイダーに直接メールを送信する必要がある理由を詳しく記入してください。 リクエストは Microsoft の裁量でレビューおよび承認されます。 リクエストは、不正行為に関する追加確認が完了した後にのみ許可されます。 

サブスクリプションが除外され、VM が Azure portal で一旦停止された後に再開されると、それ以降はそのサブスクリプション内のすべての VM が除外されます。 除外されるのは、リクエストされたサブスクリプションのみであり、インターネットに直接ルーティングされる VM トラフィックのみです。

## <a name="changing-subscription-type"></a>サブスクリプションの種類の変更

サブスクリプションの種類を変更した場合、あるいはクラウド ソリューション プロバイダーまたは従量課金制のサブスクリプションが承認された場合は、新しいポリシーを有効にするために、VM を停止し、割り当てを解除してから、再起動する必要があります。 同様に、既定で許可されているサブスクリプションの種類がある場合に、許可されていないサブスクリプションの種類に変更すると、デプロイの変更が原因で、ポート 25 がブロックされる可能性があります。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 この問題の種類を使用します。 **[Technical]\(技術\)**  >  **[仮想ネットワーク]**  >  **[接続]**  >  **[Cannot send email (SMTP/Port 25)]\(メールを送信できない (SMTP/ポート 25)\)** 。
