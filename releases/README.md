# TETRA 최신 버전 목록

`latest.json` 은 TETRA 제품 셋의 **가장 최근 배포판**을 적은 파일입니다.
로봇 웹관제·멀티관제·안드로이드 앱이 이 파일을 읽어 "새 버전이 있습니다"를 알려 줍니다.

읽는 주소 (로그인 없이 받음):

```
https://raw.githubusercontent.com/TETRA-ROBOT/.github/main/releases/latest.json
```

## ⚠️ 이 저장소는 공개입니다

누구나 이 파일과 기록(커밋 이력)을 볼 수 있습니다. 배포 노트에 아래를 **쓰지 않습니다.**

- 고객사 이름, 현장 이름, 담당자 이름
- 내부 IP·주소·호스트 이름, 내부 저장소·이슈 링크
- 비밀번호·토큰·키 같은 비밀값

한 번 올린 내용은 지워도 커밋 이력에 남습니다. 올리기 전에 다시 읽어 보세요.

## 형식 (`schema: 1`)

```json
{
  "schema": 1,
  "robot":   { "version": "26.11.1", "tag": "v26.11.1", "released_at": "2026-11-20", "api_level": 1, "notes": { ... } },
  "fleet":   { "version": "26.11.1", "tag": "v26.11.1", "released_at": "2026-11-20", "min_robot_api_level": 1, "notes": { ... } },
  "android": { "version": "26.11.1", "tag": "v26.11.1", "released_at": "2026-11-20", "min_robot_api_level": 1,
               "apk_url": "https://github.com/…/releases/download/v26.11.1/app-release.apk", "notes": { ... } }
}
```

| 키 | 제품 | 저장소(태그를 붙이는 곳) |
|---|---|---|
| `robot` | 로봇 소프트웨어 (웹관제 + ROS) | `tetra_integration` (같은 태그를 서브모듈 7개에도 붙임) |
| `fleet` | 멀티관제 | `TetraFleetManagement` |
| `android` | 안드로이드 앱 | `TetraAndroid` |

- `version`: `YY.MM.N` (년·월·그 달 안의 순번, 예 `26.11.1`). 정규식 `^[0-9]{2}\.(0[1-9]|1[0-2])\.[1-9][0-9]*$`. 제품마다 따로 올라갑니다.
- `tag`: `v` + 버전.
- `released_at`: 배포한 날 `YYYY-MM-DD`.
- `api_level` (robot): 로봇 웹관제 API(`/info` 의 `api_level`)의 호환 단계. REST·소켓 모양이 호환되지 않게 바뀔 때만 올라갑니다.
- `min_robot_api_level` (fleet, android): 이 판이 쓰려면 로봇이 적어도 이 `api_level` 이어야 합니다.
- `apk_url` (android): GitHub Release 의 APK 주소. 없으면 `null`.
- `notes`: 언어별(`ko`, `en`) 배포 노트. 각 언어에 문자열 목록 세 개가 있습니다.
  - `features`: 새로 된 것
  - `fixes`: 고친 것
  - `check_after_update`: 도킹·주행처럼 동작이 바뀌어 **업데이트 뒤 첫 운행 때 지켜봐야 하는 것**. 업데이트 확인창에 빨강으로 보입니다.

### 아직 배포한 적 없는 제품

`version`·`tag`·`released_at` 등이 `null` 이고 노트 목록이 비어 있습니다. 읽는 쪽은 `version` 이 `null` 이면
"새 버전 없음"으로 다룹니다. 키 자체는 빼지 않습니다 (읽는 쪽이 항상 같은 모양을 기대할 수 있게).

### 읽는 쪽 규칙

- `schema` 가 모르는 값이면 이 파일을 무시합니다 (형식이 바뀌면 `schema` 를 올립니다).
- 모르는 키는 무시합니다 (키를 더하는 것은 `schema` 를 올리지 않습니다).
- 버전 비교는 점으로 나눠 숫자로 비교합니다 (`26.11.10` > `26.11.2`).

## 누가, 언제 고치나

배포하는 사람이 태그를 올린 뒤 이 파일의 해당 제품 항목만 고칩니다.

- 로봇 소프트웨어·멀티관제: 각 저장소의 `scripts/release.sh <버전>` 이 배포 노트 초안과 함께 이 파일에 넣을 항목을 출력합니다.
  노트를 다듬어(위 공개 주의 확인) 붙여 넣습니다.
- 안드로이드 앱: `TetraAndroid` 에 태그를 올리면 GitHub Actions 가 APK 를 Release 에 올립니다. 그 주소를 `apk_url` 에 적습니다.
- 고친 뒤 JSON 이 깨지지 않았는지 확인합니다: `python3 -m json.tool releases/latest.json`
- 다른 제품 항목은 건드리지 않습니다.
