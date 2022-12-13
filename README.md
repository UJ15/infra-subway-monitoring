<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>


### 1단계 - 웹 성능 테스트
#### 측정 결과
- 측정 사이트 : https://pagespeed.web.dev/
- 대상 URL
    - 서울교통공사 : http://www.seoulmetro.co.kr/kr/cyberStation.do
    - 네이버지도 : https://m.map.naver.com/subway/subwayLine.naver?region=1000
    - 카카오맵 : https://m.map.kakao.com/
    - Running Map : https://shshon-infra.o-r.kr/

| 모바일 | 서울교통공사 | 네이버지도 | 카카오맵  | Running Map |
|-----|--------|-------|-------|-------------|
| FCP | 6.8s   | 2.2s  | 1.7s  | **14.8s**   |
| SI  | 10.8s  | 5.6s  | 6.2s  | **14.8s**   |
| LCP | 11.0s  | 7.2s  | 6.7s  | **15.3s**   |
| TTI | 8.3s   | 6.5s  | 4.5s  | **15.3s**   |
| TBT | 200ms  | 450ms | 60ms  | **480ms**   |
| CLS | 0.00   | 0.03  | 0.005 | **0.042**   |

| 데스크탑 | 서울교통공사 | 네이버지도 | 카카오맵  | Running Map |
|------|--------|-------|-------|-------------|
| FCP  | 1.6s   | 0.5s  | 0.5s  | **2.7s**    |
| SI   | 3.3s   | 1.3s  | 2.3s  | **3.5s**    |
| LCP  | 1.7s   | 1.0s  | 1.3s  | **2.8s**    |
| TTI  | 2.0s   | 0.6s  | 0.7s  | **2.8s**    |
| TBT  | 80ms   | 0ms   | 0ms   | **30ms**    |
| CLS  | 0.001  | 0.006 | 0.039 | **0.004**   |

#### 용어 정리
- FCP(First Contentful Paint)
    - 콘텐츠가 포함된 첫 페인트는 첫 번째 텍스트 또는 이미지가 표시되는 시간을 나타냅니다.
- SI(Speed Index)
    - 속도 색인은 페이지 콘텐츠가 얼마나 빨리 표시되는지 보여줍니다.
- LCP(Largest Contentful Paint)
    - 콘텐츠가 포함된 최대 페인트는 최대 텍스트 또는 이미지가 표시되는 시간을 나타냅니다.
- TTI(Time to Interactive)
    - 사용할 수 있을 때까지 걸리는 시간은 완전히 페이지와 상호작용할 수 있게 될 때까지 걸리는 시간입니다.
- TBT(Total Blocking Time)
    - FCP와 상호작용 시간 사이의 모든 시간의 합으로 작업 지속 시간이 50ms를 넘으면 밀리초 단위로 표현됩니다.
- CLS(Cumulative Layout Shift)
    - 누적 레이아웃 변경은 표시 영역 안에 보이는 요소의 이동을 측정합니다.

1. 웹 성능예산은 어느정도가 적당하다고 생각하시나요
- 각 지표에서 1순위에 해당 되는 수치로 목표를 잡았습니다.
- 왜냐하면 신규 서비스이기때문에 기존에 서비스되고 있는것보다 성능이 떨어지면 사용률이 급격히 낮아질것이라 생각하였습니다.

|     | 모바일(As-Is) | 모바일(To-Be) | 데스크탑(As-Is) | 데스크탑(To-Be) |
|-----|------------|------------|-------------|-------------|
| FCP | 14.8s      | **1.7s**   | 2.7s        | **0.5s**    |
| SI  | 14.8s      | **5.6s**   | 3.5s        | **1.3s**    |
| LCP | 15.3s      | **6.7s**   | 2.8s        | **1.0s**    |
| TTI | 15.3s      | **4.5s**   | 2.8s        | **0.6s**    |
| TBT | 480ms      | **60ms**   | 30ms        | **0ms**     |
| CLS | 0.042      | **0.00**   | 0.004       | **0.001**   |

2. 웹 성능예산을 바탕으로 현재 지하철 노선도 서비스의 서버 목표 응답시간 가설을 세워보세요.
- 웹 성능 테스트 사이트에서 추천해주는 방안을 우선적으로 고려해볼 것 입니다.
    - 텍스트 압축 사용
    - 사용하지 않는 자바스크립트, CSS 줄이기
    - 렌더링 차단 리소스 제거하기
    - 콘텐츠가 포함된 최대 페인트 이미지 미리 로드

---

