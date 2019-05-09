---
title: Azure Media Services と Widevine ライセンス テンプレートの概要 | Microsoft Docs
description: このトピックでは、Widevine ライセンスの構成に使用する Widevine ライセンス テンプレートの概要を示します。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c6fc363a7ab9de215647e371a9d3c846f8688bd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548715"
---
# <a name="widevine-license-template-overview"></a>Widevine ライセンス テンプレートの概要 

Azure Media Services では、**Google Widevine** を使用してコンテンツを暗号化できます。 Media Services も Widevine ライセンスを提供するサービスを提供しています。 Azure Media Services API を使用すると、Widevine ライセンスを構成できます。 プレーヤーが Widevine の保護されたコンテンツを再生しようとすると、ライセンスを取得する要求がライセンス配信サービスに送信されます。 ライセンス サービスが要求を承認すると、サービスがライセンスを発行します。 これはクライアントに送信され、指定されたコンテンツの復号化と再生に使用されます。

Widevine ライセンス要求の形式は、JSON メッセージです。  

>[!NOTE]
> 値のない空のメッセージ ("{}") を作成できます。 すると、ライセンス テンプレートが既定値で作成されます。 ほとんどの場合、既定値で問題ありません。 Microsoft に基づくライセンス配信シナリオでは常に既定値が使用されます。 "provider" 値と "content_id" 値を設定する必要がある場合、プロバイダーは Widevine 資格情報を照合する必要があります。

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>JSON メッセージ

| Name | 値 | 説明 |
| --- | --- | --- |
| payload |Base64 でエンコードされた文字列 |クライアントから送信されたライセンス要求です。 |
| content_id |Base64 でエンコードされた文字列 |各 content_key_specs.track_type のキー ID およびコンテンツ キーを取得するために使用される識別子です。 |
| provider |string |コンテンツ キーおよびポリシーを検索するために使用されます。 Microsoft キー配信が Widevine ライセンス配信に使用される場合、このパラメーターは無視されます。 |
| policy_name |string |以前に登録されたポリシーの名前です。 省略可能。 |
| allowed_track_types |enum |SD_ONLY または SD_HD。 どのコンテンツ キーをライセンスに含めるかを制御します。 |
| content_key_specs |JSON 構造体の配列。「コンテンツ キーの仕様」セクションをご覧ください。  |どのコンテンツ キーを返すかについて、より細かく制御します。 詳しくは、「コンテンツ キーの仕様」セクションをご覧ください。 allowed_track_types 値と content_key_specs 値のいずれかのみを指定できます。 |
| use_policy_overrides_exclusively |ブール値、true または false |policy_overrides に指定されたポリシー属性を使用し、前に格納したポリシーをすべて除外します。 |
| policy_overrides |JSON 構造体。「ポリシーのオーバーライド」セクションをご覧ください。 |このライセンスのポリシー設定です。  このアセットに事前定義されたポリシーがある場合、これらの指定された値が使用されます。 |
| session_init |JSON 構造体。「セッションの初期化」セクションをご覧ください。 |必要に応じて指定できるデータがライセンスに渡されます。 |
| parse_only |ブール値、true または false |ライセンス要求は解析されますが、ライセンスは発行されません。 ただし、応答でライセンス要求の値が返されます。 |

## <a name="content-key-specs"></a>コンテンツ キーの仕様
既存のポリシーが存在する場合は、"コンテンツ キーの仕様" に値を指定する必要はありません。このコンテンツに関連付けられた既存のポリシーを使用して、HDCP (高帯域幅デジタル コンテンツ保護) および CGMS (Copy General Management System) など、出力の保護が決まります。 既存のポリシーが Widevine ライセンス サーバーに登録されていない場合、コンテンツ プロバイダーはライセンス要求に値を挿入できます。   

use_policy_overrides_exclusively オプションに関係なく、各 content_key_specs 値がすべてのトラックに対して指定されている必要があります。 

| Name | 値 | 説明 |
| --- | --- | --- |
| content_key_specs track_type |string |トラックの種類の名前です。 ライセンス要求で content_key_specs が指定されている場合は、すべてのトラックの種類を明示的に指定します。 指定しないと、直前の 10 秒間を再生できません。 |
| content_key_specs  <br/> security_level |uint32 |再生に関するクライアントの堅牢性の要件を定義します。 <br/> - ソフトウェアベースのホワイトボックス暗号化が必須です。 <br/> - ソフトウェア暗号化と難読化デコーダーが必須です。 <br/> - キー マテリアルと暗号化の操作を、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。 <br/> - コンテンツの暗号化とデコードを、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。  <br/> - 暗号化、デコード、およびメディア (圧縮済みおよび圧縮解除済み) のすべての処理を、ハードウェアを基盤にした信頼できる実行環境で実行する必要があります。 |
| content_key_specs <br/> required_output_protection.hdc |文字列、HDCP_NONE、HDCP_V1、HDCP_V2 のいずれか |HDCP が必須かどうかを示します。 |
| content_key_specs <br/>key |Base64<br/>でエンコードされた文字列 |このトラックで使用するコンテンツ キーです。指定した場合、track_type または key_id が必要です。 コンテンツ プロバイダーはこのオプションを使用して、Widevine ライセンス サーバーでキーを生成または検索する代わりに、このトラックのコンテンツ キーを挿入できます。 |
| content_key_specs.key_id |Base64 でエンコードされた文字列バイナリ、16 バイト |キーの一意識別子です。 |

