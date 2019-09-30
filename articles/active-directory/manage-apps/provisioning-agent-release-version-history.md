---
title: 'Azure AD Connect プロビジョニング エージェント: バージョンのリリース履歴 | Microsoft Docs'
description: この記事では、Azure AD Connect プロビジョニング エージェントのすべてのリリースを一覧表示し、新機能と修正された問題について説明します
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861990"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect プロビジョニング エージェント: バージョンのリリース履歴
この記事では、これまでにリリースされた Azure AD Connect プロビジョニング エージェントのバージョンと機能を一覧表示します。 Azure AD チームは、プロビジョニング エージェントを新機能で定期的に更新しています。 プロビジョニング エージェントは、新しいバージョンがリリースされると自動的に更新されます。 

最新の機能とバグの修正が確実に適用されるように、エージェントの自動更新を有効にすることをお勧めします。 Microsoft では、エージェントの最新バージョンと 1 つ前のバージョンを直接サポートしています。

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>リリースの状態

2019 年 9 月 9 日: 自動更新向けにリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* プロビジョニング エージェントに関する問題をデバッグするために追加のトレースとログ記録を構成する機能
* 同期のパフォーマンスを向上させるために、マッピングで構成されている AD 属性のみを取り込む機能

### <a name="fixed-issues"></a>修正された問題

* AD 接続エラーに関する問題が発生した場合に、エージェントを応答しない状態にしていたバグを修正しました
* バイナリ データを Active Directory から読み取るときに問題を引き起こしていたバグを修正しました
* エージェントがクラウド ハイブリッド ID サービスとの信頼を更新するのを失敗させていたバグを修正しました

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>リリースの状態

2019 年 1 月 23 日: ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

* パフォーマンス、安定性、信頼性を向上させるためにプロビジョニング エージェントおよびコネクタのアーキテクチャが刷新されました 
* UI 駆動型インストール ウィザードを使用してプロビジョニング エージェントの構成が簡略化されました 
* エージェントの自動更新のサポートが追加されました

