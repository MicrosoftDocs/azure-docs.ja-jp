---
title: Avere vFXT のサポートを有効にする - Azure
description: Avere vFXT for Azure からのサポートのアップロードを有効にする方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256220"
---
# <a name="enable-support-uploads"></a>サポートのアップロードを有効にする

Avere vFXT for Azure では、ご利用のクラスターに関するサポート データを自動的にアップロードするこができます。 これらのアップロードにより、サポート スタッフは可能な限り最高の顧客サービスを提供することができます。

## <a name="steps-to-enable-uploads"></a>アップロードを有効にする手順

サポートをアクティブ化するには、Avere コントロール パネルから次の手順に従います (Avere コントロール パネルを開く方法については、「[Access the vFXT cluster](avere-vfxt-cluster-gui.md)」 (vFXT クラスターにアクセスする) をお読みください)。

1. 上部にある **[設定]** タブに移動します。
1. 左側にある **[サポート]** リンクをクリックし、プライバシー ポリシーに同意します。

   ![Avere コントロール パネルとプライバシー ポリシーに同意することを示す [確認] ボタン付きのポップアップ ウィンドウを示しているスクリーンショット](media/avere-vfxt-privacy-policy.png)

1. **[顧客情報]** の左側にある三角形をクリックして、セクションを展開します。
1. **[Validate upload information]\(アップロード情報の検証\)** ボタンをクリックします。
1. **[Unique Cluster Name]\(一意のクラスター名\)** でクラスターのサポート名を設定します。この名前でサポート スタッフがクラスターを一意に識別できるようにしてください。
1. **[Statistics Monitoring]\(統計の監視\)** 、 **[General Information Upload]\(全般情報のアップロード\)** 、および **[Crash Information Upload]\(クラッシュ情報のアップロード\)** の各チェック ボックスをオンにします。
1. **[送信]** をクリックします。

   ![サポートの設定ページの完了した [顧客情報] セクションを含むスクリーンショット](media/avere-vfxt-support-info.png)

1. **[Secure Proactive Support (SPS)]\(セキュア プロアクティブ サポート (SPS)\)** の左側にある三角形をクリックして、セクションを展開します。
1. **[Enable SPS Link]\(SPS リンクを有効にする\)** のチェック ボックスをオンにします。
1. **[送信]** をクリックします。

   ![サポートの設定ページの完了した [Secure Proactive Support]\(セキュア プロアクティブ サポート\) セクションを含むスクリーンショット](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>次の手順

オンプレミス ストレージ システムまたは既存のクラウド ストレージ システムをクラスターに追加する必要がある場合は、「[ストレージの構成](avere-vfxt-add-storage.md)」の手順に従ってください。 

クラスターへのクライアントのアタッチを開始する準備ができている場合は、「[Mount the Avere vFXT cluster](avere-vfxt-mount-clients.md)」 (Avere vFXT クラスターのマウント) をお読みください。