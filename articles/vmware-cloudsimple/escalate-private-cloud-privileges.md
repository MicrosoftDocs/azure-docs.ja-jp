---
title: プライベートクラウドの特権を昇格する
titleSuffix: Azure VMware Solution by CloudSimple
description: vCenter の管理機能のためにプライベート クラウドで特権をエスカレートする方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895824"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>CloudSimple ポータルからのプライベート クラウド vCenter 特権のエスカレート

プライベート クラウド vCenter への管理者アクセスのために、CloudSimple 特権を一時的にエスカレートすることができます。  エスカレートした特権を使用して、VMware のソリューションのインストール、ID ソースの追加、およびユーザーの管理を行うことができます。

vCenter SSO ドメインで新しいユーザーを作成し、vCenter へのアクセスを付与できます。  新しいユーザーを作成したら、vCenter にアクセスするために CloudSimple 組み込みグループに追加します。  詳細については、「[VMware vCenter の CloudSimple プライベート クラウド アクセス許可モデル](./learn-private-cloud-permissions.md)」を参照してください。

> [!CAUTION]
> 管理コンポーネントの構成は変更しないでください。 特権がエスカレートされた状態でアクションを実行すると、システムに悪影響が出たり、システムが使用できなくなったりする可能性があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="escalate-privileges"></a>権限をエスカレートする

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

2. **[Resources]\(リソース\)** ページを開き、特権をエスカレートするプライベート クラウドを選択します。

3. 概要ページの下部付近の **[Change vSphere privileges]\(vSphere 特権を変更\)** で、**[Escalate]\(エスカレート\)** をクリックします。

    ![vSphere 特権を変更する](media/escalate-private-cloud-privilege.png)

4. vSphere のユーザーの種類を選択します。  エスカレートできるのは `CloudOwner@cloudsimple.local` ローカル ユーザーのみです。

5. ドロップダウンからエスカレートの期間を選択します。 タスクを完了できる最短の期間を選択します。

6. リスクを理解したことを確認するチェック ボックスを選択します。

    ![特権をエスカレートするダイアログ](media/escalate-private-cloud-privilege-dialog.png)

7. **[OK]** をクリックします。

8. エスカレーション プロセスには数分かかる場合があります。 設定が完了したら **[OK]** をクリックします。

特権エスカレーションが開始され、選択した期間が終了するまで継続します。  プライベート クラウド vCenter にサインインして管理タスクを実行することができます。

> [!IMPORTANT]
> エスカレートされた特権を持つことができるユーザーは 1 人だけです。  そのユーザーの特権のエスカレートを解除してからでないと、別のユーザーの特権をエスカレートすることはできません。

> [!CAUTION]
> 新しいユーザーは、*Cloud-Owner-Group*、*Cloud-Global-Cluster-Admin-Group*、*Cloud-Global-Storage-Admin-Group*、*Cloud-Global-Network-Admin-Group*、または *Cloud-Global-VM-Admin-Group* にのみ追加する必要があります。  *Administrators* グループに追加されたユーザーは自動的に削除されます。  *[管理者]* グループに追加する必要があるのはサービス アカウントだけです。また、サービス アカウントを使用して vSphere Web UI にサインインすることはできません。

## <a name="extend-privilege-escalation"></a>特権エスカレーションを延長する

タスクを完了するために追加の時間が必要な場合、特権エスカレーション期間を延長することができます。  管理タスクを完了できるようにする追加のエスカレート期間を選択します。

1. CloudSimple ポータルの **[Resources]\(リソース\)**  >  **[Private Clouds]\(プライベート クラウド\)** で、特権エスカレーションを延長するプライベート クラウドを選択します。

2. 概要タブの下部付近で、**[Extend privilege escalation]\(特権エスカレーションを延長する\)** をクリックします。

    ![特権エスカレーションを延長する](media/de-escalate-private-cloud-privilege.png)

3. ドロップダウンから、エスカレートの期間を選択します。 新しいエスカレーションの終了時刻を確認します。

4. **[Save]\(保存\)** をクリックして期間を延長します。

## <a name="de-escalate-privileges"></a>特権のエスカレートを解除する

管理タスクが完了したら、特権のエスカレートを解除する必要があります。  

1. CloudSimple ポータルの **[Resources]\(リソース\)**  >  **[Private Clouds]\(プライベート クラウド\)** で、特権のエスカレートを解除するプライベート クラウドを選択します。

2. **[De-escalate]\(エスカレートの解除\)** をクリックします。

3. **[OK]** をクリックします。

> [!IMPORTANT]
> すべてのエラーを避けるためには、vCenter からサインアウトし、特権のエスカレートを解除した後にもう一度サインインします。

## <a name="next-steps"></a>次の手順

* [Active Directory を使用するための vCenter ID ソースの設定](./set-vcenter-identity.md)
* [ワークロード仮想マシンのバックアップ](./backup-workloads-veeam.md)を行うためのバックアップ ソリューションのインストール
