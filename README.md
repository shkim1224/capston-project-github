# MediaPipe Hand & Finger Recognition Project

MediaPipe를 활용한 실시간 손가락 인식 프로젝트

## System Block Diagram

```mermaid
block-beta
columns 3

block:INPUT:1
  A["Camera Input\n(Webcam)"]
end

block:PREPROCESSING:1
  B["Image Preprocessing\n(BGR → RGB 변환,\n프레임 리사이즈)"]
end

block:MEDIAPIPE:1
  C["MediaPipe Hands\n(Hand Detection +\nHand Landmark Model)"]
end

A --> B --> C

block:LANDMARK:3
  columns 5
  D0["Wrist\n(0)"]
  D1["Thumb\n(1-4)"]
  D2["Index\n(5-8)"]
  D3["Middle\n(9-12)"]
  D4["Ring+Pinky\n(13-20)"]
end

C --> D0
C --> D1
C --> D2
C --> D3
C --> D4

block:ANALYSIS:3
  columns 3
  E1["Finger State\nDetection\n(펴짐/접힘 판별)"]
  E2["Gesture\nRecognition\n(제스처 분류)"]
  E3["Position\nTracking\n(좌표 추적)"]
end

D0 --> E1
D1 --> E1
D2 --> E2
D3 --> E2
D4 --> E3

block:OUTPUT:3
  columns 3
  F1["Visual\nOverlay\n(랜드마크 시각화)"]
  F2["Gesture\nOutput\n(인식 결과 출력)"]
  F3["Application\nControl\n(제스처 기반 제어)"]
end

E1 --> F1
E2 --> F2
E3 --> F3
```

## Data Flow Diagram

```mermaid
flowchart TD
    subgraph Input ["1. 입력 단계"]
        CAM[/"웹캠 영상 캡처\n(cv2.VideoCapture)"/]
    end

    subgraph Preprocessing ["2. 전처리 단계"]
        FLIP["좌우 반전\n(cv2.flip)"]
        COLOR["색상 변환\n(BGR → RGB)"]
    end

    subgraph Detection ["3. MediaPipe 손 감지"]
        HAND_DET["Hand Detection Model\n(Palm Detection)"]
        HAND_LM["Hand Landmark Model\n(21개 랜드마크 추출)"]
    end

    subgraph Landmarks ["4. 랜드마크 데이터 (21 Points)"]
        direction LR
        L0["0: WRIST"]
        L1["1-4: THUMB\n(CMC→TIP)"]
        L2["5-8: INDEX\n(MCP→TIP)"]
        L3["9-12: MIDDLE\n(MCP→TIP)"]
        L4["13-16: RING\n(MCP→TIP)"]
        L5["17-20: PINKY\n(MCP→TIP)"]
    end

    subgraph Analysis ["5. 손가락 상태 분석"]
        ANGLE["관절 각도 계산"]
        STATE["손가락 펴짐/접힘\n판별 로직"]
        COUNT["펴진 손가락 개수\n카운팅"]
        GESTURE["제스처 매핑\n(주먹, V사인, OK 등)"]
    end

    subgraph Output ["6. 출력 단계"]
        DRAW["랜드마크 &\n연결선 시각화"]
        TEXT["손가락 개수 &\n제스처 텍스트 표시"]
        DISPLAY[/"화면 출력\n(cv2.imshow)"/]
    end

    CAM --> FLIP --> COLOR
    COLOR --> HAND_DET --> HAND_LM
    HAND_LM --> L0 & L1 & L2 & L3 & L4 & L5
    L0 & L1 & L2 & L3 & L4 & L5 --> ANGLE
    ANGLE --> STATE --> COUNT --> GESTURE
    GESTURE --> TEXT
    STATE --> DRAW
    DRAW & TEXT --> DISPLAY
    DISPLAY -->|"다음 프레임"| CAM
```

## MediaPipe Hand Landmarks Reference

```mermaid
flowchart TD
    subgraph Hand ["MediaPipe 21 Hand Landmarks"]
        W((0\nWRIST))

        W --> T1((1\nTHUMB_CMC))
        T1 --> T2((2\nTHUMB_MCP))
        T2 --> T3((3\nTHUMB_IP))
        T3 --> T4((4\nTHUMB_TIP))

        W --> I1((5\nINDEX_MCP))
        I1 --> I2((6\nINDEX_PIP))
        I2 --> I3((7\nINDEX_DIP))
        I3 --> I4((8\nINDEX_TIP))

        W --> M1((9\nMIDDLE_MCP))
        M1 --> M2((10\nMIDDLE_PIP))
        M2 --> M3((11\nMIDDLE_DIP))
        M3 --> M4((12\nMIDDLE_TIP))

        W --> R1((13\nRING_MCP))
        R1 --> R2((14\nRING_PIP))
        R2 --> R3((15\nRING_DIP))
        R3 --> R4((16\nRING_TIP))

        W --> P1((17\nPINKY_MCP))
        P1 --> P2((18\nPINKY_PIP))
        P2 --> P3((19\nPINKY_DIP))
        P3 --> P4((20\nPINKY_TIP))
    end
```

## Tech Stack

| 구분 | 기술 |
|------|------|
| Language | Python 3.x |
| Computer Vision | OpenCV (cv2) |
| Hand Detection | MediaPipe Hands |
| Visualization | Mermaid, OpenCV Drawing |

## Project Structure

```
capston-project-github/
├── README.md
├── requirements.txt
├── main.py                # 메인 실행 파일
├── hand_detector.py       # MediaPipe 손 감지 모듈
├── finger_counter.py      # 손가락 상태 분석 모듈
└── gesture_recognizer.py  # 제스처 인식 모듈
```
