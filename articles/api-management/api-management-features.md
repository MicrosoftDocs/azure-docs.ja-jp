---
title: Azure API Management レベルの機能に基づく比較 | Microsoft Docs
description: この記事では、提供される機能に基づいて API Management レベルを比較します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072531"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API Management レベルの機能に基づく比較

>[!IMPORTANT]
> Developer レベルは、非運用環境のユースケースと評価のためのものであることに注意してください。 SLA は提供されません。 

それぞれの API Management [価格レベル](https://aka.ms/apimpricing)には、機能セットとユニットごとの[容量](api-management-capacity.md)に違いがあります。 次の表は、各レベルで使用できる主な機能をまとめたものです。 一部の機能は、動作が異なります。レベルによって機能が異なる場合もあります。 このような場合、個々の機能を説明するドキュメント記事に相違点が記載されています。

| 機能                                                                                      | 消費 | 開発者      | Basic          | 標準       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD 統合<sup>1</sup>                                                             | いいえ                            | はい            | いいえ             | 可能            | はい            |
| Virtual Network (VNet) のサポート                                                               | いいえ                            | はい            | いいえ             | いいえ             | はい            |
| 複数リージョンのデプロイ                                                                      | いいえ                            | いいえ             | いいえ             | いいえ             | はい            |
| 複数のカスタム ドメイン名                                                                 | いいえ                            | いいえ             | いいえ             | いいえ             | はい            |
| 開発者ポータル<sup>2</sup>                                                                 | いいえ                            | 可能            | はい            | はい            | はい            |
| ビルトイン キャッシュ                                                                               | いいえ                            | 可能            | はい            | はい            | はい            |
| ビルトイン分析                                                                           | いいえ                            | 可能            | はい            | はい            | はい            |
| [SSL 設定](api-management-howto-manage-protocols-ciphers.md)                             | はい                            | はい            | はい            | はい            | はい            |
| [外部キャッシュ](https://aka.ms/apimbyoc)                                                    | はい                           | はい            | はい            | はい            | はい            |
| [クライアント証明書認証](api-management-howto-mutual-certificates-for-clients.md) | はい                | はい            | はい            | はい            | はい            |
| [バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)               | いいえ                            | 可能            | はい            | はい            | はい            |
| [Git による管理](api-management-configuration-repository-git.md)                        | いいえ                            | 可能            | はい            | はい            | はい            |
| ダイレクト管理 API                                                                        | いいえ                            | 可能            | はい            | はい            | はい            |
| Azure Monitor のログとメトリック                                                               | いいえ                | 可能            | はい            | はい            | はい            |
| 静的 IP                                                               | いいえ                | 可能            | はい            | はい            | はい            |

<sup>1</sup> ユーザーが開発者ポータルにサインインする際に、ID プロバイダーとして Azure AD (および Azure AD B2C) を使用できます。<br/>
<sup>2</sup> 関連機能 (例: ユーザー、グループ、問題、アプリケーション、電子メール テンプレートと通知) を含みます。<br/>
