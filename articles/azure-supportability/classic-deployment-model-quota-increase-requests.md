---
title: Azure クラシック デプロイ モデル | Microsoft Docs
description: Azure クラシック デプロイ モデル
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5ae2a56c084116ae8d57a16696223e7990258558
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313238"
---
# <a name="classic-deployment-model"></a>クラシック デプロイ モデル

クラシック デプロイ モデルとは、Azure の古い世代のデプロイ モードであり、仮想マシンと仮想マシン スケール セットに対してグローバルな vCPU クォータ制限が適用されます。 クラシック デプロイ モデルはもう推奨されていません。現在は Resource Manager モデルに置き換えられています。 この 2 つのデプロイ モデルと Resource Manager の利点の詳細については、Resource Manager デプロイ モデルのページを参照してください。 新しいサブスクリプションが作成されると、vCPU の既定のクォータが割り当てられます。 新しい VM をクラシック デプロイ モデルを使用してデプロイする場合は、常にすべてのリージョンでの新しい vCPU と既存の vCPU の使用量の合計が、クラシック デプロイ モデルで承認されている vCPU のクォータを超えないようにする必要があります。 Azure サブスクリプションのクォータとサービスの制限に関するページで詳細を確認してください。

ポータルの [ヘルプとサポート] ブレードまたは [Usages + Quota]\(使用量とクォータ\) ブレードから、クラシック デプロイ モデルに対する vCPU の制限を引き上げることを要求できます。

