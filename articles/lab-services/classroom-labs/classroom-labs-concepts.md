---
title: 클래스룸 랩 개념 - Azure 랩 서비스 | 마이크로 소프트 문서
description: 랩 서비스의 기본 개념과 랩을 쉽게 만들고 관리할 수 있는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526232"
---
# <a name="classroom-labs-concepts"></a>클래스룸 랩 개념

다음 목록에는 주요 랩 서비스 개념 및 정의가 포함되어 있습니다.

## <a name="quota"></a>할당량

할당량은 교사가 학생이 랩 VM을 사용하도록 설정할 수 있는 시간 제한(시간)입니다. 0 또는 특정 시간으로 설정할 수 있습니다. 할당량이 0으로 설정된 경우 학생은 일정이 실행 중이거나 교사가 학생의 가상 컴퓨터를 수동으로 켜는 경우에만 가상 컴퓨터를 사용할 수 있습니다.  

할당량 시간은 학생이 랩 VM을 스스로 시작할 때 계산됩니다.  교사가 학생에 대한 랩 VM을 수동으로 시작하는 경우 할당량 시간은 해당 학생에 사용되지 않습니다.

## <a name="schedules"></a>일정

일정은 학생이 수업 시간에 사용할 수 있도록 교사가 수업에 대해 만들 수 있는 시간 슬롯입니다.  일정은 일회성이거나 되풀이될 수 있습니다.  일정이 실행 중일 때 할당량 시간이 사용되지 않습니다.

일정에는 표준, 시작 전용 및 중지 전용의 세 가지 유형이 있습니다.

- **표준**.  이 일정은 지정된 시작 시간에 모든 학생 VM을 시작하고 지정된 중지 시간에 모든 학생 VM을 종료합니다.
- **시작 합니다.**   이 일정은 지정된 시간에 모든 학생 VM을 시작합니다.  학생이 Azure Lab Services 포털을 통해 VM을 중지하거나 중지 일정도 발생할 때까지 학생 VM이 중지되지 않습니다.
- **중지합니다.**  이 일정은 지정된 시간에 모든 학생 VM을 중지합니다.  

## <a name="template-virtual-machine"></a>템플릿 가상 머신

랩의 템플릿 가상 머신은 모든 사용자의 가상 머신이 만들어지는 기본 가상 시스템 이미지입니다. 트레이너/랩 작성자는 템플릿 가상 컴퓨터를 설정하고 교육 참석자에게 랩을 수행하도록 교육하려는 소프트웨어로 구성합니다. 템플릿 VM을 게시할 때 Azure Lab Services는 템플릿 VM을 기반으로 랩 VM을 만들거나 업데이트합니다.

## <a name="user-profiles"></a>사용자 프로필

이 문서에서는 Azure Lab Services에 있는 다른 사용자 프로필을 설명합니다.

### <a name="lab-account-owner"></a>랩 계정 소유자

Azure 구독을 소유한 조직내 클라우드 리소스의 IT 관리자는 일반적으로 랩 계정 소유자 역할을 하며 다음 작업을 수행합니다.

- 조직에 대한 랩 계정을 설정합니다.
- 모든 랩에서 정책을 관리하고 구성합니다.
- 조직의 사람들에게 랩 계정으로 랩을 만들 수있는 권한을 부여합니다.

### <a name="professor"></a>교수

일반적으로 강사 또는 온라인 강사와 같은 사용자는 랩 계정에 속한 클래스룸 랩을 만듭니다. 강사가 수행하는 작업은 다음과 같습니다.

- 클래스룸 랩을 만듭니다.
- 랩에서 가상 머신을 만듭니다.
- 가상 머신에 적절한 소프트웨어를 설치합니다.
- 랩에 액세스할 수 있는 사용자를 지정합니다.
- 학생들에게 랩에 대한 등록 링크를 제공합니다.

### <a name="student"></a>학생

학생이 수행하는 작업은 다음과 같습니다.

- 랩 사용자는 랩 작성자로부터 받은 등록 링크를 사용하여 랩에 등록합니다.
- 랩의 가상 머신에 연결하고 수업, 과제 및 프로젝트를 수행하는 데 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure Lab Services를 사용하여 클래스룸 랩을 만드는 데 필요한 랩 계정을 설정합니다.

- [랩 계정 설정](tutorial-setup-lab-account.md)