### 2단계 - 부하 테스트 
#### 요구사항 정리
- [X] 부하 테스트
  - [X] 테스트 전제조건 정리
    - [X] 대상 시스템 범위
    - [X] 목푯값 설정 (latency, throughput, 부하 유지기간)
    - [X] 부하 테스트 시 저장될 데이터 건수 및 크기
  - [X] 아래 시나리오 중 하나를 선택하여 스크립트 작성
    - [X] 접속 빈도가 높은 페이지
    - [X] 데이터를 갱신하는 페이지
    - [X] 데이터를 조회하는데 여러 데이터를 참조하는 페이지
  - [X] Smoke, Load, Stress 테스트 후 결과를 기록

1. 부하테스트 전제조건은 어느정도로 설정하셨나요
- 대상 시스템 범위
  - nginx(proxy) -> Spring Boot(was) -> mysql(db)

- 예상 1일 사용자 수(DAU) 예상
  - 30만 예상
    - 신규 서비스인점을 감안하고 카카오맵을 벤치마킹하여 해당 경쟁사의 DAU로 목표를 잡음
  - 네이버지도 DAU : (2129만 MAU / 30) 70만  
  - 카카오맵 DAU : (950만 MAU / 30) 31만  
  - [참고자료](https://mobile.newsis.com/view.html?ar_id=NISX20220927_0002028167)

- 피크 시간대의 집중률 (최대 트래픽 / 평소 트래픽)
  - 최대 트래픽 : 60만
  - 평소 트래픽 : 30만
  - 피크 시간 집중률 : 60만 / 30만 = 2

- 1명당 1일 평균 요청수를 예상
  - 1일 평균 요청수 : 접속수 * 요청수 = 2 * 3 = 6
  - 접속수 : 2 (출근 / 퇴근)
  - 요청수 : 3 (메인 페이지 -> 경로 탐색 페이지 -> 경로 탐색 요청)

- Throughput 계산
  - 1일 총 접속 수 : 1일 사용자 수(DAU) * 1명당 1일 평균 요청수 = 300,000 * 6 = 1,800,000
  - 1일 평균 rps : 1일 총 접속 수 / (초/지하철 운행 시간) = 1,800,000 / 64,800 = 27.77rps
    - 지하철 운행 시간 : 총 18시간, 오전 5시 ~ 오후 11시
  - 1일 최대 rps : 1일 평균 rps x 피크 시간대의 집중률 = 27.77rps * 2 = 55.54rps

- VUser
  - R: 3 (메인 페이지 -> 경로 탐색 페이지 -> 경로 탐색 요청)
  - http_req_duration : 0.5s
  - 예상 latency : 1s
  - T: (R * http_req_duration) + 예상 latency = (3 * 0.5) + 1 = 2.6
  - 평균 VUser: (1일 평균 rps * T) / R = (27.77 * 2.6) / 3 = 24.06 = 26
  - 최대 VUser: (1일 최대 rps * T) / R = (55.54 * 2.6) / 3 = 48.13 = 48
    - 소수점은 미포함

3. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요
 - smoke script
   - [script](./monitoring/smoke/smoke.js)
 - smoke k6
   ![smoke](./monitoring/smoke/smoke_k6.png)
 - smoke grapana
   ![smoke](./monitoring/smoke/smoke_grapana.png)

 - load script
   - [script](./monitoring/load/load.js)
 - load k6
   ![smoke](./monitoring/load/load_k6.png)
 - load grapana
   ![smoke](./monitoring/load/load_grapana.png)

 - stress script
   - [script](./monitoring/stress/stress.js)
 - stress k6
   ![smoke](./monitoring/stress/stress_k6.png)
 - stress grapana
   ![smoke](./monitoring/stress/stress_grapana.png)
---

### 3단계 - 로깅, 모니터링
#### 요구사항 정리
- [X] 로그 설정하기
  - [X] Application Log 파일로 저장하기
    - 회원가입, 로그인 등의 이벤트에 로깅을 설정
    - 경로찾기 등의 이벤트 로그를 JSON으로 수집
  - [X] Nginx Access Log 설정하기
- [X] Cloudwatch로 모니터링
  - [X] Cloudwatch로 로그 수집하기
  - [X] Cloudwatch로 메트릭 수집하기
  - [X] USE 방법론을 활용하기 용이하도록 대시보드 구성
    
1. 각 서버내 로깅 경로를 알려주세요
- app : ~/nextstep/infra-subway-monitoring/log
- nginx : /var/log/nginx

2. Cloudwatch 대시보드 URL을 알려주세요
- https://ap-northeast-2.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-2#dashboards:name=sanghoonson-dashboard

3. 기타 정보
- App : 
  - URL : https://shshon-infra.o-r.kr/
  - 경로 : ~/nextstep/infra-subway-monitoring
- EC2 : 
  - bastion : sanghoonson-admin-1
  - app : sanghoonson-public-1
