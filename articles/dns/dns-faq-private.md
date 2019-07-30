---
title: Azure プライベート DNS に関する FAQ
description: Azure プライベート DNS に関してよく寄せられる質問
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 480cf22491dbbfcb9fe1961b5c9a7aa6fe12a0cb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274041"
---
# <a name="azure-private-dns-faq"></a>Azure プライベート DNS に関する FAQ

> [!IMPORTANT]
> 現在、Azure プライベート DNS はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS では、プライベート ドメインはサポートされますか。

プライベート ドメインのサポートは、Azure プライベート DNS ゾーン機能を使用してサポートされます。 プライベート DNS ゾーンは、インターネットに接続する Azure DNS ゾーンと同じツールを使用して管理されます。 これらは、指定した仮想ネットワーク内からのみ解決可能です。 詳細については、[概要](private-dns-overview.md)に関するページを参照してください。

Azure での他の内部 DNS オプションの詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones は Azure リージョンを超えて機能しますか。

はい。 Azure リージョンを超えた仮想ネットワーク間での DNS 解決では、Private Zones がサポートされます。 Private Zones は、仮想ネットワークを明示的にピアリングしていない場合でも動作します。 すべての仮想ネットワークは、プライベート ゾーンの解決仮想ネットワークとして指定する必要があります。 TCP/HTTP トラフィックをあるリージョンから別のリージョンにフローするには、お客様による仮想ネットワークのピアリングが必要な場合があります。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>プライベート ゾーンでは、仮想ネットワークからインターネットに接続されている必要はありますか?

いいえ。 プライベート ゾーンは、仮想ネットワークと共に動作します。 お客様は、それらを使用して、仮想ネットワーク内またはそれらの間で、仮想マシンやその他のリソース用のドメインを管理します。 名前解決にインターネットへの接続は必要ありません。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>複数の仮想ネットワークの解決に同じプライベート ゾーンを使用できますか?

はい。 1 つのプライベート ゾーンに最大で 1,000 の仮想ネットワークを関連付けることができます。

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>別のサブスクリプションに属している仮想ネットワークを、リンクされた仮想ネットワークとしてプライベート ゾーンに追加できますか。

はい。 ユーザーが仮想ネットワークとプライベート DNS ゾーンでの書き込み操作のアクセス許可を持っている必要があります。 書き込みアクセス許可は、複数の RBAC ロールに付与することができます。 たとえば、従来のネットワーク共同作成者の RBAC ロールには仮想ネットワークに対する書き込みアクセス許可が付与されています。 RBAC ロールについて詳しくは、[ロールベースのアクセス制御](../role-based-access-control/overview.md)に関するページをご覧ください。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>プライベート ゾーンに自動的に登録された仮想マシンの DNS レコードは、仮想マシンを削除すると自動的に削除されますか。

はい。 リンクされた仮想ネットワークで自動登録が有効な場合、そのネットワーク内にある仮想マシンを削除すると、登録されているレコードは自動的に削除されます。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>リンクされた仮想ネットワークからプライベート ゾーンに自動的に登録された仮想マシンのレコードは手動で削除できますか。

はい。 自動的に登録された DNS レコードを、そのゾーンに手動で作成した DNS レコードで上書きできます。 次の質問と回答がこのトピックに対応しています。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>リンクされた仮想ネットワーク内の自動的に登録された既存の仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとするとどうなりますか。

リンクされた仮想ネットワーク内の自動的に登録された既存の仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとしたとします。 これを行った場合、自動的に登録された仮想マシン レコードが新しい DNS レコードで上書きされます。 この手動で作成した DNS レコードを再びゾーンから削除しようとすると、削除は成功します。 仮想マシンが存在し、プライベート IP がアタッチされている限り、自動登録が再度発生します。 DNS レコードがゾーン内に自動的に再作成されます。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>リンクされた仮想ネットワークをプライベート ゾーンからリンク解除するとどうなりますか。 その仮想ネットワークから自動的に登録された仮想マシンのレコードもそのゾーンから削除されますか。

