
## Tech Stack
<p align="left">
  <img src="https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white" />
  <img src="https://img.shields.io/badge/springboot-%236DB33F.svg?style=for-the-badge&logo=springboot&logoColor=white" />
  <img src="https://img.shields.io/badge/spring%20data%20jpa-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white" />
  <img src="https://img.shields.io/badge/maven-C71A36?style=for-the-badge&logo=apache%20maven&logoColor=white" />
  <img src="https://img.shields.io/badge/redis-%23DC382D.svg?style=for-the-badge&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/mysql-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white" />
</p>

---

## Experience

### NHN Academy Java Backend 개발자 과정 12기
**2025.07.28** ~ **2025.12.31**

---

## Projects

### MSA 기반 온라인 서점 'daiso-book.shop'

**2025.11.11 ~ 2025.12.31**

**담당 역할**: 도서(Books) 도메인 총괄 설계 및 구현

#### Repository
- **[daiso Repository](https://github.com/nhnacademy-be12-daiso)**
- **[도서(Books-Search) Repository](https://github.com/nhnacademy-be12-daiso/Books-Search)**

#### 주요 개발 내용 (Key Development Features)

* **외부 API 및 AI 기반 데이터 등록 자동화** 
	-  ISBN 기반 알라딘 API 연동 및 **Google Gemini AI**를 활용한 도서 요약 자동 생성 기능 구현 
	- 외부 호출 실패에 대비한 예외 처리 및 폴백(Fallback) 로직으로 데이터 등록의 연속성 확보 
* **계층형 데이터 관리 및 성능 최적화** 
	- **Tree 구조** 카테고리 설계 및 **Redis 캐싱**을 통한 조회 쿼리 부하 감소 
	- **MinIO** 오브젝트 스토리지를 활용한 이미지 자산 중앙화 및 안정적 마이그레이션 환경 구축 
* **시스템 안정성 및 데이터 정합성 설계** 
	- `@Async` 기반 **비동기 통신**으로 포인트 적립 API 호출 시 사용자 응답 지연 방지 
	- `@Retryable` 적용으로 일시적 네트워크 오류 시 서비스 간 통신 안정성 보장 
	- 복합 할인 정책 엔진 구현 및 대량 조회 시 **N+1 문제 해결**을 위한 쿼리 튜닝 수행