---
title: Horizon API
description: このガイドでは、よく使用される Horizon メソッドについて説明します。
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786789"
---
# <a name="horizon-api"></a>Horizon API 

このガイドでは、よく使用される Horizon メソッドについて説明します。

### <a name="getting-more-information"></a>詳細情報の入手先

Horizon および Defender for IoT プラットフォームでの作業の詳細については、次の情報を参照してください。

- Horizon Open Development Environment (ODE) SDK については、Defender for IoT の担当者にお問い合わせください。
- サポートとトラブルシューティングの情報については、<support@cyberx-labs.com> にお問い合わせください。

- Defender for IoT コンソールから Defender for IoT ユーザー ガイドにアクセスするには、:::image type="icon" source="media/references-horizon-api/profile.png"::: を選択し、 **[Download User Guide]\(ユーザー ガイドのダウンロード\)** を選択します。


## `horizon::protocol::BaseParser`

すべてのプラグインの概要です。 これには 2 つのメソッドがあります。

- 自分の上位で定義されたプラグイン フィルターの処理用。 この方法で、Horizon によってパーサーとの通信方法が認識されます。
- 実際のデータの処理用。

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

プラグインに対して呼び出される最初の関数により、Horizon がそれを認識して登録するためのパーサーのインスタンスが作成されます。

### <a name="parameters"></a>パラメーター 

[なし] :

### <a name="return-value"></a>戻り値

自分のパーサー インスタンスへの shared_ptr。

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

この関数は、上で登録された各プラグインに対して呼び出されます。 

ほとんどの場合、これは空になります。 問題が発生したことを Horizon が認識できるように例外をスローします。

### <a name="parameters"></a>パラメーター 

- 登録を必要とする別のプラグインの config.json で定義されている dissect_as の構造を含むマップ。

### <a name="return-value"></a>戻り値 

uint64_t の種類に処理される登録である uint64_t の配列。 つまり、マップにはポートの一覧が表示され、その値は uin64_t になります。

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

メイン関数。 具体的には、新しいパケットがパーサーに到達するたびに実行される、プラグインのロジックです。 この関数が呼び出され、パケット処理に関連するすべてのものをここで実行する必要があります。

### <a name="considerations"></a>考慮事項

この関数は異なるスレッドから呼び出される可能性があるため、プラグインはスレッド セーフである必要があります。 すべてをスタック上で定義することをお勧めします。

### <a name="parameters"></a>パラメーター

- データの格納と、SDK 関連のオブジェクト (ILayer やフィールドなど) の作成を担当する SDK 制御ユニット。
- 生パケットのデータを読み取るためのヘルパー。 これは、config.json で定義したバイト順で既に設定されています。

### <a name="return-value"></a>戻り値 

処理の結果です。 *[成功]* 、 *[不正な形式]* 、または *[Sanity]\(サニティ\)* のいずれかになります。

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

処理をサニテーション失敗としてマークします。これは、パケットが現在のプロトコルによって認識されないことを意味します。また、Horizon によってそれを他のパーサーに渡す必要があります (同じフィルターに登録されている場合)。

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

コンストラクター

### <a name="parameters"></a>パラメーター 

- config.json での定義に従って、Horizon でログ記録に使用されるエラー コードを定義します。

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

形式に誤りがある結果。これは、パケットをプロトコルとして既に認識していたものの、一部の検証に誤りがあったことを示しています (予約済みのビットがオンになっている、または一部のフィールドが不足している)。

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

コンストラクター

### <a name="parameters"></a>パラメーター  

- config.json で定義されているエラー コード。

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

正常に処理されたことを Horizon に通知します。 成功した場合、パケットは受け入れられました。データは当社に属し、すべてのデータが抽出されました。

## `horizon::protocol::SuccessResult()`

コンストラクターです。 基本的な成功の結果が作成されました。 これは、パケットに関する方向またはその他のメタデータが不明であることを意味します。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

コンストラクターです。

### <a name="parameters"></a>パラメーター 

- 識別された場合は、パケットの方向。 値には *REQUEST*、または *RESPONSE* を指定できます。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

コンストラクターです。

### <a name="parameters"></a>パラメーター

- パケットの方向 (識別されている場合) は、*REQUEST*、*RESPONSE* のいずれかにすることができます。
- [警告]。 これらのイベントは失敗しませんが、Horizon に通知されます。

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

コンストラクターです。

### <a name="parameters"></a>パラメーター 

-  [警告]。 これらのイベントは失敗しませんが、Horizon に通知されます。

## `HorizonID HORIZON_FIELD(const std::string_view &)`

