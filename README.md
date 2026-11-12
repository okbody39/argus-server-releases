# Argus Server — Releases

Argus Client 용 **정책서버 / 라이선스 서버**의 배포 파일 저장소입니다. 소스 코드는 여기에 없습니다.
실행 파일은 [Releases](../../releases)에서 받습니다.

## 배포 파일

| 파일 | 용도 |
|---|---|
| `argus-server-X.Y.Z-linux-x64` | Linux 64비트 실행 파일 1개 — glibc 2.17 이상 (RHEL/CentOS 7+, Rocky 8+, Ubuntu 18.04+, Debian 10+) |
| `argus-server-X.Y.Z-windows-x64.exe` | Windows 64비트 실행 파일 1개 — Windows 10 / Server 2016 이상 |
| `SHA256SUMS` | 파일 무결성 확인용 해시 |

**실행 파일 하나가 전부입니다.** 관리 웹 콘솔 · DB(SQLite) · 서비스 등록 기능이 들어 있어 따로 설치할 것이 없습니다.
데이터(`data/`)는 첫 실행 때 실행 파일 옆에 만들어집니다.

## 동작 모드 — 라이선스 키가 정합니다

| 등록한 키 | 동작 |
|---|---|
| 없음 | **평가판 정책서버** (단말 5대) |
| 정책서버 키 | 정책서버 — 단말 정책 · 원격 명령 · 클라이언트 자동 업데이트 · 웹 관리 콘솔 |
| 라이선스 서버 키 | 라이선스 서버 — 좌석 관리만, 웹 콘솔 없이 CLI로 관리 |

라이선스 키 발급에는 **서버 ID**가 필요합니다. 설치할 장비에서 `argus-server server-id`로 확인해 보내 주세요.

## 설치 (OS 서비스 — 부팅 시 자동 시작)

**Linux** (root 권한)
```
sudo mkdir -p /opt/argus
sudo cp argus-server-X.Y.Z-linux-x64 /opt/argus/argus-server && sudo chmod 755 /opt/argus/argus-server
sudo /opt/argus/argus-server service install
```
처음이면 설치 마법사(라이선스 키 · 관리자 비밀번호)가 먼저 뜹니다. systemd 서비스 `argus-server`로 등록되고 전용 계정 `argus`로 실행됩니다.
방화벽은 직접 엽니다(예: `sudo ufw allow 8080/tcp`, `sudo firewall-cmd --add-port=8080/tcp --permanent && sudo firewall-cmd --reload`).

**Windows** (관리자 명령 프롬프트)
```
mkdir C:\Argus
copy argus-server-X.Y.Z-windows-x64.exe C:\Argus\argus-server.exe
C:\Argus\argus-server.exe service install
```
Windows 서비스 `ArgusServer`로 등록되고 방화벽 인바운드 규칙이 함께 추가됩니다. 로그는 `C:\Argus\data\server.log`.

서비스 관리: `service status | start | stop | restart | uninstall` (`uninstall`은 데이터를 지우지 않습니다)

## 자주 쓰는 명령

```
argus-server status              # 모드 · 라이선스 · 좌석 사용량
argus-server server-id           # 라이선스 발급용 서버 ID
argus-server license set <키>    # 라이선스 키 등록 (모드가 바뀌면 재시작 없이 적용)
argus-server admin-password      # 관리자 비밀번호 변경
argus-server help                # 전체 명령
```

기본 포트는 `8080`입니다. 바꾸려면 `--port <포트>` 또는 실행 파일 옆 `.env`에 `PORT=<포트>`.
정책서버 웹 콘솔은 `http://<서버>:8080/` 입니다. 첫 관리자 비밀번호는 설치 마법사에서 정하거나, `data/initial-admin-password.txt`에 있습니다.

## 확인

```
shasum -a 256 -c SHA256SUMS        # macOS / Linux
certutil -hashfile argus-server-X.Y.Z-windows-x64.exe SHA256   # Windows
```

배포 파일은 아직 코드 서명이 없어 Windows 에서 "알 수 없는 게시자" 경고가 뜰 수 있습니다.

© Argus. All rights reserved.
