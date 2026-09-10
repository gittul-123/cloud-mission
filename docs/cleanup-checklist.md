# 리소스 정리 체크리스트

과제 실습에 사용한 AWS 리소스를 아래 순서(의존성 역순)로 정리했다.
상위 리소스(VPC 등)는 하위 리소스가 남아있으면 삭제가 거부되므로,
EC2 -> 부속 리소스 -> 네트워크 구성 요소 순으로 진행한다.

## 정리 순서 및 확인 항목

- [x] EC2 인스턴스 종료(Terminate) 확인 — `my-mission-server`
- [x] EBS 볼륨 삭제 확인 (미사용 볼륨 포함)
- [x] Elastic IP 확인 — 별도 할당하지 않았으므로 해당 없음 (자동 할당된 Public IPv4는 인스턴스 종료 시 자동 반환됨)
- [x] Security Group 삭제 확인 — `my-mission-sg`
- [x] Route Table 삭제 확인 — `public-rt` (서브넷 연결 해제 후 삭제)
- [x] Internet Gateway 분리(Detach) 및 삭제 확인 — `my-mission-igw`
- [x] Subnet 삭제 확인 — `public-subnet`
- [x] VPC 삭제 확인 — `my-mission-vpc`
- [x] NAT Gateway — 생성하지 않았으므로 해당 없음
- [x] ELB/ALB — 생성하지 않았으므로 해당 없음
- [x] RDS — 생성하지 않았으므로 해당 없음
- [ ] Key Pair 삭제 확인 (선택) — `my-mission-key`
- [ ] Billing Dashboard에서 과금 항목이 남지 않았는지 확인 (권장)

## 참고

기본 VPC(Default VPC) 및 기본 리소스는 이번 과제에서 직접 생성한 것이 아니므로
정리 대상에서 제외했다.
