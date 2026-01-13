🏗️ 도서 서비스 기술 스택 및 아키텍처 (Tech Stack & Architecture)

  작성자: hetgwi01
  프로젝트: Daiso-Shop (MSA 기반 도서 쇼핑몰)

  ## 1. 기술 스택 (Tech Stack)

  #### Backend (Books-Search Service)
   * Language: Java 21
   * Framework: Spring Boot 3.3.x
   * ORM: Spring Data JPA (Hibernate)
   * Database: MySQL 8.0 (Source-Replica)
   * Build Tool: Maven (Multi-module)
   * API Communication: Spring Cloud OpenFeign
   * External API: Aladin Open API (도서 원천 데이터 확보)
   * AI Integration: Google Gemini AI (데이터 가공 및 보완)
   * Storage: MinIO (Object Storage)
   * Message Queue: RabbitMQ (Event Driven Architecture)
   * Testing: JUnit 5, Mockito
   * Code Quality: SonarQube

  ####  Frontend (FrontServer)
   * Language: Java, JavaScript (ES6+)
   * Template Engine: Thymeleaf
   * UI Libraries: Bootstrap 5, NHN Toast UI Editor
   * Client: Spring Cloud OpenFeign

  #### Infrastructure
   * Gateway: Spring Cloud Gateway
   * Service Discovery: Netflix Eureka
   * CI/CD: GitHub Actions, Docker

  ---

  ## 2. 시스템 아키텍처 및 데이터 흐름 (System Architecture & Data Flow)

 #### 1. 🏛 System Landscape (Diagonal Flow)

```mermaid
graph TB
    %% 1. 스타일 상세 정의
    classDef actor fill:#212121,stroke:#fff,color:#fff
    classDef front fill:#E1BEE7,stroke:#4A148C,stroke-width:2px
    classDef gateway fill:#BA68C8,stroke:#333,color:#fff
    classDef core fill:#4FC3F7,stroke:#01579B,stroke-width:3px,color:#000
    classDef storage fill:#90A4AE,stroke:#333
    classDef external fill:#FFD54F,stroke:#F57F17,stroke-width:2px,color:#000

    %% 2. User Interaction
    subgraph User_Interaction [User Interaction]
        Admin((👑 Admin))
        User((👤 Client))
    end

    %% 3. Frontend Layer
    subgraph Frontend_Layer [Frontend Layer Caching]
        Front[🖥️ Front Server]
        FrontRedis[(Redis Cache)]
        Front --- FrontRedis
    end

    %% 4. API Gateway Layer
    subgraph Gateway_Layer [API Gateway Layer]
        Gateway[🚪 API Gateway]
    end

    %% 5. Backend Core Layer
    subgraph Backend_Core [Backend Core Layer]
        BookCore[📘 Books-Search Core]
    end

    %% 6. Infrastructure & External
    subgraph Infra_External [Infrastructure & External]
        MySQL[(MySQL DB)]
        MinIO[(MinIO Image)]
        Aladin[📚 Aladin API]
        Gemini[🤖 Gemini AI]
    end

    %% 7. 연결 흐름 (에러 방지를 위해 개별 선언)
    Admin --> Front
    User --> Front
    Front ==> Gateway
    Gateway ==> BookCore

    BookCore --> MySQL
    BookCore --> MinIO
    BookCore <==> Aladin
    BookCore <==> Gemini

    %% 8. Redis 갱신 흐름 (점선)
    BookCore -.->|Updated Data| Gateway
    Gateway -.->|Refresh| Front

    %% 9. 클래스 적용 (ID 기반 분리 적용)
    class Admin,User actor
    class Front front
    class Gateway gateway
    class BookCore core
    class FrontRedis,MySQL,MinIO storage
    class Aladin,Gemini external
```

---

 #### 2. 📝 TUI Editor & Registration Process (Detailed Sequence)

```mermaid
sequenceDiagram
    autonumber
    actor Admin
    participant Front as 🖥️ Front (TUI Editor)
    participant GW as 🚪 Gateway
    participant Core as 📘 Book Core
    participant Ext as 🌐 Aladin/Gemini
    participant DB as 💾 DB/MinIO

    Note over Admin, DB: Phase 1: 사전 검증 (Pre-Validation)
    Admin->>Front: 1. ISBN 입력
    Front->>GW: 중복 체크 요청
    GW->>Core: validateIsbn(isbn)
    Core-->>GW: 중복 여부 결과
    GW-->>Front: OK (미등록 도서)

    Note over Admin, DB: Phase 2: 데이터 조회 및 AI 작성 (Drafting)
    Front->>GW: 2. 도서 정보 조회 요청
    GW->>Core: fetchBookData(isbn)
    Core->>Ext: Aladin & Gemini 호출
    Ext-->>Core: 도서/AI 분석 데이터 반환
    Core-->>GW: 데이터 전달
    GW-->>Front: 제목, 저자, AI 요약문 반환
    Note right of Front: [관리자 화면에 자동 채움]

    Note over Admin, DB: Phase 3: 에디터 편집 및 이미지 삽입 (Editing)
    Admin->>Front: 3. TUI Editor에 이미지 드래그&드롭
    Front->>GW: 이미지 업로드
    GW->>Core: uploadImage(File)
    Core->>DB: MinIO 저장
    DB-->>Core: 저장 완료
    Core-->>GW: 이미지 URL 반환
    GW-->>Front: 이미지 URL 반환
    Front->>Front: 4. 에디터 본문에 URL 삽입

    Note over Admin, DB: Phase 4: 최종 등록 (Finalize)
    Admin->>Front: 5. '등록' 버튼 클릭
    Front->>GW: 전체 데이터 전송 (POST)
    GW->>Core: registerBook(Data)
    Core->>DB: 최종 저장 (Commit)
    DB-->>Core: 커밋 완료
    Core-->>GW: 처리 결과
    GW-->>Front: 처리 결과
    Front-->>Admin: 등록 완료!
```

