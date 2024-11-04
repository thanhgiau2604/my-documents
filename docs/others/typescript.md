---
sidebar_position: 2
---

**Define dynamic type**:
```ts
/* eslint-disable @typescript-eslint/no-unused-vars */

type BaseResponse<T = void> = T extends void
  ? {
      message?: string;
      code?: string;
    }
  : {
      data?: T;
      message?: string;
      code?: string;
    };

export type APIResponse<T = void, K = void> = K extends void
  ? BaseResponse<T>
  : BaseResponse<T> & K;

// example

interface User {
  name: string;
  username: string;
}

// case 1: Response include key Data
const userA: APIResponse<User> = {
  data: {
    username: '',
    name: '',
  },
  message: '',
  code: '',
};

// case 2: Response include key Data with Optional properties
const userB: APIResponse<Partial<User>> = {
  data: {},
  message: '',
  code: '',
};

// case 3: No key data
const userC: APIResponse = {
  message: '',
  code: '',
};

// case 4: No key data, but data is in level 1
const userD: APIResponse<void, User> = {
  message: '',
  code: '',
  username: '',
  name: '',
};

// case 5: No key data, but data is in level 1 with Optional properties
const userE: APIResponse<void, Partial<User>> = {
  message: '',
  code: '',
  username: '',
};

```