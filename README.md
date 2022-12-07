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

#### 요구사항

- [x] 웹 성능 예서산 작성 후 서버 목표 응답시간 도출

---

#### 용어 정리
> [라이트하우스 성능 지표 살펴보기](https://url.kr/oyga3r)
- FCP(First Contentful Paint) : 의미있는 콘텐츠 일부가 처음 화면에 렌더링 될 때까지의 시간
- SI(Speed Index) : 콘텐츠가 식별 가능하게 채워지는 속도를 보여주는 페이지 로드 성능
- LCP(Largest Contentful Paint) : 가장 큰 콘텐츠 페인팅 시간
- TTI(First Input Delay) : 페이지가 완전히 사용자와 상호 작용할 수 있는 상태가 되는데 걸리는 시간
- TBT(Total Blocking Time) : FCP 와 TTI 사이에서 사용자와 상호작용하지 못했던 시간
- CLS(Cumulative Layout Shift) : 사용자가 예상하지 못한 레이아웃을 경험하는 빈도를 정량화


#### 비교 분석 결과

- Mobile

| mobile | 서울교통공사 | 네이버지도 | 카카오맵  | Running Map |
|:------:|:------:|:-----:|:-----:|:-----------:|
|  FCP   |  6.6s  | 2.4s  | 1.7s  |    14.8s    |
|   SI   |  8.7s  | 6.3s  | 6.8s  |    14.8s    |
|  LCP   |  6.8s  | 7.2s  | 6.8s  |    15.4s    |
|  TTI   |  8.8s  | 6.8s  | 4.5s  |    15.4s    |
|  TBT   | 1080ms | 540ms | 80ms  |    490ms    |
|  CLS   |   0    | 0.03  | 0.187 |    0.041    |

- Desktop
 
| desktop | 서울교통공사 | 네이버지도 | 카카오맵  | Running Map |
|:-------:|:------:|:-----:|:-----:|:-----------:|
|   FCP   |  1.4s  | 0.5s  | 0.5s  |    2.7s     |
|   SI    |  2.3s  | 2.4s  | 2.2s  |    2.7s     |
|   LCP   |  3.7s  | 1.5s  | 1.4s  |    2.8s     |
|   TTI   |  1.9s  | 1.2s  | 0.7s  |    2.8s     |
|   TBT   | 250ms  |  0ms  |  0ms  |    40ms     |
|   CLS   | 0.001  | 0.006 | 0.021 |    0.004    |

1. 웹 성능예산은 어느정도가 적당하다고 생각하시나요
- 신규 서비스인 만큼 사용자에게 서비스 퍼포먼스에 대해 유의미한 인상을 줄 수 있어야 한다고 생각합니다. 그래서 경쟁사인 네이버지도와 카카오보다 20% 높은 성능 예산을 잡겠습니다.
- 단, FCP 와 TTI 는 각 3초, 5초 이내로 유지하겠습니다.

- 목표 성능 예산

| mobile |  현재   |  목표   |
|:------:|:-----:|:-----:|
|  FCP   | 14.8s | 2.0s  |
|   SI   | 14.8s | 5.0s  |
|  LCP   | 15.4s | 5.0s  |
|  TTI   | 15.4s | 4.0s  |

| desktop |  현재  |  목표  |
|:-------:|:----:|:----:|
|   FCP   | 2.7s | 0.4s |
|   SI    | 2.7s | 2.0s |
|   LCP   | 2.8s | 1.1s |
|   TTI   | 2.8s | 0.5s |

2. 웹 성능예산을 바탕으로 현재 지하철 노선도 서비스의 서버 목표 응답시간 가설을 세워보세요.
- `PageSpeed` 사이트에서 추천한 개선 사항을 위주로 적용하겠습니다.
- 모바일 기준

|           개선 사항           | 예상 절감치 |
|:-------------------------:|:------:|
|         텍스트 압축 사용         | 9.15s  |
|    사용하지 않는 자바스크립트 줄이기     | 3.45s  |
|       렌더링 차단 리소스 제거       | 0.75s  |
| 콘첸츠가 포함된 최대 페인트 이미지 미리 로드 | 0.48s  |

- 이 밖에 정적인 애셋 제공 등의 방법을 적용하여 `light house` 점수를 90 점 이상으로 만들겠습니다.

---

### 2단계 - 부하 테스트

1. 부하테스트 전제조건은 어느정도로 설정하셨나요

2. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요

---

### 3단계 - 로깅, 모니터링

1. 각 서버내 로깅 경로를 알려주세요

2. Cloudwatch 대시보드 URL을 알려주세요
