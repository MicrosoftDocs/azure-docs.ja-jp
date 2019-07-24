---
title: Azure Security Center の設定 | Microsoft Docs
description: Azure Security Center の設定を構成します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666496"
---
# <a name="security-center-settings"></a>Security Center の設定
この記事では、Security Center の設定の概要について説明します。

次の設定は、セキュリティ ポリシーの下にあります。

- **データ取集**: エージェントのプロビジョニングと[データ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)の設定を決定します。
- **セキュリティ ポリシー**:Security Center がどのコントロールを監視し、推奨するかを決定します。 Security Center.の[セキュリティ ポリシー](tutorial-security-policy.md)を編集できます。 [Azure Policy](tutorial-security-policy.md) を使用し、新しい定義の作成、追加のポリシーの定義、管理グループ間でのポリシーの割り当てを行うこともできます。 
- **電子メール通知**: セキュリティ連絡先と[電子メール通知](security-center-provide-security-contact-details.md)の設定を指定します。
- **価格レベル**: Free または Standard [価格選択](security-center-pricing.md)を定義します。 選択したレベルでは、スコープ内のリソースに使用できる Security Center の機能を決定します。 サブスクリプションおよびワークスペースのレベルを指定できます。

> [!NOTE]
> これらはすべてサブスクリプションごとに設定できます。 ワークスペースについては、データ収集と価格レベルのみを設定できます。
>


## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。
Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、リソースが属するサブスクリプションについて、"*所有者*"、"*共同作成者*"、"*閲覧者*" のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- **セキュリティ閲覧者**: Security Center に対する閲覧権限を持ちます。推奨事項、アラート、ポリシー、および正常性を確認できますが、変更を加えることはできません。
- **セキュリティ管理者**:*セキュリティ閲覧者*と同じ閲覧権限を持ちますが、セキュリティ ポリシーの更新と推奨事項とアラートの解除を実行することもできます。


## <a name="next-steps"></a>次の手順
この記事では、Azure Security Center のセキュリティ ポリシーについて説明しました。 Azure Security Center の詳細については、次の記事をご覧ください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md):Azure サブスクリプションのセキュリティ ポリシーの構成方法について説明します。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md):Security Center の推奨事項を Azure リソースの保護に活かす方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md): Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のデータ セキュリティ](security-center-data-security.md): Security Center でデータがどのように管理および保護されているかを説明しています。
* [Azure Security Center に関する FAQ](security-center-faq.md): このサービスの使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティに関する最新のニュースと情報を入手できます。
