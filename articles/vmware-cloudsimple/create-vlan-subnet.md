---
title: VLAN/서브넷 만들기 - 클라우드간이별 Azure VMware 솔루션
description: CloudSimple별 Azure VMware 솔루션 - 프라이빗 클라우드에 대한 VLAN/서브넷을 만들고 관리하는 방법을 설명한 다음 방화벽 규칙을 적용하는 방법을 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565998"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>프라이빗 클라우드를 위한 VLAN/서브넷 생성 및 관리

네트워크 페이지의 VLAN/서브넷 탭을 열어 프라이빗 클라우드에 대한 VLAN/서브넷을 만들고 관리합니다. VLAN/서브넷을 만든 후 방화벽 규칙을 적용할 수 있습니다.

## <a name="create-a-vlansubnet"></a>VLAN/서브넷 만들기

1. [CloudSimple 포털에 액세스하고](access-cloudsimple-portal.md) 사이드 메뉴에서 **네트워크를** 선택합니다.
2. **VLAN/서브넷을 선택합니다.**
3. **VLAN/서브넷 만들기를 클릭합니다.**

    ![VLAN/서브넷 페이지](media/vlan-subnet-page.png)

4. 새 VLAN/서브넷에 대한 프라이빗 클라우드를 선택합니다.
5. VLAN ID를 입력합니다.
6. 서브넷 이름을 입력합니다.
7. VLAN(서브넷)에서 라우팅을 사용하려면 서브넷 CIDR 범위를 지정합니다. CIDR 범위가 온-프레미스 서브넷, Azure 서브넷 또는 게이트웨이 서브넷과 겹치지 않도록 합니다.
8. **제출**을 클릭합니다.

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 프라이빗 클라우드당 30개의 VLAN 할당량이 있습니다. [지원에 문의하여](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)이러한 제한을 늘릴 수 있습니다.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN 정보를 사용하여 vSphere에서 분산 포트 그룹 설정

vSphere에서 분산 포트 그룹을 만들려면 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">vSphere 네트워킹 가이드의</a>VMware 항목 '분산 포트 그룹 추가'의 지침을 따릅니다. 분산 포트 그룹을 설정할 때 CloudSimple 구성에서 VLAN 정보를 제공합니다.

![분산 포트 그룹](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>방화벽 테이블 선택

방화벽 테이블 및 관련 규칙은 **네트워크 > 방화벽 테이블** 페이지에 정의되어 있습니다. 프라이빗 클라우드의 VLAN/서브넷에 적용할 방화벽 테이블을 선택하려면 **VLAN/서브넷** 페이지에서 **방화벽 테이블 첨부 파일을** 클릭합니다. [방화벽 테이블](firewall.md) 설정 및 규칙 정의에 대한 지침은 방화벽 테이블을 참조하십시오.

![방화벽 테이블 링크](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 서브넷은 하나의 방화벽 테이블과 연결할 수 있습니다. 방화벽 테이블은 여러 서브넷과 연결할 수 있습니다.

## <a name="edit-a-vlansubnet"></a>VLAN/서브넷 편집

VLAN/서브넷의 설정을 편집하려면 **VLAN/서브넷** 페이지에서 선택하고 **편집** 아이콘을 클릭합니다. 변경한 내용과 **하위 멧을 클릭합니다.**

## <a name="delete-a-vlansubnet"></a>VLAN/서브넷 삭제

VLAN/서브넷을 삭제하려면 **VLAN/서브넷 페이지에서 VLAN/서브넷** **Delete** 삭제 아이콘을 클릭합니다. **확인하려면 삭제를 클릭합니다.**