はい。 リンクされた仮想ネットワークをプライベート ゾーンからリンク解除するには、DNS ゾーンを更新して、関連付けられている仮想ネットワーク リンクを削除します。 このプロセスの中で、自動的に登録された仮想マシンのレコードがゾーンから削除されます。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>プライベート ゾーンにリンクされている、リンクされた仮想ネットワークを削除するとどうなりますか。 プライベート ゾーンを手動で更新して、リンクされた仮想ネットワークとしての仮想ネットワークをそのゾーンからリンク解除する必要がありますか。

はい。 プライベート ゾーンとのリンクを解除せずに、リンクされた仮想ネットワークを削除しても、削除操作は成功します。 ただし、仮想ネットワークがプライベート ゾーンにリンクされている場合、そのリンクが自動的に解除されることはありません。 仮想ネットワークのリンクはプライベート ゾーンから手動で解除する必要があります。 この理由から、仮想ネットワークを削除する前に、まずプライベート ゾーンから仮想ネットワークのリンクを解除することをお勧めします。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>既定の FQDN (internal.cloudapp.net) を使用している DNS 解決は、プライベート ゾーン (例: private.contoso.com) が仮想ネットワークにリンクされている場合でも機能しますか。

はい。 Private Zones によって、既定の DNS 解決が Azure 提供の internal.cloudapp.net ゾーンに置き換えられることはありません。 これは、追加の機能や拡張として提供されています。 Azure 提供の internal.cloudapp.net に依存している場合でも、独自のプライベート ゾーンに依存している場合でも、解決するゾーンに対して FQDN を使用できます。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>リンクされた仮想ネットワーク内の仮想マシンの DNS サフィックスは、そのプライベート ゾーンの DNS サフィックスに変更されますか。

いいえ。 リンクされた仮想ネットワーク内の仮想マシン上の DNS サフィックスは、Azure 提供の既定のサフィックス ("*.internal.cloudapp.net") のままです。 仮想マシン上のこの DNS サフィックスをプライベート ゾーンの DNS サフィックスに手動で変更できます。

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Azure プライベート DNS の使用制限とは何ですか。

Azure プライベート DNS を使用する際は、次の制限が既定で適用されます。

| Resource | 既定の制限 |
| --- | --- |
|サブスクリプションあたりのプライベート DNS ゾーン数|1000|
|プライベート DNS ゾーンあたりのレコード セット数|25,000|
|レコード セットあたりのレコード数|20|
|プライベート DNS ゾーンあたりの仮想ネットワーク リンク数|1000|
|自動登録が有効なプライベート DNS ゾーンあたりの仮想ネットワーク リンク数|100|
|自動登録が有効な状態で仮想ネットワークがリンクできるプライベート DNS ゾーンの数|1|
|仮想ネットワークがリンクできるプライベート DNS ゾーンの数|1000|

## <a name="is-there-portal-support-for-private-zones"></a>プライベート ゾーン用のポータルのサポートはありますか。

はい。API、PowerShell、CLI、および SDK で作成されたプライベート ゾーンは Azure portal に表示されます。

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>既存のプライベート DNS ゾーンが新しいポータル エクスペリエンスに表示されないのはなぜですか。

Preview Refresh リリースの一環として、Microsoft は、プライベートDNS ゾーン用の新しいリソース モデルを公開しました。 既存のプライベート DNS ゾーンは、新しいポータル エクスペリエンスで表示可能になる前に、新しいリソース モデルに移行する必要があります。 新しいリソース モデルへの移行方法については、後述する指示を参照してください。

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>どのようにして既存のプライベート DNS ゾーンを新しいモデルに移行しますか。
できるだけ早く新しいリソース モデルに移行することを強くお勧めします。 従来のリソース モデルは今後、サポートはされますが、追加機能開発のベースになることはありません。 新しいリソース モデルを優先するために、将来的には廃止される予定です。 既存のプライベート DNS ゾーンを新しいリソース モデルに移行する方法については、[Azure DNS プライベート ゾーンの移行ガイド](private-dns-migration-guide.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure プライベート DNS の詳細](private-dns-overview.md)