## <a name="policy-overrides"></a>ポリシーのオーバーライド
| Name | 値 | 説明 |
| --- | --- | --- |
| policy_overrides&#46;can_play |ブール値、true または false |コンテンツの再生が許可されていることを示します。 既定値は false です。 |
| policy_overrides&#46;can_persist |ブール値、true または false |オフラインで使用するために、ライセンスを非揮発性ストレージに保持できることを示します。 既定値は false です。 |
| policy_overrides&#46;can_renew |ブール値、true または false |このライセンスの更新が許可されていることを示します。 true の場合、ライセンスの期間をハートビートにより延長できます。 既定値は false です。 |
| policy_overrides&#46;license_duration_seconds |int64 |この特定のライセンスの期間を示します。 値 0 は、期間に制限がないことを示します。 既定値は 0 です。 |
| policy_overrides&#46;rental_duration_seconds |int64 |再生が許可されている期間を示します。 値 0 は、期間に制限がないことを示します。 既定値は 0 です。 |
| policy_overrides&#46;playback_duration_seconds |int64 |ライセンス期間内の再生開始後の視聴期間です。 値 0 は、期間に制限がないことを示します。 既定値は 0 です。 |
| policy_overrides&#46;renewal_server_url |string |このライセンスのすべてのハートビート (更新) 要求を、指定した URL に転送します。 このフィールドは、can_renew が true の場合にのみ使用されます。 |
| policy_overrides&#46;renewal_delay_seconds |int64 |license_start_time から、最初に更新が試行されるまでの秒数です。 このフィールドは、can_renew が true の場合にのみ使用されます。 既定値は 0 です。 |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |ライセンスの更新要求が失敗した場合の後続の要求までの遅延 (秒) を指定します。 このフィールドは、can_renew が true の場合にのみ使用されます。 |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |ライセンス サーバーのバックエンドの問題が原因で、更新が試行されているがまだ成功していないときに、再生の継続が許可される期間です。 値 0 は、期間に制限がないことを示します。 このフィールドは、can_renew が true の場合にのみ使用されます。 |
| policy_overrides&#46;renew_with_usage |ブール値、true または false |使用が開始されたときに、更新のためにライセンスが送信されることを示します。 このフィールドは、can_renew が true の場合にのみ使用されます。 |

## <a name="session-initialization"></a>セッションの初期化
| Name | 値 | 説明 |
| --- | --- | --- |
| provider_session_token |Base64 でエンコードされた文字列 |このセッション トークンはライセンスに渡され、後続の更新に含まれます。 セッション トークンは、セッションの範囲を超えて保持されません。 |
| provider_client_token |Base64 でエンコードされた文字列 |ライセンスの応答で返信されるクライアント トークンです。 ライセンス要求にクライアント トークンが含まれる場合、この値は無視されます。 クライアント トークンは、ライセンス セッションの範囲を超えて保持されます。 |
| override_provider_client_token |ブール値、true または false |値が false で、ライセンス要求にクライアント トークンが含まれているときは、この構造体でクライアント トークンを指定している場合でも要求からのトークンが使用されます。 true の場合は、常に、この構造体で指定したトークンが使用されます。 |

## <a name="configure-your-widevine-license-with-net"></a>.NET を使用した Widevine ライセンスの構成 

Media Services には、Widevine ライセンスを構成するためのクラスが用意されています。 ライセンスを構築するには、JSON を [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate) に渡します。

テンプレートを構成するには、次の操作を実行できます。

### <a name="directly-construct-a-json-string"></a>JSON 文字列を直接構築します。

この方法は、エラーが発生しやすい可能性があります。 「[定義が必要なクラスと JSON へのシリアル化](#classes)」で説明されているその他の方法を使用することをお勧めします。

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a id="classes"></a>定義が必要なクラスと JSON へのシリアル化

#### <a name="define-classes"></a>クラスの定義

次の例では、Widevine JSON スキーマにマップするクラスの定義の例を示します。 それらのクラスを JSON 文字列にシリアル化する前に、クラスをインスタンス化できます。  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>ライセンスの構成

前のセクションで定義したクラスを使用して、[WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate) を構成するための JSON を作成します。

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="next-steps"></a>次の手順

[DRM での保護](protect-with-drm.md)に関するページを参照してください。
