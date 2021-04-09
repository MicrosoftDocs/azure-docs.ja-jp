---
title: Azure AD Connect - パススルー認証 - 認証エージェントのアップグレード | Microsoft Docs
description: この記事では、Azure Active Directory (Azure AD) のパススルー認証の構成をアップグレードする方法について説明します。
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973050"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory パススルー認証: 認証エージェント (プレビュー) のアップグレード

## <a name="overview"></a>概要

この記事では、プレビューで Azure AD のパススルー認証を使っているユーザーに向けたものです。 認証エージェント ソフトウェアは最近アップグレード (およびブランド名を変更) されました。 ユーザーは、オンプレミスのサーバーにインストールされているプレビューの認証エージェントを "_手作業で_" アップグレードする必要があります。 この手作業によるアップグレードは、1 回限りの操作です。 認証エージェントの将来の更新はすべて自動で行われます。 次のような理由でアップグレードを行う必要があります。

- プレビュー バージョンの認証エージェントは、セキュリティ修正またはバグ修正は受け取りません。
-   プレビュー バージョンの認証エージェントは、高可用性のために追加のサーバーにインストールできません。

## <a name="check-versions-of-your-authentication-agents"></a>認証エージェントのバージョンを確認する

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>ステップ 1: 認証エージェントがインストールされている場所を確認する

以下の手順に従って、認証エージェントがインストールされている場所を確認します。

1. テナントのグローバル管理者の資格情報を使って、[Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。
2. 左側のナビゲーションで、**[Azure Active Directory]** を選びます。
3. **[Azure AD Connect]** を選びます。 
4. **[パススルー認証]** を選択します。 このブレードには、認証エージェントがインストールされているサーバーが一覧表示されます。

![Azure Active Directory 管理センター - [パススルー認証] ブレード](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>ステップ 2: 認証エージェントのバージョンを確認する

前のステップでわかった各サーバーで、認証エージェントのバージョンを確認するには、次の手順のようにします。

1. オンプレミスのサーバーで **[コントロール パネル] -> [プログラム] -> [プログラムと機能]** に移動します。
2. **[Microsoft Azure AD Connect Authentication Agent]\(Microsoft Azure AD Connect 認証エージェント\)** のエントリがある場合、そのサーバーでは何もする必要はありません。
3. **[Microsoft Azure AD Application Proxy Connector]\(Microsoft Azure AD アプリケーション プロキシ コネクタ\)** のエントリがある場合は、そのサーバーを手作業でアップグレードする必要があります。

![認証エージェントのプレビュー バージョン](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>アップグレードを開始する前に従うベスト プラクティス

アップグレードの前に、次のことを行っておく必要があります。

1. **クラウド専用のグローバル管理者アカウントを作成する**: パススルー認証エージェントが正常に動作していない緊急の状況で使うクラウド専用のグローバル管理者アカウントを用意しないで、アップグレードを行わないでください。 クラウド専用のグローバル管理者アカウントを追加する手順については、[こちら](../fundamentals/add-users-azure-active-directory.md)をご覧ください。 これを実行することは欠かせない手順で、テナントからロックアウトされないようになります。
2.  **高可用性を確保する**: まだ行っていない場合、[こちらの説明](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)に従って、サインイン要求に高可用性を提供するための 2 番目のスタンドアロン認証エージェントをインストールします。

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Azure AD Connect サーバーの認証エージェントをアップグレードする

同じサーバー上の認証エージェントをアップグレードする前に、Azure AD Connect をアップグレードする必要があります。 プライマリとステージング両方の Azure AD Connect サーバーで、以下の手順のようにします。

1. **Azure AD Connect をアップグレードする**: [こちらの記事](how-to-upgrade-previous-version.md)に従って、最新バージョンの Azure AD Connect にアップグレードします。
2. **プレビュー バージョンの認証エージェントをアンインストールする**: [この PowerShell スクリプト](https://aka.ms/rmpreviewagent)をダウンロードし、サーバーで管理者として実行します。
3. **最新バージョン (1.5.389.0 以降) の認証エージェントをダウンロードする**: テナントのグローバル管理者の資格情報で [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。 **[Azure Active Directory] -> [Azure AD Connect] -> [パススルー認証] -> [エージェントのダウンロード]** の順に選びます。 [サービスの条項](https://aka.ms/authagenteula)を受け入れ、最新バージョンの認証エージェントをダウンロードします。 認証エージェントは[ここ](https://aka.ms/getauthagent)からダウンロードすることもできます。
4. **最新バージョンの認証エージェントをインストールする**: ステップ 3 でダウンロードした実行可能ファイルを実行します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
5. **最新バージョンがインストールされたことを確認する**: 前と同じように **[コントロール パネル] -> [プログラム] -> [プログラムと機能]** に移動し、**[Microsoft Azure AD Connect Authentication Agent]\(Microsoft Azure AD Connect 認証エージェント\)** のエントリがあることを確認します。

>[!NOTE]
>上記の手順を完了した後に [Azure Active Directory 管理センター](https://aad.portal.azure.com)の [パススルー認証] ブレードを見ると、各サーバーに **アクティブ** と **非アクティブ** の 2 つの認証エージェントのエントリがあることがわかります。 これは "_予期されること_" です。 **非アクティブ** のエントリは、数日後に自動的に削除されます。

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>他のサーバーの認証エージェントをアップグレードする

以下の手順のようにして、(Azure AD Connect がインストールされていない) 他のサーバーで認証エージェントをアップグレードします。

1. **プレビュー バージョンの認証エージェントをアンインストールする**: [この PowerShell スクリプト](https://aka.ms/rmpreviewagent)をダウンロードし、サーバーで管理者として実行します。
2. **最新バージョン (1.5.389.0 以降) の認証エージェントをダウンロードする**: テナントのグローバル管理者の資格情報で [Azure Active Directory 管理センター](https://aad.portal.azure.com)にサインインします。 **[Azure Active Directory] -> [Azure AD Connect] -> [パススルー認証] -> [エージェントのダウンロード]** の順に選びます。 サービスの条項に同意し、最新バージョンをダウンロードします。
3. **最新バージョンの認証エージェントをインストールする**: ステップ 2 でダウンロードした実行可能ファイルを実行します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
4. **最新バージョンがインストールされたことを確認する**: 前と同じように **[コントロール パネル] -> [プログラム] -> [プログラムと機能]** に移動し、**[Microsoft Azure AD Connect Authentication Agent]\(Microsoft Azure AD Connect 認証エージェント\)** というエントリがあることを確認します。

>[!NOTE]
>上記の手順を完了した後に [Azure Active Directory 管理センター](https://aad.portal.azure.com)の [パススルー認証] ブレードを見ると、各サーバーに **アクティブ** と **非アクティブ** の 2 つの認証エージェントのエントリがあることがわかります。 これは "_予期されること_" です。 **非アクティブ** のエントリは、数日後に自動的に削除されます。

## <a name="next-steps"></a>次のステップ
- [**トラブルシューティング**](tshoot-connect-pass-through-authentication.md) - この機能に関する一般的な問題を解決する方法を確認します。