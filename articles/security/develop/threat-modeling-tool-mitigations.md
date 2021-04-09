---
title: 軽減策 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Microsoft Threat Modeling Tool の Mitigations (軽減策) ページでは、生成された脅威のうち、最も危険な脅威の軽減策が強調表示されます。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728045"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool の軽減策

Threat Modeling Tool は、Microsoft セキュリティ開発ライフサイクル (SDL) の主要な要素です。 これを使用すると、ソフトウェア アーキテクトは早い段階で潜在的なセキュリティの問題を特定し、危険を軽減することができます。早い段階であれば、問題の解決は比較的容易で、コスト効率も良くなります。 そのため、開発総コストを大幅に軽減できます。 また、このツールはセキュリティの専門家ではないユーザーを想定して設計され、脅威モデルの作成と分析に関するわかりやすいガイダンスが用意されているため、すべての開発者が簡単に脅威をモデリングできます。

**[Threat Modeling Tool](threat-modeling-tool.md)** にアクセスして今日から始めましょう。

## <a name="mitigation-categories"></a>軽減策のカテゴリ

Threat Modeling Tool の軽減策は、Web アプリケーションのセキュリティ フレームに基づいて分類されます。このフレームの構成要素は、次のとおりです。

| カテゴリ | 説明 |
| -------- | ----------- |
| **[監査とログ記録](threat-modeling-tool-auditing-and-logging.md)** | だれがいつ何をしたか。 監査とログ記録とは、アプリケーションがセキュリティ関連のイベントを記録する方法のことです |
| **[認証](threat-modeling-tool-authentication.md)** | ユーザーはだれか 認証は、エンティティが他のエンティティの ID を証明するプロセスであり、通常はユーザー名、パスワードなどの資格情報を使用します |
| **[承認](threat-modeling-tool-authorization.md)** | 何を実行できるか 承認は、アプリケーションがリソースや操作に対するアクセス制御を提供する方法です |
| **[通信のセキュリティ](threat-modeling-tool-communication-security.md)** | 通信相手はだれか。 通信のセキュリティでは、行われるすべての通信が可能な限り保護されることが保証されます |
| **[構成管理](threat-modeling-tool-configuration-management.md)** | アプリケーションがだれとして実行されるか。 どのデータベースに接続するか。 アプリケーションはどのように管理されるか。 これらの設定はどのように保護されるか。 構成管理とは、アプリケーションがこれらの操作上の問題を扱う方法のことです |
| **[暗号化](threat-modeling-tool-cryptography.md)** | どのように秘密を保持するか (機密性)。 データまたはライブラリを改ざんからどのように保護するか (整合性)。 暗号的に強固である必要があるランダムな値のシードをどのように指定するか。 暗号化とは、アプリケーションが機密性と整合性を強制的に確保する方法のことです |
| **[例外管理](threat-modeling-tool-exception-management.md)** | アプリケーションで呼び出したメソッドが失敗した場合、アプリケーションはどうするか。 どの程度公開するか。 エンド ユーザーにわかりやすいエラー情報を返すか。 呼び出し元に有益な例外情報を戻すか。 アプリケーションの失敗時に正常に終了させるか。 |
| **[入力検証](threat-modeling-tool-input-validation.md)** | アプリケーションが受け取る入力が有効で安全であることをどうやって知ることができるか。 入力の検証とは、処理を進める前にアプリケーションが入力をフィルター処理、スクラブ、または拒否する方法のことです。 エントリ ポイントで入力を制限し、エグジット ポイントで出力をエンコードすることを検討してください。 データベースやファイル共有などのソースからのデータを信頼するか。 |
| **[機微なデータ](threat-modeling-tool-sensitive-data.md)** | アプリケーションが機微なデータをどのように処理するか。 機微なデータとは、メモリ内、ネットワーク上、または固定ストア内で保護する必要があるデータをアプリケーションが処理する方法のことです |
| **[セッションの管理](threat-modeling-tool-session-management.md)** | アプリケーションがユーザー セッションをどのように処理および保護するか。 セッションとは、ユーザーと Web アプリケーション間の一連の関連するやり取りのことです |

これにより、以下のことを特定できます。

* 最も一般的な間違いがどこで起きるか
* 最も実践的な機能強化をどこで行うことができるか

そのため、これらのカテゴリを使用して、セキュリティに関連する作業の対象と優先順位を定めることができます。最も一般的なセキュリティの問題が入力の検証、認証、および承認のカテゴリで発生することが判明した場合は、そこから作業を開始できます。 詳細については、**[このブログ投稿](https://www.google.com/patents/US7818788)** を参照してください。

## <a name="next-steps"></a>次のステップ

「**[Threat Modeling Tool Threats](threat-modeling-tool-threats.md)**」(Threat Modeling Tool の脅威) に進み、設計上の脅威の可能性を生成するときにツールで使用される脅威のカテゴリの詳細について学びます。