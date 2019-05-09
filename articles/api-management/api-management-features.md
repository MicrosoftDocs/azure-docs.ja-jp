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
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156699"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API Management レベルの機能に基づく比較

それぞれの API Management [価格レベル](https://aka.ms/apimpricing)には、機能セットとユニットごとの[容量](api-management-capacity.md)に違いがあります。 次の表は、各レベルで使用できる主な機能をまとめたものです。 一部の機能は、動作が異なります。レベルによって機能が異なる場合もあります。 このような場合、個々の機能を説明するドキュメント記事に相違点が記載されています。

| 機能                                                                                      | 従量課金<sup>プレビュー</sup> | 開発者      | Basic          | 標準       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD 統合<sup>1</sup>                                                             | いいえ                             | はい            | いいえ              | 可能             | はい            |
| Virtual Network (VNet) のサポート                                                               | いいえ                             | はい            | いいえ              | いいえ              | はい            |
| 複数リージョンのデプロイ                                                                      | いいえ                             | いいえ              | いいえ              | いいえ              | はい            |
| 複数のカスタム ドメイン名                                                                 | いいえ                             | いいえ              | いいえ              | いいえ              | はい            |
| 開発者ポータル<sup>2</sup>                                                                 | いいえ                             | 可能             | はい            | はい            | はい            |
| ビルトイン キャッシュ                                                                               | いいえ                             | 可能             | はい            | はい            | はい            |
| ビルトイン分析                                                                           | いいえ                             | 可能             | はい            | はい            | はい            |
| [SSL 設定](api-management-howto-manage-protocols-ciphers.md)                             | いいえ                             | 可能             | はい            | はい            | はい            |
| [外部キャッシュ](https://aka.ms/apimbyoc)                                                    | はい                           | いいえ<sup>3</sup> | いいえ<sup>3</sup> | いいえ<sup>3</sup> | いいえ<sup>3</sup> |
| [クライアント証明書認証](api-management-howto-mutual-certificates-for-clients.md) | いいえ<sup>4</sup>                | はい            | はい            | はい            | はい            |
| [バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)               | いいえ                             | 可能             | はい            | はい            | はい            |
| [Git による管理](api-management-configuration-repository-git.md)                        | いいえ                             | 可能             | はい            | はい            | はい            |
| ダイレクト管理 API                                                                        | いいえ                             | 可能             | はい            | はい            | はい            |
| Azure Monitor のログとメトリック                                                               | いいえ<sup>5</sup>                | はい            | はい            | はい            | はい            |

<sup>1</sup> ユーザーが開発者ポータルにサインインする際に、ID プロバイダーとして Azure AD (および Azure AD B2C) を使用できます。<br/>
<sup>2</sup> 関連機能 (例: ユーザー、グループ、問題、アプリケーション、電子メール テンプレートと通知) を含みます。<br/>
<sup>3</sup> このレベルの外部キャッシュのサポートは、近日中に対応される予定です。<br/>
<sup>4</sup> 従量課金レベルのクライアント証明書認証は、一般公開前に追加される予定です。<br/>
<sup>5</sup> 従量課金レベルの Azure Monitor の完全サポートは、今後追加される予定です。
