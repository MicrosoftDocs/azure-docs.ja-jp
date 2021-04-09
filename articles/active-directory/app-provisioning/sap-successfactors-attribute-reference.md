---
title: SAP SuccessFactors 属性のリファレンス
description: SuccessFactors-HR ドリブン プロビジョニングによってサポートされる SuccessFactors の属性について説明します。
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255544"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 属性のリファレンス

この記事では、次の情報を取り上げます。

- [SuccessFactors のエンティティと属性](#supported-successfactors-entities-and-attributes)
- [既定の属性マッピング](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>サポートされている SuccessFactors エンティティと属性

以下の表は、次の 2 つのプロビジョニング アプリに既定で含まれる SuccessFactors の属性の一覧を示しています。

- [Active Directory ユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [Azure AD ユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

その他の属性用にスキーマを拡張するには、[SAP SuccessFactors の統合のリファレンス](./sap-successfactors-integration-reference.md#retrieving-additional-attributes)を参照してください。 

| \# | SuccessFactors エンティティ                  | SuccessFactors 属性     | 操作の種類 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Read           |
| 2  | PerPerson                              | personId                     | Read           |
| 3  | PerPerson                              | perPersonUuid                | Read           |
| 4  | PerPersonal                            | displayName                  | Read           |
| 5  | PerPersonal                            | firstName                    | Read           |
| 6  | PerPersonal                            | gender                       | Read           |
| 7  | PerPersonal                            | lastName                     | Read           |
| 8  | PerPersonal                            | middleName                   | Read           |
| 9  | PerPersonal                            | preferredName                | Read           |
| 10 | User                                   | addressLine1                 | Read           |
| 11 | User                                   | addressLine2                 | Read           |
| 12 | User                                   | addressLIne3                 | Read           |
| 13 | User                                   | businessPhone                | Read           |
| 14 | User                                   | cellPhone                    | Read           |
| 15 | User                                   | city                         | Read           |
| 16 | User                                   | country                      | Read           |
| 17 | User                                   | custom01                     | Read           |
| 18 | User                                   | custom02                     | Read           |
| 19 | User                                   | custom03                     | Read           |
| 20 | User                                   | custom04                     | Read           |
| 21 | User                                   | custom05                     | Read           |
| 22 | User                                   | custom06                     | Read           |
| 23 | User                                   | custom07                     | Read           |
| 24 | User                                   | custom08                     | Read           |
| 25 | User                                   | custom09                     | Read           |
| 26 | User                                   | custom10                     | Read           |
| 27 | User                                   | custom11                     | Read           |
| 28 | User                                   | custom12                     | Read           |
| 29 | User                                   | custom13                     | Read           |
| 30 | User                                   | custom14                     | Read           |
| 31 | User                                   | empId                        | Read           |
| 32 | User                                   | homePhone                    | Read           |
| 33 | User                                   | jobFamily                    | Read           |
| 34 | User                                   | nickname                     | Read           |
| 35 | User                                   | state                        | Read           |
| 36 | User                                   | timeZone                     | Read           |
| 37 | User                                   | username                     | Read           |
| 38 | User                                   | zipCode                      | Read           |
| 39 | PerPhone                               | areaCode                     | Read           |
| 40 | PerPhone                               | countryCode                  | Read           |
| 41 | PerPhone                               | 拡張機能                    | Read           |
| 42 | PerPhone                               | phoneNumber                  | Read           |
| 43 | PerPhone                               | phoneType                    | Read           |
| 44 | PerEmail                               | emailAddress                 | 読み取り、書き込み    |
| 45 | PerEmail                               | emailType                    | Read           |
| 46 | EmpEmployment                          | firstDateWorked              | Read           |
| 47 | EmpEmployment                          | lastDateWorked               | Read           |
| 48 | EmpEmployment                          | userId                       | Read           |
| 49 | EmpEmployment                          | isContingentWorker           | Read           |
| 50 | EmpJob                                 | countryOfCompany             | Read           |
| 51 | EmpJob                                 | emplStatus                   | Read           |
| 52 | EmpJob                                 | endDate                      | Read           |
| 53 | EmpJob                                 | startDate                    | Read           |
| 54 | EmpJob                                 | jobTitle                     | Read           |
| 55 | EmpJob                                 | position                     | Read           |
| 65 | EmpJob                                 | customString13               | Read           |
| 56 | EmpJob                                 | managerId                    | Read           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Read           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Read           |
| 59 | EmpJob\.Company                        | company                      | Read           |
| 60 | EmpJob\.Company                        | companyId                    | Read           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Read           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Read           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Read           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Read           |
| 65 | EmpJob\.Department                     | department                   | Read           |
| 66 | EmpJob\.Department                     | departmentId                 | Read           |
| 67 | EmpJob\.Division                       | division                     | Read           |
| 68 | EmpJob\.Division                       | divisionId                   | Read           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Read           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Read           |
| 71 | EmpJob\.Location                       | LocationName                 | Read           |
| 72 | EmpJob\.Location                       | officeLocationAddress        | Read           |
| 73 | EmpJob\.Location                       | officeLocationCity           | Read           |
| 74 | EmpJob\.Location                       | officeLocationCustomString4  | Read           |
| 75 | EmpJob\.Location                       | officeLocationZipCode        | Read           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Read           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Read           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Read           |

## <a name="default-attribute-mapping"></a>既定の属性マッピング

次の表は、上に示した SuccessFactors 属性と AD/Azure AD 属性の間の既定の属性マッピングを示しています。 Azure AD プロビジョニング アプリの [マッピング] ブレードでは、この既定のマッピングを変更して、上記の一覧の属性を含めることができます。 

| \# | SuccessFactors エンティティ                  | SuccessFactors 属性 | 既定の AD/Azure AD 属性マッピング   | 処理に関する注釈                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 一致する属性として使用されます                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[マップされない\- ソース アンカーとして使用される\] | 初期同期中、プロビジョニング サービスは、personUuid を既存の objectGuid\ にリンクします。  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob\.Division                       | division                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.Location                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.Location                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | activeEmploymentsCount=0 の場合、account\ を無効にします。                                           |
