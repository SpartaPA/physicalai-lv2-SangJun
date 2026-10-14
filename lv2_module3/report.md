# LV2 Module 3 과제 보고서

## 1. 실험 목적

과제에서 요구한 ROS 2 이미지 처리 자료와 두 설정 비교 실험을 현재 확보된 기록으로 분석한다. 실험 1은 HSV S 하한값, 실험 2는 최소 검출 면적 비율 변경을 다룬다. 실험별 설정, 화면에 표시된 값, 입력 조건의 확인 범위를 분리하고, 실제 근거가 없는 결과나 최종 설정 선택은 만들지 않는다.

## 2. 실행 환경 및 입력 정보

- [`results/environment/live_environment.txt`](results/environment/live_environment.txt)는 `2026-10-14T15:39:50+09:00` 시각과 ROS 2 배포판 `lyrical`을 기록한 기존 스냅샷이다. 해당 스냅샷이 개별 실험과 동일한 시점·환경인지는 확인되지 않는다.
- OS, Python, OpenCV 버전은 [`results/environment/environment.txt`](results/environment/environment.txt)에 미확인으로 기록되어 있다.
- 실험 1-A의 [`settings.txt`](results/experiment_1_hsv/A/settings.txt)에는 `Input: Live RealSense camera`라고 적혀 있다. 이는 텍스트 기록에 적힌 출처이며, 실제 캡처와 연결된 프레임 ID·수신 시각·장치 로그는 없다.
- 실험 1-B와 실험 2-A 입력 출처 및 프레임/구간은 확인되지 않는다. rosbag 사용을 증명하는 자료도 없다.
- 조건별 원본 프레임, rosbag, 메시지 저장 로그 및 실행 시각 로그는 제출 폴더에서 확인되지 않는다. 화면 캡처에 표시된 장면이 입력이라는 정황은 있으나, 정확한 프레임이나 설정 적용 시점을 확정하지 않는다.

## 3. ROS 2 토픽 구성과 수신 방식

기존 [`results/environment/topic_info.txt`](results/environment/topic_info.txt)와 [`results/environment/live_environment.txt`](results/environment/live_environment.txt)의 기록은 다음과 같다.

| 용도 | 토픽 | 타입 | 기록된 발행 노드 | 수신 관련 근거 |
|---|---|---|---|---|
| Raw Image | `/camera/camera/color/image_raw` | `sensor_msgs/msg/Image` | `/camera/camera` | topic_info에 `rqt_image_view`에서 수신·표시했다고 기재. 실험 1-A 화면에도 토픽 선택과 영상이 보임 |
| Mask | `/perception/mask` | `sensor_msgs/msg/Image` | `perception_node` | topic_info에 `rqt_image_view` 표시 기록. 실험 1-A 화면에 선택된 토픽과 마스크가 보임 |
| 검출 영상 | `/perception/debug_image` | `sensor_msgs/msg/Image` | `perception_node` | topic_info에 `rqt_image_view` 표시 기록. 실험 1-A 화면에 선택된 토픽과 오버레이가 보임 |
| 검출 좌표 | `/target` | `geometry_msgs/msg/PointStamped` | topic_info: `perception_node` (publisher endpoint 2개); live 스냅샷: `/perception_node` endpoint 1개 | topic_info는 `ros2 topic echo --once` 수신을 기록. No-target 텍스트에도 명령과 0값 메시지가 기록됨 |

화면은 `rqt_image_view` UI에서 토픽을 선택하고 영상·마스크·검출 오버레이를 표시한 사례를 보여준다. 이것은 토픽이 표시된 정황 증거다. 캡처 시각·프레임 ID·조건별 실행 로그가 없으므로 화면만으로 네 조건 각각의 토픽 수신을 프레임 단위로 입증하지는 못한다.

`topic_info.txt`의 `/target` publisher endpoint 2개와 `live_environment.txt`의 1개, 그리고 live 스냅샷의 subscription count 0은 관측값이 서로 다르다. 이전 기록의 측정 시각은 확인되지 않으므로 어느 쪽이 맞는지 판단하지 않는다.

## 4. Raw Image → Mask → 검출 결과 처리 흐름

