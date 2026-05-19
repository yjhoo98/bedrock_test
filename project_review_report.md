# 코드 리뷰 리포트

## sample_code.py (214줄)

### 스타일 검사
PEP 8 기준으로 분석한 스타일 위반 사항은 다음과 같습니다.

**라인 길이 초과 (최대 79자)**
- [119] 라인 길이 초과: `if matrix_data[vertex][next_vertex] == 1 and not visited[next_vertex]:` 가 79자를 초과합니다. 조건을 분리하거나 백슬래시/괄호로 줄바꿈 처리가 필요합니다.
- [155] 라인 길이 초과: `undirected_adjacency_list = create_undirected_list(vertex_count_sample, edge_list_sample)` 가 79자를 초과합니다.
- [159] 라인 길이 초과: `directed_adjacency_list = create_directed_list(vertex_count_sample, edge_list_sample)` 가 79자를 초과합니다.

**모듈 수준 실행 코드 구조 위반**
- [128~] `if __name__ == "__main__":` 블록 누락: 전역 스코프에서 직접 실행되는 변수 선언, 함수 호출, print 문 등은 모두 `if __name__ == "__main__":` 블록 안에 위치시켜야 합니다.

**Docstring 누락**
- [4], [13], [21], [31], [39], [46], [53], [60], [71], [80], [91], [103] 모든 public 함수에 목적, 매개변수, 반환값을 설명하는 docstring이 없습니다.

**변수/매개변수명 명확성 부족**
- [60] `dfs_stack(graph_data, start)`: `graph_data`는 실제로 인접 리스트이므로 `adjacency_list`처럼 더 구체적인 이름이 권장됩니다.
- [103] `bfs_matrix(matrix_data, start)`: `matrix_data` → `adjacency_matrix` 등 더 명확한 이름이 권장됩니다.

### 보안 검사
제공된 코드를 SQL Injection, XSS, 하드코딩된 비밀번호 관점에서 분석한 결과는 다음과 같습니다.

---

## 보안 취약점 분석 결과

### ✅ SQL Injection
- **발견되지 않음**: 코드 내에 데이터베이스 쿼리 관련 코드가 전혀 없습니다.

---

### ✅ XSS (Cross-Site Scripting)
- **발견되지 않음**: 웹 출력이나 HTML 렌더링 관련 코드가 없습니다.

---

### ✅ 하드코딩된 비밀번호
- **발견되지 않음**: 비밀번호, API 키, 토큰 등 민감한 자격증명이 없습니다.

---

## 기타 보안/품질 이슈

### ⚠️ 1. 입력값 검증 부재
- **위치**: `create_undirected_matrix`, `create_directed_matrix`, `dfs_stack`, `bfs_list`, `bfs_matrix` 등 전반
- **유형**: 입력값 미검증 (Input Validation)
- **심각도**: 낮음 (Low)
- **설명**: edge_list의 vertex 인덱스가 vertex_count 범위를 초과할 경우 `IndexError` 또는 의도치 않은 동작이 발생할 수 있습니다.
- **수정 제안**:
```python
def create_undirected_matrix(vertex_count, edge_list):
    if vertex_count <= 0:
        raise ValueError("vertex_count must be positive")
    for v, w in edge_list:
        if not (0 <= v < vertex_count and 0 <= w < vertex_count):
            raise ValueError(f"Invalid edge ({v}, {w}) for vertex_count {vertex_count}")
    ...
```

---

### ⚠️ 2. 재귀 깊이 제한 없음
- **위치**: `dfs_recursive`, `dfs_dict` 함수
- **유형**: 서비스 거부 (DoS) 가능성
- **심각도**: 낮음 (Low)
- **설명**: 그래프의 노드 수가 매우 많거나 깊은 경로가 있을 경우, Python의 기본 재귀 한도(1000)를 초과해 `RecursionError`가 발생할 수 있습니다.
- **수정 제안**: 재귀 대신 스택 기반 반복 구현 사용 또는 `sys.setrecursionlimit` 설정.

---

## 종합 의견

해당 코드는 **그래프 자료구조 및 탐색 알고리즘(DFS/BFS)**을 구현한 순수 알고리즘 코드로, 웹 입출력, DB 연동, 인증 처리 등이 없어 SQL Injection, XSS, 하드코딩된 비밀번호 취약점은 **존재하지 않습니다**. 다만, 실제 서비스에 통합될 경우 입력값 검증과 재귀 깊이 제한을 추가하는 것을 권장합니다.

---

