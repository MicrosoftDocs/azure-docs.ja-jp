---
title: 'Azure AD パススルー認証: バージョンのリリース履歴 | Microsoft Docs'
description: この記事では、Azure AD パススルー認証エージェントのすべてのリリースを一覧で示します
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333428"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD パススルー認証エージェント: バージョンのリリース履歴 
 
パススルー認証を有効にするオンプレミスにインストールされたエージェントは、新しい機能を提供するために定期的に更新されます。 この記事では、新しい機能が導入されるときに追加されるバージョンと機能の一覧を示します。 パススルー認証エージェントは、新しいバージョンがリリースされると自動的に更新されます。 

関連トピックは以下のとおりです。 

- [Azure AD パススルー認証によるユーザー サインイン](how-to-connect-pta.md) 
- [Azure AD パススルー認証エージェントのインストール](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>リリースの状態: 
2020 年 4 月 9 日:ダウンロード対象としてリリース済み

### <a name="new-features-and-improvements"></a>新機能と機能強化

- インストール後にターゲットとなるクラウド環境向けのサポートを追加しました。 バンドルは、特定のクラウド環境にピン留めできます。



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>リリースの状態 
2019 年 1 月 22 日: ダウンロード対象としてリリース済み  
### <a name="new-features-and-improvements"></a>新機能と機能強化 
- 送信接続の別の層の接続回復性を追加するために Service Bus の信頼性の高いチャネルのサポートを追加しました 
- TLS 1.2 をエージェントの登録時に適用します 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>リリースの状態 
2018 年 4 月 10 日: ダウンロード対象としてリリース済み  
### <a name="new-features-and-improvements"></a>新機能と機能強化 
- Web ソケット接続のサポート 
- TLS 1.2 をエージェントの既定のプロトコルとして設定します 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>リリースの状態 
2018 年 1 月 31 日: ダウンロード対象としてリリース済み  
### <a name="fixed-issues"></a>修正された問題 
- エージェントでメモリ リークの原因となっていたバグを修正しました。 
- Azure Service Bus のバージョンを更新しました。このバージョンでは、コネクタ タイムアウトの問題に関するバグが修正されています。 
### <a name="new-features-and-improvements"></a>新機能と機能強化 
- 接続の回復性を向上させるために、エージェントと Azure AD サービス間の Web ソケット ベースの接続のサポートを追加しました

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>リリースの状態 
2017 年 11 月 25 日: ダウンロード対象としてリリース済み  
### <a name="fixed-issues"></a>修正された問題 
- 既定のプロキシ シナリオにおける DNS キャッシュに関連するバグを修正しました 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>リリースの状態 
2017 年 10 月 17 日: ダウンロード対象としてリリース済み  
### <a name="new-features-and-improvements"></a>新機能と機能強化 
- DNS エラーからの回復性を追加するために送信接続用の DNS キャッシュ機能を追加しました 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>リリースの状態 
2017 年 8 月 31 日: ダウンロード対象としてリリース済み  
### <a name="new-features-and-improvements"></a>新機能と機能強化 
- Azure AD パススルー認証エージェントの GA バージョン 

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory パススルー認証によるユーザー サインイン](how-to-connect-pta.md)
