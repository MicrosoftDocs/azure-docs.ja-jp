---
title: Azure AD 多要素認証のデータ所在地
description: Azure AD 多要素認証によってお客様とユーザーに関するどのような個人および組織データが格納され、どのようなデータが元の国/地域内に残されるかについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561466"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD 多要素認証のデータ所在地と顧客データ

Azure Active Directory (Azure AD) は、Microsoft 365 や Azure などの Microsoft オンライン サービスをサブスクライブするときに組織が提供する住所に基づいて、地理的な場所に顧客データを保存します。 ご自身の顧客のデータがどこに保存されるかについては、Microsoft トラスト センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

クラウドベースの Azure AD 多要素認証と Azure AD Multifactor Authentication Server では、個人データと組織データが処理されて格納されます。 この記事では、どのようなデータがどこに保存されるのかについて、概要を説明します。

Azure AD 多要素認証サービスには、米国、ヨーロッパ、アジア太平洋にデータセンターがあります。 次のアクティビティは、特に明記されている場合を除き、リージョンのデータセンターから生じたものです。

* 多要素認証通話は、米国のデータセンターから発信され、グローバル プロバイダーによってルーティングされます。
* 他のリージョンからの汎用ユーザー認証要求は、現在、ユーザーの場所に基づいて処理されています。
* Microsoft Authenticator アプリを使用したプッシュ通知は、現在、ユーザーの場所に基づいて、リージョンのデータセンターで処理されています。 Apple プッシュ通知サービスなどのベンダー固有のデバイス サービスは、ヨーロッパの外部にある可能性があります。

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多要素認証によって格納される個人データ

個人データは、特定のユーザーに関連付けられているユーザー レベルの情報です。 次のデータ ストアには、個人情報が含まれています。

* ブロックされたユーザー
* バイパスされたユーザー
* Microsoft Authenticator デバイス トークン変更要求
* 多要素認証アクティビティ レポート
* Microsoft Authenticator のアクティブ化

この情報は 90 日間保持されます。

Azure AD 多要素認証では、ユーザー名、電話番号、IP アドレスなどの個人データは記録されません。 ただし、 *UserObjectId* は、ユーザーへの認証試行を識別します。 ログ データは 30 日間保存されます。

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多要素認証によって格納されるデータ

Azure パブリック クラウド (Azure AD B2C 認証、NPS 拡張機能、および Windows Server 2016 または 2019 Active Directory フェデレーション サービス (AD FS) アダプターを除く) では、次の個人データが格納されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ     |
| 単方向 SMS                          | 多要素認証ログ     |
| 音声通話                           | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア<br/>ブロックされたユーザー (不正アクセスが報告された場合) |
| Microsoft Authenticator 通知 | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア<br/>ブロックされたユーザー (不正アクセスが報告された場合)<br/>Microsoft Authenticator デバイス トークン変更時の変更要求 |

Microsoft Azure Government、Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Azure AD B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターの場合、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア |
| 単方向 SMS                          | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア |
| 音声通話                           | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア<br/>ブロックされたユーザー (不正アクセスが報告された場合) |
| Microsoft Authenticator 通知 | 多要素認証ログ<br/>多要素認証アクティビティ レポート データ ストア<br/>ブロックされたユーザー (不正アクセスが報告された場合)<br/>Microsoft Authenticator デバイス トークン変更時の変更要求 |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Azure Multifactor Authentication Server によって格納されるデータ

Azure Multifactor Authentication Server を使用すると、次の個人データが格納されます。

> [!IMPORTANT]
> 2019 年 7 月 1 日時点で、Microsoft では、新しいデプロイに対して Multifactor Authentication Server を提供しません。 ユーザーへの多要素認証を必要とする新しいお客様は、クラウドベースの Azure AD 多要素認証をご利用ください。 2019 年 7 月 1 日より前に Multifactor Authentication Server をアクティブ化した既存のお客様は、最新バージョンおよび更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ<br />多要素認証アクティビティ レポート データ ストア |
| 単方向 SMS                          | 多要素認証ログ<br />多要素認証アクティビティ レポート データ ストア |
| 音声通話                           | 多要素認証ログ<br />多要素認証アクティビティ レポート データ ストア<br />ブロックされたユーザー (不正アクセスが報告された場合) |
| Microsoft Authenticator 通知 | 多要素認証ログ<br />多要素認証アクティビティ レポート データ ストア<br />ブロックされたユーザー (不正アクセスが報告された場合)<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD 多要素認証によって格納される組織データ

組織データは、構成または環境のセットアップが公開される可能性がある、テナントレベルの情報です。 次に示す Azure portal の多要素認証ページにあるテナント設定には、着信電話認証要求のロックアウトしきい値や発信者 ID 情報など、組織データが格納される場合があります。

* アカウントのロックアウト
* 不正アクセスのアラート
* 通知
* 電話の設定

Azure Multifactor Authentication Server の場合、次の Azure portal ページに組織データが含まれている可能性があります。

* サーバーの設定
* ワンタイム バイパス
* キャッシュ規則
* Multifactor Authentication Server の状態

## <a name="multifactor-authentication-logs-location"></a>多要素認証ログの場所

次の表に、パブリック クラウドのサービス ログの場所を示します。

| パブリック クラウド| サインイン ログ | 多要素認証アクティビティ レポート        | 多要素認証サービス ログ       |
|-------------|--------------|----------------------------------------|------------------------|
| United States          | United States           | United States                                     | United States                     |
| ヨーロッパ      | ヨーロッパ       | United States                                     | ヨーロッパ <sup>2</sup>    |
| オーストラリア   | オーストラリア    | 米国<sup>1</sup>                         | オーストラリア <sup>2</sup> |

<sup>1</sup>OATH コードのログはオーストラリアに格納されます。

<sup>2</sup>音声通話の多要素認証サービスのログは米国に格納されます。

次の表に、ソブリン クラウドのサービス ログの場所を示します。

| ソブリン クラウド                      | サインイン ログ                         | 多要素認証アクティビティ レポート (個人データを含む)| 多要素認証サービス ログ |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | ドイツ                              | United States                            | United States               |
| Azure China 21Vianet                 | 中国                                | United States                            | United States               |
| Microsoft Government Cloud           | United States                                   | United States                            | United States               |

多要素認証アクティビティ レポートには、ユーザー プリンシパル名 (UPN) や完全な電話番号などの個人データが含まれます。

多要素認証サービス ログは、サービスを運用するために使用されます。

## <a name="next-steps"></a>次のステップ

クラウドベースの Azure AD 多要素認証と Azure Multifactor Authentication Server によって収集されるユーザー情報の詳細については、[Azure AD 多要素認証によるユーザー データの収集](howto-mfa-reporting-datacollection.md)に関するページを参照してください。
