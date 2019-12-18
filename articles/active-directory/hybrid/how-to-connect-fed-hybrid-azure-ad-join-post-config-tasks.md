---
title: Azure AD Connect:Hybrid Azure AD 参加の構成後タスク | Microsoft Docs
description: このドキュメントでは、ハイブリッド Azure AD 参加を完了するために必要な構成後のタスクについて詳しく説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70e3267792f27a170efa26cc4267d1b25045a099
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231242"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成後のタスク

Azure AD Connect を実行してハイブリッド Azure AD 参加用に組織を構成した後は、そのセットアップを仕上げるために完了する必要のある追加手順がいくつかあります。  お使いのデバイスに適用される手順のみを実行します。

## <a name="1-configure-controlled-rollout-optional"></a>1.制御されたロールアウトを構成する (省略可能)
Windows 10 および Windows Server 2016 を搭載するすべてのドメイン参加済みデバイスは、すべての構成手順が完了すると、Azure AD に自動的に登録します。 この自動登録ではなく、制御されたロールアウトの方がよい場合は、グループ ポリシーを使って、自動ロールアウトを選択的に有効または無効にすることができます。  このグループ ポリシーは、他の構成手順を開始する前に設定する必要があります。
* Active Directory でグループ ポリシー オブジェクトを作成します。
* グループ ポリシーの名前を設定します (例: ハイブリッド Azure AD 参加)。
* 編集して、移動します:[コンピューターの構成] > [ポリシー] > [管理用テンプレート] > [Windows コンポーネント] > [デバイス登録]。

>[!NOTE]
>2012R2 の場合、ポリシーの設定は **[コンピューターの構成] > [ポリシー] > [管理用テンプレート] > [Windows コンポーネント] > [社内参加] > [Automatically workplace join client computers]\(クライアント コンピューターを自動的に社内参加させる\)** にあります。

* この設定を有効にします:ドメインに参加しているコンピューターをデバイスとして登録します。
* 適用して [OK] をクリックします。
* GPO を任意の場所 (組織単位、セキュリティ グループ、またはすべてのデバイスのドメイン) にリンクします。

## <a name="2-configure-network-with-device-registration-endpoints"></a>2.デバイス登録エンドポイントとのネットワークを構成する
Azure AD への登録のため、組織ネットワーク内のコンピューターから次の URL にアクセスできることを確認します。

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3.Windows 10 デバイスに WPAD を実装する
送信プロキシ経由でインターネットにアクセスする組織の場合は、Windows 10 コンピューターを Azure AD に登録できるように、Web プロキシ自動発見 (WPAD) を実装します。

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4.Azure AD Connect によって構成されなかったフォレストに SCP を構成する 

サービス接続ポイント (SCP) には、デバイスによって自動登録用に使われる Azure AD テナントの情報が含まれます。  Azure AD Connect からダウンロードした PowerShell スクリプト ConfigureSCP.ps1 を実行します。

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5.Azure AD Connect によって構成されなかったフェデレーション サービスを構成する

組織がフェデレーション サービスを使って Azure AD にサインインしている場合は、Azure AD の証明書利用者信頼の要求規則で、デバイスの認証を許可する必要があります。 AD FS でフェデレーションを使っている場合は、[AD FS のヘルプ](https://aka.ms/aadrptclaimrules)を参考にして要求規則を生成します。 Microsoft 以外のフェデレーション ソリューションを使っている場合は、ガイダンスについてプロバイダーに問い合わせてください。  

>[!NOTE]
>ダウンレベルの Windows デバイスがある場合は、サービスは Azure AD 信頼への要求を受け取るときに、authenticationmethod および wiaormultiauthn 要求の発行をサポートする必要があります。 AD FS では、この認証方法をパススルーする発行変換規則を追加する必要があります。

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6.ダウンレベルの Windows デバイスに対して Azure AD シームレス SSO を有効にする

組織が Azure AD へのサインインにパスワード ハッシュ同期またはパススルー認証を使っている場合は、ダウンレベルの Windows デバイスを認証するため、そのサインイン方法で Azure AD シームレス SSO を有効にします。 https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso をご覧ください。 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7.ダウンレベルの Windows デバイスに対する Azure AD ポリシーを設定する

ダウンレベルの Windows デバイスを登録するには、Azure AD のポリシーにおいてユーザーがデバイスを登録できるようになっている必要があります。 

* Azure portal でアカウントにログインします。
* 参照先:[Azure Active Directory] > [デバイス] > [デバイスの設定]
* [ユーザーはデバイスを Azure AD に登録できます] を [すべて] に設定します。
* [保存] をクリックします。

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8.ダウンレベルの Windows デバイスに Azure AD エンドポイントを追加する

デバイスの認証時に証明書の指定を求めるメッセージが表示されないように、ダウンレベルの Windows デバイス上のローカル イントラネット ゾーンに、Azure AD デバイス認証エンドポイントを追加します。 https://device.login.microsoftonline.com 

[シームレス SSO](how-to-connect-sso.md) を使っている場合は、そのゾーンで [スクリプトを介したステータス バーの更新を許可する] も有効にして、次のエンドポイントを追加します。 https://autologon.microsoftazuread-sso.com 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9.ダウンレベルの Windows デバイスに Microsoft Workplace Join をインストールする

インストーラーは、ユーザーのコンテキストで実行するデバイス システムにスケジュールされたタスクを作成します。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 このタスクは、統合 Windows 認証を使用して認証した後、ユーザーの資格情報を使用して、デバイスを Azure AD に参加させます。 ダウンロード センターは https://www.microsoft.com/download/details.aspx?id=53554 にあります。 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10.デバイスの登録を許可するグループ ポリシーを構成する

* Active Directory でグループ ポリシー オブジェクトを作成します (まだ作成されていない場合)。
* グループ ポリシーの名前を設定します (例: ハイブリッド Azure AD 参加)。
* 編集と移動:[コンピューターの構成] > [ポリシー] > [管理用テンプレート] > [Windows コンポーネント] > [デバイス登録]
* 有効にする:ドメインに参加しているコンピューターをデバイスとして登録します
* 適用して [OK] をクリックします。
* GPO を任意の場所 (組織単位、セキュリティ グループ、またはすべてのデバイスのドメイン) にリンクします。

>[!NOTE]
>2012R2 の場合、ポリシーの設定は **[コンピューターの構成] > [ポリシー] > [管理用テンプレート] > [Windows コンポーネント] > [社内参加] > [Automatically workplace join client computers]\(クライアント コンピューターを自動的に社内参加させる\)** にあります。

## <a name="next-steps"></a>次の手順
[デバイス ライトバックの構成](how-to-connect-device-writeback.md)
