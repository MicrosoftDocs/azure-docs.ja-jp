---
title: Azure Multi-Factor Authentication のデータの保存場所
description: Azure Multi-Factor Authentication でご自身とご自身のユーザーに関してどのような個人情報と組織データが保存されているか、および発生国/地域にどのようなデータが残っているかを説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4b15311dfefecd9a533add9c5a028a9b7b22fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87051161"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のデータの保存場所と顧客データ

Office 365 や Azure などの Microsoft オンライン サービスにサブスクライブするとき、ご自身の組織によって提供されるアドレスに基づいて、Azure AD により顧客データが地理的な場所に保存されます。 ご自身の顧客のデータがどこに保存されるかについては、Microsoft トラスト センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

クラウドベースの Azure Multi-Factor Authentication と Azure Multi-Factor Authentication Server では、一定量の個人データと組織データが処理され、保存されます。 この記事では、どのようなデータがどこに保存されるのかについて、概要を説明します。

現在、次の Multi-Factor Authentication アクティビティは、明記されている場合を除き、米国のデータセンターから発信されます。

* 電話または SMS を使用する 2 要素認証は、通常、米国のデータセンターから発信され、グローバル プロバイダーによってルーティングされます。
    * ヨーロッパやオーストラリアなどの他のリージョンからの汎用ユーザー認証要求は、現在そのリージョン内のデータセンターによって処理されています。 セルフサービスによるパスワードのリセット、Azure B2C イベントのほか、NPS 拡張機能または AD FS アダプターを使用したハイブリッド シナリオなどのその他のイベントは、現在すべて米国のデータセンターによって処理されています。
* Microsoft Authenticator アプリを使用するプッシュ通知は、米国内データセンターから発信されます。 さらに、デバイス ベンダー固有のサービスが機能している場合があり、それらのサービスはさまざまなリージョンから提供される場合があります。
* OATH コードは現在、通常は米国内で検証されています。
    * ここでも、ヨーロッパやオーストラリアなどの他のリージョンで発生した汎用ユーザー認証イベントは、そのリージョン内のデータセンターによって処理されます。 現在、米国のデータセンターでは、追加のイベントが処理されています。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication によって保存される個人データ

個人データは、特定のユーザーに関連付けられているユーザー レベルの情報です。 次のデータ ストアには、個人情報が含まれています。

* ブロックされたユーザー
* バイパスされたユーザー
* Microsoft Authenticator デバイス トークン変更要求
* Multi-Factor Authentication アクティビティ レポート
* Microsoft Authenticator のアクティブ化

この情報は 90 日間保持されます。

Azure Multi-Factor Authentication では、ユーザー名、電話番号、IP アドレスなどの個人データは記録されませんが、Multi-Factor Authentication での試行をユーザーに対して識別する *UserObjectId* があります。 ログ データは 30 日間保存されます。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Azure のパブリック クラウドでは、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターを除き、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内     |
| 単方向 SMS                          | Multi-Factor Authentication のログ内     |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

> [!NOTE]
> Multi-Factor Authentication アクティビティ レポート データ ストアは、認証要求を処理するリージョンに関係なく、すべてのクラウドについて米国内に保存されます。 Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Microsoft Government Cloud には、パブリック クラウド リージョンのデータ ストアとは別の独立したデータ ストアがありますが、このデータは常に米国内に保存されます。

Microsoft Azure Government、Microsoft Azure Germany、21Vianet が運営する Microsoft Azure、Azure B2C 認証、NPS 拡張機能、Windows Server 2016 または 2019 AD FS アダプターの場合、次の個人データが保存されます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 単方向 SMS                          | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

Azure Multi-Factor Authentication Server をデプロイして実行すると、次の個人データが保存されます。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft から新しいデプロイに対して Multi-Factor Authentication Server はもう提供されません。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に Multi-Factor Authentication Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

| イベントの種類                           | データ ストアの種類 |
|--------------------------------------|-----------------|
| OATH トークン                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 単方向 SMS                          | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア |
| 音声通話                           | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー |
| Microsoft Authenticator 通知 | Multi-Factor Authentication のログ内<br />Multi-Factor Authentication アクティビティ レポート データ ストア<br />不正アクセスが報告された場合にブロックされたユーザー<br />Microsoft Authenticator デバイス トークン変更時の変更要求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication によって保存される組織のデータ

組織のデータは、構成または環境のセットアップが公開される可能性がある、テナントレベルの情報です。 次に示す Azure portal の Multi-Factor Authentication ページにあるテナント設定には、着信電話認証要求のロックアウトしきい値や発信者 ID 情報などの組織のデータが保存される場合があります。

* アカウントのロックアウト
* 不正アクセスのアラート
* 通知
* 電話の設定

また、Azure Multi-Factor Authentication Server の場合、次の Azure portal ページに組織のデータが含まれている可能性があります。

* サーバーの設定
* ワンタイム バイパス
* キャッシュ規則
* Multi-Factor Authentication Server の状態

## <a name="log-data-location"></a>ログ データの場所

ログ情報が保存される場所は、ログ情報がどのリージョンで処理されるかによって異なります。 ほとんどの地域にはネイティブの Azure Multi-Factor Authentication 機能があるため、ログ データは、Multi-Factor Authentication 要求を処理するのと同じリージョンに保存されます。 ネイティブの Azure Multi-Factor Authentication がサポートされていない地域では、米国またはヨーロッパのどちらかの地域によってサービスが提供され、ログ データは Multi-Factor Authentication 要求を処理するのと同じリージョンに保存されます。

一部の主要な認証ログ データは、米国内にのみ保存されます。 Microsoft Azure Germany と、21Vianet が運営する Microsoft Azure は、常にそれぞれのクラウドに保存されます。 Microsoft Government Cloud のログ データは、常に米国内に保存されます。

## <a name="next-steps"></a>次のステップ

クラウドベースの Azure Multi-Factor Authentication Server によって収集されるユーザー情報の詳細については、「[Azure Multi-Factor Authentication によるユーザー データの収集](howto-mfa-reporting-datacollection.md)」を参照してください。