フィールド名への文字列ベースの参照 (たとえば、function_code) を HorizonID に変換します。

### <a name="parameters"></a>パラメーター 

- 変換する文字列。

### <a name="return-value"></a>戻り値

- 文字列から作成された HorizonID。

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

新しいレイヤーを作成します。これにより、プラグインがいくつかのデータを格納する必要があることが Horizon にわかります。 これは、使用する必要のあるベース ストレージ ユニットです。

### <a name="return-value"></a>戻り値

作成されたレイヤーへの参照。そこにデータを追加できます。

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

フィールド管理オブジェクトを取得します。これは、さまざまなオブジェクト (たとえば、ILayer) に対するフィールドの作成を担当します。

### <a name="return-value"></a>戻り値

マネージャーへの参照。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

要求された ID を持つ 64 ビットの新しい数値フィールドをレイヤーに作成します。

### <a name="parameters"></a>パラメーター 

- 前に作成したレイヤー。
- **HORIZON_FIELD** マクロによって作成された HorizonID。
- 格納する生の値。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

要求された ID を持つ新しい文字列フィールドをレイヤーに作成します。 メモリが移動されるため、注意してください。 この値を再び使用することはできません。

### <a name="parameters"></a>パラメーター  

- 前に作成したレイヤー。
- **HORIZON_FIELD** マクロによって作成された HorizonID。
- 格納する生の値。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

要求された ID を持つ新しい生の値 (バイトの配列) フィールドをレイヤーに作成します。 メモリは移動されることにご注意ください。この値を再び使用することはできません。

### <a name="parameters"></a>パラメーター

- 前に作成したレイヤー。
- **HORIZON_FIELD** マクロによって作成された HorizonID。
- 格納する生の値。

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

要求された ID を使用して、配列値 (配列) フィールドを指定した種類のレイヤーに作成します。

### <a name="parameters"></a>パラメーター

- 前に作成したレイヤー。
- **HORIZON_FIELD** マクロによって作成された HorizonID。
- 配列内に格納される値の型。

### <a name="return-value"></a>戻り値

値を追加する必要のある配列への参照。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

前に作成した配列に新しい整数値を追加します。

### <a name="parameters"></a>パラメーター

- 前に作成した配列。
- 配列に格納される生の値。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

前に作成した配列に新しい文字列値を追加します。 メモリは移動されることにご注意ください。この値を再び使用することはできません。

### <a name="parameters"></a>パラメーター

- 前に作成した配列。
- 配列に格納される生の値。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

前に作成した配列に新しい生の値を追加します。 メモリは移動されることにご注意ください。この値を再び使用することはできません。

### <a name="parameters"></a>パラメーター

- 前に作成した配列。
- 配列に格納される生の値。

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

バッファーに少なくとも X バイトが含まれていることを確認します。

### <a name="parameters"></a>パラメーター

存在する必要のあるバイト数。

### <a name="return-value"></a>戻り値

バッファーに少なくとも X バイトが含まれている場合は True。 それ以外の場合は、`False` となります。

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

バイト順に従って、バッファーから uint8 値 (1 バイト) を読み取ります。

### <a name="return-value"></a>戻り値

バッファーから読み取られた値。

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

バイト順に従って、バッファーから uint16 値 (2 バイト) を読み取ります。

### <a name="return-value"></a>戻り値

バッファーから読み取られた値。

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

バイト順に従って、バッファーから uint32 値 (4 バイト) を読み取ります。

### <a name="return-value"></a>戻り値

バッファーから読み取られた値。

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

バイト順に従って、バッファーから uint64 値 (8 バイト) を読み取ります。

### <a name="return-value"></a>戻り値

バッファーから読み取られた値。

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

指定されたサイズの事前割り当て済みメモリに読み取りを行うと、実際にデータがメモリ領域にコピーされます。

### <a name="parameters"></a>パラメーター 

- データのコピー先のメモリ領域。
- メモリ領域のサイズ。このパラメーターでは、コピーするバイト数も定義します。

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

バッファーから文字列への読み取り。

### <a name="parameters"></a>パラメーター 

- 読み取る必要のあるバイト数。

### <a name="return-value"></a>戻り値

文字列のメモリ領域への参照。

## `size_t horizon::general::IDataBuffer::getRemainingData()`

バッファーに残っているバイト数を示します。

### <a name="return-value"></a>戻り値

バッファーの残りサイズ。

## `void horizon::general::IDataBuffer::skip(size_t)`

バッファー内の X バイトをスキップします。

### <a name="parameters"></a>パラメーター

- スキップするバイト数。
