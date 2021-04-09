---
title: Azure Marketplace での仮想マシン オファー プレビュー対象ユーザーの追加
description: Azure Marketplace で、仮想マシン オファーのプレビュー対象ユーザーを追加する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 854d0fa9b34d495f03a0c3c6203ceb227e4712d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629514"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>仮想マシン オファーのプレビュー対象ユーザーを追加する方法

**[プレビュー]** ページ (パートナー センターの左側のナビゲーション メニューから選択) で、コマーシャル マーケットプレースの幅広い対象ユーザーに公開する前にオファーを検証するために、限られた **プレビュー対象ユーザー** を選択します。

> [!IMPORTANT]
> **[プレビュー]** ペインでオファーを確認した後、 **[一般公開する]** を選択して、コマーシャル マーケットプレースのパブリック対象ユーザーに対してオファーを発行します。

プレビュー対象ユーザーは、**Azure サブスクリプション ID** の GUID と、それぞれに対する省略可能な **説明** によって識別されます。 いずれのフィールドも顧客は表示できません。 Azure サブスクリプション ID は、Azure portal の **[サブスクリプション]** ページにあります。

少なくとも 1 つの Azure サブスクリプション ID を、個別に (最大 10 ID)、または CSV ファイルをアップロードすることによって (最大 100 ID) 追加します。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に公開されている場合も、オファーの変更や更新をテストするために、プレビュー対象ユーザーを定義することができます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 プレビュー対象ユーザーは、Azure Marketplace で一般公開される "*前に*"、オファーにアクセスすることができます。 プレビュー対象ユーザーは、オファーが Azure Marketplace に完全に発行された後でプライベート対象ユーザーだけが使用できるプランも含めて、すべてのプランを表示し、検証することができます。 プライベート対象ユーザーは ( **[価格と利用可否]** ペインで定義します)、特定のプランに排他的にアクセスできます。

変更を行った場合は、左側のナビゲーション メニューの次のタブである **[プランの概要]** に進む前に、 **[下書きの保存]** を選択してください。

## <a name="next-steps"></a>次の手順

- [プランを作成する](azure-vm-create-plans.md)
