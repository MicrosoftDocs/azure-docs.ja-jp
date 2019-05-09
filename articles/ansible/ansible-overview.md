---
title: Azure での Ansible の使用
description: Ansible を使用してクラウド プロビジョニング、構成管理、およびアプリケーションのデプロイを自動化する方法の概要。
ms.service: azure
keywords: Ansible, Azure, DevOps, 概要, クラウド プロビジョニング, 構成管理, アプリケーションのデプロイ, Ansible モジュール, Ansible プレイブック
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: d7fc86f9968ede37623ea9c89a666a35cba7eee8
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791598"
---
# <a name="ansible-with-azure"></a>Ansible と Azure

[Ansible](https://www.ansible.com) は、クラウド プロビジョニング、構成管理、およびアプリケーションのデプロイを自動化するオープンソース製品です。 Ansible を使用すると、仮想マシン、コンテナー、およびネットワークをプロビジョニングし、クラウド インフラストラクチャを完成させることができます。 また、Ansible を使用すると、環境でのリソースのデプロイと構成を自動化することができます。

この記事では、Azure で Ansible を使用するいくつかの利点の基本概要を示します。

## <a name="ansible-playbooks"></a>Ansible プレイブック

[Ansible プレイブック](https://docs.ansible.com/ansible/latest/playbooks.html)は、Ansible の構成、デプロイ、およびオーケストレーション言語です。 プレイブックには、リモート システムで実行するポリシーや、一般的な IT プロセスの一連の手順を記述できます。 プレイブックを作成する場合は、構成またはプロセスのモデルを定義する YAML を使用します。

## <a name="ansible-modules"></a>Ansible モジュール

Ansible には一連の [Ansible モジュール](https://docs.ansible.com/ansible/latest/modules_by_category.html)が含まれています。これらのモジュールは、リモート ホストで直接実行することも、[プレイブック](https://docs.ansible.com/ansible/latest/playbooks.html)を介して実行することもできます。 また、ユーザーが自分でモジュールを作成することもできます。 モジュールを使用すると、サービス、パッケージ、ファイルなどのシステム リソースを制御したり、システム コマンドを実行したりすることができます。

Azure サービスとの対話用に、Ansible には、Azure 上でインフラストラクチャを簡単に作成して調整するためのツールを提供する、一連の [Ansible クラウド モジュール](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)が含まれています。 

## <a name="migrate-existing-workload-to-azure"></a>Azure への既存のワークロードの移行

Ansible を使用してインフラストラクチャを定義したら、アプリケーションのプレイブックを適用して、Azure が必要に応じて自動的に環境を拡大縮小できるようにします。 

## <a name="automate-cloud-native-application-in-azure"></a>Azure のクラウドネイティブ アプリケーションの自動化

Ansible を使用すると、[Azure Functions](https://azure.microsoft.com//services/functions/) や [Kubernetes on Azure](https://azure.microsoft.com/services/container-service/kubernetes/) などの Azure マイクロサービスを使用して Azure のクラウドネイティブ アプリケーションを自動化することができます。  

## <a name="manage-deployments-with-dynamic-inventory"></a>動的インベントリによるデプロイの管理
Ansible では、[動的インベントリ](https://docs.ansible.com/ansible/intro_dynamic_inventory.html)機能を使用して、Azure リソースからインベントリをプルすることができます。 既存の Azure デプロイにタグを付け、タグを付けたデプロイを Ansible で管理することができます。

## <a name="additional-azure-marketplace-options"></a>その他の Azure Marketplace オプション
Red Hat の [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Azure Marketplace イメージは、組織が物理、仮想、およびクラウド インフラストラクチャにわたって IT 自動化をスケーリングし、複雑なデプロイを管理するのに役立ちます。 Ansible Tower は、今日の企業に必要な可視性、制御、セキュリティ、および効率性をさらに高める機能を備えています。 Ansible Tower では、Azure および SSH キーなどの資格情報を暗号化することにより、資格情報が漏えいするリスクを伴わずに、経験の少ない従業員に業務を委任できます。

## <a name="ansible-module-and-version-matrix-for-azure"></a>Azure の Ansible モジュールとバージョンのマトリックス
Ansible には多数のモジュールが付属しています。これらのモジュールは、リモート ホストで直接実行することも、プレイブックを介して実行することもできます。
[Ansible モジュールおよびバージョン マトリックス](./ansible-matrix.md)では、仮想マシン、ネットワーク、コンテナー サービスなどの Azure クラウド リソースをプロビジョニングできる Azure 用 Ansible モジュールの一覧を示します。 

## <a name="next-steps"></a>次の手順
- [Ansible の構成](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Linux VM の作成](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
