---
title: "仮想ネットワーク (クラシック) で使用される DNS サーバーの管理 - Azure Portal (クラシック) | Microsoft Docs"
description: "Azure Portal (クラシック) を使用して仮想ネットワーク (クラシック) の DNS サーバーを追加および削除する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: b582be7d-dc78-4cfe-a766-185bd7e5dc68
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: b765fb94f881453ae6a90ec0ae6b6f06843b3aa2
ms.lasthandoff: 02/28/2017


---
# <a name="manage-dns-servers-used-by-a-virtual-network-classic-using-the-azure-portal-classic"></a>Azure Portal (クラシック) を使用して仮想ネットワーク (クラシック) によって作成された DNS サーバーを管理する

仮想ネットワーク (VNet) で使用される DNS サーバーの一覧は、Azure Portal (クラシック) またはネットワーク構成ファイルで管理できます。 VNet ごとに最大 12 台の DNS サーバーを追加できます。 DNS サーバーを指定する際に重要なのは、環境に適した順で DNS サーバーが一覧表示されていることを確認することです。 DNS サーバーの一覧はラウンド ロビンに対応していません。 指定した順序で使用されます。 一覧の先頭にある DNS サーバーに到達できる場合は、DNS サーバーが正しく動作しているかどうかに関係なく、クライアントはその DNS サーバーを使用します。 仮想ネットワーク用に DNS サーバーの順序を変更するには、DNS サーバーを一覧から削除して、希望の順序になるように再度追加します。

> [!WARNING]
> DNS の一覧が更新された後は、新しい DNS サーバーの設定が取得されるように、仮想ネットワークにある仮想マシンを再起動する必要があります。 仮想マシンは、再起動されるまで、現在の構成を引き続き使用します。
> 
> 

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-azure-portal-classic"></a>Azure Portal (クラシック) を使用した仮想ネットワークの DNS サーバー一覧の編集
1. [Azure Portal (クラシック)](https://manage.windowsazure.com) にログオンします。
2. ナビゲーション ウィンドウで、**[ネットワーク]** をクリックし、**[名前]** 列の仮想ネットワーク名をクリックします。
3. **[構成]**をクリックします。
4. **[DNS サーバー]**では、次の構成を行うことができます。
   
   * **新しい DNS サーバーの登録 (追加)** : ボックスに名前と IP アドレスを入力するだけです。 これにより、DNS サーバーは、仮想ネットワークの DNS サーバーの一覧に追加され、さらに Azure に登録されます。
   * **以前登録された DNS サーバーの追加** : DNS サーバーを既に Azure に登録している場合は、設定済みの一覧から選択できます。
   * **仮想ネットワークからの DNS サーバーの削除** : 削除するサーバーの横にある [x] をクリックします。 この操作では、仮想ネットワークの一覧からサーバーが削除されるだけです。 DNS サーバーは、他の仮想ネットワークで使用するために Azure に登録されたままになります。 サブスクリプションから DNS サーバーを削除するには、**[ネットワーク]、[DNS サーバー]** ページの順に移動します。
   * **DNS サーバーの順序変更** : 一覧表示されたすべての DNS サーバーを削除してから、希望する順序で再度追加します。 ラウンド ロビン DNS の一覧ではないことに注意してください。
   * **DNS サーバーの名前変更** : 一覧で DNS サーバーを強調表示し、新しい名前を入力します。 これにより、新しい DNS サーバーが Azure に登録されると同時に、仮想ネットワークの DNS サーバーの一覧にも追加されます。 古い DNS サーバーとその IP アドレスは Azure に登録されたままになります。 他の仮想ネットワークで使用しない場合は、 **[DNS サーバー]** ページで削除できます。
5. ページの下部にある **[保存]** をクリックし、新しい DNS サーバーの構成を保存します。
6. 仮想ネットワークにある仮想マシンを再起動して、新しい DNS の設定を取得できるようにします。

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>ネットワーク構成ファイルによる DNS サーバー一覧の編集
ネットワーク構成ファイルを使用して DNS サーバーの一覧を編集するには、最初に管理ポータルから構成設定をエクスポートします。 次に、ネットワーク構成ファイルを編集し、Azure Portal (クラシック) でもう一度インポートします。 このプロセスを完了するための手順の概要は次のとおりです。

1. 仮想ネットワークの設定をネットワーク構成ファイルにエクスポートします。 ネットワーク構成設定のエクスポートの詳細と手順については、「 [ネットワーク構成ファイルへの仮想ネットワーク設定のエクスポート](virtual-networks-using-network-configuration-file.md)」をご覧ください。
2. 仮想ネットワークの DNS サーバーの情報を指定します。 DNS サーバーの指定の詳細については、 [仮想ネットワーク構成ファイルでの DNS サーバーの指定](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)に関するページをご覧ください。 ネットワーク構成ファイルの詳細については、「[Azure Virtual Network の構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」と「[ネットワーク構成ファイルを使用した仮想ネットワークの構成](virtual-networks-using-network-configuration-file.md)」をご覧ください。
3. ネットワーク構成ファイルをインポートします。 ネットワーク構成ファイルのインポートの詳細と手順については、「 [ネットワーク構成ファイルのインポート](virtual-networks-using-network-configuration-file.md)」をご覧ください。
4. 仮想ネットワークにある仮想マシンを再起動して、新しい DNS の設定を取得できるようにします。


