---
title: OpenShift Container Platform 4.x の Azure へのデプロイ
description: OpenShift Container Platform 4.x を Azure にデプロイする。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: e8650802b4add9b33664205367bb3242b32b9754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670399"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>OpenShift Container Platform 4.x の Azure へのデプロイ

OpenShift Container Platform (OCP) 4.2 のデプロイは、Installer-Provisioned Infrastructure (IPI) モデルを通じて Azure でサポートされるようになりました。  OpenShift 4 をお試しいただけるランディング ページは [try.openshift.com](https://try.openshift.com/) です。 Azure に OCP 4.2 をインストールするには、[Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) のページにアクセスします。  このサイトにアクセスするには、Red Hat 資格情報が必要です。


## <a name="notes"></a>Notes 

 - Azure で OCP 4.x をインストールして実行するには、Azure Active Directory (AAD) サービス プリンシパル (SP) が必要です
     - SP には、Azure Active Directory Graph における **Application.ReadWrite.OwnedBy** の API アクセス許可が付与されている必要があります
     - この API のアクセス許可を有効にするには、AAD テナント管理者が管理者の同意を付与する必要があります
     - SP は、サブスクリプションの **[共同作成者]** ロールと **[ユーザーアクセス管理者]** ロールが付与されている必要があります
 - OCP 4.x のインストール モデルは 3.x とは異なり、Azure で OCP 4.x をデプロイするために使用できる Azure Resource Manager テンプレートはありません
 - インストール プロセス中に問題が発生した場合は、適切な会社 (Microsoft または Red Hat) にお問い合わせください

| 問題の説明 | コンタクト ポイント |
|-------------------|---------------|
| Azure 固有の問題 (AAD、SP、Azure サブスクリプションなど)                              | Microsoft |
| OpenShift 固有の問題 (インストール エラー/エラー、Red Hat サブスクリプションなど) |  Red Hat  |




## <a name="next-steps"></a>次のステップ

- [OpenShift Container Platform の概要](https://docs.openshift.com)