과제에서 검증하려는 단계는 `Raw Image 수신 → HSV 조건으로 Mask 생성 → 검출 후보 필터링 → 검출 결과 발행`이다. 토픽 기록과 이미지 UI 캡처는 Raw Image·Mask·검출 영상 토픽의 존재 및 화면 표시를 뒷받침한다. 그러나 처리 노드의 소스 코드가 제출 폴더에 없어 실제 코드 위치, HSV 조건 적용, 면적 필터, 발행 로직을 직접 대조하지 않았다. 따라서 이 흐름은 과제의 분석 대상이며, 구현 검증을 완료했다는 주장은 아니다.

## 5. 실험 1: HSV S 하한값 비교

### 목적과 가설 기록의 시점

목적은 S 하한값을 달리해 Mask 및 검출 화면이 어떻게 달라지는지 비교하는 것이다. A의 설정 문서에는 S 하한 변경 시 낮은 채도 픽셀이 제외될 수 있다는 예상이 적혀 있다. 다만 이 텍스트가 실험 전에 작성됐는지 확인할 기록 시각은 없다. 따라서 이를 사전 등록 가설이라고 단정하지 않고, 설정 기록에 포함된 예상 및 현재 설정값으로부터의 이론적 예상으로 다룬다. `hypothesis.md`의 정리 역시 실행 전 기록 여부가 미확인이다.

### 설정 및 통제 변수

| 설정 | A | B |
|---|---|---|
| `hsv_lower` | `[93, 120, 35]` | `[93, 160, 35]` |
| `hsv_upper` | `[130, 255, 255]` | `[130, 255, 255]` |
| `min_area_ratio` | 0.002 | 0.002 |
| 변경 변수 | 기준 S 하한 120 | S 하한 160 |
| 통제 설계 |  | 나머지 HSV 경계와 최소 면적 비율을 같게 설정 |
| 실제 적용 확인 | 설정 파일 기록만 있음; 적용 로그 없음 | 설정 파일 기록만 있음; 적용 로그 없음 |

설정 근거는 [`A/settings.txt`](results/experiment_1_hsv/A/settings.txt), [`B/settings.txt`](results/experiment_1_hsv/B/settings.txt)다. 동일 입력 프레임을 썼는지 확인할 근거는 없다.

### 확보된 화면과 관측값

- A Raw Image: [`스크린샷 2026-10-12 17-15-31.png`](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-15-31.png)은 `/camera/camera/color/image_raw`가 선택된 UI 화면이다.
- A Mask: [`스크린샷 2026-10-12 17-16-20.png`](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-16-20.png)은 `/perception/mask`와 흰색으로 표시된 영역이 보인다.
- A 검출 화면: [`스크린샷 2026-10-12 17-15-46.png`](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-15-46.png)은 `/perception/debug_image`의 오버레이 화면이다. 이미지에 보이는 표시는 `x=+0.255`, `y=-0.505`, `z=0.0308`, `candidates=1`, 별도 `dist=26 cm`다.
- B Raw Image: 별도 원본 파일 없음.
- B Mask: [`mask.png`](results/experiment_1_hsv/B/mask.png)에 마스크 화면이 있다.
- B 검출 화면: [`detection.png`](results/experiment_1_hsv/B/detection.png)의 오버레이에 `x=+0.223`, `y=+0.408`, `z=0.0473`, `candidates=1`, 별도 `dist=24 cm`가 표시되어 있다.

위 숫자는 해당 PNG 화면에서 읽은 오버레이 텍스트이며, 원시 `/target` 메시지 기록이나 측정 로그로 보지 않는다. 두 화면은 손으로 들고 있는 대상과 책상 위 대상 등 서로 다른 장면 구성을 보여 동일 입력이라고 확인할 수 없다. 각 설정이 실제 화면 생성 때 적용됐다는 증거도 없다.

### 해석 및 최종 판단

설정값상 S 하한을 높이면 S 120~159인 픽셀은 허용 범위에서 제외될 수 있다는 이론적 예상이 성립한다. 화면 오버레이는 각 화면에서 후보 1개와 서로 다른 표시값을 보여주지만, 장면이 다르고 입력 프레임·적용 설정이 연결되지 않았다. 그러므로 `z` 표시가 0.0308에서 0.0473으로 달라진 것을 S 하한의 효과라고 해석할 수 없다. 관측된 차이를 설정 변경의 인과 효과로 확정할 수 없으며, A/B 성능 비교와 설정값 선택은 **판정 보류**다.

## 6. 실험 2: 최소 검출 면적 비율 비교

### 목적과 가설 기록의 시점

