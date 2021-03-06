---
title: 사용자 지정 정책에서 SAML 발급사에 대한 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 SAML(보안 어설션 태그 언어 토큰) 발급사에 대한 기술 프로필을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967262"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 SAML 토큰 발급사에 대한 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)는 각 인증 흐름을 처리할 때 여러 형식의 보안 토큰을 내보냅니다. SAML 토큰 발급자에 대한 기술 프로필은 SAML 토큰을 방출하여 통신 사업자 응용 프로그램(서비스 공급자)에게 반환됩니다. 일반적으로 이 기술 프로필은 사용자 경험의 마지막 오케스트레이션 단계입니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. **OutputTokenFormat** 요소를 `SAML2`로 설정합니다.

다음 예제는 `Saml2AssertionIssuer`의 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>입력, 출력 및 유지 클레임

**InputClaims**, **OutputClaims** 및 **PersistClaims** 요소가 비어 있거나 없습니다. **InutputClaimsTransformations** 및 **OutputClaimsTransformations** 요소도 없습니다.

## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| IssuerUri | 예 | SAML 응답에 나타나는 발급자 이름입니다. 값은 의존자 응용 프로그램에서 구성된 것과 같아야 합니다. |

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소에는 다음 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| MetadataSigning | yes | SAML 메타데이터에 서명을 하는 데 사용할 X509 인증서(RSA 키 집합)입니다. Azure AD B2C는 이 키를 사용하여 메타데이터에 서명을 합니다. |
| SamlMessageSigning| yes| SAML 메시지에 서명하는 데 사용할 X509 인증서(RSA 키 집합)를 지정합니다. Azure AD B2C는 이 키를 사용하여 응답 `<samlp:Response>` 송신에 서명합니다.|

## <a name="session-management"></a>세션 관리

Azure AD B2C SAML 세션을 공급자 응용 프로그램 간에 구성하려면 `UseTechnicalProfileForSessionManagement` 요소의 특성인 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 세션을 참조합니다.

## <a name="next-steps"></a>다음 단계

SAML 발급자 기술 프로필 사용에 대한 예는 다음 문서를 참조하십시오.

- [Azure AD B2C에서 SAML 응용 프로그램 등록](connect-with-saml-service-providers.md)












