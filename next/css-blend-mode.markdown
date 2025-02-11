# css mix-blend-mode

- 흰색 배경이 주가 되는 이미지에 회색 배경을 입히는 작업 필요
- div를 overlay 시킬지 검토하던 중에 mix-blend-mode에 대해 알게되어 정리.

## css `mix-blend-mode`

두 개 이상의 요소가 겹칠때, 각각의 색상을 어떻게 섞어서 보일지 결정

- 부모에 background-color를 설정하고, 이미지에 `mix-blend-mode`를 설정해야함.

## option

- normal
  - 섞이지 않음
- multiply
  - 색을 곱해서 어둡게 보이게 함.
  - 흰색은 영항을 주지 않고, 검은색은 더 어둡게 만듬
  - 텍스트나 이미지가 배경에 자연스럽게 섞이게 할때
- screen
  - 반대로 곱해서 밝게 보이게함
  - 검은색은 영향 주지 않고, 흰색은 더 밝게 만듦.
  - 빛 효과나 글로우 효과 만들때
- overlay
  - multiply나 screen조합
  - 밝은 곳은 더 밝아지고 어두운 곳은 더 어두워짐
  - 강한대비
- difference
  - 두 색상을 빼서 극적인 색 변화
  - 강렬한 대비
