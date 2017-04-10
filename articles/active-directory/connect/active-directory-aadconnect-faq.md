---
title: 'Azure Active Directory Connect: FAQ | Microsoft Docs'
description: "このページでは、Azure AD Connect についてよく寄せられる質問を紹介します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 27cc51d3f9220756fc1188f978dc158f17037bc3
ms.lasthandoff: 03/30/2017


---
# <a name="frequently-asked-questions-for-azure-active-directory-connect"></a>Azure Active Directory Connect についてよく寄せられる質問

## <a name="general-installation"></a>一般的なインストール
**Q: Azure AD のグローバル管理者が 2FA を有効化している場合、インストールを実行できますか。**  
2016 年 2 月以降のビルドでは、これがサポートされています。

**Q: Azure AD Connect を無人インストールする方法はありますか。**  
インストール ウィザードを使用して Azure AD Connect をインストールする場合にのみサポートされます。 サイレント モードでの無人インストールはサポートされていません。

**Q: ドメインに接続できないフォレストがあります。Azure AD Connect をインストールにはどうすればよいですか。**  
2016 年 2 月以降のビルドでは、これがサポートされています。

**Q: AD DS の正常性エージェントはサーバー コアで稼働しますか。**  
はい。 エージェントをインストールした後、次の PowerShell コマンドレットを使用して登録プロセスを実行できます。 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>ネットワーク
**Q: ファイアウォールやネットワーク デバイスなど、ネットワーク上で接続を開ける最大時間を制限するものがあります。Azure AD Connect を使用する場合、クライアント側のタイムアウトしきい値はどのくらいにすればいいでしょうか。**  
すべてのネットワーク ソフトウェアや物理デバイスなど、接続を開ける最大時間を制限するものは、Azure AD Connect クライアントがインストールされているサーバーと Azure Active Directory 間の接続に対して少なくとも 5 分 (300 秒) のしきい値を使用する必要があります。 これは、以前リリースされたすべての Microsoft Identity 同期ツールにも適用されます。

**Q: SLD (シングル ラベル ドメイン) はサポートされていますか。**  
いいえ、Azure AD Connect は、SLD を使用するオンプレミスのフォレスト/ドメインはサポートしていません。

**Q: 「ドット形式」の NetBios 名はサポートされていますか。**  
いいえ、Azure AD Connect では、NetBios 名にピリオド (.) が含まれているオンプレミスのフォレスト/ドメインはサポートしていません。

## <a name="federation"></a>フェデレーション
**Q: Office 365 の証明書を更新するように求める電子メールを受け取った場合はどうすればいいですか。**  
[証明書の更新](active-directory-aadconnect-o365-certs.md) に関する記事に記載されているガイダンスに従って、証明書を更新してください。

**Q: O365 証明書利用者の "証明書利用者の自動更新" を設定しました。トークン署名証明書が自動的にロールオーバーされるときに、何か必要な操作はありますか。**  
[証明書の更新](active-directory-aadconnect-o365-certs.md)に関する記事に記載されているガイダンスに従ってください。

## <a name="environment"></a>環境
**Q: Azure AD Connect のインストール後のサーバー名の変更はサポートされていますか。**  
いいえ。 サーバー名を変更すると、同期エンジンが SQL データベースに接続できなくなり、サービスを開始できなくなります。

## <a name="identity-data"></a>ID データ
**Q: Azure AD の UPN (userPrincipalName) 属性がオンプレミスの UPN と一致しません。なぜでしょうか。**  
次の記事を参照してください。

* [Office 365、Azure、Intune におけるユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://support.microsoft.com/en-us/kb/2523192)
* [異なるフェデレーション ドメインを使用するようにユーザー アカウントの UPN を変更した後、Azure Active Directory 同期ツールによって変更が同期されない](https://support.microsoft.com/en-us/kb/2669550)

また、「 [Azure AD Connect 同期サービスの機能](active-directory-aadconnectsyncservice-features.md)」に記載された手順に従って、同期エンジンによる userPrincipalName の更新が許可されるように Azure AD を構成することもできます。

**Q: オンプレミスの AD グループ/連絡先オブジェクトと既存の Azure AD グループ/連絡先オブジェクトとのあいまい一致はサポートされていますか。**  
いいえ。現在これはサポートされていません。

**Q: 既存の Azure AD グループ/連絡先オブジェクトに対して手動で設定された ImmutableId 属性とオンプレミスの AD グループ/連絡先オブジェクトとの完全一致はサポートされていますか。**  
いいえ。現在これはサポートされていません。



## <a name="custom-configuration"></a>カスタム構成
**Q: Azure AD Connect 用の PowerShell コマンドレットのドキュメントはどこにありますか。**  
このサイトに記載されているコマンドレットを除き、Azure AD Connect で使用されている PowerShell コマンドレットは、ユーザーによる使用をサポートしていません。

***Q:*Synchronization Service Manager** の [サーバーのエクスポート/インポート] を使用して、サーバー間で構成を移動できますか。  
いいえ。 このオプションはすべての構成設定を取得しないため、使用すべきではありません。 代わりに、2 台目のサーバーでウィザードを使用して基本構成を作成し、同期ルール エディターを使用して PowerShell スクリプトを生成し、サーバー間でカスタム ルールを移動してください。 「[スウィング移行](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)」を参照してください。

**Q: Azure サインイン ページではパスワードがキャッシュされますか。また、パスワード入力要素のオートコンプリート属性を "false" に設定することで、これを防ぐことはできますか。**</br>
現在、オートコンプリート タグを含め、パスワード入力フィールドの HTML 属性を変更することはできません。 現在、パスワード フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。 この機能は、2017 年後半に提供される予定です。

**Q: Azure サインイン ページでは、以前正常にサインインしたユーザーのユーザー名が表示されますか。また、この動作は無効にできますか。**</br>
現在、サインイン ページの HTML 属性を変更することはできません。 現在、パスワード フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。 この機能は、2017 年後半に提供される予定です。

**Q: 同時セッションを防ぐ方法はありますか。**</br>
いいえ。



## <a name="troubleshooting"></a>トラブルシューティング
**Q: Azure AD Connect に関するヘルプを参照する方法を教えてください。**

[Microsoft サポート技術情報 (KB) の検索](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Azure AD Connect のサポートに関する一般的な破損時補償の技術的な解決策について、Microsoft サポート技術情報 (KB) を検索してください。

[Microsoft Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* このコミュニティで技術的な質問と回答を検索して参照したり、 [こちら](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)をクリックして独自の質問を行ったりできます。

[Azure AD Connect のカスタマー サポート](https://manage.windowsazure.com/?getsupport=true)

* このリンクを使用して、Azure Portal からサポートを受けることができます。


