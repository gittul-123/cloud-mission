# cloud-mission

## 제출물

### 아키텍쳐 다이어그램

- 제출 최소 규격 : docs/architecture.png
(https://github.com/gittul-123/cloud-mission/blob/main/docs/architecture.png)
![image](./docs/architecture.png)


### 웹 서비스 외부 접속 증빙

- 방식 1개를 선택해 외부에서 접속을 검증한다
- 선택 방식 : (A) 브라우저 접속
- URL : http://43.203.154.65/ 
- 제출 최소 규격: docs/access-screenshot.png
(https://github.com/gittul-123/cloud-mission/blob/main/docs/access-screenshot.png)
![image](./docs/access-screenshot.png)

### 트러블슈팅 보고서 1개

- 제출 최소 규격 : docs/troubleshooting.md
(https://github.com/gittul-123/cloud-mission/blob/main/docs/troubleshooting.md)

### 리소스 정리 체크리스트 1개

- 제출 최소 규격 : docs/cleanup-checklist.md
(https://github.com/gittul-123/cloud-mission/blob/main/docs/cleanup-checklist.md)


## 설계 요약

**네트워크 / 라우팅**
Public Subnet(`10.0.1.0/24`)의 Route Table(`public-rt`)에는 `0.0.0.0/0 → Internet Gateway` 경로를 명시적으로 연결했다. 이 경로가 없으면 EC2에 Public IP가 있어도 실제로 외부와 트래픽을 주고받을 수 없기 때문에, VPC 내부 통신(`10.0.0.0/16 → local`)과 별개로 반드시 필요하다.

**보안 그룹 (Security Group)**
인바운드는 두 규칙만 허용했다.
- HTTP(80): `0.0.0.0/0` — 웹 서비스는 누구나 접근 가능해야 하므로 전체 공개
- SSH(22): 학습자 개인 공인 IP `/32` — 서버 관리용 포트는 관리자만 접근해야 하므로 최소 범위로 제한

`0.0.0.0/0`에 대한 전체 포트(0-65535) 허용 규칙은 생성하지 않았다.

**IAM 최소 권한**
실습용 IAM 사용자(`cloud-mission-user`)에는 `AmazonEC2FullAccess`, `AmazonVPCFullAccess`만 연결했다. `AdministratorAccess`는 부여하지 않았으며, S3/RDS 등 실습과 무관한 서비스 권한도 부여하지 않았다. Security Group이 "네트워크 접근"을 통제한다면, IAM은 "AWS 리소스 관리 작업 권한"을 통제한다는 점에서 서로 다른 계층의 보안 경계다.


