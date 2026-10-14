# LV2 Module 3 실험 자료 안내

## 과제 목적

파란색 타깃 검출에서 HSV S 하한값과 최소 검출 면적 비율을 바꾸는 비교 실험을 문서화하고, 무검출 시 `/target` 관측 및 실험 환경 정보를 함께 제출합니다. 이 자료 묶음은 확보된 기록과 이미지에 한정하며, 미실측 조건의 결과를 포함하지 않습니다.

## 사용한 팀 저장소

팀 프로젝트 저장소: https://github.com/SpartaPA/Lv2_EYE4_Assignment

실제 사용한 커밋, 코드 경로 및 실행 파일은 별도 기록에서 확인되지 않습니다.

## 실험 목적과 변경 변수

| 실험 | 목적 | 변경 변수 | 현재 상태 |
|---|---|---|---|
| 실험 1: HSV S 하한 | HSV S 하한 변경에 따른 마스크와 검출 결과 비교 | S 하한 A=120, B=160. 나머지 HSV 경계 및 `min_area_ratio=0.002`는 통제 설계 | A 화면 캡처 3개와 B 마스크·검출 이미지가 있음. 같은 입력 및 실행 조건인지는 미확인, 성능 판정 보류 |
| 실험 2: 최소 면적 비율 | 최소 검출 면적 비율 변경에 따른 작은 영역 검출 결과 비교 | A=0.002, B=0.005. 나머지 조건은 동일하게 유지하는 설계 | A 자료만 있음. B 미실측 |
| No-target 관측 | 무검출로 기록된 상황의 `/target` 메시지 확인 | 해당 기록에서 비교 변수를 정하지 않음 | 텍스트 기록에 frame_id 및 0값이 있음. 상세 실행 조건 미확인 |

## 실제 제출 파일

```text
lv2_module3/
├── README.md
├── report.md
└── results/
    ├── environment/
    │   ├── environment.txt
    │   ├── live_environment.txt
    │   └── topic_info.txt
    ├── experiment_1_hsv/
    │   ├── hypothesis.md
    │   ├── A/
    │   │   ├── settings.txt
    │   │   ├── 스크린샷 2026-10-12 17-15-31.png
    │   │   ├── 스크린샷 2026-10-12 17-15-46.png
    │   │   └── 스크린샷 2026-10-12 17-16-20.png
    │   └── B/
    │       ├── settings.txt
    │       ├── mask.png
    │       └── detection.png
    ├── experiment_2_filter/
    │   ├── hypothesis.md
    │   ├── A/
    │   │   ├── settings.txt
    │   │   ├── mask.png
    │   │   └── detection.png
    │   └── B/
    │       └── settings.txt
    └── no_target/
        ├── settings.txt
        └── observed_result.txt
```

## 제출 상태

이 제출물은 현재 확보된 문서와 증거를 기준으로 마무리한 최종 기록입니다. 현재 장비를 사용할 수 없어 추가 실험 및 실측 증거 확보는 진행하지 못했습니다. 문서 정리는 완료했지만, 실험 1의 통제 비교와 실험 2 A/B 비교는 실험 완료로 판정하지 않습니다. 실험 2-B는 미실측이며 최종 설정값 선택은 보류 상태입니다. 보고서의 평가표와 한계 항목을 함께 제출해 확인된 내용과 미완료 항목을 구분합니다.

## 실험 증거 및 확인 상태

- 실험 1-A 설정: [settings.txt](results/experiment_1_hsv/A/settings.txt)
- 실험 1-A Raw Image 토픽 선택 UI 캡처 (원본 프레임 파일 아님): [스크린샷 2026-10-12 17-15-31.png](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-15-31.png)
- 실험 1-A 검출 오버레이 화면: [스크린샷 2026-10-12 17-15-46.png](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-15-46.png)
- 실험 1-A 마스크 화면: [스크린샷 2026-10-12 17-16-20.png](results/experiment_1_hsv/A/스크린샷%202026-10-12%2017-16-20.png)
- 실험 1-B 설정: [settings.txt](results/experiment_1_hsv/B/settings.txt); 마스크: [mask.png](results/experiment_1_hsv/B/mask.png); 검출 화면: [detection.png](results/experiment_1_hsv/B/detection.png). B 원본 입력 이미지는 없음.
- 실험 2-A 설정 기록: [settings.txt](results/experiment_2_filter/A/settings.txt); 마스크: [mask.png](results/experiment_2_filter/A/mask.png); 검출 화면: [detection.png](results/experiment_2_filter/A/detection.png).
- 실험 2-B 계획 설정: [settings.txt](results/experiment_2_filter/B/settings.txt). 미실측이며 결과 이미지와 측정값은 없음.
- 무검출 메시지 관측 기록: [observed_result.txt](results/no_target/observed_result.txt); 실행 조건 기록: [settings.txt](results/no_target/settings.txt).
- 실험 가설 및 조건: [실험 1](results/experiment_1_hsv/hypothesis.md), [실험 2](results/experiment_2_filter/hypothesis.md).
- 환경과 토픽: [environment.txt](results/environment/environment.txt), [기존 live 스냅샷](results/environment/live_environment.txt), [topic_info.txt](results/environment/topic_info.txt).

## 관측값 해석과 미확인 사항

검출 화면에 표시된 오버레이 값은 보고서에 화면 표시값으로 정리했습니다. 이는 원시 `/target` 메시지 로그가 아니며, 입력 프레임과 설정 적용 시점도 확인되지 않습니다. 실험 1 A/B 화면의 장면 구성이 달라 동일 입력 비교로 볼 수 없습니다. 최종 설정값 선택은 근거가 부족해 보류했습니다.

실험별 실제 설정 적용 여부, A/B 입력 장면·조명·거리·측정 시점의 일치 여부는 자료만으로 확인되지 않습니다. 실험 1-B 원본 영상은 없고, 실험 2-B는 실행하지 않았습니다. OS, Python, OpenCV 버전도 확인되지 않았습니다. ROS 2 `lyrical` 표기는 기존 live 스냅샷에 한정되며 각 실험 당시 환경과 같다고 확인된 것은 아닙니다. 토픽 기록 사이 endpoint 수 차이도 측정 시점 불명으로 판정하지 않았습니다.

## 읽는 순서

1. 이 README에서 제출 자료와 실험 상태를 확인합니다.
2. [실험 보고서](report.md)에서 목적, 환경, 관측 및 한계를 읽습니다.
3. 각 `hypothesis.md`와 `settings.txt`를 확인한 뒤 README에 연결된 이미지·기록을 증거로 검토합니다.

