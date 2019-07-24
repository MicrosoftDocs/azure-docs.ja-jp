---
title: Media Services PlayReady ライセンス テンプレートの概要
description: このトピックでは、PlayReady ライセンスの設定に使用する PlayReady ライセンス テンプレートの概要を示します。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d2831472bcddf3480a87e69661b39e4ec715c786
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58257697"
---
# <a name="media-services-playready-license-template-overview"></a>Media Services PlayReady ライセンス テンプレートの概要
Azure Media Services では、PlayReady ライセンスの配信サービスが提供されるようになりました。 プレーヤー (Silverlight など) は、PlayReady で保護されたコンテンツを再生しようとする際に、ライセンス配信サービスにライセンス取得要求を送信します。 ライセンス サービスはその要求を承認した後、ライセンスを発行します。このライセンスはクライアントに送信され、指定されたコンテンツの暗号化解除と再生に用いられます。

Media Services では、PlayReady ライセンス設定用の API も提供されます。 ライセンスには、保護されたコンテンツをユーザーが再生しようとしたときに PlayReady デジタル著作権管理 (DRM) ランタイムが適用する権限や制限が含まれます。
指定可能な PlayReady ライセンス制限の例の一部を以下に示します。

* ライセンスが有効になる日時。
* ラインセンスの有効期限が切れる日時の値。 
* クライアントの永続的なストレージに保存されるライセンスの場合。 永続的なライセンスは、通常、コンテンツのオフライン再生を可能にするために使用します。
* コンテンツを再生するためにプレーヤーが満たす必要のある最低限のセキュリティ レベル。 
* 音声/ビデオ コンテンツの出力を制御する出力保護レベル。 
* 詳細については、『[PlayReady Compliance Rules](https://www.microsoft.com/playready/licensing/compliance/)』(PlayReady のコンプライアンス ルール) ドキュメントの「Output Controls」(出力制御) セクション (3.5) を参照してください。

> [!NOTE]
> 現時点では、PlayReady ライセンスの PlayRight のみを設定することができます。 このライセンスは必須です。 PlayRight を使用すると、クライアントはコンテンツを再生できるようになります。 PlayRight を使用して再生に固有の制限を構成することもできます。 詳細については、「 [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight)」を参照してください。
> 
> 

Media Services を使用して PlayReady ライセンスを設定するには、Media Services PlayReady ライセンス テンプレートを構成する必要があります。 このテンプレートは XML で定義されます。

次の例では、基本的なストリーミング ライセンスを構成する最も簡単な (および最も一般的な) テンプレートを示します。 このライセンスを使用すると、クライアントは PlayReady で保護されたコンテンツを再生できるようになります。

    <?xml version="1.0" encoding="utf-8"?>
    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                      xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <PlayRight />
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

XML は、「PlayReady ライセンス テンプレート XML スキーマ」セクションで定義された PlayReady ライセンス テンプレート XML スキーマに準拠しています。

Media Services は、XML へのシリアル化および XML からの逆シリアル化に使用できる一連の .NET クラスも定義します。 主要なクラスの説明については、「[ライセンス テンプレートの構成に使用する Media Services の .NET クラス](media-services-playready-license-template-overview.md#classes)」をご覧ください。

エンドツーエンドでの .NET クラスを使用した PlayReady ライセンス テンプレートの構成例については、「 [PlayReady 動的暗号化とライセンス提供サービスの使用](media-services-protect-with-playready-widevine.md)」を参照してください。

## <a id="classes"></a>ライセンス テンプレートの構成に使用する Media Services の .NET クラス
次のクラスは、Media Services PlayReady ライセンス テンプレートの設定に使用される主な .NET クラスです。 これらのクラスは、 [PlayReady ライセンス テンプレート XML スキーマ](media-services-playready-license-template-overview.md#schema)で定義された型にマップされます。

[MediaServicesLicenseTemplateSerializer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer.aspx) クラスは、Media Services ライセンス テンプレート XML へのシリアル化およびこの XML からの逆シリアル化に使用します。

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate.aspx): このクラスは、ユーザーに送り返される応答のためのテンプレートを表します。 このクラスには、ライセンス サーバーとアプリケーションの間のカスタム データ文字列のフィールド (カスタムのアプリケーション ロジックに役立ちます) が含まれています。 また、1 つ以上のテンプレートのリストが含まれています。

テンプレート階層で "最上位" のクラスとして、応答用テンプレートにはライセンス テンプレートのリストが含まれています。 ライセンス テンプレートには、シリアル化されるテンプレート データを構成する他のすべてのクラスが (直接的または間接的に) 含まれています。

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate.aspx): このクラスは、ユーザーに返される PlayReady ライセンスを作成するために使用されるライセンス テンプレートを表します。 これにはライセンスのコンテンツ キーのデータが含まれています。 また、コンテンツ キーの使用時に PlayReady DRM ランタイムが適用する必要がある権限または制限も含まれます。

### <a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright.aspx): このクラスは、PlayReady ライセンスの PlayRight を表します。 このクラスにより、ユーザーは、ライセンスおよび PlayRight 自体で設定された任意の (再生固有ポリシーに関する) 制限の対象となるコンテンツを再生できるようになります。 PlayRight に関するポリシーの多くは、コンテンツを再生できる出力の種類を制御する出力制限に関係しています。 また、特定の出力が使用されたときに適用する必要がある制限も含まれます。 たとえば、DigitalVideoOnlyContentRestriction が有効な場合、DRM ランタイムはデジタル出力でのみビデオを表示できます (アナログ ビデオ出力はコンテンツを渡すことができません)。

> [!IMPORTANT]
> こうした種類の制限は強力ですが、コンシューマー エクスペリエンスに影響を及ぼす可能性があります。 出力保護が極端に限定的な場合、一部のクライアントでコンテンツを再生できなくなる可能性があります。 詳細については、[PlayReady のコンプライアンス ルール](https://www.microsoft.com/playready/licensing/compliance/)に関するドキュメントを参照してください。
> 
> 

Silverlight でサポートされる保護レベルの例については、「[Silverlight での出力保護のサポート](https://go.microsoft.com/fwlink/?LinkId=617318)」を参照してください。

## <a id="schema"></a>PlayReady ライセンス テンプレート XML スキーマ
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
      <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
      <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Unspecified" />
          <xs:enumeration value="UltravioletDownload" />
          <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
      <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
      <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence />
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
      <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:PlayReadyContentKey">
            <xs:sequence>
              <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
      <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
          <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
          <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
      <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
          <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
          <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
          <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
      <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
          <xs:enumeration value="Nonpersistent" />
          <xs:enumeration value="Persistent" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
      <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
          <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
          <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
            <xs:annotation>
              <xs:appinfo>
                <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
              </xs:appinfo>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:complexType>
      <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
      <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
          <xs:enumeration value="NotAllowed" />
          <xs:enumeration value="Allowed" />
          <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
      </xs:simpleType>
      <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
      <xs:complexType name="ScmsRestriction">
        <xs:sequence>
          <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
    </xs:schema>



## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