목적은 최소 검출 면적 비율 변경이 후보 검출에 미치는 영향을 비교하는 것이다. A 설정 기록에는 기준값을 높이면 작은 후보가 탈락할 수 있다는 예상이 적혀 있다. 기록 시각이 없어 실험 전 가설이라고 단정하지 않는다. 이 예상은 설정값으로부터의 이론적 설명이다.

### 설정 및 통제 변수

| 설정 | A | B |
|---|---|---|
| `min_area_ratio` | 0.002 (설정 기록) | 0.005 (계획값, 미실측) |
| 변경 변수 | 기준 조건 | 최소 검출 면적 비율 |
| 통제 설계 | HSV 범위를 같게 유지한다고 기록 | A와 같게 유지할 계획 |
| 실제 적용 확인 | 설정 파일 기록; 실행 적용 로그 없음 | 실행하지 않음 |

A 설정은 [`A/settings.txt`](results/experiment_2_filter/A/settings.txt)에 있다. 이 파일의 구체적 HSV 전체값은 미확인이다. B의 [`B/settings.txt`](results/experiment_2_filter/B/settings.txt)는 계획값을 기록하며 실제 실행을 뜻하지 않는다.

### 확보된 화면과 관측값

- A Raw Image: 파일 없음.
- A Mask: [`mask.png`](results/experiment_2_filter/A/mask.png)에 마스크 화면이 있다.
- A 검출 화면: [`detection.png`](results/experiment_2_filter/A/detection.png)의 오버레이에 `x=+0.223`, `y=+0.407`, `z=0.0478`, `candidates=1`, 별도 `dist=24 cm`가 보인다.
- B Raw Image·Mask·검출 결과: **미실측**, 파일과 관측값 없음.

A의 오버레이 값은 이미지에 표시된 텍스트일 뿐 메시지 로그나 실험 적용 설정의 독립 증거가 아니다. B 결과는 없다.

### 해석 및 최종 판단

최소 면적 비율을 높이면 면적 기준보다 작은 후보가 필터링될 수 있다는 것은 설정의 이론적 영향이다. A 화면에는 후보 1개가 표시되어 있으나 입력, 적용 설정과 코드 동작을 연결할 자료가 없다. B가 미실측이므로 필터 변화, A/B 차이 및 최종 설정을 판정할 수 없다. 실험 2 비교는 **미완료**다.

## 7. No-target 관측

[`results/no_target/observed_result.txt`](results/no_target/observed_result.txt)의 기존 텍스트 기록은 `ros2 topic echo /target --once` 명령에서 다음 값을 수신했다고 적고 있다.

- `frame_id: camera_color_optical_frame`
- `point.x = 0.0`
- `point.y = 0.0`
- `point.z = 0.0`

[`results/no_target/settings.txt`](results/no_target/settings.txt)에 따르면 입력 장면·카메라 조건·실행 시각·별도 적용 설정은 확인되지 않는다. 해당 No-target 입력의 Raw Image, Mask, 검출 화면 및 처리 노드의 지속 동작 증거도 연결되어 있지 않다. 따라서 결론은 기록된 한 메시지의 0값 수신으로 제한하며, 일반적인 무검출 정상 동작이나 카메라/노드 장애 감지를 입증하지 않는다.

## 8. 결과 분석 및 최종 판단

### 실제 화면 표시값과 해석 범위

| 화면 증거 | 표시된 `x`, `y`, `z` | 후보 수 | 별도 `dist` 표시 | 해석 한계 |
|---|---|---:|---|---|
| 실험 1-A 검출 화면 | `+0.255`, `-0.505`, `0.0308` | 1 | 26 cm | 오버레이 문자 판독값. 원시 메시지·입력 조건 미확인 |
| 실험 1-B 검출 화면 | `+0.223`, `+0.408`, `0.0473` | 1 | 24 cm | A와 장면이 다르고 프레임·설정 적용 여부 미확인 |
| 실험 2-A 검출 화면 | `+0.223`, `+0.407`, `0.0478` | 1 | 24 cm | 단일 A 화면이며 B 대조 없음 |

`/target`의 기록된 타입은 `geometry_msgs/msg/PointStamped`다. 이 프로젝트의 설명에 따라 `point.z`는 검출 영역의 화면 내 면적 비율이며 거리값으로 해석하지 않는다. 표의 `dist`는 화면에 별도로 표시된 문자열을 옮긴 것이며, 그 값의 측정 방식이나 정확성은 제출 자료에서 검증되지 않는다. `point.z`의 구현 근거를 확인할 소스 코드도 제출 폴더에 없다.

