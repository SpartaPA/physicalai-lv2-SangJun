# Lv2 Module 1 — OpenCR·다이나믹셀 통합 4문제

## 환경

- Raspberry Pi 4 Model B / Ubuntu Server 22.04
- OpenCR 1.0
- Dynamixel ID 12 / Protocol 2.0 / 1 Mbps
- USB Serial `/dev/ttyACM0`
- `opencr_position_p.ino`

## 문제별 결과

- [문제 1 — OpenCR 환경 구성 및 P 제어](./report.md#문제-1-opencr-환경-구성-및-p-제어-실행)
  - [업로드 로그](./results/problem1_upload.log)
  - [Execution A 로그](./results/problem1_execution_A.log)

- [문제 2 — 오차 계산 및 피드백 분석](./report.md#문제-2-오차-계산-및-센서통신-경로)
  - [계산 및 분석](./results/problem2_calculation.md)

- [문제 3 — Kp 변경에 따른 제어 응답 비교](./report.md#문제-3-kp-변경에-따른-제어-응답-비교)
  - [Execution A 로그](./results/problem3_execution_A.log)
  - [Execution B 로그](./results/problem3_execution_B.log)

- [문제 4 — ROS2 · micro-ROS Agent · OpenCR · Dynamixel](./report.md#문제-4-ros2--micro-ros-agent--opencr--dynamixel-통합-구조)
  - [설계 내용](./results/problem4_design.md)

## 보고서

- [전체 보고서](./report.md)
- [Results](./results/README.md)