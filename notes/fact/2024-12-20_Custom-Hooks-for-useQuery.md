---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Custom Hooks for useQuery

## Reason for wrapping useQuery in a custom hook

In larger app, making custom hook for each type of data has many benefits:

- Can access from multiple components
- No risk of mixing up keys (we also manage keys in one place)
- Query function encapsulated in custom hook, so we don't import it in every component
- Abstracts implementation from display layer
  - Update hook if you change implementation
  - No need to update components

Reference:
https://tanstack.com/query/latest/docs/react/examples/react/custom-hooks


## Coding

```bash
~/D/g/s/t/u/base-lazy-days > tree client -I 'node_modules'
client
├── index.html
├── package.json
├── package-lock.json
├── public
│   └── vite.svg
├── README.md
├── src
│   ├── auth
│   │   ├── AuthContext.tsx
│   │   ├── local-storage.ts
│   │   ├── types.ts
│   │   └── useAuthActions.tsx
│   ├── axiosInstance
│   │   ├── constants.js 
│   │   └── index.ts  # provides instance of axios which already has baseURL set
│   ├── components
│   │   ├── app
│   │   │   ├── App.tsx
│   │   │   ├── Home.tsx
│   │   │   ├── hooks
│   │   │   │   └── useCustomToast.ts
│   │   │   ├── Loading.tsx
│   │   │   ├── Navbar.tsx
│   │   │   └── toast.tsx
│   │   ├── appointments
│   │   │   ├── Appointment.tsx
│   │   │   ├── Calendar.tsx
│   │   │   ├── DateBox.tsx
│   │   │   ├── hooks
│   │   │   │   ├── constants.ts
│   │   │   │   ├── monthYear.ts
│   │   │   │   ├── useAppointments.ts
│   │   │   │   ├── useCancelAppointment.ts
│   │   │   │   └── useReserveAppointment.ts
│   │   │   ├── tests
│   │   │   │   ├── appointmentMutations.test.tsx
│   │   │   │   └── useAppointments.test.tsx
│   │   │   ├── types.ts
│   │   │   └── utils.ts
│   │   ├── common
│   │   │   ├── BackgroundImage.tsx
│   │   │   └── Card.tsx
│   │   ├── staff
│   │   │   ├── AllStaff.tsx
│   │   │   ├── hooks
│   │   │   │   └── useStaff.ts
│   │   │   ├── Staff.tsx
│   │   │   ├── tests
│   │   │   │   ├── AllStaff.error.test.tsx
│   │   │   │   ├── AllStaff.test.tsx
│   │   │   │   └── useStaff.test.tsx
│   │   │   └── utils.ts
│   │   ├── treatments
│   │   │   ├── hooks
│   │   │   │   └── useTreatments.ts  # we want to complete this file
│   │   │   ├── tests
│   │   │   │   └── Treatments.test.tsx
│   │   │   ├── Treatments.tsx
│   │   │   └── Treatment.tsx
│   │   └── user
│   │       ├── hooks
│   │       │   ├── usePatchUser.ts
│   │       │   ├── useUserAppointments.ts
│   │       │   └── useUser.ts
│   │       ├── Signin.tsx
│   │       ├── UserAppointments.tsx
│   │       └── UserProfile.tsx
│   ├── images
│   │   └── splash.jpg
│   ├── main.tsx
│   ├── mocks
│   │   ├── handlers.js
│   │   ├── mockData.js
│   │   └── server.js
│   ├── react-query
│   │   ├── constants.js  # manage query keys here
│   │   └── queryClient.ts
│   ├── setupTests.js
│   ├── test-utils
│   │   └── index.tsx
│   ├── theme
│   │   └── index.js
│   └── types
│       └── images.d.ts
├── tsconfig.json
└── vite.config.js
```

```ts
// client/src/components/treatments/hooks/useTreatments.ts 

import type { Treatment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery } from "@tanstack/react-query";

// write query function here
async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get('/treatments');
  return data;
}

// wrap useQuery in custom hook
export function useTreatments(): Treatment[] {
  const { data } = useQuery({
    queryKey: [queryKeys.treatments],
    //         ^^^^^^^^^^^^^^^^^^^^ here we use a file to manage all query keys
    queryFn: getTreatments
  })

  return data;
}
```

We use a file to manage all query keys as below:

```ts
// client/src/react-query/constants.js 

export const queryKeys = {
  treatments: "treatments",
  appointments: "appointments",
  staff: "staff",
  user: "user",
};
```

Like query client, we setup axios instance in a seperate file:

```ts
// client/src/axiosInstance/index.ts 

import axios, { AxiosRequestConfig } from "axios";

import { baseUrl } from "./constants";

export function getJWTHeader(userToken: string): Record<string, string> {
  return { Authorization: `Bearer ${userToken}` };
}

const config: AxiosRequestConfig = { baseURL: baseUrl };
export const axiosInstance = axios.create(config);

```

Now we already completed the custom hook of treatments. Let's test it:

launch backend server...

```bash
~/D/g/s/t/u/base-lazy-days > cd server
~/D/g/s/t/u/b/server > npm start

> lazy-days-spa-server@1.0.0 start
> node src/index.cjs

Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Creating appointments...
Spa server listening on port 3030!
```

launch frontend dev server...

```bash
~/D/g/s/t/u/base-lazy-days > cd client/
~/D/g/s/t/u/b/client > npm run dev

> base-lazy-days@0.0.0 dev
> vite


  VITE v4.5.0  ready in 183 ms

  ➜  Local:   http://localhost:3000/
  ➜  Network: use --host to expose
  ➜  press h to show help

```

![error-map.png](../assets/imgs/error-map.png)



I met an error when we visit the Treatments page... We'll solve it [[2024-12-20_Fallback|in this topic]]

