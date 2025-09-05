# 📊 Mini View Ranking Dashboard

여러 장소를 저장한 폴더 기반 데이터를 활용하여  
**조회수 증가 시뮬레이션 → 로그 기록 → 자동화(cron) → 데이터 처리(AWK) → 시각화(web)**  
까지 구현하는 미니 프로젝트입니다.

---
## 👥 Team
<table>
  <tr>
    <!-- 이름 (링크) -->
    <td align="center">
      <a href="https://github.com/imewuzin"><strong>장송하</strong></a>
    </td>
    <td align="center">
      <a href="https://github.com/Minkyoungg0"><strong>문민경</strong></a>
    </td>
  </tr>
  <tr>
    <!-- 프로필 사진 -->
    <td align="center">
      <img src="https://github.com/songhajang.png" width="100"/>
    </td>
    <td align="center">
      <img src="https://github.com/Minkyoungg0.png" width="100"/>
    </td>
  </tr>
</table>

<br/>

---

## ✅ 목표

폴더별 **조회수를 랜덤으로 증가**시키고, 자동화를 통해 **최신 상태를 유지.**<br>
상위 항목을 집계해 결과를 **시각적으로 보여줌**으로써 데이터 변화를 쉽게 확인할 수 있도록 함.

---

## 📁 파일 구조
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
csv 저장 양식
```csv 
카테고리,파일명,장소,방문 날짜,별점,리뷰,조회수,폴더 주인,폴더 마지막 수정 시간
맛집,광안리_20240315.txt,광안리 해수욕장,2024-03-15,5,직원분들이 친절해서 기분 좋게 다녀왔습니다.,88,3040,2024-11-27 01:48:13
```
## 🔹 컬럼 설명
카테고리           : 데이터 유형 (맛집, 카페 등)<br>
파일명             : 리뷰가 기록된 파일 이름<br>
장소               : 방문한 위치 이름<br>
방문 날짜           : 실제 방문 날짜<br>
별점               : 1~5점 척도로 평가<br>
리뷰               : 방문 경험에 대한 설명<br>
조회수             : 해당 리뷰 조회 횟수<br>
폴더 주인          : 리뷰 데이터 폴더 소유자 정보<br>
폴더 마지막 수정 시간: 폴더가 마지막으로 수정된 날짜 및 시간

---

### ⚡ 조회수 증가 시뮬레이션
- 스크립트를 통해 (파일명, 소유자) 단위로 조회수를 무작위로 증가시킴
- 증가된 결과는 데이터에 반영되며, 동시에 로그와 상위 5개 항목 기록에도 저장됨

---

### 🕒 Cron 자동화

- **crontab** 에 등록하여 **1분마다 자동 실행**  
- 1분 주기마다 sh파일을 실행해 데이터가 지속적으로 변화하도록 설정  
- 이를 통해 실제 서비스처럼 **조회수가 계속 누적되는 환경**을 시뮬레이션 가능  

---

### 🔎 AWK 활용

- **데이터 처리 및 관리**에 `awk`를 활용
- 무작위로 증가된 조회수를 반영한 뒤, 상위 5개 항목을 집계하여 별도 파일에 저장
- 최신 결과 기준의 상위 5개 항목만 추려내어 기록

---

### 🌐 웹 대시보드

- 웹 페이지를 통해 **실시간 랭킹**을 시각화 
- **상위 5개 장소**를 카드 형태로 표시
- 조회수, 카테고리, 최근 업데이트 날짜 등을 직관적으로 확인 가능 
- JavaScript를 활용해 **1분마다 자동 갱신**

---

## 🎬 실시간 실행 데모


<img src="https://github.com/user-attachments/assets/0e08cef4-6b31-4f9a-913c-d85ad9362935" width="500">

---

## 🛠️ 트러블 슈팅


### 1️⃣ CSV 조회수 업데이트 
**문제**
- 조회수가 갱신되지 않고 상위 항목 순서도 틀림
- 
**원인**
- 조회수 컬럼을 $8로 잘못 참조함 (실제는 $7)

**해결**
- 참조 컬럼을 $7로 수정하여 정상적으로 증가·집계·정렬됨
---

### 2️⃣ Nginx TXT 파일 HTML 서빙 
문제: Nginx에서 HTML 파일 접근 시 403 Forbidden 오류 발생

원인: 상위 디렉토리와 파일의 소유권 및 접근 권한이 올바르게 설정되지 않음

해결: 디렉토리와 파일 권한을 조정하고, 소유권을 www-data로 변경하여 정상적으로 서빙됨
**문제**
- Nginx에서 HTML 파일 접근 시 403 Forbidden 오류 발생

**원인**
- 상위 디렉토리와 파일의 소유권 및 접근 권한이 올바르게 설정되지 않음
**해결**
- 디렉토리와 파일 권한을 조정하고, 소유권을 www-data로 변경하여 정상적으로 서빙됨
  ```bash
  sudo chmod 755 /home/ubuntu
  sudo chown -R www-data:www-data /home/ubuntu/YGJG
  sudo chmod 755 /home/ubuntu/YGJG
  sudo chmod 644 /home/ubuntu/YGJG/range_rank.html
```