---

 #### 3. 🧩 Core Logic & Redis Strategy

```mermaid
flowchart LR
    %% 1. 스타일 정의
    classDef front fill:#E1BEE7,stroke:#4A148C,stroke-width:2px
    classDef gateway fill:#BA68C8,stroke:#333,color:#fff
    classDef back fill:#4FC3F7,stroke:#01579B,stroke-width:3px,color:#000
    classDef storage fill:#90A4AE,stroke:#333
    classDef redis fill:#D32F2F,stroke:#B71C1C,color:#fff

    %% 2. Front Server Area
    subgraph Front_Server [Front Server]
        F_Svc[Front CategoryService]
        F_Redis[(Front Redis)]
        
        %% 마침표를 제거하여 리스트 오인 방지
        F_Svc -- "Step1 캐시 조회 및 저장" --> F_Redis
    end

    %% 3. Gateway
    GW[🚪 API Gateway]

    %% 4. Backend Area
    subgraph Backend_Core [Backend Core]
        B_Ctrl[Category Controller]
        B_Svc[Category Service<br/>Business Logic]
        DB[(MySQL)]
        
        B_Ctrl -- "전달" --> B_Svc
        B_Svc -- "Step3 DB 트리 조회" --> DB
    end

    %% 5. 전체 흐름 연결
    %% 마침표 없이 텍스트 작성
    F_Svc -- "Step2 Cache Miss 요청" --> GW
    GW -- "API 전달" --> B_Ctrl
    B_Ctrl -- "JSON 반환" --> F_Svc

    %% 6. 스타일 적용
    class F_Svc front
    class F_Redis redis
    class GW gateway
    class B_Ctrl,B_Svc back
    class DB storage
```

  ---

  ## 3. 핵심 아키텍처 특징 (Key Architectural Features)

  #### A. AI 기반 지능형 도서 등록 파이프라인
   1. ISBN 중복 검사: 관리자가 ISBN을 입력하면 먼저 로컬 MySQL DB를 조회하여 중복 여부를 확인합니다.
   2. 외부 데이터 확보 (Aladin Open API): DB에 없는 신규 도서일 경우, 알라딘 Open API를 호출하여 도서의 기본 메타데이터(제목, 저자, 출판사, 원본 이미지 등)를 확보합니다.
   3. AI 데이터 가공 (Gemini AI):
       * 알라딘에서 가져온 Raw 데이터는 우리 서비스의 카테고리 분류나 태그 정책과 맞지 않는 경우가 많습니다.
       * 확보한 데이터를 Gemini AI에게 전송하여, 우리 서비스 포맷에 맞는 카테고리 매핑, 태그 추출, 부족한 상세 설명 보완 작업을 수행하도록 하고 그 결과를 받아 자동 입력합니다.

  #### B. 통신 최적화 컨트롤러 설계 (Bulk Data Transfer)
   * `BookManagementController` 구현:
       * 관리자 페이지나 메인 페이지 렌더링 시 필요한 작가 목록, 태그 목록, 카테고리 트리 등 수많은 메타데이터를 개별 API로 요청할 경우 네트워크 오버헤드(RTT)가 발생합니다.
       * 이를 해결하기 위해 메타데이터 전용 컨트롤러(`BookManagementController`)를 별도로 설계하여, 한 번의 API 호출로 화면 렌더링에 필요한 모든 메타데이터를 통합(Aggregated)하여 반환하도록 설계했습니다. 이를 통해 빈번한 통신을 획기적으로 줄였습니다.

  #### C. 성능 최적화 아키텍처
   1. Browser & Server Caching:
       * 이미지 프록시 응답에 Cache-Control을 적용해 브라우저 단에서 이미지를 캐싱.
       * 변경이 적은 카테고리 정보는 FrontServer 메모리에 캐싱하여 DB 조회 제거.
   2. Database Access Optimization:
       * JSON_ARRAYAGG를 활용한 Native Query로 N+1 문제를 해결하고 단일 쿼리로 데이터 집계.

  #### D. 객체 스토리지 및 보안
   * Proxy Pattern: MinIO 내부 URL을 노출하지 않고 FrontServer를 경유하는 프록시 패턴을 적용하여 보안성을 강화하고 HTTPS 혼합 콘텐츠 문제를 해결했습니다.

  ---

  ## 4. 도서 서비스 내부 레이어 구조 (Internal Layering)

   * Controller Layer:
       * BookV2Controller: 일반 사용자용 조회 API.
       * BookManagementController: 관리자용 및 메타데이터 통합 조회 API (통신 최적화).
   * Facade Layer (`BookFacade`):
       * AI 등록 프로세스(DB 조회 -> 알라딘 API -> Gemini AI -> DB 저장)의 전체 흐름을 제어.
       * 트랜잭션 관리 및 외부 시스템 통신 조율.
   * Core Service Layer (`BookCoreService`):
       * 순수 도서 도메인 로직(Entity 상태 변경, 유효성 검사) 담당.
   * Repository Layer:
       * JPA와 Native Query(Projection)를 혼용하여 생산성과 성능의 균형 유지.