### 과제 요구사항별 상태

`충족`은 직접 증거가 요구를 입증할 때만 사용한다.

| 요구사항 | 상태 | 근거 |
|---|---|---|
| 기존 비전 노드의 출처·커밋·실행 방법 | 미확인 | 해당 기록과 소스 없음 |
| 모터 제어/신규 알고리즘 범위 확인 | 미확인 | 구현 소스 없음 |
| Raw Image·Mask·검출 토픽 이름 및 일부 타입 기록 | 부분 충족 | [`topic_info.txt`](results/environment/topic_info.txt), [`live_environment.txt`](results/environment/live_environment.txt) |
| 실제 수신 및 수신 도구 증거 | 부분 충족 | topic_info 텍스트와 A UI 화면은 있으나 조건·프레임·시각별 ROS 수신 로그 없음 |
| 입력 출처(카메라/rosbag) | 부분 충족 | 실험 1-A 설정에 Live RealSense 표기만 있음. 다른 조건 및 rosbag 미확인 |
| 동일 입력 및 설정 통제 | 부분 충족 | 설정 기록상 실험 1은 S만 다름. A/B 입력이 같지 않고 적용 로그도 미확인. 실험 2-B 미실측 |
| 실험 1 결과 비교와 최종 설정 선택 | 미충족 | 오버레이 값은 보이나 장면 및 입력이 달라 인과 비교 불가 |
| 실험 2 A/B 결과 비교 | 미충족 | A 화면만 있고 B 미실측 |
| Raw Image→Mask→검출→발행 구현 흐름 검증 | 미확인 | 소스 코드 미제출 |
| `/target` 타입과 `point.z` 의미 | 부분 충족 | 타입은 토픽 기록에 있음. z 면적비 설명은 유지하나 코드로 독립 검증하지 못함 |
| No-target 무목표 입력 및 처리 지속성 | 미충족 | 한 메시지 수신 기록만 있으며 Raw/Mask 및 지속 동작 증거 없음 |
| 실행 환경 버전 | 부분 충족 | ROS 2 `lyrical` 스냅샷만 있음. OS/Python/OpenCV 미확인 |
| 제출 문서와 내부 구조 | 충족 | README, report, results 및 내부 자료 존재 |
| Public 비로그인 접근성 | 미확인 | 원격 공개 상태를 확인하지 않음 |

### 최종 판단

문서는 현재 증거를 반영해 정리됐지만 실험 완료와는 구분해야 한다. 실험 1은 화면에서 오버레이 값을 확인할 수 있으나 입력 및 적용 설정이 통제되지 않아 S 하한 선택 근거로 사용할 수 없다. 실험 2-B는 미실측이므로 비교가 미완료다. 현재 자료만으로 최종 우수 설정값을 선택하지 않는다.

## 9. 한계 및 추가 확인 사항

- 동일 입력을 보장하는 프레임 파일 또는 rosbag 구간과 조건별 연결 기록이 없다.
- 실행 시 적용된 파라미터, 해상도, 촬영 조건, 측정 시각, 반복 횟수를 확인할 로그가 없다.
- 기존 노드의 출처, 커밋, 실행 방법 및 코드상 HSV/면적 처리 로직을 확인할 소스가 없다.
- 실험 1은 조건별 화면이 일부 있으나 통제된 Raw Image·Mask·검출 비교가 부족하다.
- 실험 2-A에는 Raw Image 자료가 없고, B는 Raw Image·Mask·검출 결과 모두 미실측이다.
- No-target 텍스트 기록에는 입력 영상과 처리 노드 상태 증거가 없다.
- OS, Python, OpenCV 및 각 실험 당시 전체 ROS 환경은 미확인이다.
- `topic_info.txt`와 live 스냅샷의 `/target` endpoint 수 차이는 측정 시점 불명으로 해소되지 않았다.
- 저장소 Public 여부 및 로그아웃 상태에서의 접근성은 별도 확인이 필요하다.

본 보고서는 현재 확보된 자료로 작성하는 제출용 최종 기록이다. 현재 장비를 사용할 수 없어 추가 실험 및 실측 증거 확보가 불가능한 상태다. 문서 작성은 마무리했지만 실험 결과를 추가로 만들어내지 않았으며, 실험 1의 비교 판정과 최종 설정 선택은 보류하고 실험 2-B는 미실측으로 남긴다. 이에 따라 제출물 자체는 완성됐으나 모든 실험 요구가 수행·입증된 것은 아니다.
