---
title: 'Azure AD Connect 同期: 関数参照 | Microsoft Docs'
description: Azure AD Connect Sync での宣言型のプロビジョニングの式の参照
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7454733233f1fd487d774d52a6f46187354ae05c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246708"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect 同期: 関数参照
Azure AD Connect では、同期時の属性値を操作するために関数を使用します。  
関数の構文は、次の形式を使用して表されます。  
`<output type> FunctionName(<input type> <position name>, ..)`

関数がオーバーロード状態の場合に複数の構文を受け入れると、すべての有効な構文が一覧表示されます。  
関数は厳密に型指定され、渡された型が文書化された型と一致することを確認します。  
型が一致しない場合は、エラーがスローされます。

型は次の構文で表されます。

* **bin** – バイナリ
* **bool** – ブール値
* **dt** – UTC の日付/時刻
* **enum** – 既知の定数の列挙
* **exp** – 評価結果にブール値が想定される式
* **mvbin** – 複数値のバイナリ
* **mvstr** – 複数値の文字列
* **mvref** – 複数値の参照
* **num** – 数値
* **ref** – 参照
* **str** – 文字列
* **var** – その他の (ほとんど) すべての型のバリアント
* **void** – 値を返しません

**mvbin**、**mvstr**、**mvref** 型の関数は、複数値の属性のみに有効です。 **bin**、**str**、**ref** 型の関数は、単一値と複数値両方の属性に有効です。

## <a name="functions-reference"></a>関数参照

