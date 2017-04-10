---
title: "コネクタ バージョンのリリース履歴 | Microsoft Docs"
description: "このトピックでは、Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタの全リリース一覧を提供します"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 244ca634cfd47ee37e3845380ac05dc68d406621
ms.lasthandoff: 03/29/2017


---
# <a name="connector-version-release-history"></a>コネクタ バージョンのリリース履歴
Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタは頻繁に更新されます。

> [!NOTE]
> このトピックでは、FIM と MIM のみを取り上げています。 これらのコネクタは、Azure AD Connect ではサポートされていません。

このトピックでは、リリース済みのすべてのバージョンのコネクタを一覧にまとめています。

関連リンク:

* [最新コネクタのダウンロード](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Generic LDAP コネクタ](active-directory-aadconnectsync-connector-genericldap.md) リファレンス ドキュメント
* [Generic SQL コネクタ](active-directory-aadconnectsync-connector-genericsql.md) リファレンス ドキュメント
* [Web サービス コネクタ](http://go.microsoft.com/fwlink/?LinkID=226245) リファレンス ドキュメント
* [PowerShell コネクタ](active-directory-aadconnectsync-connector-powershell.md) リファレンス ドキュメント
* [Lotus Domino コネクタ](active-directory-aadconnectsync-connector-domino.md) リファレンス ドキュメント

## <a name="114430"></a>1.1.443.0

リリース日: 2017 年 3 月

### <a name="enhancements"></a>強化された機能
* Generic SQL:</br>
  **シナリオの現象:** 1 つのオブジェクトの種類への参照のみが許可され、メンバーによる相互参照が必要であるということは、よく知られている SQL コネクタの制限事項です。 </br>
  **ソリューションの説明:** [*] オプションが選択されている参照の処理手順では、オブジェクトの種類の "すべての" 組み合わせが同期エンジンに返されます。

>[!Important]
- これにより、多数のプレースホルダーが作成されます。
- オブジェクトの種類全体で名前が一意であることを確認する必要があります。


* Generic LDAP:</br>
 **シナリオ:** 特定のパーティションで少数のコンテナーのみを選択した場合でも、検索はパーティション全体で実行されます。 特定のパーティションは Synchronization Service によってフィルター処理されますが、MA によってフィルター処理されることはありません。これにより、パフォーマンスの低下が発生することがあります。 </br>

 **ソリューションの説明:** パーティション全体を検索する代わりに、各コンテナでオブジェクトを検索できるように GLDAP コネクタのコードを変更しました。


* Lotus Domino:

  **シナリオ:** エクスポート時のユーザーの削除で、Domino メールの削除をサポートします。 </br>
  **ソリューション:** エクスポート時のユーザーの削除で、構成可能な Domino メールの削除をサポートします。

### <a name="fixed-issues"></a>修正された問題:
* 一般的な Web サービス:
 * WebService 構成ツールを使用して既定の SAP wsconfig プロジェクト内のサービス URL を変更すると、次のエラーが発生します: パスの一部が見つかりません。

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Generic LDAP:
 * GLDAP コネクタは、AD LDS 内のすべての属性を認識しません。
 * LDAP ディレクトリ スキーマから UPN 属性が検出されない場合、ウィザードは中断します。
 * "objectclass" 属性が選択されていない場合、デルタ インポートは、フル インポート時には存在しない検出エラーで失敗します。
 * [パーティションと階層の構成] 構成ページに、その種類が Generic LDAP MA 内の Novel サーバーのパーティションと同じであるオブジェクトが表示されません  
。 RootDSE パーティションからのオブジェクトのみを表示していました。


* Generic SQL:
 * Generic SQL のウォーターマーク差分インポートで複数値属性がインポートされないバグを修正した。
 * 複数値属性の削除または追加された値をエクスポートしても、データ ソース内で値が削除または追加されることはありません。  


* Lotus Notes:
 * [完全な名前] フィールドはメタバースに正しく表示されますが、Notes にエクスポートすると、属性の値は Null または空白になります。
 * 証明者の重複エラーを修正しました。
 * Lotus Domino コネクタでデータのないオブジェクトと他のオブジェクトを同時に選択すると、フル インポートの実行中に検出エラーが発生します。
 * Lotus Domino コネクタでデルタ インポートを実行すると、実行の最後に、Microsoft.IdentityManagement.MA.LotusDomino.Service.exe サービスがアプリケーション エラーを返すことがあります。
 * グループ メンバーシップは全体的に問題なく動作します。ただし、エクスポートの実行時にメンバーシップからユーザーを削除しても、更新では成功したと表示されますが、ユーザーは実際に は Lotus Notes のメンバーシップから削除されていません。
 * Lotus MA の構成 GUI で、エクスポート モードとして “項目を末尾に追加する” ことを選択できようになりました。このモードでは、複数値属性のエクスポート時に、新しい項目は末尾に追加されます。
 * コネクタは、メール フォルダーと ID コンテナーからファイルを削除するために必要なロジックを追加します。
 * NAB メンバー間でのメンバーシップの削除は機能しません。
 * 値は、複数値属性から正常に削除する必要があります。

## <a name="111170"></a>1.1.117.0
リリース日: 2016 年 3

**新しいコネクタ**  
[Generic SQL コネクタ](active-directory-aadconnectsync-connector-genericsql.md)の初回リリース。

**新機能:**

* Generic LDAP コネクタ:
  * Isode によるデルタ インポート対応になりました。
* Web サービス コネクタ:
  * csEntryChangeResult アクティビティと setImportErrorCode アクティビティが更新され、オブジェクト レベルのエラーを同期エンジンに返すことができるようになりました。
  * SAP6 テンプレートと SAP6User テンプレートが更新され、新しいオブジェクト レベルのエラー機能を利用できるようになりました。
* Lotus Domino コネクタ:
  * エクスポート用として、アドレス帳ごとに 1 人の証明者が必要です。 すべての証明に同じパスワードを利用できるようになり、管理が簡単になりました。

**修正された問題:**

* Generic LDAP コネクタ:
  * IBM Tivoli DS の場合、一部の参照属性が正しく検出されませんでした。
  * Open LDAP の場合、デルタ インポートのとき、文字列の始めと終わりの空白文字が切り詰められました。
  * Novell と NetIQ では、OU/コンテナー間でオブジェクトを移動すると同時にオブジェクトの名前を変更した場合、エクスポートに失敗していました。
* Web サービス コネクタ:
  * Web サービスで同じバインディングに対して複数のエンドポイントが存在するとき、コネクタはそのようなエンドポイントを正しく検出しませんでした。
* Lotus Domino コネクタ:
  * fullName 属性をメールイン データベースにエクスポートできませんでした。
  * 追加されたメンバーと削除されたメンバーの両方をグループからエクスポートすると、追加されたメンバーだけがエクスポートされました。
  * Notes ドキュメントが無効の場合 (isValid 属性が false に設定されている場合)、コネクタでエラーが発生します。

## <a name="older-releases"></a>以前のリリース
2016 年 3 月まで、コネクタはサポート トピックとしてリリースされていました。

**Generic LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597、2015 年 9 月
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549、2015 年 3 月
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534、2015 年 1 月
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419、2014 年 9 月
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082、2014 年 3 月

**Web サービス**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419、2014 年 9 月

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419、2014 年 9 月

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597、2015 年 9 月
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549、2015 年 3 月
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712、2014 年 8 月
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003、2014 年 2 月  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721、2013 年 10 月
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534、2013 年 8 月

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

