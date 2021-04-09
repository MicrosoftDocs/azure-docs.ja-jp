---
title: 'Azure AD Connect クラウド プロビジョニング エージェント: 自動アップグレード | Microsoft Docs'
description: この記事では、Azure AD Connect クラウド プロビジョニング エージェントの組み込みの自動アップグレード機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612815"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect クラウド プロビジョニング エージェント: 自動アップグレード

Azure Active Directory (Azure AD) Connect クラウド プロビジョニング エージェントのインストールを常に最新の状態に保つことは、自動アップグレード機能を使用すれば簡単です。

エージェントはこちらにインストールされます: "Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェント ファイルのバージョン](media/how-to-automatic-upgrade/agent-1.png)

エージェント アップデーターはこちらにインストールされます: "Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

バージョンを確認するには、実行可能ファイルを右クリックして、[プロパティ]、[詳細] の順に選択します。

![エージェントアップデーターのバージョン](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>エージェントのアンインストール
エージェントを削除するには、**[プログラムのアンインストールまたは変更]** に移動して、次のものをアンインストールします。

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect プロビジョニング エージェント**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![エージェントの削除](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)

