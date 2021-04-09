---
title: Azure Resource Manager のテンプレート
description: Azure Site Recovery 機能を使用するための Azure Resource Manager テンプレート。
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720530"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Site Recovery 用 Azure Resource Manager テンプレート

次の表は、Azure Site Recovery 機能を使用するための Azure Resource Manager テンプレートのリンク一覧です。

| テンプレート | 説明 |
|---|---|
|**Azure から Azure** | |
| [Recovery Services コンテナーの作成](./quickstart-create-vault-template.md)| Recovery Services コンテナーを作成する。 コンテナーは、Azure Backup および Azure Site Recovery に使用できます。 |
| [Azure VM のレプリケーションの有効化](https://aka.ms/asr-arm-enable-replication) | 既存のコンテナーとカスタム ターゲット設定を使用して、Azure VM のレプリケーションを有効にします。|
| [フェールオーバーをトリガーして再保護する](https://aka.ms/asr-arm-failover-reprotect) | 一連の Azure VM に対してフェールオーバーおよび再保護の操作をトリガーします。 |
| [Azure VM に対してエンド ツー エンドの DR フローを実行する](https://aka.ms/asr-arm-e2e-flow) | Azure VM に対して完全なエンド ツー エンドのディザスター リカバリー フロー (レプリケーションの有効化 + フェールオーバーと再保護 + フェールバックと再保護) を開始します。これは、540°フローとも呼ばれます。|
|   |   |