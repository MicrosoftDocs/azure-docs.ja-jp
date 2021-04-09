---
title: MFA サーバーの構成 - Azure Active Directory
description: Azure portal で Azure MFA サーバーの設定を構成する方法の詳細
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689047"
---
# <a name="configure-mfa-server-settings"></a>MFA Server 設定の構成

この記事は、Azure portal で Azure MFA Server の設定を管理するうえで役立ちます。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure AD Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

次の MFA Server の設定を使用できます。

| 特徴量 | 説明 |
| ------- | ----------- |
| サーバーの設定 | MFA サーバーをダウンロードして、環境を初期化するためのアクティブ化資格情報を生成します |
| [ワンタイム バイパス](#one-time-bypass) | 期間限定で、多要素認証を実行することなくユーザーを認証できるようにします。 |
| [キャッシュ規則](#caching-rules) |  キャッシュは主に、VPN などのオンプレミス システムによって複数の認証要求が送信され、最初の要求がまだ処理中である場合に使用されます。 この機能を使用すれば、処理中の最初の認証にユーザーが成功した後で、後続の要求が自動的に処理されるようになります。 |
| サーバーの状態 | バージョン、状態、IP、最後の通信日時など、オンプレミスの MFA サーバーの状態を表示します。 |

## <a name="one-time-bypass"></a>ワンタイム バイパス

ワンタイム バイパス機能は、多要素認証の実行なしでユーザーを 1 回だけ認証できるようにします。 このバイパスは一時的なものであり、指定された秒数が経過すると無効になります。 モバイル アプリまたは電話で通知または通話に対応しない状況でも、ワンタイム バイパスを使用することで、ユーザーは目的のリソースにアクセスできるようになります。

ワンタイム バイパスを作成するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
1. **[Azure Active Directory]** を探して選択してから、 **[セキュリティ]**  >  **[MFA]**  >  **[ワンタイム バイパス]** の順に選択します。
1. **[追加]** を選択します。
1. 必要に応じて、バイパスのレプリケーション グループを選択します。
1. ユーザー名を「`username@domain.com`」のように入力します。 バイパスが持続する秒数と、バイパスする理由を入力します。
1. **[追加]** を選択します。 制限時間はすぐに有効になります。 ユーザーはワンタイム バイパスの有効期限が切れる前にサインインする必要があります。

この同じウィンドウから、ワンタイム バイパス レポートを表示することもできます。

## <a name="caching-rules"></a>キャッシュ規則

_キャッシュ_ 機能を使用して、ユーザー認証後の認証の試みを許可する期間を設定することができます。 指定した期間内のユーザーの後続の認証の試みは自動的に成功します。

キャッシュは主に、VPN などのオンプレミス システムによって複数の認証要求が送信され、最初の要求がまだ処理中である場合に使用されます。 この機能を使用すれば、処理中の最初の認証にユーザーが成功した後で、後続の要求が自動的に処理されるようになります。

>[!NOTE]
> キャッシュ機能は Azure Active Directory (Azure AD) へのサインインに使用するためのものではありません。

キャッシュを設定するには、次の手順を実行します。

1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[MFA]**  >  **[キャッシュ規則]** の順に移動します。
1. **[追加]** を選択します。
1. ドロップダウン リストから **キャッシュの種類** を選択します。 **キャッシュする秒数** の最大数を入力します。
1. 必要に応じて、認証の種類を選択し、アプリケーションを指定します。
1. **[追加]** を選択します。

## <a name="next-steps"></a>次のステップ

MFA サーバーのその他の構成オプションは、MFA サーバー自体の Web コンソールから設定できます。 また、[高可用性のために Azure MFA サーバーを構成する](howto-mfaserver-deploy-ha.md)こともできます。
