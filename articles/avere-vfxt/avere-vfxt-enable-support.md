---
title: Avere vFXT のサポートを有効にする - Azure
description: Avere vFXT for Azure のクラスターに関するサポート データの自動アップロードを有効にして、サポートによる顧客サービスの提供に役立てる方法について説明します。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272730"
---
# <a name="enable-support-uploads"></a>サポートのアップロードを有効にする

Avere vFXT for Azure では、ご利用のクラスターに関するサポート データを自動的にアップロードできます。 これらのアップロードにより、サポート スタッフは可能な限り最高の顧客サービスを提供することができます。

## <a name="steps-to-enable-uploads"></a>アップロードを有効にする手順

サポートをアクティブ化するには、Avere コントロール パネルから次の手順に従います (Avere コントロール パネルを開く方法については、「[vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)」を参照してください)。

1. 上部にある **[設定]** タブに移動します。
1. 左側にある **[サポート]** リンクをクリックし、プライバシー ポリシーに同意します。

   ![Avere コントロール パネルとプライバシー ポリシーに同意することを示す [確認] ボタン付きのポップアップ ウィンドウを示しているスクリーンショット](media/avere-vfxt-privacy-policy.png)

1. [サポート構成] ページで、左側にある三角形をクリックして **[顧客情報]** セクションを開きます。
1. **[Validate upload information]\(アップロード情報の検証\)** ボタンをクリックします。
1. **[一意のクラスター名]** に、クラスターのサポート名を設定します。 この名前によって、サポート スタッフがクラスターを一意に識別できることを確認します。
1. **[Statistics Monitoring]\(統計の監視\)** 、 **[General Information Upload]\(全般情報のアップロード\)** 、および **[Crash Information Upload]\(クラッシュ情報のアップロード\)** の各チェック ボックスをオンにします。
1. **[送信]** をクリックします。

   ![サポートの設定ページの完了した [顧客情報] セクションを含むスクリーンショット](media/avere-vfxt-support-info.png)

1. **[Secure Proactive Support (SPS)]\(セキュア プロアクティブ サポート (SPS)\)** の左側にある三角形をクリックして、セクションを展開します。
1. **[Enable SPS Link]\(SPS リンクを有効にする\)** のチェック ボックスをオンにします。
1. **[送信]** をクリックします。

   ![サポートの設定ページの完了した [Secure Proactive Support]\(セキュア プロアクティブ サポート\) セクションを含むスクリーンショット](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>次のステップ

オンプレミス ストレージ システムまたは既存のクラウド ストレージ システムをクラスターに追加する必要がある場合は、「[ストレージの構成](avere-vfxt-add-storage.md)」の手順に従ってください。

クラスターへのクライアントのアタッチを開始する準備ができている場合は、「[Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md)」 (Avere vFXT クラスターのマウント) をお読みください。
