```text
physicalai-lv2-이름/              # GitHub 제출 저장소
│
├── README.md                         # 저장소 소개 및 보고서 링크
│
└── lv2_module3/
    │
    ├── README.md                     # 실행 환경 및 실행 방법
    ├── report.md                     # 과제 분석 보고서
    │
    └── results/                      # 실험 결과 및 증거
        │
        ├── environment/
        │   ├── environment.txt       # OS, ROS 2, Python, OpenCV 버전
        │   └── topic_info.txt        # 실제 토픽 이름, 타입, 발행 노드
        │
        ├── experiment_1_hsv/
        │   ├── hypothesis.md         # 가설과 통제 조건
        │   ├── A/
        │   │   ├── raw_image.png
        │   │   ├── mask.png
        │   │   ├── detection.png
        │   │   └── settings.txt
        │   └── B/
        │       ├── raw_image.png
        │       ├── mask.png
        │       ├── detection.png
        │       └── settings.txt
        │
        ├── experiment_2_filter/
        │   ├── hypothesis.md
        │   ├── A/
        │   │   ├── raw_image.png
        │   │   ├── mask.png
        │   │   ├── detection.png
        │   │   └── settings.txt
        │   └── B/
        │       ├── raw_image.png
        │       ├── mask.png
        │       ├── detection.png
        │       └── settings.txt
        │
        └── no_target/
            ├── raw_image.png
            ├── mask.png
            ├── detection.png
            └── settings.txt
```