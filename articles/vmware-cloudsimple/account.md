---
title: アカウント管理 - Azure VMware Solution by CloudSimple ポータル
description: Azure VMware Solution by CloudSimple ポータルでアカウントを管理する方法について説明します。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4c26d5accce77ce6fd8c9b6c2b519b93f95013ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895174"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Azure VMware Solution by CloudSimple ポータルでアカウントを管理する

CloudSimple サービスを作成する際、CloudSimple にアカウントを作成します。 アカウントは、サービスが配置されている Azure サブスクリプションに関連付けられています。 サブスクリプション内に所有者ロールと共同作成者ロールを持つすべてのユーザーが CloudSimple ポータルにアクセスできます。 CloudSimple サービスに関連付けられた Azure サブスクリプション ID とテナント ID は、[アカウント] ページ上にあります。

Cloudsimple ポータルでアカウントを管理するには、[ポータルにアクセス](access-cloudsimple-portal.md)し、サイド メニューで **[アカウント]** を選択します。

**[概要]** を選択して、会社の CloudSimple 構成に関する情報を表示します。 プライベート クラウドの数、合計ストレージ、vSphere クラスターの構成、ノードの数、コンピューティング コアの数など、クラウド構成の現在の容量が表示されます。 現在の構成がすべてのニーズを満たしていない場合は、追加のノードを購入するためのリンクも表示されます。

## <a name="email-alerts"></a>メール アラート

プライベート クラウドの構成への変更について通知するユーザーのメール アドレスを追加することができます。

1. **[Additional email alerts]\(追加のメール アラート\)** 領域で、**[新規追加]** をクリックします。
2. メール アドレスを入力します。
3. Return キーを押します。  

エントリを削除するには、**[X]** をクリックします。

## <a name="cloudsimple-operator-access"></a>CloudSimple 演算子へのアクセス

演算子のアクセス設定を使用して、CloudSimple ポータルへのサインインをサポート エンジニアに許可すると、CloudSimple をトラブルシューティングに役立てることができます。  既定では、この設定は有効です。 サポート エンジニアが顧客アカウントにログインしたときに実行するすべてのアクションが記録され、 **[アクティビティ]**  >  **[監査]** ページで確認できるようになります。

**[CloudSimple operator access enabled]\(CloudSimple 演算子のアクセスが有効\)** トグルをクリックして、アクセスを有効または無効にします。
