# 1. StoryBook이란?

## 공식문서 표현

- "스토리북(Storybook)은 개발 모드에서 앱과 함께 실행됩니다. 스토리북은 비즈니스 로직과 맥락(context)으로부터 분리된 UI 컴포넌트를 만들 수 있도록 도와줍니다."

## 내 생각

- 컴포넌트 단위의 UI 문서화가 주된 핵심 기능.
- 아직 제대로 사용해본 건 아니지만 테스트는 문서화가 되며 테스트가 진행되는 느낌을 받음.

## Story는?

- 스토리는 컴포넌트를 문서화 하는 단위로, 실행 가능한 컴포넌트 단위

# 2. StoryBook 파보기

## main.ts

- 스토리북의 구성 파일
```tsx
import type { StorybookConfig } from '@storybook/react-vite';

const config: StorybookConfig = {

  stories: ['../src/**/*.mdx', '../src/**/*.stories.@(js|jsx|mjs|ts|tsx)'],

  addons: [
    '@storybook/addon-links', // 스토리 간 링크 제공
    '@storybook/addon-onboarding', // 온보딩 과정
    '@storybook/addon-essentials', // controls, actions, docs 등의 기본 기능들을 한 번에 제공
    '@chromatic-com/storybook', // 테스트 및 리뷰 배포 및 자동화
    '@storybook/addon-interactions', // 컴포넌트 이벤트 혹은 행동 테스트
    '@storybook/addon-mdx-gfm', // MDX 문법과 GFM 지원
    '@storybook/addon-a11y', // 접근성 검사를 통해 UI 접근성 표준 준수 여부 확인(lighthouse 같은 거)
  ],

  framework: {
    name: '@storybook/react-vite',
    options: {},
  },

  docs: {},

  typescript: {
    reactDocgen: 'react-docgen-typescript',
  },
};
export default config;
```
### main.ts 내부 구성
stories
    - stories 파일 경로 설정
- addon
    - 원하는 storybook의 플러그인 기능을 추가하여 사용 가능
    - ex) Chromatic : 시각적 테스트와 리뷰를 자동화 및 배포할 수 있게  해주는 플러그인
- frameworK
    - storybook이 사용할 프레임워크와 빌드 도구
    - 해당 프로젝트가 vite를 사용했으므로 vite 사용
- docs
    - 문서화 관련 설정 항목
- typescript
    - typescript 관련 설정
    - 객체 안 요소는 React 컴포넌트의 prop 타입 및 문서화 설정

<br>

## preview.ts
- 스토리북의 미리보기에 대한 전역 설정

```tsx
import type { Preview } from '@storybook/react';
import { ThemeProvider } from 'styled-components';
import { defaultTheme } from '../src/style/theme';
import { GlobalStyle } from '../src/style/global';

import React from 'react';

const preview: Preview = {
  parameters: {
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/i,
      },
    },
  },

  tags: ['autodocs'],
  decorators: [
    (Story) => {
      return (
        <ThemeProvider theme={defaultTheme}>
          <GlobalStyle />
          <Story />
        </ThemeProvider>
      );
    },
  ],
};

export default preview;
```
### preview.ts 내부 구성
- parameters
    - 스토리북의 여러 파라미터 설정
- controls
    - 스토리북 컨트롤 패널이 특정 prop들을 자동으로 감지
    - ex ) prop 이름에 "background" 혹은 "color"가 포함되어 있다면 색상 컨트롤, "Date"로 끝나면 날짜 컨트롤
- tags
    - autodocs를 통해 스토리북의 자동 문서화 기능 활성화
- decorators
    - 모든 스토리에 공통 적용할 데코레이터 정의
    - 모든 스토리에 theme 적용

## 적용한 Modal 스토리 예시

```tsx
import type { Meta, StoryObj } from '@storybook/react';
import Modal from './Modal';
import { fn } from '@storybook/test';
const meta = {
  title: 'Component/Common/Modal',
  component: Modal,
  tags: ['autodocs'],
  parameters: {
    layout: 'centered',
  },
  argTypes: {
    isOpen: { control: 'boolean' },
    onClose: { action: 'closed' },
    children: { control: 'text' },
  },
  args: {
    onClose: fn(),
  },
} satisfies Meta<typeof Modal>;

export default meta;
type Story = StoryObj<typeof meta>;

export const Default: Story = {
  args: {
    isOpen: true,
    children: 'modal test',
  },
};
```
- meta 
    - 메타 데이터 설정 후 타입으로 Deafault에 이식
- title 
    - 스토리북에서 사이드바의 스토리 폴더 구조 정의
- component
    - 스토리할 컴포넌트 지정
- parameters
    - 스토리 추가 설정으로, 렌더링 되는 컴포넌트의 레이아웃 설정

- argTypes
    - 해당 컴포넌트에 대한 3개의 props의 타입 정의
    - 사실상 interface props와 비슷
    - onClose의 경우 스토리북에게 이벤트 함수임을 알려주고, 해당 함수 호출 시 'closed'라는 액션 기록 설정
- args
    - 스토리에 사용할 기본으로 적용할 prop 값 지정
    - onClose에 전달할 기본값  결정
    - fn()은 실제 로직 없이 호출 시 스토리북의 Actinos 패널에 호출 기록을 남기는 모의 함수 제공

# 3. 느끼고 배운 점

- 스토리에서 parameter 설정 시에 컴포넌트의 기존 레이아웃과 충돌될 가능성이 있기 때문에 유의해야 함.

- 문서화 한다는 개념이 어떤 개념인 지 생소했는데 배포되는 걸 보고 단 번에 이해 했음. 한마디로 누구나 보기 쉽게 정리된 것.

- addon-ally 같은 경우 lighthouse 처럼 브라우저 환경에서의 접근성 위반 적발 기능을 제공. <br>
addon을 통해 정말 다양한 걸 시도해볼 수 있겠단 생각이 듬.

- 제일 신기했던 건 공식문서에 나와 있는 decorator를 활용하여 상태를 전달한 것
    ```tsx
    export const WithPinnedTasks = {
    decorators: [
        (story) => {
        const pinnedtasks = [
            ...MockedState.tasks.slice(0, 5),
            { id: '6', title: 'Task 6 (pinned)', state: 'TASK_PINNED' },
        ];

        return (
            <Mockstore
            taskboxState={{
                ...MockedState,
                tasks: pinnedtasks,
            }}
            >
            {story()}
            </Mockstore>
        );
        },
    ],
    };
    ```
    - 보면 decorators를 사용해 해당 스토리에 상태를 변경
    - 즉, decorators는 스타일 뿐 아니라 스토리를 감싸 필요한 상태나 컨텍스트를 설정하는 데에도 활용 가능

- 직접 작업에 들어가기 전에 기본 공용 컴포넌트의 경우 배포 해놓고 디자니어와 실시간으로 소통할 수도 있겠구나 싶었음.

 - 간단한 컴포넌트 단위에 대한 이해가 되지만, 컴포넌트들이 묶여 있는 페이지에 대한 건 복잡할 수 있겠다란 생각이 듬.
