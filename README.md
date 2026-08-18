# 📡 Telecom Security Engineering & TSG Technical Guide

이씨큐벨리에서 글로벌 통신 보안 솔루션인 **SecurityGen TSG(Telecom Security Guard)**를 전담하며 쌓은 실무 지식과 엔지니어링 가이드라인을 정리한 저장소입니다. 
(※ 회사 보안 규정상 민감한 내부 설정, 고객사 IP, 벤더사 핵심 기밀은 모두 삭제했습니다.)

## 🎯 핵심 기술 역량 (Core Skills)
* **Telecom Core Net:** 5G/LTE 핵심망 트래픽 모니터링 환경 분석
* **Protocol Analysis:** GTPv2, Diameter, SS7 시그널링 프로토콜 에러 로그 추적
* **OS/Infra/Web:** Linux 환경 기반의 보안 솔루션 트러블슈팅 및 Nginx 웹 서버 하드닝

---

## 🛠️ 실무 운영 및 트러블슈팅 가이드

### 1. TSG 서비스 관리 및 데몬 재시작
통신망 시그널링 트래픽 탐지 이상 또는 데몬 비정상 작동 시, `systemctl` 명령어로 방화벽(FW) 및 네트워크 핵심 서비스를 제어합니다.
```bash
# Diameter 방화벽 서비스 재시작
sudo systemctl restart tsg-fw-diameter.service

# 통신 네트워크 인터페이스 서비스 상태 확인 및 재시작
sudo systemctl status ace-net-diameter.service
sudo systemctl restart ace-net-diameter.service
```

### 2. Nginx 웹 서버 보안 강화 및 취약점 조치
솔루션 내부 웹 인터페이스 및 프록시 서버의 정보 노출 취약점을 최소화하기 위해 Nginx 설정을 직접 점검하고 조치한 항목입니다.
```bash
# Nginx 웹 서버 설정 파일 편집 (버전 정보 노출 비활성화)
sudo vi /etc/nginx/conf.d/redirect.conf

# 아래 설정 항목의 주석(#)을 제거하거나 추가하여 활성화
server_tokens off;

# Nginx 설정 파일 구문 오류 검증 후 서비스 재시작
sudo nginx -t
sudo systemctl restart ace.target nginx
```
* **조치 사유:** 에러 페이지나 HTTP 헤더에서 Nginx 버전 정보가 노출될 경우, 알려진 취약점(CVE)을 이용한 표적 공격의 타겟이 될 수 있어 정보를 숨기는 하드닝 작업을 수행했습니다.

---

## 🤝 [조직 기여] 사내 기존 솔루션(GateONE) 온보딩 개선
* **배경:** 입사 후 조직을 보니 전사 메인 솔루션(GateONE 시스템 접근제어)의 신입 교육이 매뉴얼 없이 "모르면 구두로 물어보라"는 식이라, 신입들이 실무 독립까지 평균 6개월씩 고생(삽질)하는 비효율이 있었습니다.
* **행동:** 제가 맡은 TSG 솔루션 외적으로 선배들의 반복 질문 내용, 자주 터지는 장애 패턴, Linux 필수 명령어를 직접 취합하여 **'GateONE 기술 가이드북'**으로 표준 문서화를 주도했습니다.
* **효과:** 신입이 질문 전 문서를 보고 1차 진단을 할 수 있게 만들어, 팀 내 커뮤니케이션 병목을 줄이고 신입 적응 기간을 단축시키는 기반을 만들었습니다.
