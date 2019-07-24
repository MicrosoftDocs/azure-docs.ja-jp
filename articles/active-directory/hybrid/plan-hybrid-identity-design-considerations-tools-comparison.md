---
title: 'ハイブリッド ID: ディレクトリ統合ツールの比較 | Microsoft Docs'
description: このページでは、ディレクトリ統合に使用できる各種ディレクトリ統合ツールを包括的に比較した表を提供しています。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189117"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>ハイブリッド ID ディレクトリ統合ツールの比較
ディレクトリ統合ツールは、長年にわたって成長および進化してきました。  このドキュメントでは、このようなツールをまとめた表と、各ツールで使用できる機能の比較を示しています。

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect には、以前 Dirsync と AAD Sync としてリリースされたコンポーネントと機能が組み込まれています。これらのツールは、個別にリリースされなくなりました。今後の機能強化はすべて Azure AD Connect の更新プログラムに含まれるため、最新機能の入手場所はいつでもわかります。
> 
> DirSync と Azure AD Sync は非推奨となります。 詳しくは、 [こちら](reference-connect-dirsync-deprecated.md)を参照してください。
> 
> 

各表で使用されるキーは次のとおりです。

● = 現在利用可能  
FR = 今後リリース  
PP = パブリック プレビュー  

## <a name="on-premises-to-cloud-synchronization"></a>オンプレミスからクラウドへの同期
| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス (AAD Sync) - サポートされなくなりました | Azure Active Directory 同期ツール (DirSync) - サポートされなくなりました | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 単一のオンプレミス AD フォレストへの接続 |● |● |● |● |● |
| 複数のオンプレミス AD フォレストへの接続 |● |● | |● |● |
| 複数のオンプレミス Exchange 組織への接続 |● | | | | |
| 単一のオンプレミス LDAP ディレクトリへの接続 |●* | | |● |● | 
| 複数のオンプレミス LDAP ディレクトリへの接続 |●*  | | |● |● | 
| オンプレミス AD とオンプレミス LDAP ディレクトリへの接続 |●* | | |● |● | 
| カスタム システム (SQL、Oracle、MySQL など) への接続 |FR | | |● |● |
| ユーザー定義属性の同期 (ディレクトリ拡張機能) |● | | | | |
| オンプレミス HR (SAP、Oracle eBusiness、PeopleSoft など) への接続 |FR | | |● |● |
| FIM 同期ルールとコネクタによるオンプレミス システムへのプロビジョニングのサポート | | | |● |● |

 
&#42; 現在、このために 2 つのオプションがサポートされています。  次に例を示します。 

   1. Generic LDAP コネクタを使用して、それを Azure AD Connect の外部で有効にすることができます。  これは複雑であり、オンボーディングのためのパートナーと維持のための Premier サポート契約が必要になります。  このオプションでは、単一および複数の LDAP ディレクトリを処理できます。 

   2. LDAP から Active Directory にオブジェクトを移動するための独自のソリューションを開発することができます。  その後、オブジェクトを Azure AD Connect と同期します。  オブジェクトの移動のためのソリューションとして、MIM または FIM を使用することができます。 

## <a name="cloud-to-on-premises-synchronization"></a>クラウドからオンプレミスへの同期
| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス - サポートされなくなりました  | Azure Active Directory 同期ツール (DirSync) - サポートされなくなりました  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| デバイスの書き戻し |● | |● | | |
| 属性の書き戻し (Exchange ハイブリッド デプロイの場合) |● |● |● |● |● |
| グループ オブジェクトの書き戻し |● | | | | |
| (セルフ サービスによるパスワードのリセット (SSPR) とパスワードの変更による) パスワードの書き戻し |● |● | | | |

## <a name="authentication-feature-support"></a>認証機能のサポート
| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス - サポートされなくなりました  | Azure Active Directory 同期ツール (DirSync) - サポートされなくなりました  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 単一のオンプレミス AD フォレストのパスワード ハッシュ同期 |●|●|● | | |
| 複数のオンプレミス AD フォレストのパスワード ハッシュ同期 |●|● | | | |
| 単一のオンプレミス AD フォレストのパススルー認証 |●| | | | |
| フェデレーションを使用したシングル サインオン |● |● |● |● |● |
| シームレス シングル サインオン|● |||||
| (SSPR とパスワードの変更による) パスワードの書き戻し |● |● | | | |

## <a name="set-up-and-installation"></a>セットアップとインストール
| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス - サポートされなくなりました  | Azure Active Directory 同期ツール (DirSync) - サポートされなくなりました  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| ドメイン コントローラーへのインストールのサポート |● |● |● | |
| SQL Express を使用したインストールのサポート |● |● |● | |
| DirSync からの簡単なアップグレード |● | | | |
| 管理のユーザー エクスペリエンスを Windows Server の各種言語にローカリゼーション |● |● |● | |
| エンド ユーザーのユーザー エクスペリエンスを Windows Server の各種言語にローカリゼーション | | | |● |
| Windows Server 2008 および Windows Server 2008 R2 のサポート |● (同期の場合。フェデレーションの場合は利用不可) |● |● |● |
| Windows Server 2012 および Windows Server 2012 R2 のサポート |● |● |● |● |

## <a name="filtering-and-configuration"></a>フィルター処理と構成
| 機能 | Azure Active Directory Connect | Azure Active Directory 同期サービス - サポートされなくなりました  | Azure Active Directory 同期ツール (DirSync) - サポートされなくなりました  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| ドメインおよび組織単位でのフィルター処理 |● |● |● |● |● |
| オブジェクトの属性値でのフィルター処理 |● |● |● |● |● |
| 最小の属性セットの同期 (MinSync) の許可 |● |● | | | |
| 属性フローへのさまざまなサービス テンプレートの適用の許可 |● |● | | | |
| AD から Azure AD に流れる属性の削除の許可 |● |● | | | |
| 属性フローの高度なカスタマイズの許可 |● |● | |● |● |

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

