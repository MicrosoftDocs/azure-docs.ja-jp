---
title: Azure Stack コンテナーを使用するための Kubernetes をデプロイする | Microsoft Docs
description: Azure Stack でコンテナーを使用するための Kubernetes をデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 77275ec274a9c76918874007cfe564eea09e6de5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877195"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Azure Stack でコンテナーを使用するための Kubernetes をデプロイする

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

> [!Note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。 Azure Stack の切断されたシナリオは、プレビューでは現在サポートされていません。

この記事の手順に従えば、1 回の連携した操作で Kubernetes のリソースをデプロイおよび設定することができます。 この手順では、Azure Resource Manager ソリューション テンプレートを使用します。 Azure Stack のインストールに関する必要な情報を収集し、テンプレートを生成してから、クラウドにデプロイする必要があります。 Azure Stack テンプレートでは、グローバルな Azure で提供されているのと同じマネージド型の AKS サービスは使用されません。

## <a name="kubernetes-and-containers"></a>Kubernetes とコンテナー

Azure Stack 上の ACS エンジンによって生成された Azure Resource Manager テンプレートを使用して、Kubernetes をインストールできます。 [Kubernetes](https://kubernetes.io) はコンテナー内でのアプリケーションのデプロイ、スケーリング、管理を自動化するオープンソース システムです。 [コンテナー](https://www.docker.com/what-container)はイメージに含まれます。 コンテナー イメージは VM と似ていますが、VM とは違って、コンテナーには、コード、コードを実行するためのランタイム、特定のライブラリ、および設定など、アプリケーションを実行するために必要なリソースのみが含まれています。

Kubernetes は、次の目的で使用できます。

- 数秒でデプロイできる極めてスケーラブルかつアップグレード可能なアプリケーションを開発する。 
- アプリケーションの設計を簡素化し、異なる Helm アプリケーションによって信頼性を向上させる。 [Helm](https://github.com/kubernetes/helm) は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。
- アプリケーションの正常性を簡単に監視および診断する。

クラスターをサポートするノードに必要なコンピューティングの使用量にのみ課金されます。 詳細については、「[Azure Stack での使用量と請求](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback)」を参照してください。

## <a name="deploy-kubernetes-to-use-containers"></a>コンテナーを使用するための Kubernetes をデプロイする

Azure Stack で Kubernetes クラスターをデプロイする手順は、ご使用の ID 管理サービスによって異なります。 Azure Stack のインストールで使用される ID 管理ソリューションを確認します。 ご使用の ID 管理サービスを確認するには、Azure Stack 管理者に問い合わせてください。

- **Azure Active Directory (Azure AD)**  
Azure AD を使用した場合のクラスターのインストール手順については、「[Deploy Kubernetes to Azure Stack using Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)」 (Azure Active Directory (Azure AD) を使用して Kubernetes を Azure Stack にデプロイする) を参照してください。

- **Active Directory フェデレーション サービス (AD FS)**  
AD FS を使用した場合のクラスターのインストール手順については、「[Deploy Kubernetes to Azure Stack using Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)」 (Active Directory フェデレーション サービス (AD FS) を使用して Kubernetes を Azure Stack にデプロイする) を参照してください。

## <a name="connect-to-your-cluster"></a>クラスターへの接続

これで、クラスターに接続する準備ができました。 マスターはクラスター リソース グループ内にあり、`k8s-master-<sequence-of-numbers>` という名前です。 マスターに接続するには SSH クライアントを使います。 マスターでは、Kubernetes のコマンドライン クライアントである **kubectl** を使ってクラスターを管理できます。 方法については、[Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview) をご覧ください。

クラスターへのアプリのインストールと展開には、**Helm** パッケージ マネージャーも役に立ちます。 クラスターでの Helm のインストールと使用の方法については、[helm.sh](https://helm.sh/) をご覧ください。

## <a name="next-steps"></a>次の手順

[Kubernetes ダッシュボードの有効化](azure-stack-solution-template-kubernetes-dashboard.md)

[Kubernetes を Marketplace に追加する (Azure Stack のオペレーター)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure Active Directory (Azure AD) を使用して Azure Stack に Kubernetes をデプロイする](azure-stack-solution-template-kubernetes-azuread.md)

[Active Directory フェデレーション サービス (AD FS) を使用して Azure Stack に Kubernetes をデプロイする](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes on Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