* **[MSSQLSERVER のプロトコルのプロパティ]**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **変換**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **日付/時刻**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Now](#now)
  * [NumFromDate](#numfromdate)
* **ディレクトリ**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **評価**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **算術**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Multi*valued**
  * [Contains](#contains)
  * [Count](#count)
  * [Item](#item)
  * [ItemOrNull](#itemornull)
  * [Join](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Split](#split)
* **プログラム フロー**
  * [Error](#error)
  * [IIF](#iif)
  * [Select](#select)
  * [スイッチ](#switch)
  * [Where](#where)
  * [With](#with)
* **[テキスト]**
  * [GUID](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Left](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Mid](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Replace](#replace)
  * [ReplaceChars](#replacechars)
  * [Right](#right)
  * [RTrim](#rtrim)
  * [Trim](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**説明:**  
BitAnd 関数は、値に指定のビットを設定します。

**構文 :**  
`num BitAnd(num value1, num value2)`

* value1, value2: ともに AND になる数値

**備考:**  
この関数は両方のパラメーターをバイナリ表現に変換し、ビットを次に設定します。

* 0 - *value1* と *value2* 内の対応するビットの 1 つまたは両方が 0 の場合
* 1 - 対応するビットの両方が 1 の場合。

つまり、両方のパラメーターの対応するビットが 1 の場合を除くすべてのケースで 0 を返します。

**例:**  
`BitAnd(&HF, &HF7)`  
16 進数の "F" と "F7" の AND は 7 と評価されるため、7 を返します。

---
### <a name="bitor"></a>BitOr
**説明:**  
BitOr 関数は、値に指定のビットを設定します。

**構文 :**  
`num BitOr(num value1, num value2)`

* value1, value2: ともに OR になる数値

**備考:**  
この関数は両方のパラメーターをバイナリ表現に変換して、マスクとフラグで対応するビットの 1 つまたは両方が 1 の場合はビットを 1 に設定し、対応する両方のビットが 0 の場合は 0 に設定します。 つまり、両方のパラメーターの対応するビットが 0 の場合を除くすべてのケースで 1 を返します。

---
### <a name="cbool"></a>CBool
**説明:**  
CBool 関数は、式の評価結果に基づいてブール値を返します。

**構文 :**  
`bool CBool(exp Expression)`

**備考:**  
式の評価結果が 0 以外の値の場合は CBool によって True が返され、それ以外の場合は False が返されます。

**例:**  
`CBool([attrib1] = [attrib2])`  

両方の属性が同じ値を持つ場合は、True を返します。

---
### <a name="cdate"></a>CDate
**説明:**  
CDate 関数は、文字列から UTC DateTime を返します。 DateTime は Sync ではネイティブの属性の型ではありませんが、一部の関数で使用されます。

**構文 :**  
`dt CDate(str value)`

* 値:日付、時刻、オプションでタイム ゾーンを含む文字列

**備考:**  
文字列は常に UTC で返されます。

**例:**  
`CDate([employeeStartTime])`  
従業員の作業開始時刻に基づいて、DateTime を返します。

`CDate("2013-01-10 4:00 PM -8")`  
"2013-01-11 12:00 AM" を表す DateTime を返します。


---
### <a name="certextensionoids"></a>CertExtensionOids
**説明:**  
証明書オブジェクトのすべての重要な拡張機能の Oid 値を返します。

**構文 :**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certformat"></a>CertFormat
**説明:**  
この X.509v3 証明書の形式の名前を返します。

**構文 :**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certfriendlyname"></a>CertFriendlyName
**説明:**  
証明書に関連付けられている別名を返します。

**構文 :**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certhashstring"></a>CertHashString
**説明:**  
X.509v3 証明書の SHA1 ハッシュ値を 16 進数文字列で返します。

**構文 :**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certissuer"></a>CertIssuer
**説明:**  
X.509v3 証明書を発行した証明機関の名前を返します。

**構文 :**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certissuerdn"></a>CertIssuerDN
**説明:**  
証明書の発行者の識別名を返します。

**構文 :**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certissueroid"></a>CertIssuerOid
**説明:**  
証明書の発行者の識別証明書の発行者の Oid を返します。

**構文 :**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**説明:**  
この X.509v3 証明書のキー アルゴリズム情報を文字列で返します。

**構文 :**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**説明:**  
X.509v3 証明書のキー アルゴリズム パラメーターを 16 進数文字列で返します。

**構文 :**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certnameinfo"></a>CertNameInfo
**説明:**  
証明書のサブジェクトと発行者名を返します。

**構文 :**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。
*   X509NameType:サブジェクトの X509NameType 値。
*   includesIssuerName: 発行者名を含める場合は true、それ以外の場合は false。

---
### <a name="certnotafter"></a>CertNotAfter
**説明:**  
その後は証明書が有効ではなくなる日付を現地時刻で返します。

**構文 :**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certnotbefore"></a>CertNotBefore
**説明:**  
証明書が有効になる日付を現地時刻で返します。

**構文 :**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**説明:**  
X.509v3 証明書の公開キーの Oid を返します。

**構文 :**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**説明:**  
X.509v3 証明書の公開キーのパラメーターの Oid を返します。

**構文 :**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certserialnumber"></a>CertSerialNumber
**説明:**  
X.509v3 証明書のシリアル番号を返します。

**構文 :**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**説明:**  
証明書の署名の作成に使用されるアルゴリズムの Oid を返します。

**構文 :**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certsubject"></a>CertSubject
**説明:**  
証明書のサブジェクト識別名を取得します。

**構文 :**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**説明:**  
証明書のサブジェクト識別名を返します。

**構文 :**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**説明:**  
証明書のサブジェクト名の Oid を返します。

**構文 :**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certthumbprint"></a>CertThumbprint
**説明:**  
証明書の拇印を返します。

**構文 :**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="certversion"></a>CertVersion
**説明:**  
証明書の X.509 形式のバージョンを返します。

**構文 :**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。

---
### <a name="cguid"></a>CGuid
**説明:**  
CGuid 関数は、GUID の文字列表現をそのバイナリ表現に変換します。

**構文 :**  
`bin CGuid(str GUID)`

* このパターンで書式設定される文字列: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx または {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**説明:**  
Contains 関数は、複数値の属性内で文字列を検索します。

**構文 :**  
`num Contains (mvstring attribute, str search)` - 大文字小文字を区別  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` - 大文字小文字を区別

* attribute: 検索対象の複数値の属性。
* search: 属性で検索する文字列。
* Casetype:CaseInsensitive または CaseSensitive。

文字列が見つかった複数値の属性にインデックスを返します。 文字列が見つからない場合は 0 を返します。

**備考:**  
複数値の文字列属性の場合、検索では、値の部分文字列が検出されます。  
参照属性の場合、一致と見なされるためには、検索された文字列は正確に値と一致する必要があります。

**例:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
proxyAddresses 属性にプライマリ メール アドレスが含まれている場合は (大文字の "SMTP:" で表されます) proxyAddress 属性を返し、それ以外の場合はエラーを返します。

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**説明:**  
ConvertFromBase64 関数は、指定した base64 でエンコードされた値を正規の文字列に変換します。

**構文 :**  
`str ConvertFromBase64(str source)` - エンコードには Unicode を想定しています  
`str ConvertFromBase64(str source, enum Encoding)`

* source:Base64 でエンコードされた文字列  
* Encoding:Unicode、ASCII、UTF8

**例**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

どちらの例でも "*Hello world!* " を返します。

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**説明:**  
ConvertFromUTF8Hex 関数は、指定した UTF8 の 16 進数でエンコードされた値を文字列に変換します。

**構文 :**  
`str ConvertFromUTF8Hex(str source)`

* source:UTF8 の 2 バイトでエンコードされた文字列

**備考:**  
この関数と ConvertFromBase64([],UTF8) との違いは、結果が DN 属性で表示される点です。  
この形式は Azure Active Directory で DN として使用されます。

**例:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
"*Hello world!* " を返します。

---
### <a name="converttobase64"></a>ConvertToBase64
**説明:**  
ConvertToBase64 関数は、文字列を Unicode の base64 文字列に変換します。  
整数の配列の値を、base 64 桁でエンコードされているそれと同等の文字列表現に変換します。

**構文 :**  
`str ConvertToBase64(str source)`

**例:**  
`ConvertToBase64("Hello world!")`  
"SABlAGwAbABvACAAdwBvAHIAbABkACEA" を返します。

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**説明:**  
ConvertToUTF8Hex 関数は、文字列を UTF8 の 16 進数でエンコードされた値に変換します。

**構文 :**  
`str ConvertToUTF8Hex(str source)`

**備考:**  
この関数の出力形式は、DN 属性の形式として Azure Active Directory で使用されます。

**例:**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 を返します。

---
### <a name="count"></a>Count
**説明:**  
Count 関数は、複数値の属性内の要素数を返します。

**構文 :**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**説明:**  
CNum 関数は、文字列を受け取り、数値データ型を返します。

**構文 :**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**説明:**  
文字列を参照属性に変換します。

**構文 :**  
`ref CRef(str value)`

**例:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**説明:**  
CStr 関数は、文字列データ型に変換します。

**構文 :**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* value: 数値、参照属性、ブール値を指定できます。

**例:**  
`CStr([dn])`  
"cn=Joe,dc=contoso,dc=com" を返します。

---
### <a name="dateadd"></a>DateAdd
**説明:**  
指定した時間間隔が追加された日付を含む Date を返します。

**構文 :**  
`dt DateAdd(str interval, num value, dt date)`

* interval:追加する時間間隔を表す文字列式。 文字列には次のいずれかの値が必要です。
  * yyyy: 年
  * q: 四半期
  * m: 月
  * y: 年間通算日
  * d: 日
  * w: 週日
  * ww: 週
  * h: 時
  * n: 分
  * s: 秒
* value: 追加する単位の数。 正の数 (将来の日時を取得する場合) または負の数 (過去の日時を取得する場合) を指定できます。
* date:間隔が追加される日付を表す DateTime。

**例:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 か月を追加した結果の "2001-04-01" を表す DateTime を返します。

---
### <a name="datefromnum"></a>DateFromNum
**説明:**  
DateFromNum 関数は、AD の日付形式の値を DateTime 型に変換します。

**構文 :**  
`dt DateFromNum(num value)`

**例:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00 を表す DateTime を返します。

---
### <a name="dncomponent"></a>DNComponent
**説明:**  
DNComponent 関数は、指定した DN コンポーネントの値を左から返します。

**構文 :**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: 解釈する参照属性
* ComponentNumber:返される DN のコンポーネント

**例:**  
`DNComponent(CRef([dn]),1)`  
dn が "cn=Joe,ou=…," の場合は、Joe が返されます。

---
### <a name="dncomponentrev"></a>DNComponentRev
**説明:**  
DNComponentRev 関数は、指定した DN コンポーネントの値を右 (端) から返します。

**構文 :**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: 解釈する参照属性
* ComponentNumber - 返される DN のコンポーネント
* オプション:DC – "dc =" ですべてのコンポーネントを無視します。

**例:**  
dn が "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" の場合、  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
両方が US を返します。

---
### <a name="error"></a>エラー
**説明:**  
Error 関数は、カスタム エラーを返すために使用します。

**構文 :**  
`void Error(str ErrorMessage)`

**例:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
属性 accountName が存在しない場合は、オブジェクトでエラーをスローします。

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**説明:**  
EscapeDNComponent 関数は、DN のコンポーネントを 1 つ受け取り、LDAP で表示できるようにそれをエスケープします。

**構文 :**  
`str EscapeDNComponent(str value)`

**例:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
displayName 属性に LDAP でエスケープする必要のある文字が含まれている場合でも、LDAP ディレクトリでオブジェクトを作成できるようにします。

---
### <a name="formatdatetime"></a>FormatDateTime
**説明:**  
FormatDateTime 関数は、DateTime を指定した形式の文字列に設定するために使用します。

**構文 :**  
`str FormatDateTime(dt value, str format)`

* value: DateTime 形式の値
* format: 変換する形式を表す文字列。

**備考:**  
形式で有効な値については、「[Custom date and time formats for the FORMAT function](/dax/custom-date-and-time-formats-for-the-format-function)」(FORMAT 関数用のカスタム日付/時刻形式) を参照してください。

**例:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-MM-dd")`  
結果は "2007-12-25" となります。

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
"20140905081453.0Z" などの結果になります。

---
### <a name="guid"></a>Guid
**説明:**  
GUID 関数は、新しいランダムな GUID を生成します。

**構文 :**  
`str Guid()`

---
### <a name="iif"></a>IIF
**説明:**  
IIF 関数は、指定した条件に基づいて、使用できる一連の値のうち、いずれかを返します。

**構文 :**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition: 評価結果が true または false になる任意の値または式。
* valueIfTrue:条件の評価結果が true の場合に返される値。
* valueIfFalse:条件の評価結果が false の場合に返される値。

**例:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 ユーザーがインターンの場合はユーザーのエイリアスの先頭に "t-" を付けて返し、そうでない場合はユーザーのエイリアスをそのまま返します。

---
### <a name="instr"></a>InStr
**説明:**  
InStr 関数は文字列内の最初の部分文字列を検索します。

**構文 :**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: 検索対象の文字列
* stringmatch: 検出対象の文字列
* start: 部分文字列の検索開始位置
* compare: vbTextCompare または vbBinaryCompare

**備考:**  
部分文字列が見つかった位置を返します。見つからなかった場合は 0 を返します。

**例:**  
`InStr("The quick brown fox","quick")`  
評価結果は 5 になります。

`InStr("repEated","e",3,vbBinaryCompare)`  
評価結果は 7 になります。

---
### <a name="instrrev"></a>InStrRev
**説明:**  
InStrRev 関数は文字列内の最後の部分文字列を検索します。

**構文 :**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: 検索対象の文字列
* stringmatch: 検出対象の文字列
* start: 部分文字列の検索開始位置
* compare: vbTextCompare または vbBinaryCompare

**備考:**  
部分文字列が見つかった位置を返します。見つからなかった場合は 0 を返します。

**例:**  
`InStrRev("abbcdbbbef","bb")`  
7 を返します。

---
### <a name="isbitset"></a>IsBitSet
**説明:**  
IsBitSet 関数は、ビットが設定されているかどうかをテストします。

**構文 :**  
`bool IsBitSet(num value, num flag)`

* value: 評価対象の数値。flag: 評価対象のビットがある数値。

**例:**  
`IsBitSet(&HF,4)`  
ビット "4" が 16 進数値 "F" で設定されているため True が返されます。

---
### <a name="isdate"></a>IsDate
**説明:**  
式が DateTime 型として評価できる場合、IsDate 関数の評価結果は True になります。

**構文 :**  
`bool IsDate(var Expression)`

**備考:**  
CDate() が成功するかどうかを判断するために使用します。

---
### <a name="iscert"></a>IsCert
**説明:**  
生データを .NET X509Certificate2 証明書オブジェクトにシリアル化できる場合は true を返します。

**構文 :**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData:X.509 証明書のバイト配列の表現。 バイト配列には、バイナリ (DER) または Base64 でエンコードされた X.509 データを指定できます。
---
### <a name="isempty"></a>IsEmpty
**説明:**  
属性が CS または MV に存在しても評価結果が空の文字列である場合、IsEmpty 関数の評価結果は True になります。

**構文 :**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**説明:**  
文字列が GUID に変換できる場合、IsGUID 関数の評価結果は true になります。

**構文 :**  
`bool IsGuid(str GUID)`

**備考:**  
GUID は次のいずれかのパターンに従った文字列として定義されます: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx または {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

CGuid() が成功するかどうかを判断するために使用します。

**例:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
StrAttribute に GUID 形式がある場合はバイナリ表現を返します。それ以外の場合は Null を返します。

---
### <a name="isnull"></a>IsNull
**説明:**  
式の評価結果が Null の場合、IsNull 関数は true を返します。

**構文 :**  
`bool IsNull(var Expression)`

**備考:**  
属性の場合、Null は属性の不在によって表されます。

**例:**  
`IsNull([displayName])`  
CS または MV に属性がない場合は True を返します。

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**説明:**  
式が null または空の文字列の場合、IsNullOrEmpty 関数は true を返します。

**構文 :**  
`bool IsNullOrEmpty(var Expression)`

**備考:**  
属性の場合は、属性がないか、存在しても空の文字列の場合、評価結果は True になります。  
この関数の逆の関数は IsPresent です。

**例:**  
`IsNullOrEmpty([displayName])`  
CS または MV に属性がないか、空の文字列の場合は True を返します。

---
### <a name="isnumeric"></a>IsNumeric
**説明:**  
IsNumeric 関数は、式を数値型として評価できるかどうかを示すブール値を返します。

**構文 :**  
`bool IsNumeric(var Expression)`

**備考:**  
CNum() が式の解析に成功するかどうかを判断するために使用します。

---
### <a name="isstring"></a>IsString
**説明:**  
式を文字列型として評価できる場合、IsString 関数の評価結果は True になります。

**構文 :**  
`bool IsString(var expression)`

**備考:**  
CStr() が式の解析に成功するかどうかを判断するために使用します。

---
### <a name="ispresent"></a>IsPresent
**説明:**  
式の評価結果が Null でもなく、空でもない文字列の場合、IsPresent 関数は true を返します。

**構文 :**  
`bool IsPresent(var expression)`

**備考:**  
この関数の逆関数は IsNullOrEmpty です。

**例:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**説明:**  
Item 関数は複数値の文字列/属性から 1 つの項目を返します。

**構文 :**  
`var Item(mvstr attribute, num index)`

* attribute: 複数値の属性
* index: 複数値の文字列内の項目へのインデックス。

**備考:**  
Contains 関数は複数値の属性内の項目に対するインデックスを返すため、Item 関数を Contains 関数と共に使用すると便利です。

インデックスが範囲外にある場合は、エラーをスローします。

**例:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
プライマリ メール アドレスを返します。

---
### <a name="itemornull"></a>ItemOrNull
**説明:**  
ItemOrNull 関数は複数値の文字列/属性から 1 つの項目を返します。

**構文 :**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute: 複数値の属性
* index: 複数値の文字列内の項目へのインデックス。

**備考:**  
ItemOrNull 関数は複数値の属性内の項目に対するインデックスを返すため、ItemOrNull 関数を Contains 関数と共に使用すると便利です。

インデックスが範囲外にある場合は、Null 値を返します。

---
### <a name="join"></a>Join
**説明:**  
Join 関数は、複数値の文字列を受け取り、指定した区切り記号が項目間に挿入された、単一値の文字列を返します。

**構文 :**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* 属性を探します。結合対象の文字列が含まれる複数値の属性。
* delimiter:返される文字列内で部分文字列を区切るために使用する任意の文字列。 省略した場合は、空白文字 (" ") が使用されます。 delimiter が長さ 0 の文字列 ("") または Nothing の場合、リスト内のすべての項目は、区切り記号なしで連結されます。

**解説**  
Join 関数と Split 関数の間には類似点があります。 Join 関数は、文字列の配列を受け取り、区切り文字列を使用してそれらを結合し、単一の文字列を返します。 Split 関数は、文字列を受け取って区切り記号で分割し、文字列の配列を返します。 ただし、Join 関数が任意の区切り文字列を使った文字列を連結できるのに対し、Split 関数で文字列を分割する際には 1 文字の区切り記号しか使用できないという大きな違いがあります。

**例:**  
`Join([proxyAddresses],",")`  
"SMTP:john.doe@contoso.com,smtp:jd@contoso.com" などを返します

---
### <a name="lcase"></a>LCase
**説明:**  
LCase 関数は、文字列内のすべての文字を小文字に変換します。

**構文 :**  
`str LCase(str value)`

**例:**  
`LCase("TeSt")`  
"test" を返します。

---
### <a name="left"></a>Left
**説明:**  
Left 関数は文字列の左端から数えて指定した文字数分の文字を返します。

**構文 :**  
`str Left(str string, num NumChars)`

* string: 返される文字を含む文字列
* NumChars: 文字列の左端から数えて返される文字数を指定する数値

**備考:**  
string 内の最初の numChars 文字分の文字を含む文字列。

* numChars = 0 の場合、空の文字列を返します。
* numChars < 0 の場合、入力文字列を返します。
* string が null の場合、空の文字列を返します。

string に含まれる文字数が numChars で指定した数より少ない場合は、string と同一の文字列 (パラメーター 1 のすべての文字が含まれる) が返されます。

**例:**  
`Left("John Doe", 3)`  
"Joh" を返します。

---
### <a name="len"></a>Len
**説明:**  
Len 関数は文字列の文字数を返します。

**構文 :**  
`num Len(str value)`

**例:**  
`Len("John Doe")`  
8 を返します。

---
### <a name="ltrim"></a>LTrim
**説明:**  
LTrim 関数は文字列の先頭の空白文字を削除します。

**構文 :**  
`str LTrim(str value)`

**例:**  
`LTrim(" Test ")`  
"Test " を返します。

---
### <a name="mid"></a>Mid
**説明:**  
Mid 関数は文字列の指定した位置から数えて、指定した文字数分の文字を返します。

**構文 :**  
`str Mid(str string, num start, num NumChars)`

* string: 返される文字を含む文字列
* start: 文字列内で返される文字の開始位置を指定する数値
* NumChars: 文字列の位置から数えて返される文字数を指定する数値

**備考:**  
string 内の start 位置から数えて numChars 文字分の文字を返します。  
文字列内の start 位置から数えて numChars 文字分の文字が含まれる文字列。

* numChars = 0 の場合、空の文字列を返します。
* numChars < 0 の場合、入力文字列を返します。
* start > 文字列の長さの場合、入力文字列を返します。
* start < = 0 の場合、入力文字列を返します。
* string が null の場合、空の文字列を返します。

string で start 位置から後に numChar 文字が残っていない場合、できるだけ多くの文字が返されます。

**例:**  
`Mid("John Doe", 3, 5)`  
"hn Do" を返します。

`Mid("John Doe", 6, 999)`  
"Doe" を返します。

---
### <a name="now"></a>Now
**説明:**  
Now 関数は、コンピューターのシステムの日付と時刻に従って、現在の日付と時刻を指定する DateTime を返します。

**構文 :**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**説明:**  
NumFromDate 関数は、AD の日付形式で日付を返します。

**構文 :**  
`num NumFromDate(dt value)`

**例:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000 を返します。

---
### <a name="padleft"></a>PadLeft
**説明:**  
PadLeft 関数は、指定した埋め込み文字を使用して、指定した長さになるまで左側に文字列を埋め込みます。

**構文 :**  
`str PadLeft(str string, num length, str padCharacter)`

* string: 埋め込む文字列。
* length:文字列の必要な長さを表す整数。
* padCharacter:埋め込み文字として使用する 1 文字で構成された文字列。

**備考:**

* 文字列の長さが length 未満の場合、長さが length と等しくなるまで文字列の左端に padCharacter が繰り返し追加されます。
* PadCharacter には空白文字を指定できますが、null 値を指定することはできません。
* 文字列の長さが length 以上の場合、文字列は変更されない状態で返されます。
* 文字列に length 以上の長さがある場合、文字列と同一の文字列が返されます。
* 文字列の長さが length 未満の場合、padCharacter が埋め込まれた文字列が含まれる、必要な長さの新しい文字列が返されます。
* string が null の場合、この関数は空の文字列を返します。

**例:**  
`PadLeft("User", 10, "0")`  
"000000User" を返します。

---
### <a name="padright"></a>PadRight
**説明:**  
PadRight 関数は、指定した埋め込み文字を使用して、指定した長さになるまで右側に文字列を埋め込みます。

**構文 :**  
`str PadRight(str string, num length, str padCharacter)`

* string: 埋め込む文字列。
* length:文字列の必要な長さを表す整数。
* padCharacter:埋め込み文字として使用する 1 文字で構成された文字列。

**備考:**

* 文字列の長さが length 未満の場合、長さが length と等しくなるまで文字列の右端に padCharacter が繰り返し追加されます。
* PadCharacter には空白文字を指定できますが、null 値を指定することはできません。
* 文字列の長さが length 以上の場合、文字列は変更されない状態で返されます。
* 文字列に length 以上の長さがある場合、文字列と同一の文字列が返されます。
* 文字列の長さが length 未満の場合、padCharacter が埋め込まれた文字列が含まれる、必要な長さの新しい文字列が返されます。
* string が null の場合、この関数は空の文字列を返します。

**例:**  
`PadRight("User", 10, "0")`  
"User000000" を返します。

---
### <a name="pcase"></a>PCase
**説明:**  
PCase 関数は、文字列内のスペースで区切られた単語の最初の文字を大文字に変換し、その他のすべての文字を小文字に変換します。

**構文 :**  
`String PCase(string)`

**備考:**

* 現在この関数では、頭字語などの大文字のみで構成された単語を変換する際に、大文字と小文字を正しく区別することができません。

**例:**  
`PCase("TEsT")`  
"test" を返します。

`PCase(LCase("TEST"))`  
"Test" を返します。

---
### <a name="randomnum"></a>RandomNum
**説明:**  
RandomNum 関数は、指定した範囲内の乱数を返します。

**構文 :**  
`num RandomNum(num start, num end)`

* start: 生成するランダムな値の下限を指定する数値
* end: 生成するランダムな値の上限を指定する数値

**例:**  
`Random(100,999)`  
734 などを返します。

---
### <a name="removeduplicates"></a>RemoveDuplicates
**説明:**  
RemoveDuplicates 関数は複数値の文字列を受け取り、各値が一意になるように処理します。

**構文 :**  
`mvstr RemoveDuplicates(mvstr attribute)`

**例:**  
`RemoveDuplicates([proxyAddresses])`  
重複する値がすべて削除された、校正済みの proxyAddress 属性を返します。

---
### <a name="replace"></a>Replace
**説明:**  
Replace 関数は、見つかった文字列をすべて別の文字列に置き換えます。

**構文 :**  
`str Replace(str string, str OldValue, str NewValue)`

* string:値を置換する文字列。
* OldValue:検索し、置換される文字列。
* NewValue:置換する文字列。

**備考:**  
この関数は次の特殊なモニカーを認識します。

* \n – 新しい行
* \r – キャリッジ リターン
* \t – タブ

**例:**  
`Replace([address],"\r\n",", ")`  
CRLF をコンマとスペースで置き換え、"One Microsoft Way, Redmond, WA, USA" などの文字列にします。

---
### <a name="replacechars"></a>ReplaceChars
**説明:**  
ReplaceChars 関数は、ReplacePattern 文字列に見つかったすべての文字を置き換えます。

**構文 :**  
`str ReplaceChars(str string, str ReplacePattern)`

* string:文字を置換する文字列。
* ReplacePattern: 置換する文字のディクショナリが含まれる文字列。

形式は {source1}:{target1},{source2}:{target2},{sourceN},{targetN} です。source は検索対象の文字、target は置換する文字列です。

**備考:**

* この関数は定義した source の見つかった位置を受け取り、target と置き換えます。
* source は、厳密に 1 文字 (unicode) である必要があります。
* source は、空にすることも 2 文字以上にすることもできません (解析エラー)。
* target は、ö:oe、 β:ss などの複数の文字を持つことができます。
* target を空にして、文字を削除する必要があることを指定できます。
* source は、大文字と小文字を区別し、完全に一致する必要があります。
* 「,」(コンマ) と「:」(コロン) は予約文字であり、この関数を使用して置き換えることはできません。
* ReplacePattern 文字列内のスペースやその他の空白文字は無視されます。

**例:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas を返します。

`ReplaceChars("O’Neil",%ReplaceString%)`  
"ONeil" を返します。単一引用符を削除するように定義されています。

---
### <a name="right"></a>Right
**説明:**  
Right 関数は文字列の右端から数えて指定した文字数分の文字を返します。

**構文 :**  
`str Right(str string, num NumChars)`

* string: 返される文字を含む文字列
* NumChars: 文字列の右端から数えて返される文字数を指定する数値

**備考:**  
string の末尾から数えて numChars 文字分の文字が返されます。

文字列内の最後の numChars 文字分の文字を含む文字列。

* numChars = 0 の場合、空の文字列を返します。
* numChars < 0 の場合、入力文字列を返します。
* string が null の場合、空の文字列を返します。

文字列に含まれる文字数が NumChars に指定した数より少ない場合は、文字列と同一の文字列が返されます。

**例:**  
`Right("John Doe", 3)`  
"Doe" を返します。

---
### <a name="rtrim"></a>RTrim
**説明:**  
RTrim 関数は文字列の末尾の空白文字を削除します。

**構文 :**  
`str RTrim(str value)`

**例:**  
`RTrim(" Test ")`  
" Test" を返します。

---
### <a name="select"></a>Select
**説明:**  
指定された関数に基づいて、複数値の属性 (または式の出力) 内のすべての値を処理します。

**構文 :**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item:複数値の属性内の要素を表します
* attribute: 複数値の属性
* expression: 値のコレクションを返す式
* condition: 属性内の項目を処理できる任意の関数

**例:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
ハイフン (-) の削除後に、複数値の属性 otherPhone 内のすべての値を返します。

---
### <a name="split"></a>Split
**説明:**  
Split 関数は区切り記号で区切られた文字列を受け取り、複数値の文字列にします。

**構文 :**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* value: 分離する区切り文字が含まれる文字列。
* delimiter: 区切り記号として使用される 1 文字。
* limit: 返すことができる値の最大数。

**例:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
proxyAddress 属性に有用な 2 つの要素が含まれる複数値の文字列を返します。

---
### <a name="stringfromguid"></a>StringFromGuid
**説明:**  
StringFromGuid 関数は、バイナリ GUID を受け取り、文字列に変換します。

**構文 :**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**説明:**  
StringFromSid 関数は、セキュリティ識別子が含まれるバイト配列を文字列に変換します。

**構文 :**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Switch
**説明:**  
Switch 関数は、条件の評価結果に基づいて 1 つの値を返すために使用します。

**構文 :**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr:評価する必要のあるバリアント型の式。
* value: 対応する式が True の場合に返される値。

**備考:**  
Switch 関数の引数リストは、式と値のペアで構成されます。 式は左から右に評価され、評価結果が True になる最初の式に関連付けられている値が返されます。 各部分が正しくペアリングされていないと、実行時エラーが発生します。

たとえば、expr1 が True の場合、Switch は value1 を返します。 たとえば、expr1 が False でも expr2 が True の場合、Switch は value2 を返します。

次の場合、Switch は Nothing を返します。

* すべての式が True でない場合。
* 最初の True 式に、Null である対応する値がある場合。

Switch は、返される式が 1 つであってもすべての式を評価します。 このため、望ましくない影響が発生しないよう注意する必要があります。 たとえば、式の評価結果が 0 除算のエラーの場合、エラーが発生します。

値には、カスタム文字列を返す Error 関数を指定することもできます。

**例:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
一部の主要都市で話される言語を返します。それ以外の場合はエラーを返します。

---
### <a name="trim"></a>Trim
**説明:**  
Trim 関数は、文字列の先頭と末尾の空白文字を削除します。

**構文 :**  
`str Trim(str value)`  

**例:**  
`Trim(" Test ")`  
"test" を返します。

`Trim([proxyAddresses])`  
proxyAddress 属性の値ごとに先頭と末尾の空白文字を削除します。

---
### <a name="ucase"></a>UCase
**説明:**  
UCase 関数は文字列内のすべての文字を大文字に変換します。

**構文 :**  
`str UCase(str string)`

**例:**  
`UCase("TeSt")`  
"test" を返します。

---
### <a name="where"></a>Where

**説明:**  
特定の条件に基づいて、複数値の属性 (または式の出力) の値のサブセットを返します。

**構文 :**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item:複数値の属性内の要素を表します
* attribute: 複数値の属性
* condition: 評価結果が true または false になる任意の式
* expression: 値のコレクションを返す式

**例:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
有効期限が切れていない、複数値の属性 userCertificate 内の証明書の値を返します。

---
### <a name="with"></a>With
**説明:**  
With 関数は、複雑な式の中に 1 回以上現れる部分式を表す変数を使用することで、複雑な式を簡略化する方法となります。

**構文:** 
`With(var variable, exp subExpression, exp complexExpression)`  
* variable:部分式を表します。
* subExpression: 変数によって表される部分式。
* complexExpression:複雑な式。

**例:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
上の記述は下の記述と機能的に同等です。  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
userCertificate 属性内の、期限が切れていない証明書の値のみが返されます。


---
### <a name="word"></a>Word
**説明:**  
Word 関数は、使用する区切り記号と返す単語の番号を表すパラメーターに基づいて、文字列内に含まれる単語を返します。

**構文 :**  
`str Word(str string, num WordNumber, str delimiters)`

* string: 返される単語を含む文字列。
* WordNumber: 返すべき単語の番号を指定する数値。
* delimiters: 単語を識別するために使用される区切り記号を表す文字列

**備考:**  
delimiters 内のいずれかの文字で区切られた string 内の各文字列が、単語として識別されます。

* num < 1 の場合、空の文字列を返します。
* string が null の場合、空の文字列を返します。

string に含まれる単語の数が指定より少ないか、区切り記号文字で識別されるどの単語も string に含まれていない場合は、空の文字列が返されます。

**例:**  
`Word("The quick brown fox",3," ")`  
"brown" を返します。

`Word("This,string!has&many separators",3,",!&#")`  
"has" を返します。

## <a name="additional-resources"></a>その他のリソース
* [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect 同期: 同期オプションをカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
