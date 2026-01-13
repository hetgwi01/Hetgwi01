# OpenAPI definition

**Version**: v2  

## Server Information

- **URL**: `http://localhost:8088`

- Generated server url

## Table of Contents

1. [[#Book Management Admin|Book Management Admin]]  
   [Book Management Admin](#book-management-admin)

2. [[#Book Order|Book Order]]  
   [Book Order](#book-order)

3. [[#Book V2 API|Book V2 API]]  
   [Book V2 API](#book-v2-api)

4. [[#Category Coupon & Info|Category Coupon & Info]]  
   [Category Coupon & Info](#category-coupon--info)

5. [[#Category V2|Category V2]]  
   [Category V2](#category-v2)

6. [[#Image API|Image API]]  
   [Image API](#image-api)

7. [[#Like V2 API|Like V2 API]]  
   [Like V2 API](#like-v2-api)

8. [[#Review V2|Review V2]]  
   [Review V2](#review-v2)

9. [[#System Dev Tools|System Dev Tools]]  
   [System Dev Tools](#system-dev-tools)


---

## Book Management Admin

관리자용 도서 관리 및 메타데이터 API

### `GET` /api/v2/books/{isbn}/register-page

**Summary**: ISBN 기반 도서 검색

**Description**: 외부 API 혹은 기존 DB에서 ISBN으로 도서 정보를 사전 조회합니다.

  

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `isbn` | path | string | ✓ | - |
  

#### Responses

**200**: 조회 성공
- Returns: `FindIsbnMetaData`

**400**: ISBN 형식이 올바르지 않음
- Returns: `FindIsbnMetaData`

**404**: 해당 ISBN으로 검색된 도서 정보가 없음
- Returns: `FindIsbnMetaData`

### `GET` /api/v2/books/{bookId}/metadata/modification

**Summary**: 도서 수정용 상세 정보 조회

**Description**: 특정 도서의 기존 정보와 수정 시 필요한 선택지 데이터를 함께 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | - |

#### Responses

**200**: 조회 성공

- Returns: `ModifyBookMetaData`

  

**404**: 수정할 도서 정보를 찾을 수 없음

- Returns: `ModifyBookMetaData`

  
  

### `GET` /api/v2/books/metadata/registration

**Summary**: 도서 등록용 기초 데이터 조회

**Description**: 도서 등록 시 선택해야 하는 카테고리, 출판사 리스트 등 기초 데이터를 조회합니다.

#### Responses

**200**: 조회 성공

- Returns: `RegisterBookMetaData`

### `GET` /api/v2/books/main-page

**Summary**: 메인 페이지 도서 목록 조회

**Description**: 사용자 메인 화면에 노출될 도서 목록을 조회합니다. 사용자 ID 전달 시 개인화된 정보를 포함할 수 있습니다.

#### Parameters


| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `pageable` | query | unknown | ✓ | 페이지 번호, 크기, 정렬 정보 |
| `X-User-Id` | header | integer(int64) | | 사용자 식별 ID (선택 사항) |

#### Responses

**200**: 조회 성공

- Returns: `MainPageBookListRespDTO`

### `GET` /api/v2/books/admin

**Summary**: 관리자 도서 목록 페이지 정보 조회

**Description**: 도서 관리 테이블에 필요한 데이터와 페이징 정보를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `pageable` | query | unknown | ✓ | - |

#### Responses

**200**: 조회 성공

- Returns: `AdminBookMetaData`

  

**403**: 관리자 권한 없음

- Returns: `AdminBookMetaData`

---

## Book Order


주문 서비스 연동용 도서 정보 조회 API

### `POST` /api/v2/books/order-service/order-cancel

**Summary**: 주문 취소 시 재고 및 데이터 복구

**Description**: 주문이 취소되었을 때 해당 도서들의 상태를 업데이트하거나 관련 처리를 수행합니다.

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `OrderCancelRequest`
- 
#### Responses

**204**: 취소 처리 성공

**404**: 주문 정보를 찾을 수 없음

### `POST` /api/v2/books/order-service/list/book-review

**Summary**: 사용자 도서 리뷰 상태 조회

**Description**: 주문한 도서들에 대해 사용자가 리뷰를 작성했는지 여부를 포함한 목록을 조회합니다.

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `BookReviewRequest`

#### Responses

**200**: OK

### `POST` /api/v2/books/order-service/books/list

**Summary**: 주문 도서 요약 목록 조회

**Description**: 장바구니나 주문 목록 표시를 위한 도서 요약 정보를 조회합니다.

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `BookIdListReqDTO`

#### Responses

**200**: OK

### `POST` /api/v2/books/order-service/books/info

**Summary**: 주문 도서 상세 정보 조회

**Description**: 주문 처리에 필요한 도서들의 상세 정보를 리스트로 조회합니다.

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `BookIdListReqDTO`
- 
#### Responses

**200**: 조회 성공

- Returns: `OrderBooksInfoRespDTO`

**400**: 잘못된 도서 ID 리스트

- Returns: `OrderBooksInfoRespDTO`

---

## Book V2 API

도서 등록, 수정, 삭제 및 조회 기능을 제공하는 API (v2)

### `GET` /api/v2/books/categories/{categoryId}

**Summary**: 카테고리별 도서 목록 조회

**Description**: 특정 카테고리에 속한 도서 목록을 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `pageable` | query | unknown | ✓ | - |
| `categoryId` | path | integer(int64) | ✓ | - |
| `X-User-Id` | header | integer(int64) | | - |

#### Responses

**200**: OK

- Returns: `BookListByCategoryRespDTO`

### `GET` /api/v2/books

**Summary**: 리스트 타입별 도서 조회

**Description**: 베스트셀러, 신간 등 타입별 도서 목록을 가져옵니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `pageable` | query | unknown | ✓ | - |
| `type` | query | string | ✓ | - |
| `X-User-Id` | header | integer(int64) | | - |

#### Responses

**200**: OK

- Returns: `SortBookListRespDTO`

### `POST` /api/v2/books

**Summary**: 단일 도서 등록

**Description**: 메타데이터 JSON과 최대 5개의 이미지를 업로드하여 도서를 등록합니다.

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**201**: 도서 등록 성공

### `POST` /api/v2/books/batch

**Summary**: 복수 도서 등록 (Batch)

**Description**: 파일(CSV 등)을 업로드하여 여러 권의 도서를 한 번에 등록합니다.

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**201**: 일괄 등록 요청 성공
  
### `GET` /api/v2/books/{bookId}

**Summary**: 도서 상세 정보 조회

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | - |
| `X-User-Id` | header | integer(int64) | | - |

#### Responses

**200**: OK

- Returns: `BookRespDTO`

### `DELETE` /api/v2/books/{bookId}

**Summary**: ID로 도서 삭제

**Description**: 도서 고유 ID를 이용해 삭제합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | - |

#### Responses

**204**: 도서 삭제 성공

### `PATCH` /api/v2/books/{bookId}

**Summary**: 단일 도서 수정

**Description**: 기존 도서의 정보 및 이미지를 수정합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | - |

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**204**: 도서 수정 성공 (반환 값 없음)

### `GET` /api/v2/books/{bookId}/modify


**Summary**: 수정용 도서 데이터 조회

**Description**: 수정 화면 구성을 위한 기존 데이터를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | - |

#### Responses

**200**: OK

- Returns: `BookUpdateView`

### `GET` /api/v2/books/isbn/{isbn}/exist

**Summary**: ISBN 중복 체크

**Description**: 해당 ISBN이 이미 등록되어 있는지 확인합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `isbn` | path | string | ✓ | - |

#### Responses

**200**: OK

### `GET` /api/v2/books/admin-total-info

**Summary**: 관리자 통계 데이터 조회

#### Responses

**200**: OK

- Returns: `TotalDataRespDTO`

### `GET` /api/v2/books/admin-book-list

**Summary**: 관리자용 도서 전체 목록 조회

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `pageable` | query | unknown | ✓ | - |

#### Responses

**200**: OK

- Returns: `PageBookAdminResponseDTO`

### `DELETE` /api/v2/books/isbn/{isbn}

**Summary**: ISBN으로 도서 삭제

**Description**: 도서 ISBN 번호를 이용해 삭제합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `isbn` | path | string | ✓ | - |

#### Responses

**204**: 도서 삭제 성공  

---

## Category Coupon & Info

쿠폰 적용 및 도서 정보 조회를 위한 카테고리 데이터 API

### `GET` /api/books/{bookId}/category

**Summary**: 도서의 카테고리 정보 조회

**Description**: 특정 도서가 속한 카테고리 정보를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | 도서 고유 ID |

#### Responses

**200**: 조회 성공

- Returns: `BookCategoryResponse`

**404**: 해당 도서의 카테고리 정보를 찾을 수 없음

- Returns: `BookCategoryResponse`

### `GET` /api/books/categoriesIds

**Summary**: 카테고리 ID 리스트로 단순 정보 조회

**Description**: 쿠폰 적용 범위 확인 등을 위해 여러 카테고리의 이름과 ID 목록을 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `ids` | query | array | ✓ | 조회할 카테고리 ID 목록 |

#### Responses

**200**: 조회 성공

---

## Category V2

도서 카테고리 관리 API (계층 구조 지원)

### `PUT` /api/v2/books/categories/{categoryId}

**Summary**: 기존 카테고리 수정

**Description**: 특정 카테고리의 정보를 변경합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `categoryId` | path | integer(int64) | ✓ | - |

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `CategoryModifyReqDTO`

#### Responses

**204**: 카테고리 수정 성공

**404**: 존재하지 않는 카테고리 ID

### `DELETE` /api/v2/books/categories/{categoryId}

**Summary**: 카테고리 삭제

**Description**: 특정 카테고리를 삭제합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `categoryId` | path | integer(int64) | ✓ | - |

#### Responses

**204**: 카테고리 삭제 성공

**409**: 하위 카테고리 존재 등 연관 데이터로 인한 삭제 불가

### `GET` /api/v2/books/categories

**Summary**: 전체 카테고리 목록 조회

**Description**: 모든 카테고리를 평면적인 리스트 형태로 조회합니다.

#### Responses

**200**: OK

- Returns: `CategoryList`

### `POST` /api/v2/books/categories

**Summary**: 새 카테고리 등록

**Description**: 새로운 도서 카테고리를 생성합니다.

#### Request Body

- **Content-Type**: `application/json`

- **Schema**: `CategoryRegisterReqDTO`

#### Responses

**201**: 카테고리 생성 성공

**400**: 잘못된 입력 값 (중복된 이름 등)

### `GET` /api/v2/books/categories/tree

**Summary**: 카테고리 트리 구조 조회

**Description**: 부모-자식 관계가 포함된 트리 구조의 카테고리 목록을 조회합니다.

#### Responses

**200**: OK

- Returns: `CategoryTreeListRespDTO`

---

## Image API

이미지 업로드 및 관리 API

### `POST` /api/books/images/upload

**Summary**: 이미지 단일 업로드

**Description**: 파일을 업로드하고, 프록시를 통해 접근 가능한 이미지 경로를 반환받습니다.

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**200**: 업로드 성공 (프록시 URL 반환)

**400**: 잘못된 파일 형식 또는 업로드 실패

**500**: 서버 내부 이미지 처리 오류

---

## Like V2 API

도서 좋아요 등록, 취소 및 내 좋아요 목록 조회 기능을 제공합니다.

### `POST` /api/v2/books/{bookId}/likes

**Summary**: 도서 좋아요 등록

**Description**: 특정 도서에 좋아요를 표시합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | 도서 고유 ID |
| `X-User-Id` | header | integer(int64) | ✓ | 사용자 고유 ID |

#### Responses

**201**: 좋아요 등록 성공

**404**: 존재하지 않는 도서 ID

### `DELETE` /api/v2/books/{bookId}/likes

**Summary**: 도서 좋아요 취소

**Description**: 기존에 표시했던 도서의 좋아요를 삭제합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | 도서 고유 ID |
| `X-User-Id` | header | integer(int64) | ✓ | 사용자 고유 ID |

#### Responses

**204**: 좋아요 취소 성공

**404**: 좋아요 기록을 찾을 수 없음

### `GET` /api/v2/likes/me

**Summary**: 내 좋아요 목록 조회

**Description**: 현재 로그인한 사용자가 좋아요를 누른 도서 목록을 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `X-User-Id` | header | integer(int64) | ✓ | 사용자 고유 ID |

#### Responses

**200**: 조회 성공

- Returns: `MyLikeList`

---

## Review V2

리뷰 등록, 조회, 수정 API

### `GET` /api/reviews/{reviewId}

**Summary**: 리뷰 상세 조회

**Description**: 리뷰 ID를 통해 특정 리뷰의 상세 정보를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `reviewId` | path | integer(int64) | ✓ | 리뷰 고유 ID |

#### Responses

**200**: OK

- Returns: `ReviewRespDTO`

### `PUT` /api/reviews/{reviewId}

**Summary**: 리뷰 수정

**Description**: 기존에 작성한 리뷰 내용 및 이미지를 수정합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `reviewId` | path | integer(int64) | ✓ | 수정할 리뷰 ID |

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**204**: 리뷰 수정 성공 (반환 데이터 없음)

**403**: 수정 권한 없음

**404**: 리뷰를 찾을 수 없음

### `POST` /api/reviews

**Summary**: 리뷰 등록

**Description**: 도서 리뷰를 등록하고 포인트 적립을 요청합니다. 이미지 최대 3장까지 지원합니다.

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: Object with properties

#### Responses

**201**: 리뷰 등록 성공

**400**: 잘못된 요청 파라미터

**404**: 도서를 찾을 수 없음

### `GET` /api/reviews/me

**Summary**: 내 리뷰 목록 조회

**Description**: 로그인한 사용자가 작성한 모든 리뷰를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `X-User-Id` | header | integer(int64) | ✓ | 사용자 고유 ID |

#### Responses

**200**: OK

### `GET` /api/reviews/books/{bookId}

**Summary**: 도서별 리뷰 조회

**Description**: 특정 도서에 달린 모든 리뷰를 조회합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | path | integer(int64) | ✓ | 도서 고유 ID |

#### Responses

**200**: OK

---

## System Dev Tools

시스템 관리, 이미지 마이그레이션 및 테스트용 API

### `POST` /migrate-images

**Summary**: 이미지 일괄 마이그레이션 실행

**Description**: 기존 서버의 이미지들을 배치를 통해 MinIO로 이전합니다.

#### Responses

**200**: OK

### `POST` /create

**Summary**: 도서 생성 통합 테스트

**Description**: Multipart Form 데이터를 사용하여 도서 메타데이터와 이미지를 한 번에 등록 테스트합니다.

#### Request Body

- **Content-Type**: `multipart/form-data`

- **Schema**: `TestReq`

#### Responses

**201**: 테스트 등록 성공

### `GET` /upload

**Summary**: 외부 URL 이미지 MinIO 업로드 테스트

**Description**: 이미지 URL을 통해 MinIO 스토리지로 직접 업로드를 시도합니다.

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `bookId` | query | integer(int64) | ✓ | - |
| `imageUrl` | query | string | ✓ | - |

#### Responses

**200**: OK

### `DELETE` /delete

**Summary**: MinIO 객체 삭제 테스트

#### Parameters

| Name | In | Type | Required | Description |
|------|-----|------|----------|-------------|
| `objectName` | query | string | ✓ | - |

#### Responses

**200**: OK

---

## Data Schemas

아래는 API에서 사용되는 주요 데이터 스키마입니다.

### Schema List

- [`AdminBookMetaData`](#adminbookmetadata)
- [`AuthorInfoData`](#authorinfodata)
- [`AuthorRespDTO`](#authorrespdto)
- [`BookAdminResponseDTO`](#bookadminresponsedto)
- [`BookCategoryResponse`](#bookcategoryresponse)
- [`BookIdListReqDTO`](#bookidlistreqdto)
- [`BookInfoDataView`](#bookinfodataview)
- [`BookListByCategoryRespDTO`](#booklistbycategoryrespdto)
- [`BookListRespDTO`](#booklistrespdto)
- [`BookOrderDetailRequest`](#bookorderdetailrequest)
- [`BookRespDTO`](#bookrespdto)
- [`BookResponse`](#bookresponse)
- [`BookReviewRequest`](#bookreviewrequest)
- [`BookReviewResponse`](#bookreviewresponse)
- [`BookUpdateView`](#bookupdateview)
- [`CategoryList`](#categorylist)
- [`CategoryModifyReqDTO`](#categorymodifyreqdto)
- [`CategoryPath`](#categorypath)
- [`CategoryRegisterReqDTO`](#categoryregisterreqdto)
- [`CategoryRespDTO`](#categoryrespdto)
- [`CategorySimpleResponse`](#categorysimpleresponse)
- [`CategoryTree`](#categorytree)
- [`CategoryTreeListRespDTO`](#categorytreelistrespdto)
- [`FindIsbnMetaData`](#findisbnmetadata)
- [`ImageRespDTO`](#imagerespdto)
- [`LikeListRespDTO`](#likelistrespdto)
- [`MainPageBookListRespDTO`](#mainpagebooklistrespdto)
- [`ModifyBookMetaData`](#modifybookmetadata)
- [`MyLikeList`](#mylikelist)
- [`OrderBookInfoRespDTO`](#orderbookinforespdto)
- [`OrderBookSummeryDTO`](#orderbooksummerydto)
- [`OrderBooksInfoRespDTO`](#orderbooksinforespdto)
- [`OrderCancelRequest`](#ordercancelrequest)
- [`PageBookAdminResponseDTO`](#pagebookadminresponsedto)
- [`PageBookListRespDTO`](#pagebooklistrespdto)
- [`Pageable`](#pageable)
- [`PageableObject`](#pageableobject)
- [`RegisterBookMetaData`](#registerbookmetadata)
- [`ReviewRespDTO`](#reviewrespdto)
- [`RoleNameListRespDTO`](#rolenamelistrespdto)
- [`SortBookListRespDTO`](#sortbooklistrespdto)
- [`SortObject`](#sortobject)
- [`TagInfoData`](#taginfodata)
- [`TagRespDTO`](#tagrespdto)
- [`TestReq`](#testreq)
- [`TotalDataRespDTO`](#totaldatarespdto)
  
### AdminBookMetaData

| Property | Type | Description |
|----------|------|-------------|
| `bookAdminResponseDTOS` | → PageBookAdminResponseDTO | - |
| `totalDate` | → TotalDataRespDTO | - |

### AuthorInfoData

| Property | Type | Description |
|----------|------|-------------|
| `authorName` | string | - |
| `roleName` | string | - |

### AuthorRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `authorId` | integer(int64) | - |
| `authorName` | string | - |
| `roleId` | integer(int64) | - |
| `roleName` | string | - |

### BookAdminResponseDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `isbn` | string | - |
| `title` | string | - |
| `imageList` | Array\<ImageRespDTO\> | - |
| `price` | integer(int64) | - |
| `discountPercentage` | number | - |
| `discountPrice` | integer(int64) | - |
| `stock` | integer(int32) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `publicationDate` | string(date) | - |
| `publisher` | string | - |
| `isDeleted` | boolean | - |

### BookCategoryResponse

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `primaryCategoryId` | integer(int64) | - |
| `secondaryCategoryId` | integer(int64) | - |

### BookIdListReqDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookIdList` | Array\<integer\> | - |

### BookInfoDataView

| Property | Type | Description |
|----------|------|-------------|
| `isbn` | string | - |
| `title` | string | - |
| `index` | string | - |
| `description` | string | - |
| `authorList` | Array\<AuthorInfoData\> | - |
| `publisher` | string | - |
| `publicationDate` | string(date) | - |
| `price` | integer(int64) | - |
| `imageList` | Array\<ImageRespDTO\> | - |
| `volumeNo` | integer(int32) | - |
| `categories` | Array\<CategoryRespDTO\> | - |
| `tags` | Array\<TagInfoData\> | - |

### BookListByCategoryRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookList` | → PageBookListRespDTO | - |

### BookListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `isbn` | string | - |
| `title` | string | - |
| `description` | string | - |
| `authorList` | Array\<AuthorRespDTO\> | - |
| `publisher` | string | - |
| `publicationDate` | string(date) | - |
| `price` | integer(int64) | - |
| `discountPercentage` | number | - |
| `discountPrice` | integer(int64) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `imageList` | Array\<ImageRespDTO\> | - |
| `categories` | Array\<CategoryRespDTO\> | - |
| `tags` | Array\<TagRespDTO\> | - |
| `volumeNo` | integer(int32) | - |
| `isPackaging` | boolean | - |
| `isLike` | boolean | - |
| `isDeleted` | boolean | - |

### BookOrderDetailRequest

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `orderDetailId` | integer(int64) | - |

### BookRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `isbn` | string | - |
| `title` | string | - |
| `index` | string | - |
| `description` | string | - |
| `authorList` | Array\<AuthorRespDTO\> | - |
| `publisher` | string | - |
| `publicationDate` | string(date) | - |
| `price` | integer(int64) | - |
| `discountPercentage` | number | - |
| `discountPrice` | integer(int64) | - |
| `isPackaging` | boolean | - |
| `stock` | integer(int32) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `imageList` | Array\<ImageRespDTO\> | - |
| `volumeNo` | integer(int32) | - |
| `categories` | Array\<CategoryRespDTO\> | - |
| `tags` | Array\<TagRespDTO\> | - |
| `likeCount` | integer(int32) | - |
| `isLike` | boolean | - |
| `reviewList` | Array\<ReviewRespDTO\> | - |
| `isDeleted` | boolean | - |

### BookResponse

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `title` | string | - |
| `imageList` | Array\<ImageRespDTO\> | - |

### BookReviewRequest

| Property | Type | Description |
|----------|------|-------------|
| `userId` | integer(int64) | - |
| `bookOrderDetailRequests` | Array\<BookOrderDetailRequest\> | - |

### BookReviewResponse

| Property | Type | Description |
|----------|------|-------------|
| `book` | → BookResponse | - |
| `orderDetailId` | integer(int64) | - |
| `reviewId` | integer(int64) | - |

### BookUpdateView

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `isbn` | string | - |
| `title` | string | - |
| `index` | string | - |
| `description` | string | - |
| `authorList` | Array\<AuthorRespDTO\> | - |
| `publisher` | string | - |
| `publicationDate` | string(date) | - |
| `price` | integer(int64) | - |
| `isPackaging` | boolean | - |
| `stock` | integer(int32) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `imageList` | Array\<ImageRespDTO\> | - |
| `volumeNo` | integer(int32) | - |
| `categories` | Array\<CategoryRespDTO\> | - |
| `tags` | Array\<TagRespDTO\> | - |
| `isDeleted` | boolean | - |

### CategoryList

| Property | Type | Description |
|----------|------|-------------|
| `categoryPathList` | Array\<CategoryPath\> | - |

### CategoryModifyReqDTO

| Property | Type | Description |
|----------|------|-------------|
| `name` | string | - |
| `deep` | integer(int32) | - |

### CategoryPath

| Property | Type | Description |
|----------|------|-------------|
| `categoryId` | integer(int64) | - |
| `categoryName` | string | - |
| `path` | string | - |

### CategoryRegisterReqDTO

| Property | Type | Description |
|----------|------|-------------|
| `categoryId` | integer(int64) | - |
| `name` | string | - |
| `deep` | integer(int32) | - |
| `preCategoryId` | integer(int64) | - |

### CategoryRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `categoryId` | integer(int64) | - |
| `categoryName` | string | - |
| `deep` | integer(int32) | - |
| `preCategoryId` | integer(int64) | - |
| `preCategoryName` | string | - |

### CategorySimpleResponse

| Property | Type | Description |
|----------|------|-------------|
| `categoryId` | integer(int64) | - |
| `categoryName` | string | - |

### CategoryTree

| Property | Type | Description |
|----------|------|-------------|
| `categoryId` | integer(int64) | - |
| `name` | string | - |
| `preCategoryId` | integer(int64) | - |
| `deep` | integer(int32) | - |

### CategoryTreeListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `categoryTreeList` | Array\<CategoryTree\> | - |

### FindIsbnMetaData

| Property | Type | Description |
|----------|------|-------------|
| `refinedBook` | → BookInfoDataView | - |
| `categoryList` | → CategoryList | - |
| `roleNameList` | → RoleNameListRespDTO | - |

### ImageRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `no` | integer(int64) | - |
| `path` | string | - |
| `imageType` | enum: COVER, DETAIL, REVIEW | - |

### LikeListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `likeId` | integer(int64) | - |
| `bookId` | integer(int64) | - |
| `isbn` | string | - |
| `title` | string | - |
| `authorRespDTOList` | Array\<AuthorRespDTO\> | - |
| `price` | integer(int64) | - |
| `discountPercentage` | number | - |
| `discountPrice` | integer(int64) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `image` | string | - |
| `volumeNo` | integer(int32) | - |
| `packaging` | boolean | - |
| `createdAt` | string(date-time) | - |

### MainPageBookListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookOfTheWeek` | → SortBookListRespDTO | - |
| `newReleases` | → SortBookListRespDTO | - |

### ModifyBookMetaData

| Property | Type | Description |
|----------|------|-------------|
| `bookUpdateView` | → BookUpdateView | - |
| `categoryList` | → CategoryList | - |
| `roleNameList` | → RoleNameListRespDTO | - |

### MyLikeList

| Property | Type | Description |
|----------|------|-------------|
| `likeList` | Array\<LikeListRespDTO\> | - |

### OrderBookInfoRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `title` | string | - |
| `price` | integer(int64) | - |
| `stock` | integer(int32) | - |
| `status` | enum: ON_SALE, SOLD_OUT, DISCONTINUE, UNPUBLISHED | - |
| `discountPercentage` | number | - |
| `discountPrice` | integer(int64) | - |
| `coverImage` | string | - |
| `volumeNo` | integer(int32) | - |
| `isPackaging` | boolean | - |

### OrderBookSummeryDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `title` | string | - |
| `price` | integer(int64) | - |

### OrderBooksInfoRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `orderBookInfoRespDTOList` | Array\<OrderBookInfoRespDTO\> | - |

### OrderCancelRequest

| Property | Type | Description |
|----------|------|-------------|
| `bookId` | integer(int64) | - |
| `quantity` | integer(int32) | - |

### PageBookAdminResponseDTO

| Property | Type | Description |
|----------|------|-------------|
| `totalElements` | integer(int64) | - |
| `totalPages` | integer(int32) | - |
| `pageable` | → PageableObject | - |
| `first` | boolean | - |
| `last` | boolean | - |
| `numberOfElements` | integer(int32) | - |
| `size` | integer(int32) | - |
| `content` | Array\<BookAdminResponseDTO\> | - |
| `number` | integer(int32) | - |
| `sort` | → SortObject | - |
| `empty` | boolean | - |

### PageBookListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `totalElements` | integer(int64) | - |
| `totalPages` | integer(int32) | - |
| `pageable` | → PageableObject | - |
| `first` | boolean | - |
| `last` | boolean | - |
| `numberOfElements` | integer(int32) | - |
| `size` | integer(int32) | - |
| `content` | Array\<BookListRespDTO\> | - |
| `number` | integer(int32) | - |
| `sort` | → SortObject | - |
| `empty` | boolean | - |

### Pageable

| Property | Type | Description |
|----------|------|-------------|
| `page` | integer(int32) | - |
| `size` | integer(int32) | - |
| `sort` | Array\<string\> | - |

### PageableObject

| Property | Type | Description |
|----------|------|-------------|
| `paged` | boolean | - |
| `pageNumber` | integer(int32) | - |
| `pageSize` | integer(int32) | - |
| `unpaged` | boolean | - |
| `offset` | integer(int64) | - |
| `sort` | → SortObject | - |

### RegisterBookMetaData

| Property | Type | Description |
|----------|------|-------------|
| `categoryList` | → CategoryList | - |
| `roleNameList` | → RoleNameListRespDTO | - |

### ReviewRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `id` | integer(int64) | - |
| `bookId` | integer(int64) | - |
| `bookTitle` | string | - |
| `userId` | integer(int64) | - |
| `content` | string | - |
| `rating` | integer(int32) | - |
| `createdAt` | string(date-time) | - |
| `modifiedAt` | string(date-time) | - |
| `reviewImages` | Array\<ImageRespDTO\> | - |

### RoleNameListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `roleNames` | Array\<string\> | - |

### SortBookListRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `bookListRespDTOS` | Array\<BookListRespDTO\> | - |

### SortObject

| Property | Type | Description |
|----------|------|-------------|
| `sorted` | boolean | - |
| `unsorted` | boolean | - |
| `empty` | boolean | - |

### TagInfoData

| Property | Type | Description |
|----------|------|-------------|
| `tagName` | string | - |

### TagRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `tagId` | integer(int64) | - |
| `tagName` | string | - |

### TestReq

| Property | Type | Description |
|----------|------|-------------|
| `metadata` | string | - |
| `image0` | string(binary) | - |
| `image1` | string(binary) | - |
| `image2` | string(binary) | - |
| `image3` | string(binary) | - |
| `image4` | string(binary) | - |

### TotalDataRespDTO

| Property | Type | Description |
|----------|------|-------------|
| `totalCount` | integer(int64) | - |
| `soldOutCount` | integer(int64) | - |
| `newReviewCount` | integer(int64) | - |
| `categoryCount` | integer(int64) | - |