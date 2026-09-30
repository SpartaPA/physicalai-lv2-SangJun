# 문제 4 설계

PC ROS2 Node → micro-ROS Agent → OpenCR → Dynamixel

제어 10ms, 상태 발행 100ms → 상태 1회당 제어 10회. 통신 timeout 시 stale target을 사용하지 않고 출력 0 및 정지.

가상 기록: 0.0s target=30°, 0.1s=2°, 0.5s=12°, 1.0s=22°, 2.0s=29°.
