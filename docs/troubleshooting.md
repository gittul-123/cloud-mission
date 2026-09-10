# 트러블슈팅 보고서

## 사례 1: Windows PowerShell에서 `ssh` 명령어 인식 불가

### 증상
Windows PowerShell에서 EC2 인스턴스에 접속하기 위해 다음 명령어를 실행했다.

```
ssh -i my-mission-key.pem ubuntu@43.203.154.65
```

다음과 같은 에러가 발생하며 접속이 진행되지 않았다.

```
ssh : 'ssh' 용어가 cmdlet, 함수, 스크립트 파일 또는 실행할 수 있는 프로그램 이름으로
인식되지 않습니다.
    + CategoryInfo          : ObjectNotFound: (ssh:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

### 원인 가설
최신 Windows(10/11)에는 OpenSSH 클라이언트가 기본 내장되어 있다고 알려져 있으나,
이 PC에는 해당 기능이 설치되어 있지 않을 가능성이 있다고 판단했다.

### 검증
아래 명령어로 OpenSSH 클라이언트 실행 파일이 실제로 존재하는지 확인했다.

```
Test-Path C:\Windows\System32\OpenSSH\ssh.exe
```

결과는 `False`로, 해당 기능이 설치되어 있지 않음을 확인했다.

이어서 Windows 기본 제공 명령어로 직접 설치를 시도했다.

```
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

그러나 다음과 같은 에러로 설치에 실패했다.

```
Add-WindowsCapability : 지정된 모듈을 찾을 수 없습니다.
    + FullyQualifiedErrorId : Microsoft.Dism.Commands.AddWindowsCapabilityCommand
```

이를 통해 이 PC의 Windows 기능 설치 시스템(DISM) 자체에 제약이 있어,
표준 방법으로는 OpenSSH 클라이언트를 설치할 수 없다고 판단했다.

### 조치
대안으로 Git for Windows를 설치했다. Git for Windows에는 SSH 클라이언트가 포함된
Git Bash 터미널이 함께 제공되므로, 이를 통해 SSH 접속 문제를 우회할 수 있다고
판단했다.

설치 후 Git Bash에서 키 파일 권한을 설정하고 접속을 재시도했다.

```
chmod 400 my-mission-key.pem
ssh -i my-mission-key.pem ubuntu@43.203.154.65
```

### 결과
Git Bash에서 동일한 명령어를 실행한 결과, EC2 인스턴스에 정상적으로 접속되었다.

```
Welcome to Ubuntu 26.04 LTS (GNU/Linux 7.0.0-1006-aws x86_64)
```

이후 모든 SSH 접속 및 원격 명령 실행(Nginx 설치 등)은 Git Bash를 통해 정상적으로
진행되었다.

### 재발 방지
- Windows 환경에서 AWS 실습을 시작하기 전, `ssh -V` 명령어로 SSH 클라이언트 설치
  여부를 사전에 확인하는 절차를 준비 단계 체크리스트에 추가한다.
- Git for Windows는 이후 GitHub 저장소 관리(커밋/푸시)에도 필요하므로, 실습 초기
  단계에서 미리 설치해 SSH 클라이언트 문제를 예방한다.
- Windows 내장 기능(OpenSSH) 설치가 실패하는 환경에서는 곧바로 Git Bash 같은
  대체 수단으로 전환하여 시간을 절약한다.
