---
title: Azure Lab Services のファイアウォール設定
description: 情報をファイアウォール規則に追加できるように、ラボ内の仮想マシンのパブリック IP アドレスとポート番号の範囲を特定する方法について説明します。
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445850"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services のファイアウォール設定

組織や学校はそれぞれ、自分たちのニーズに最も合った方法で、所有するネットワークを設定します。  時にはそれに、所有するネットワークの外にあるマシンに対するリモート デスクトップ プロトコル (rdp) 接続や Secure Shell (ssh) 接続をブロックするファイアウォール規則の設定が含まれています。  Azure Lab Services はパブリック クラウドで実行されるため、キャンパス ネットワークからの接続時に VM へのアクセスを学生に許可するために、追加の構成が必要になる場合があります。

各ラボで、単一のパブリック IP アドレスと複数のポートを使用します。  すべての VM、つまりテンプレート VM と 学生 VM の両方で、このパブリック IP アドレスを使用します。  ラボが使われている間、パブリック IP アドレスは変更されません。  ただし、各 VM のポート番号は異なるものになります。  ポート番号の範囲は 49152 ～ 65535 です。  指導者と学生を正しい VM に接続するために、パブリック IP アドレスとポート番号の組み合わせが使用されます。  この記事では、ラボで使用される特定のパブリック IP アドレスを見つける方法について説明します。  この情報は、学生が自分の VM にアクセスできるように、インバウンドとアウトバウンドのファイアウォール規則を更新するために使用できます。

>[!IMPORTANT]
>各ラボは、異なるパブリック IP アドレスを持つことになります。

## <a name="find-public-ip-for-a-lab"></a>ラボのパブリック IP を見つける

各ラボのパブリック IP アドレスは、Lab Services のラボ アカウントの **[すべてのラボ]** ページに一覧表示されます。  **[すべてのラボ]** ページを検索する方法については、「[ラボ アカウント内のラボを表示する](manage-labs.md#view-labs-in-a-lab-account)」を参照してください。  

> [!div class="mx-imgBorder"]
> ![[すべてのラボ] ページ](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>ラボのテンプレート マシンがまだ公開されていない場合、パブリック IP アドレスは表示されません。

## <a name="conclusion"></a>まとめ

これでラボのパブリック IP アドレスがわかりました。  パブリック IP アドレスとポート範囲 49152 ～ 65535 について、組織のファイアウォールのインバウンド規則とアウトバウンド規則を作成できます。  ルールが更新されると、学生はネットワーク ファイアウォールでアクセスがブロックされることなく、自分の VM にアクセスできます。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ作成者にラボの場所の選択を許可する](allow-lab-creator-pick-lab-location.md)
- [ラボのネットワークとピア仮想ネットワークを接続する](how-to-connect-peer-virtual-network.md)
- [ラボに共有イメージ ギャラリーをアタッチする](how-to-attach-detach-shared-image-gallery.md)
- [ユーザーをラボ所有者として追加する](how-to-add-user-lab-owner.md)
- [ラボのファイアウォール設定の表示](how-to-configure-firewall-settings.md)
- [ラボのその他の設定を構成する](how-to-configure-lab-accounts.md)
