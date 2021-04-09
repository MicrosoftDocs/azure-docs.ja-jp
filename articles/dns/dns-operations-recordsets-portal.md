---
title: Azure DNS で DNS レコード セットとレコードを管理する
description: Azure DNS には、ドメインをホストしている場合に、DNS レコード セットとレコードを管理する機能が用意されています。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 10/6/2018
ms.author: rohink
ms.openlocfilehash: 22ceba69ecf865d906021068a39a9d273b842ca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965733"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Azure ポータルを使用した DNS レコードとレコード セットの管理

この記事では、Azure ポータルを使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。 レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。 詳細については、「 [Azure ポータルを使用した DNS レコード セットとレコードの作成](./dns-getstarted-portal.md)」を参照してください。

## <a name="create-a-new-record-set-and-record"></a>新しいレコード セットとレコードを作成する

Azure ポータルでレコード セットを作成する方法については、 [Azure ポータルを使用した DNS レコードの作成に関するページ](./dns-getstarted-portal.md)を参照してください。

## <a name="view-a-record-set"></a>レコード セットを表示する

1. Azure ポータルで、 **DNS ゾーン** ブレードに移動します。
2. レコード セットを検索して選択します。 レコード セットのプロパティが表示されます。

    ![レコード セットの検索](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>レコード セットに新しいレコードを追加する

すべてのレコード セットに対して、最大 20 個のレコードを追加できます。 レコード セットには、同一の 2 つのレコードを含めることはできません。 空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。 レコード セットの種類が CNAME の場合、そこに含めることができるレコードは 1 つまでです。

1. DNS ゾーンの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを追加するレコード セットをクリックします。

    ![レコード セットの選択](./media/dns-operations-recordsets-portal/selectset500.png)

2. 各フィールドに入力して、レコード セットのプロパティを指定します。

    ![レコードの追加](./media/dns-operations-recordsets-portal/addrecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。 次に、ブレードを閉じます。
4. 画面の隅に、レコードが保存されていることを示すメッセージが表示されます。

    ![レコード セットの保存](./media/dns-operations-recordsets-portal/saving150.png)

レコードが保存されると、 **DNS ゾーン** ブレードの値にはレコードの追加が反映されます。

## <a name="update-a-record"></a>レコードを更新する

既存のレコード セット内のレコードを更新する場合、使用しているレコードの種類によって更新できるフィールドが異なります。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。
2. レコードを変更します。 レコードを変更するときは、レコードで利用可能な設定を変更できます。 下の例では、 **[IP アドレス]** フィールドを選択して、IP アドレスを変更する処理を行っています。

    ![レコードの変更](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。 画面の右上隅に、レコードが保存されたことを示す通知が表示されます。

    ![保存されたレコード セット](./media/dns-operations-recordsets-portal/saved150.png)

レコードが保存されると、 **DNS ゾーン** ブレードのレコード セットの値にはレコードの更新が反映されます。

## <a name="remove-a-record-from-a-record-set"></a>レコード セットからレコードを削除する

Azure ポータルを使用して、レコード セットからレコードを削除することができます。 なお、レコード セットから最後のレコードを削除しても、レコード セットは削除されません。

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、レコードを検索します。
2. 削除するレコードをクリックして、 **[削除]** を選択します。

    ![レコードの削除](./media/dns-operations-recordsets-portal/removerecord500.png)

3. ブレードの上部にある **[保存]** をクリックして設定を保存します。
4. レコードが削除されると、 **DNS ゾーン** ブレードのレコードの値にはレコードの削除が反映されます。

## <a name="delete-a-record-set"></a><a name="delete"></a>レコード セットの削除

1. レコード セットの **[Record set properties (レコード セットのプロパティ)]** ブレードで、**[削除]** をクリックします。

    ![レコード セットの削除](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. レコード セットを削除してもよいかを確認するメッセージが表示されます。
3. 削除するレコード セットの名前が正しいことを確認し、 **[はい]** をクリックします。
4. **DNS ゾーン** のブレードで、レコード セットが表示されなくなっていることを確認します。

## <a name="work-with-ns-and-soa-records"></a>NS レコードと SOA レコードを使用する

自動的に作成される NS レコードと SOA レコードは、他の種類のレコードとは異なる方法で管理されます。

### <a name="modify-soa-records"></a>SOA レコードを変更する

ゾーンの頂点 (名前は "\@") に自動的に作成された SOA レコード セットのレコードを追加または削除することはできません。 ただし、("ホスト" を除く) SOA レコードおよびレコード セットの TTL 内のパラメーターを変更することはできます。

### <a name="modify-ns-records-at-the-zone-apex"></a>ゾーンの頂点にある NS レコードを変更する

ゾーンの頂点にある NS レコード セットは各 DNS ゾーンで自動的に作成されます。 ゾーンに割り当てられている Azure DNS ネーム サーバーの名前が含まれています。

複数の DNS プロバイダーによる共同ホスト ドメインをサポートする目的で、この NS レコード セットにネーム サーバーを追加できます。 このレコード セットの TTL とメタデータを変更することもできます。 ただし、あらかじめ入力されている Azure DNS ネーム サーバーを削除または変更することはできません。

これは、ゾーンの頂点にある NS レコード セットにのみ適用されます。 (子ゾーンの委任に使用される) ゾーンの他の NS レコード セットは制約なしで変更できます。

### <a name="delete-soa-or-ns-record-sets"></a>SOA レコード セットまたは NS レコード セットを削除する

ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "\@") の SOA および NS レコード セットは削除できません。 ゾーンを削除すると、自動的に削除されます。

## <a name="next-steps"></a>次のステップ

* Azure DNS の詳細については、「 [Azure DNS の概要](dns-overview.md)」を参照してください。
* DNS 作成の自動化については、「 [.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。
* 逆引き DNS レコードについて詳しくは、「[逆引き DNS と Azure でのサポートの概要](dns-reverse-dns-overview.md)」をご覧ください。
* Azure DNS エイリアス レコードの詳細については、「[Azure DNS エイリアス レコードの概要](dns-alias.md)」を参照してください。