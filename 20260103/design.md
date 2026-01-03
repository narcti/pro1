# [Design Spec] ZPD 디지털 채용 센터 & 수사 본부

## 1. 기본 정보 (General Info)

- **프로젝트명:** 주토피아 홍보 캠페인 - AI 프로필 & 수사 본부
    
- **해상도 기준:** Mobile Web (375 x 812pt 기준 / @3x Export)
    
- **그리드 시스템:** 4 Columns / Margin 20px / Gutter 16px
    

## 2. 컬러 시스템 (Color System)

### 2.1. Brand Colors

주토피아 경찰국(ZPD)의 아이덴티티와 수사 현장의 긴박함을 표현합니다.

- **ZPD Navy (Primary)**
    
    - Hex: `#003366`
        
    - Role: 배경, 헤더, 주요 버튼 배경
        
    - Description: 깊이감 있는 경찰 제복 컬러, 신뢰감 형성.
        
- **Traffic Neon (Secondary)**
    
    - Hex: `#DDFF00`
        
    - Role: CTA 버튼 텍스트, 하이라이트, 수사 테이프
        
    - Description: 시선을 사로잡는 형광 옐로우, 야간 투시경 느낌.
        
- **Holo Silver (Accent)**
    
    - Hex: `#E0E0E0` (Opacity 80%)
        
    - Role: 카드 테두리, 보조 텍스트, 홀로그램 효과
        
    - Description: 미래적인 금속 재질감.
        

### 2.2. Semantic Colors

- **Error / Danger:** `#FF3B30` (경고, 시스템 오류)
    
- **Success:** `#34C759` (스캔 완료, 미션 성공)
    
- **Text (White):** `#FFFFFF` (기본 본문)
    
- **Text (Gray):** `#8E8E93` (설명, 비활성 텍스트)
    

## 3. 타이포그래피 (Typography)

**Font Family:** `Pretendard` (국문/영문 겸용) / `Impact` (숫자 강조용)

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**Role**|**Weight**|**Size (pt)**|**Line Height**|**Letter Spacing**|**비고**|
|**H1 (Main Title)**|Bold (700)|28|140%|-0.02em|메인 헤드라인|
|**H2 (Section Title)**|SemiBold (600)|20|140%|-0.01em|섹션 구분|
|**Body 1 (Main Text)**|Medium (500)|16|150%|0|본문 기본|
|**Body 2 (Sub Text)**|Regular (400)|14|150%|0|설명문|
|**Caption**|Regular (400)|12|140%|0|법적 고지, 라벨|
|**Button**|Bold (700)|16|100%|0|버튼 텍스트|

## 4. UI 컴포넌트 상세 (Component Specs)

### 4.1. Buttons (CTA)

- **Primary Button (입사 지원하기, 스캔 시작)**
    
    - Size: W 100% (Margin 20px 제외) x H 56px
        
    - Background: `Gradient (Linear): #0055AA to #003366`
        
    - Border: `1px solid #DDFF00`
        
    - Radius: `12px`
        
    - Shadow: `0 4px 12px rgba(0, 85, 170, 0.4)`
        
    - State:
        
        - Default: Opacity 100%
            
        - Pressed: Scale 98%
            
- **Floating Button (수사 수첩)**
    
    - Size: 64 x 64px (Circle)
        
    - Position: Bottom 32px, Right 20px (Fixed)
        
    - Icon: 수첩 아이콘 (24px)
        
    - Background: `#DDFF00`
        

### 4.2. Input Fields (DNA Scan)

- **Camera Frame**
    
    - Size: Aspect Ratio 3:4
        
    - Border: Corners only (ㄱ, ㄴ, ... 형태), 4px solid `#DDFF00`
        
    - Overlay: `Scan Grid` 패턴 (Opacity 20%)
        

### 4.3. Card UI (ZPD 시민증)

- **Dimensions:** 300 x 480px (모바일 화면 중앙 배치)
    
- **Shape:** Radius `16px`
    
- **Effect:** `Backdrop Blur 10px`, `Border 1px Solid rgba(255,255,255,0.2)`
    
- **Front Side:**
    
    - Photo Area: 200 x 200px (Circle Mask)
        
    - Rank Badge: 우측 상단 배치 (48 x 48px)
        
- **Back Side (Stats):**
    
    - Radar Chart: Hexagon shape, Stroke `#DDFF00`
        

## 5. 인터랙션 및 애니메이션 (Interaction & Motion)

### 5.1. Loading (Scanning)

- **Trigger:** 사진 업로드 직후
    
- **Action:**
    
    1. 청색 레이저 라인(`Height 2px`, `Glow Effect`)이 상단에서 하단으로 이동 (Duration: 1.5s, Ease-in-out).
        
    2. 레이저 통과 후 얼굴 부분에 Wireframe 메쉬 오버레이 생성.
        
- **Lottie JSON:** `scan_effect.json` (개발 전달용 파일)
    

### 5.2. Card Flip (결과 확인)

- **Trigger:** 시민증 카드 탭 (Tap)
    
- **Action:**
    
    - Y축 기준 180도 회전 (CSS `transform: rotateY(180deg)`)
        
    - Duration: 0.6s
        
    - Perspective: 1000px (입체감 부여)
        

### 5.3. Stamp Motion (미션 완료)

- **Trigger:** 미션 완료 팝업 확인 시
    
- **Action:**
    
    - 도장 이미지가 Scale 1.5 → 1.0으로 빠르게 축소되며 찍힘 (`Bounce` 효과).
        
    - 찍히는 순간 파티클(별 모양) 폭죽 효과 발생.
        

## 6. 에셋 리스트 (Assets To-Do)

|   |   |   |   |
|---|---|---|---|
|**구분**|**항목**|**포맷**|**비고**|
|**Icons**|ZPD 뱃지, 지문 아이콘, 수첩 아이콘, 닫기 버튼|SVG|Stroke 기반 아이콘|
|**Images**|클로하우저 3D 렌더, 도넛(로딩용), 배경 텍스처(노이즈)|PNG/WEBP|투명 배경 필수|
|**Sound**|스캔음, 도장 쾅 소리, 사이렌(Short)|MP3|용량 최적화 (100kb 이하)|

## 7. 개발자 유의사항 (Dev Note)

1. **다크 모드 강제:** 본 서비스는 영화 분위기를 위해 시스템 설정과 무관하게 'Dark Theme'을 기본으로 합니다.
    
2. **3D 렌더링 최적화:** AI 결과 이미지 로딩 시, Placeholder로 '분석 중' 애니메이션을 반드시 노출하여 이탈을 방지해주세요.
    
3. **Haptic API:** 지원하는 모바일 기기(Android/iOS)에서는 버튼 클릭 및 스캔 완료 시 `Light Impact` 진동을 호출해주세요.