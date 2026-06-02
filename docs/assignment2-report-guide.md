# 과제2 보고서 작성 정리

## 기본 정보

- 과목명: 웹 프레임워크2
- 과제명: Spring Boot + Spring Data JPA + Security Product 기능 확장
- 학번: 2071146
- 성명: 이정환
- GitHub Repository URL: https://github.com/jwlee1008/webframework2-2
- 제출 파일명: 2071146_이정환_과제2보고서.pdf
- 제출 마감: 2026년 6월 7일 일요일 오후 11시 55분

## 보고서 페이지 구성

1. 1페이지 표지
   - 학번, 성명, GitHub Repository URL을 크게 작성
   - URL은 클릭 가능하도록 작성

2. 2페이지 아키텍처
   - Spring Security 필터 체인 흐름 다이어그램 작성
   - 예시 흐름:
     - HTTP 요청
     - SecurityFilterChain
     - UsernamePasswordAuthenticationFilter
     - DaoAuthenticationProvider
     - CustomUserDetailsService
     - BCryptPasswordEncoder.matches()
     - SecurityContext 저장
     - Controller 접근

3. 3페이지 코드 분석: Spring Data JPA 페이징/검색
   - `ProductRepository.findByNameContaining`
   - `ProductService.getProducts`, `searchProducts`
   - `ProductController.list`
   - 핵심 설명:
     - `PageRequest.of(page, size, Sort.by("id"))`로 페이지 요청 생성
     - 검색어가 있으면 `@Query`의 JPQL LIKE 검색 실행
     - 검색어가 없으면 `findAll(pageable)`로 전체 목록 페이징
     - Thymeleaf는 `productPage.content`를 반복 출력

4. 4페이지 코드 분석: 상품 수정과 비밀번호 변경
   - 상품 수정:
     - `ProductService.updateProduct`
     - `@Transactional` 안에서 조회한 엔티티의 필드만 변경
     - 별도 `save()` 없이 트랜잭션 종료 시 더티 체킹으로 UPDATE
   - 비밀번호 변경:
     - `PasswordChangeDto`
     - `UserService.changePassword`
     - 현재 비밀번호는 `passwordEncoder.matches()`로 확인
     - 새 비밀번호는 `passwordEncoder.encode()` 후 저장

5. 5페이지 이후 실행 화면 스크린샷
   - 모든 스크린샷에 날짜, 시간, 학번, 성명 footer가 보이게 캡처
   - 캡처 순서 예시:
     - 1. 로그인 후 `/home`
     - 2. `/products` 첫 페이지: 총 20개, 1/4 페이지 확인
     - 3. `/products?page=1&size=5` 페이지 이동 확인
     - 4. `keyword=삼성전자` 검색 결과 3건
     - 5. 검색 결과 없음 화면
     - 6. ADMIN 상품 수정 폼 pre-fill
     - 7. 상품 수정 완료 후 목록 성공 메시지
     - 8. 일반 USER로 `/products/{id}/edit` 접근 시 403
     - 9. 비밀번호 변경 폼
     - 10. 현재 비밀번호 불일치 오류
     - 11. 비밀번호 변경 성공 후 `/home` 이동

6. 마지막 페이지 채점 기준표
   - 과제 문서의 자기 평가 체크리스트 표를 그대로 포함
   - 자기 평가 칸은 최종 확인 후 직접 기입
   - 학습 소감 3~5문장 작성

## 실행 전 체크

```bash
docker compose up -d
mvn spring-boot:run
```

- 로그인 계정: `admin@hansung.ac.kr / admin1234`
- 접속 주소: `http://localhost:8080/login`
- Docker Desktop이 켜져 있어야 MySQL 컨테이너가 실행됨
- 로컬 테스트 명령:

```bash
mvn test
```

## 구현 커밋 목록

- `98bebc4 feat: 실습 정보 표시 추가`
- `92ef8da feat: 상품 페이징 검색 추가`
- `9237d7e feat: 상품 목록 화면 수정`
- `1806e68 feat: 상품 수정 기능 추가`
- `b2ce753 feat: 비밀번호 변경 추가`
- `2aa2349 test: 과제 기능 테스트 추가`

## 브라우저 검증 결과

- 로그인 화면 footer 표시 확인
- 관리자 로그인 성공 확인
- 상품 목록 20개, 5개씩 4페이지 확인
- `삼성전자` 검색 결과 3건 확인
- 상품 수정 폼 기존 데이터 pre-fill 확인
- 비밀번호 변경 폼 확인
- 현재 비밀번호 불일치 오류 메시지 확인

## 학습 소감 작성 예시 방향

- Spring Data JPA의 `Pageable`을 사용하니 직접 limit/offset SQL을 작성하지 않아도 페이지 처리가 가능했다.
- 상품 수정에서는 `save()`를 호출하지 않아도 트랜잭션 안의 엔티티 변경이 DB UPDATE로 반영되는 더티 체킹 개념을 이해했다.
- 비밀번호 변경에서는 평문 비밀번호를 직접 비교하지 않고 BCrypt의 `matches()`를 사용해야 안전하다는 점을 확인했다.
- AI 도구는 요구사항 정리, 코드 작성 보조, 테스트 확인에 활용했고, 최종 코드는 직접 실행 결과를 보면서 확인했다.
