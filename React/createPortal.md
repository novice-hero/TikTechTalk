# createPortal

createPortal을 사용하면 일부 자식을 DOM의 다른 부분으로 렌더링할 수 있습니다.

portal은 DOM 노드의 물리적 배치만 변경합니다. 다른 모든 면에서 portal에 렌더링하는 JSX는 이를 렌더링하는 React 컴포넌트의 자식 노드 역할을 합니다. 예를 들어, 자식은 부모 트리가 제공하는 컨텍스트에 접근할 수 있으며, 이벤트는 React 트리에 따라 자식에서 부모로 버블링 됩니다.

### createPortal(children, domNode, key?)

- `children`: `<div />`나 `<AnyComponent />`와 같은 JSX, `Fragment` 같은 리액트로 렌더링할 수 있는 모든 것이 들어갈 수 있습니다.
- `domNode`: `document.getElementById()`가 반환하는 것과 같은 일부 DOM 노드. 노드는 이미 존재하고 있어야 합니다. 업데이트 중에 다른 DOM 노드를 전달하면 포털 콘텐츠가 다시 생성됩니다.
- `key`: optional, portal의 키로 사용할 고유 문자열 또는 숫자가 들어갈 수 있습니다.
- `return`: JSX에 포함되거나 React 컴포넌트에서 반환될 수 있는 React 노드를 반환합니다. React가 렌더링 출력물에서 이를 발견하면, 제공된 `children`을 제공된 `domNode` 안에 배치합니다.

### 주의사항

포털의 이벤트는 DOM 트리가 아닌 React 트리에 따라 전파됩니다. 

예를 들어, 포털 내부를 클릭했을 때 포털이 `<div onClick>`으로 감싸져 있으면 해당 `onClick` 핸들러 이벤트가 실행됩니다. 이로 인해 문제가 발생한다면, 포털 내부에서 이벤트 전파를 중지하거나 포털 자체를 React 트리에서 위로 옮겨야합니다.

<br>

## 사용하는 이유

- DOM tree 상의 부모-자식 컴포넌트 간의 제약에서 Portal을 통해 독립적인 구조와 부모-자식 관계를 동시에 유지할 수 있습니다.
- 모달 같은 오버레이 되는 컴포넌트가 부모 컴포넌트 내부에서 렌더링 된다는 것은 의미적으로, 구조적으로 좋지 않습니다.
    - 오버레이는 모든 컴포넌트들의 제일 위에 존재하는데, 다른 컴포넌트 내부에서 생성된다면 좋은 구조가 아닙니다.
    - 따라서 Portal을 사용해 독립적으로 작동시키는 편이 좀 더 명확합니다.

<br>

## 사용법

```html
// index.html

<!DOCTYPE html>
<html lang="en">

<head>
  ...
</head>

<body>
  <div id="overlay-root"></div> // 모달이 생기는 부분
  <div id="root"></div>
  <script type="module" src="/src/main.tsx"></script>
</body>

</html>
```

```tsx
// 모달 컴포넌트
const Modal = ({
  children,
  styles,
  backdropCallback,
  className,
}: {
  children?: Props['children'];
  styles?: IModalContainer;
  backdropCallback?: (e: React.MouseEvent<HTMLDivElement>) => void;
  handleCloseBtn?: () => void;
  className?: string[];
}) => {
  return ReactDOM.createPortal(
    <ModalWrapper className={className ? className[0] : ''}>
      <Backdrop onClick={backdropCallback} />
      <ModalContainer
        onClick={(e: React.MouseEvent<HTMLElement>) => {
          e.stopPropagation();
        }}
        className={className ? className[1] : ''}
        {...styles}
      >
        {children}
      </ModalContainer>
    </ModalWrapper>,
    document.getElementById('overlay-root')! // id를 찾아서 넣어줌
  );
};
```

<br>

## 참고

https://react.dev/reference/react-dom/createPortal