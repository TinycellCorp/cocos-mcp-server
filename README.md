# Cocos Creator MCP 서버 플러그인

**[📖 English](README.EN.md)** **[📖 中文](README.CN.md)** **[📖 한국어](README.md)**

Cocos Creator 3.5+ 용 MCP(Model Context Protocol) 서버 플러그인입니다. AI 어시스턴트가 표준화된 프로토콜을 통해 Cocos Creator 에디터와 상호작용할 수 있게 해줍니다. 원클릭 설치, 복잡한 환경설정 불필요. Claude 데스크톱, Claude CLI, Cursor 등 주요 클라이언트에서 테스트 완료되었으며, 다른 에디터도 이론상 완벽 지원합니다.

**50개 핵심 도구로 에디터 기능 99% 커버!**

## 영상 데모

[<img width="503" height="351" alt="데모 영상" src="https://github.com/user-attachments/assets/f186ce14-9ffc-4a29-8761-48bdd7c1ea16" />](https://www.bilibili.com/video/BV1mB8dzfEw8?spm_id_from=333.788.recommend_more_video.0&vd_source=6b1ff659dd5f04a92cc6d14061e8bb92)

## 바로가기

- **[📖 전체 기능 가이드 (English)](FEATURE_GUIDE_EN.md)** - 전체 도구 상세 문서 (보완 중)
- **[📖 전체 기능 가이드 (中文)](FEATURE_GUIDE_CN.md)** - 전체 도구 상세 문서 (보완 중)

---

## 빠른 설치 (Claude CLI 스킬)

Claude CLI를 사용 중이라면 `/cocos-mcp-setup` 스킬로 Cocos 3.x 프로젝트에 원클릭 설치할 수 있습니다.

### 사전 준비

1. [Claude CLI](https://docs.anthropic.com/en/docs/claude-code)가 설치되어 있어야 합니다.
2. 스킬 파일(`skills/cocos-mcp-setup/SKILL.md`)을 Claude가 인식할 수 있도록 설정해야 합니다.

### 스킬 설치

이 저장소의 `skills/` 디렉토리를 프로젝트의 `.claude/skills/`에 복사하거나, 전역 스킬로 등록합니다.

```bash
# 방법 1: 프로젝트 로컬 스킬로 복사
mkdir -p .claude/skills
cp -r <cocos-mcp-server 경로>/skills/cocos-mcp-setup .claude/skills/

# 방법 2: 전역 스킬로 복사 (모든 프로젝트에서 사용)
mkdir -p ~/.claude/skills
cp -r <cocos-mcp-server 경로>/skills/cocos-mcp-setup ~/.claude/skills/
```

### 실행

Cocos Creator 프로젝트 루트에서 Claude CLI를 열고 다음을 입력합니다.

```
/cocos-mcp-setup
```

스킬이 자동으로 다음 작업을 수행합니다:

1. 현재 디렉토리가 Cocos Creator 프로젝트인지 확인 (`assets/` 폴더 존재 여부)
2. `extensions/cocos-mcp-server/`에 플러그인을 git clone (이미 있으면 git pull로 업데이트)
3. `npm install --production` 으로 의존성 설치

### 설치 후 설정

```bash
# 1. Cocos Creator 에디터에서 프로젝트를 열고
#    확장 > Cocos MCP Server 패널에서 서버를 시작합니다.

# 2. MCP 헬스체크 (포트는 에디터에서 설정한 번호)
curl http://127.0.0.1:3000/mcp

# 3. Claude CLI에 MCP 서버 등록
claude mcp add --transport http --scope local cocos-creator http://127.0.0.1:3000/mcp
```

---

## 수동 설치

### 1. 플러그인 복사

`cocos-mcp-server` 폴더 전체를 Cocos Creator 프로젝트의 `extensions` 디렉토리에 복사합니다. 에디터의 확장 관리자에서 직접 임포트할 수도 있습니다.

```
프로젝트/
├── assets/
├── extensions/
│   └── cocos-mcp-server/          <- 여기에 배치
│       ├── source/
│       ├── dist/
│       ├── package.json
│       └── ...
├── settings/
└── ...
```

### 2. 의존성 설치

```bash
cd extensions/cocos-mcp-server
npm install
```

### 3. 빌드

```bash
npm run build
```

### 4. 플러그인 활성화

1. Cocos Creator를 재시작하거나 확장을 새로고침합니다.
2. 확장 메뉴에 플러그인이 나타납니다.
3. `확장 > Cocos MCP Server`를 클릭해 제어판을 엽니다.

---

## MCP 클라이언트 설정

### Claude CLI

```bash
claude mcp add --transport http cocos-creator http://127.0.0.1:3000/mcp
```

### Claude 데스크톱

```json
{
  "mcpServers": {
    "cocos-creator": {
      "type": "http",
      "url": "http://127.0.0.1:3000/mcp"
    }
  }
}
```

### Cursor / VS Code 계열

```json
{
  "mcpServers": {
    "cocos-creator": {
      "url": "http://localhost:3000/mcp"
    }
  }
}
```

> 포트 번호는 에디터 패널에서 설정한 값으로 변경하세요. 기본값은 3000입니다.

---

## 사용법

### 서버 시작

1. `확장 > Cocos MCP Server`에서 MCP 서버 패널을 엽니다.
2. 설정 항목:
   - **포트**: HTTP 서버 포트 (기본: 3000)
   - **자동 시작**: 에디터 시작 시 서버 자동 실행
   - **디버그 로그**: 개발 디버깅을 위한 상세 로그
   - **최대 연결 수**: 최대 동시 연결 수
3. "서버 시작" 버튼을 클릭합니다.

### AI 어시스턴트 연결

서버는 `http://localhost:3000/mcp` (또는 설정한 포트)에서 HTTP 엔드포인트를 제공합니다. AI 어시스턴트가 MCP 프로토콜로 연결하여 모든 도구에 접근할 수 있습니다.

---

## 도구 체계

모든 도구는 "카테고리_동작" 형태로 명명되며, 통일된 Schema 파라미터와 액션 코드를 사용합니다. 50개 핵심 도구가 에디터 전체 기능을 커버합니다.

### 호출 예시

```json
{
  "tool": "node_lifecycle",
  "arguments": {
    "action": "create",
    "name": "MyNode",
    "parentUuid": "parent-uuid",
    "nodeType": "2DNode"
  }
}
```

---

## 기능 목록

### 씬 조작 (scene_*)
- **scene_management**: 씬 관리 - 현재 씬 조회, 열기/저장/생성/닫기, 씬 목록 조회
- **scene_hierarchy**: 씬 계층 구조 - 전체 씬 구조 조회, 컴포넌트 정보 포함
- **scene_execution_control**: 실행 제어 - 컴포넌트 메서드 실행, 씬 스크립트 실행, 프리팹 동기화

### 노드 조작 (node_*)
- **node_query**: 노드 조회 - 이름/패턴으로 노드 검색, 노드 정보 조회, 2D/3D 타입 감지
- **node_lifecycle**: 노드 생명주기 - 생성/삭제, 컴포넌트 사전 장착, 프리팹 인스턴스화
- **node_transform**: 노드 변환 - 이름, 위치, 회전, 스케일, 가시성 등 속성 수정
- **node_hierarchy**: 노드 계층 - 이동, 복사, 붙여넣기, 계층 구조 조작
- **node_clipboard**: 노드 클립보드 - 복사/붙여넣기/잘라내기
- **node_property_management**: 속성 관리 - 노드, 컴포넌트, 변환 속성 초기화

### 컴포넌트 조작 (component_*)
- **component_manage**: 컴포넌트 관리 - 엔진 컴포넌트 추가/삭제 (cc.Sprite, cc.Button 등)
- **component_script**: 스크립트 컴포넌트 - 커스텀 스크립트 장착/제거
- **component_query**: 컴포넌트 조회 - 컴포넌트 목록, 상세 정보, 사용 가능한 타입
- **set_component_property**: 속성 설정 - 단일/다중 컴포넌트 속성값 설정

### 프리팹 조작 (prefab_*)
- **prefab_browse**: 프리팹 탐색 - 목록 조회, 정보 확인, 파일 검증
- **prefab_lifecycle**: 프리팹 생명주기 - 노드에서 프리팹 생성, 삭제
- **prefab_instance**: 프리팹 인스턴스 - 씬에 인스턴스화, 링크 해제, 변경 적용, 원본 복원
- **prefab_edit**: 프리팹 편집 - 편집 모드 진입/종료, 저장, 변경 테스트

### 프로젝트 제어 (project_*)
- **project_manage**: 프로젝트 관리 - 실행, 빌드, 프로젝트 정보 및 설정 조회
- **project_build_system**: 빌드 시스템 - 빌드 패널 제어, 빌드 상태 확인, 프리뷰 서버 관리

### 디버그 도구 (debug_*)
- **debug_console**: 콘솔 관리 - 콘솔 로그 조회/삭제, 필터링 및 제한
- **debug_logs**: 로그 분석 - 프로젝트 로그 파일 읽기/검색/분석, 패턴 매칭
- **debug_system**: 시스템 디버그 - 에디터 정보, 성능 통계, 환경 정보

### 리소스 관리 (asset_*)
- **asset_manage**: 리소스 관리 - 일괄 임포트/삭제, 메타데이터 저장, URL 생성
- **asset_analyze**: 리소스 분석 - 의존 관계 조회, 리소스 매니페스트 내보내기
- **asset_system**: 리소스 시스템 - 리소스 새로고침, DB 상태 조회
- **asset_query**: 리소스 조회 - 타입/폴더별 조회, 상세 정보
- **asset_operations**: 리소스 조작 - 생성/복사/이동/삭제/저장/재임포트

### 환경설정 (preferences_*)
- **preferences_manage**: 환경설정 관리 - 에디터 환경설정 조회/설정
- **preferences_global**: 전역 설정 - 전역 구성 및 시스템 설정 관리

### 서버 및 브로드캐스트 (server_* / broadcast_*)
- **server_info**: 서버 정보 - 서버 상태, 프로젝트 정보, 환경 정보
- **broadcast_message**: 메시지 브로드캐스트 - 커스텀 메시지 수신 및 전송

### 참조 이미지 (referenceImage_*)
- **reference_image_manage**: 참조 이미지 관리 - 씬 뷰에서 참조 이미지 추가/삭제/관리
- **reference_image_view**: 참조 이미지 뷰 - 표시 및 편집 제어

### 씬 뷰 (sceneView_*)
- **scene_view_control**: 씬 뷰 제어 - Gizmo 도구, 좌표계, 뷰 모드 제어
- **scene_view_tools**: 씬 뷰 도구 - 각종 도구 및 옵션 관리

### 검증 도구 (validation_*)
- **validation_scene**: 씬 검증 - 씬 무결성 확인, 누락된 리소스 검사
- **validation_asset**: 리소스 검증 - 리소스 참조 및 무결성 검사

### 도구 관리
- **도구 설정 시스템**: 도구 선택적 활성화/비활성화, 다중 설정 지원
- **설정 영속화**: 자동 저장 및 로드
- **설정 가져오기/내보내기**: 도구 설정 파일 교환
- **실시간 상태 관리**: 도구 상태 실시간 업데이트 및 동기화

---

## 핵심 장점

- **액션 코드 통일**: 모든 도구가 "카테고리_동작" 명명, 파라미터 Schema 통일
- **높은 재사용성**: 50개 핵심 도구로 에디터 기능 99% 커버
- **AI 친화적**: 파라미터 명확, 문서 완비, 호출 간편
- **성능 최적화**: 토큰 소비 50% 절감, AI 호출 성공률 향상
- **완벽 호환**: Cocos Creator 공식 API와 100% 정렬

---

## 업데이트 로그

### v1.5.0 - 2025년 7월 29일 (Cocos 스토어 업데이트 완료, GitHub 버전은 다음 버전에 동기화)

Cocos Store: https://store.cocos.com/app/detail/7941

- **도구 정비 및 리팩토링**: 기존 150+ 도구를 50개 고재사용/고커버리지 핵심 도구로 압축, 모든 불필요한 코드 제거
- **액션 코드 통일**: 모든 도구에 "액션 코드+파라미터" 패턴 적용, AI 호출 흐름 대폭 간소화, 토큰 50% 절감
- **프리팹 기능 전면 업그레이드**: 프리팹 생성, 인스턴스화, 동기화, 참조 등 모든 핵심 기능 완전 수정 및 보완
- **이벤트 바인딩 및 기존 기능 보완**: 이벤트 바인딩, 노드/컴포넌트/리소스 등 기존 기능 구현 완료
- **인터페이스 최적화**: 모든 인터페이스 파라미터 명확화, 문서 보완
- **플러그인 패널 최적화**: 패널 UI 간소화, 조작 직관성 향상
- **성능 및 호환성 개선**: 전체 아키텍처 효율화, Cocos Creator 3.8.6 이상 모든 버전 호환

### v1.4.0 - 2025년 7월 26일 (현재 GitHub 버전)

#### 주요 기능 수정
- **프리팹 생성 기능 완전 수정**: 프리팹 생성 시 컴포넌트/노드/리소스 타입 참조 소실 문제 완전 해결
- **올바른 참조 처리**: 수동 생성과 완전히 동일한 참조 포맷 구현
  - **내부 참조**: 프리팹 내부의 노드/컴포넌트 참조를 `{"__id__": x}` 포맷으로 정확히 변환
  - **외부 참조**: 외부 노드/컴포넌트 참조를 `null`로 올바르게 설정
  - **리소스 참조**: 프리팹, 텍스처, 스프라이트 프레임 등 리소스 참조 UUID 포맷 완전 보존
- **컴포넌트/스크립트 제거 API 표준화**: 제거 시 컴포넌트의 cid(type 필드)를 전달해야 함. 스크립트명이나 클래스명 사용 불가. getComponents로 type 필드(cid)를 먼저 조회 후 removeComponent에 전달

#### 핵심 개선
- **인덱스 순서 최적화**: 프리팹 객체 생성 순서를 Cocos Creator 표준 포맷에 맞게 조정
- **컴포넌트 타입 지원**: cc.으로 시작하는 모든 컴포넌트 타입 참조 감지 확장 (Label, Button, Sprite 등)
- **UUID 매핑 메커니즘**: 내부 UUID-인덱스 매핑 시스템 보완
- **속성 포맷 표준화**: 컴포넌트 속성 순서 및 포맷 수정, 엔진 파싱 오류 해소

#### 버그 수정
- `Cannot read properties of undefined (reading '_name')` 오류 해결
- `placeHolder.initDefault is not a function` 오류 해결
- `_objFlags` 등 핵심 속성이 컴포넌트 데이터에 의해 덮어쓰이는 문제 수정
- 모든 타입의 참조가 올바르게 저장/로드되도록 수정

### v1.3.0 - 2025년 7월 25일

- 통합 도구 관리 패널 추가
- 도구 설정 시스템 (선택적 활성화/비활성화, 영속화)
- 동적 도구 로딩 (158개 도구 자동 발견)
- Vue 3 Composition API 적용
- IPC 통신 문제 수정

### v1.2.0 - 이전 버전

- 초기 릴리즈, 151개 도구
- 기본 MCP 서버 기능
- 씬, 노드, 컴포넌트, 프리팹 조작
- 프로젝트 제어 및 디버그 도구

---

## 개발

### 프로젝트 구조

```
cocos-mcp-server/
├── source/                    # TypeScript 소스
│   ├── main.ts               # 플러그인 엔트리
│   ├── mcp-server.ts         # MCP 서버 구현
│   ├── settings.ts           # 설정 관리
│   ├── types/                # TypeScript 타입 정의
│   ├── tools/                # 도구 구현
│   │   ├── scene-tools.ts
│   │   ├── node-tools.ts
│   │   ├── component-tools.ts
│   │   ├── prefab-tools.ts
│   │   ├── project-tools.ts
│   │   ├── debug-tools.ts
│   │   ├── preferences-tools.ts
│   │   ├── server-tools.ts
│   │   ├── broadcast-tools.ts
│   │   ├── scene-view-tools.ts
│   │   ├── reference-image-tools.ts
│   │   └── asset-advanced-tools.ts
│   ├── panels/               # UI 패널 구현
│   └── test/                 # 테스트 파일
├── skills/                    # Claude CLI 스킬
│   └── cocos-mcp-setup/      # MCP 설치 스킬
├── dist/                     # 컴파일된 JavaScript 출력
├── static/                   # 정적 리소스 (아이콘 등)
├── i18n/                     # 국제화 파일
├── package.json              # 플러그인 설정
└── tsconfig.json             # TypeScript 설정
```

### 소스에서 빌드

```bash
# 의존성 설치
npm install

# 개발 빌드 (감시 모드)
npm run watch

# 프로덕션 빌드
npm run build
```

### 새 도구 추가

1. `source/tools/`에 새 도구 클래스 생성
2. `ToolExecutor` 인터페이스 구현
3. `mcp-server.ts` 초기화에 도구 추가
4. 도구가 자동으로 MCP 프로토콜을 통해 노출됨

---

## 문제 해결

### 자주 묻는 질문

1. **서버가 시작되지 않음**: 포트 가용성 및 방화벽 설정 확인
2. **도구가 동작하지 않음**: 씬이 로드되어 있고 UUID가 유효한지 확인
3. **빌드 오류**: `npm run build`로 TypeScript 오류 확인
4. **연결 문제**: HTTP URL과 서버 상태 확인

### 디버그 모드

플러그인 패널에서 디버그 로그를 활성화하면 상세한 동작 로그를 확인할 수 있습니다.

---

## 시스템 요구사항

- Cocos Creator 3.5.0 이상 (3.8.6+ 권장)
- Node.js (Cocos Creator에 내장)
- TypeScript (개발 의존성으로 설치)

## 라이선스

본 플러그인은 Cocos Creator 프로젝트용이며, 소스 코드가 함께 패키징됩니다. 학습 및 교류 목적으로 사용할 수 있습니다. 암호화되어 있지 않으며, 직접 2차 개발/최적화가 가능합니다. 단, 본 프로젝트 코드 또는 파생 코드의 상업적 이용 및 재판매는 금지됩니다. 상업적 사용이 필요한 경우 저자에게 연락해주세요.

## 연락처 / 커뮤니티

<img alt="QR코드" src="https://github.com/user-attachments/assets/a276682c-4586-480c-90e5-6db132e89e0f" width="400" height="400" />
