# 📊 Mini View Ranking Dashboard

장소/폴더 기반 데이터(`place_list.csv`)를 활용하여  
**조회수 증가 시뮬레이션 → 로그 기록 → 자동화(cron) → 데이터 처리(AWK) → 시각화(web)**  
까지 구현하는 미니 프로젝트입니다.

---

## ✅ 목표
1. **데이터 관리** : `place_list.csv`를 기반으로 `(name + owner)` 단위로 조회수 관리  
2. **로그 기록** : `rank_log.txt`에 모든 조회 이벤트 기록  
3. **자동화** : `update.sh`를 cron에 등록하여 주기적 실행  
4. **데이터 처리** : AWK를 활용하여 Top 5 조회수 집계, CSV 갱신, 로그 생성  
5. **시각화** : `range_rank.html` + JavaScript를 통해 실시간 대시보드 제공

---

## 📁 프로젝트 구조
```
/home/ubuntu/YGJG/
 ├── place_list.csv       # 장소 목록 (추천 데이터의 원본 CSV)
 ├── rank_log.txt         # 추천 내역 로그 파일
 ├── top_rank.txt         # 랭킹 top5개만 저장하는 파일
 ├── range_rank.html      # 웹에서 보여지는 실시간 랭킹 페이지
 ├── update_range_rank.sh # 데이터 갱신 및 로그 기록용 스크립트
 └── tmp.csv              # 임시 파일 (데이터 갱신 시 사용)

```
---

## 📑 데이터 파일

- **place_list.csv**  
  - 장소의 카테고리, 폴더명(Filename), 세부 장소명, 방문일자, 평점, 리뷰, 조회수(Viewers), 작성자(Owner), 마지막 수정일을 저장  
  - `Viewers` 컬럼은 스크립트 실행 시마다 무작위로 증가  

- **rank_log.txt**  
  - 실행 시점 시간 표시
  - CSV 헤더 출력 후 상위 추천 장소 순서대로 기록
  - 실시간 조회수 변동 반영
    ```bash
    저장 예시
    ==== Fri Sep  5 02:59:01 PM KST 2025 ====
    카테고리,파일명,장소,방문 날짜,별점,리뷰,조회수,폴더 주인,폴더 마지막 수정 시간
    여행,에버랜드,튤립정원,2024-07-10,2,가격 대비 만족도가 높아요. 다시 방문하고 싶습니다.,4976,78,2025-04-14 01:48:13
    ```
---

### ⚡ 조회수 증가 시뮬레이션

- `update_range_rank.sh` 스크립트를 통해 `(Filename, Owner)` 단위로 조회수를 무작위 증가
- 증가 결과는 즉시 **CSV**에 반영되고, 동시에 **로그 파일**과 **탑 5 기록 파일**에 저장

---

### 🕒 Cron 자동화

- 크론탭(crontab)에 등록하여 **1분마다 자동 실행**  
- 1분 주기마다 sh파일을 실행해 데이터가 지속적으로 변화하도록 설정  
- 이를 통해 실제 서비스처럼 조회수가 계속 누적되는 환경을 시뮬레이션 가능  

---

### 🔎 AWK 활용

- **데이터 처리 및 관리**에 `awk`를 활용  
  - 랜덤 조회수 증가 후 Top 5 조회수의 값 range_rank.txt에 저장
  - 마지막 결과값에 대한 top5만 top_rank.txt에 저장

---

### 🌐 웹 대시보드

- **index.html**을 통해 실시간 랭킹 시각화  
- CSV 파일을 직접 읽어와 상위 5개 장소를 카드 형태로 표시  
- 조회수, 카테고리, 최근 업데이트 날짜 등을 직관적으로 확인 가능  
- JavaScript를 사용하여 **1분마다 자동 갱신**  


---

## 🎬 실시간 실행 데모

샤진 넣기~~

---

## 🛠️ 트러블 슈팅

### 1️⃣ CSV 조회수 업데이트 문제

- 조회수 컬럼 번호 오류 ($8 → $7)
- 랜덤 증가 적용 누락
- 상위 3개 출력 순서 오류

### 해결
- 컬럼 번호 수정
- TMP 파일 사용하여 안전하게 덮어쓰기
- `srand()` 적용, 파일별 랜덤 1~5 증가
- 상위 3개 선택 시 파일명 중복 처리 후 조회수 기준 정렬

---

### 2️⃣ Nginx TXT 파일 HTML 서빙 문제

- 403 Forbidden 발생 (디렉토리 접근 권한 부족)
- 정적 HTML 사용 시 변경 반영 안됨

### 해결
- 상위 디렉토리 및 파일 소유권/권한 조정
  ```bash
  sudo chmod 755 /home/ubuntu
  sudo chown -R www-data:www-data /home/ubuntu/YGJG
  sudo chmod 755 /home/ubuntu/YGJG
  sudo chmod 644 /home/ubuntu/YGJG/range_rank.html

