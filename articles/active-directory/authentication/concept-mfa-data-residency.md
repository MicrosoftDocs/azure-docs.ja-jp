---
title: Azure AD Multifactor Authentication のデータ所在地
description: Azure AD Multifactor Authentication によってお客様とユーザーに関するどのような個人および組織データが格納され、どのようなデータが元の国/地域内に残されるかについて説明します。
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
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208354"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication のデータ所在地と顧客データ

Microsoft 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、顧客データは Azure AD により、組織によって提供されたアドレスに基づく地理的な場所に格納されます。 ご自身の顧客のデータがどこに保存されるかについては、Microsoft トラスト センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

クラウドベースの Azure AD Multifactor Authentication と Azure AD Multifactor Authentication Server では、一定量の個人データと組織データが処理されて格納されます。 この記事では、どのようなデータがどこに保存されるのかについて、概要を説明します。

Azure AD Multifactor Authentication サービスには、米国、ヨーロッパ、アジア太平洋にデータセンターがあります。 次のアクティビティは、特に明記されている場合を除き、リージョンのデータセンターから生じたものです。

* 電話を使用する多要素認証は、米国のデータセンターから発信され、グローバル プロバイダーによってルーティングされます。
* ヨーロッパやオーストラリアなどの他のリージョンからの汎用ユーザー認証要求は、現在ユーザーの場所に基づいて処理されています。
* Microsoft Authenticator アプリを使用したプッシュ通知は、現在、ユーザーの場所に基づいて、リージョンのデータセンターで処理されています。
    * Apple プッシュ通知などのデバイス ベンダー固有のサービスは、ヨーロッパの外部にある可能性があります。

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication によって格納される個人データ

個人データは、特定のユーザーに関連付けられているユーザー レベルの情報です。 次のデータ ストアには、個人情報が含まれています。

* ブロックされたユーザー
* バイパスされたユーザー
* Microsoft Authenticator デバイス トークン変更要求
* 多要素認証アクティビティ レポート
* Microsoft Authenticator のアクティブ化

この情報は 90 日間保持されます。

Azure AD Multifactor Authentication では、ユーザー名、電話番号、IP アドレスなどの個人データは記録されませんが、ユーザーに対する多要素認証の試行を識別する *UserObjectId* があります。 ログ データは 30 日間保存されます。

### <a name="azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication

Azure のパブリック クラウドでは、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターを除き、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ内     |
| 単方向 SMS                          | 多要素認証ログ内     |
| 音声通話                           | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

Microsoft Azure Government、Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターの場合、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア |
| 単方向 SMS                          | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア |
| 音声通話                           | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

### <a name="multifactor-authentication-server"></a>Multifactor Authentication Server

Azure AD Multifactor Authentication Server をデプロイして実行すると、次の個人データが格納されます。

> [!IMPORTANT]
> 2019 年 7 月 1 日時点で、Microsoft では、新しいデプロイに対して Multifactor Authentication Server を提供しません。 ユーザーの多要素認証を必要とする新しいお客様は、クラウドベースの Azure AD Multifactor Authentication をご利用ください。 7 月 1 日より前に Multifactor Authentication Server をアクティブ化した既存のお客様は、最新バージョンや今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成できます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア |
| 単方向 SMS                          | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア |
| 音声通話                           | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | 多要素認証ログ内<br />多要素認証アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication によって格納される組織データ

組織のデータは、構成または環境のセットアップが公開される可能性がある、テナントレベルの情報です。 次に示す Azure portal の多要素認証ページにあるテナント設定には、着信電話認証要求のロックアウトしきい値や発信者 ID 情報など、組織データが格納される場合があります。

* アカウントのロックアウト
* 不正アクセスのアラート
* 通知
* 電話の設定

また、Azure AD Multifactor Authentication Server の場合、次の Azure portal ページに組織データが含まれている可能性があります。

* サーバーの設定
* ワンタイム バイパス
* キャッシュ規則
* Multifactor Authentication Server の状態

## <a name="multifactor-authentication-logs-location"></a>多要素認証ログの場所

次の表に、パブリック クラウドのサービス ログの場所を示します。

| パブリック クラウド| サインイン ログ | 多要素認証アクティビティ レポート        | 多要素認証サービス ログ       |
|-------------|--------------|----------------------------------------|------------------------|
| US          | US           | US                                     | US                     |
| ヨーロッパ      | ヨーロッパ       | US                                     | ヨーロッパ <sup>2</sup>    |
| オーストラリア   | オーストラリア    | US<sup>1</sup>                         | オーストラリア <sup>2</sup> |

<sup>1</sup>OATH コードのログはオーストラリアに格納されます。

<sup>2</sup>音声通話の多要素認証サービスのログは米国に格納されます。

次の表に、ソブリン クラウドのサービス ログの場所を示します。

| ソブリン クラウド                      | サインイン ログ                         | 多要素認証アクティビティ レポート (個人データを含む)| 多要素認証サービス ログ |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | ドイツ                              | US                            | US               |
| 21Vianet によって運営される Microsoft Azure | 中国                                | US                            | US               |
| Microsoft Government Cloud           | US                                   | US                            | US               |

多要素認証アクティビティ レポートのデータには、ユーザー プリンシパル名 (UPN) や完全な電話番号などの個人データが含まれます。

多要素認証サービス ログは、サービスを運用するために使用されます。

## <a name="next-steps"></a>次の手順

クラウドベースの Azure AD Multifactor Authentication と Azure AD Multifactor Authentication Server によって収集されるユーザー情報の詳細については、[Azure AD Multifactor Authentication によるユーザー データの収集](howto-mfa-reporting-datacollection.md)に関するページを参照してください。
