---
title: Azure ゲスト OS のサポートと提供終了のポリシーのガイド | Microsoft Docs
description: Cloud Services で使用される Azure ゲスト OS について Microsoft がサポートする内容について説明します。
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68945444"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure ゲスト OS のサポートと提供終了のポリシー
このページの情報は、Cloud Services の worker ロールと Web ロール (PaaS) Azure ゲスト オペレーティング システム ([ゲスト OS](cloud-services-guestos-update-matrix.md)) に関連しています。 このページの情報は、Virtual Machines (IaaS) には適用されません。

Microsoft では、 [ゲスト OS のサポート ポリシー](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq)を公開しています。 現在表示されているページでは、ポリシーの実装方法について説明します。

ポリシーは次のとおりです。

1. Microsoft では、**少なくとも最新の 2 つのゲスト OS ファミリ** をサポートします。 ファミリが提供終了になるときには、正式な提供終了日から 12 か月の猶予期間が設けられます。その間に、サポートされている新しいゲスト OS ファミリに更新する必要があります。
2. Microsoft では、 **サポートされているゲスト OS ファミリの少なくとも最新の 2 つのバージョン** をサポートします。
3. Microsoft では、**Azure SDK の少なくとも最新の 2 つのバージョンをサポートします**。 SDK のバージョンが提供終了になるときには、正式な提供終了日から 12 か月の猶予期間が設けられます。その間に、より新しいバージョンに更新する必要があります。

ときには 3 つ以上のファミリやリリースがサポートされる場合もあります。 ゲスト OS のサポートに関する公式の情報については、 [Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)を参照してください。

## <a name="when-a-guest-os-version-is-retired"></a>ゲストOSバージョンが削除されている場合
新しいゲスト OS **バージョン** は、最新の MSRC 更新プログラムを組み込むようにほぼ毎月導入されます。 月単位の定期的な更新により、ゲスト OS バージョンは通常、リリースから約 60 日後に無効になります。 この結果、各ファミリで少なくとも 2 つのバージョンのゲスト OS が使用できる状態が維持されます。

### <a name="process-during-a-guest-os-family-retirement"></a>ゲスト OS ファミリの提供終了処理中のプロセス
提供終了が公表されると、古いファミリがサービスから公式に削除される前に、12 か月間の "移行" の期間が設けられます。 この移行期間は、Microsoft の判断で延長される場合があります。 更新は、 [Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)に投稿されます。

移行期間が 6 か月目になると、段階的な提供終了のプロセスが開始されます。 この期間中、以下のような処理が行われます。

1. Microsoft から、提供終了に関する通知が行われます。
2. 提供終了になったゲスト OS ファミリが Azure SDK の新しいバージョンでサポートされなくなります。
3. 提供終了になったファミリで Cloud Services の新しいデプロイおよび再デプロイが許可されなくなります。

Microsoft は、"有効期限" と呼ばれる移行期間の最終日まで、最新の MSRC 更新プログラムが組み込まれた新しいゲスト OS バージョンを導入し続けます。 最終日の時点で、まだ実行されている Cloud Services は、Azure SLA ではサポートされなくなります。 Microsoft の判断により、その日以降にサービスを強制的にアップグレード、削除、または停止する場合があります。

### <a name="process-during-a-guest-os-version-retirement"></a>ゲスト OS バージョンの提供終了処理中のプロセス
ゲスト OS が自動的に更新されるように設定している場合は、ゲスト OS バージョンについて考慮する必要はありません。 常に最新のゲスト OS バージョンを使用できます。

ゲスト OS バージョンは毎月リリースされます。 定期的なリリースの頻度により、各バージョンには固定された有効期間があります。

バージョンは、有効期間の 60 日目に "*無効*" になります。 "無効" になるとは、そのバージョンがポータルから削除されることを意味します。 また、CSCFG 構成ファイルから設定することもできなくなります。 既存のデプロイは実行されたままになりますが、 新しいデプロイや、既存のデプロイに対するコードや構成の更新は許可されなくなります。

ゲスト OS バージョンは "無効" になってからしばらくすると "有効期限切れ" となります。まだその期限切れのバージョンを実行しているインストールはセキュリティと脆弱性の問題に晒されます。 一般に、有効期限切れにする操作は一括して行われるため、無効になったときから有効期限切れまでの期間は変動する場合があります。

ゲスト OS を手動で更新するようにサービスを構成したお客様は、そのサービスが、サポートされているゲスト OS で実行されていることを確認する必要があります。 ゲスト OS を自動的に更新するようにサービスを構成している場合は、基になるプラットフォームでコンプライアンスが確保され、最新のゲスト OS にアップグレードされます。

顧客が容易に移行できるように、これらの期間は Microsoft の判断で延長される場合があります。 変更は、 [Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)に反映されます。

### <a name="notifications-during-retirement"></a>提供終了処理中の通知
* **ファミリの提供終了** <br>ブログ投稿とポータルで通知されます。 提供終了になったゲスト OS ファミリをまだ使用しているお客様に対しては、割り当てられたサービス管理者への直接の連絡 (メール、ポータル メッセージ、電話) による通知が行われます。 すべての変更は、[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)に投稿されます。
* **バージョンの提供終了** <br>リリース、無効化、期限切れなど、すべての変更とその変更日は、「[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)」に投稿される予定です。 無効になったゲスト OS バージョンまたはファミリで実行中のデプロイがある場合、サービス管理者はメールを受け取ります。 これらのメールのタイミングは変わる場合があります。 通常は、無効になる日の少なくとも 1 か月前ですが、このタイミングは正式な SLA ではありません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
**移行の影響を軽減するにはどうすればよいですか。**

最新のゲスト OS ファミリを使用して Cloud Services を設計することをお勧めします。

1. 新しいファミリへの移行の計画を早めに開始します。
2. 一時的なテスト デプロイをセットアップして、クラウド サービスを新しいファミリでテストします。
3. ゲスト OS バージョン ( **.cscfg** ファイルの osVersion=*) を [Automatic](cloud-services-model-and-package.md#cscfg) に設定して、新しいゲスト OS バージョンへの移行が自動的に行われるようにします。

**Web アプリケーションで OS との緊密な統合が必要な場合はどうすればよいでしょうか。**

Web アプリケーションのアーキテクチャが、オペレーティング システムの基になる機能に依存する場合は、[スタートアップ タスク](cloud-services-startup-tasks.md)のような機能や、他の拡張メカニズムなど、プラットフォームでサポートされる機能を使用します。 または、[Azure Virtual Machines](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS: Infrastructure as a Service) を使用することもできます。この場合、ユーザーが基になるオペレーティング システムの管理を担当します。

## <a name="next-steps"></a>次のステップ
最新の [ゲスト OS リリース](cloud-services-guestos-update-matrix.md)を確認します。
