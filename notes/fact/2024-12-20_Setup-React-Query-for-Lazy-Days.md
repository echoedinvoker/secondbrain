---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Setup React Query for Lazy Days

For big project, we usually create the instance of `QueryClient` in a separate file...

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
│   │   └── index.ts
│   ├── components
│   │   ├── app
│   │   │   ├── App.tsx  # 2. provide the instance of QueryClient by QueryClientProvider and setup ReactQueryDevtools
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
│   │   │   │   └── useTreatments.ts
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
│   │   ├── constants.js
│   │   └── queryClient.ts  # 1. create instance of QueryClient
│   ├── setupTests.js
│   ├── test-utils
│   │   └── index.tsx
│   ├── theme
│   │   └── index.js
│   └── types
│       └── images.d.ts
├── tsconfig.json # plus. setup alias for paths such as "@/"
└── vite.config.js
```

```ts
// client/src/react-query/queryClient.ts 

import { QueryClient } from "@tanstack/react-query";

export const queryClient = new QueryClient();

```

```tsx
~/D/g/s/t/u/base-lazy-days > cat client/src/components/app/App.tsx 
import { ChakraProvider } from "@chakra-ui/react";
import { BrowserRouter, Route, Routes } from "react-router-dom";

import { Home } from "./Home";
import { Loading } from "./Loading";
import { Navbar } from "./Navbar";
import { ToastContainer } from "./toast";

import { AuthContextProvider } from "@/auth/AuthContext";
import { Calendar } from "@/components/appointments/Calendar";
import { AllStaff } from "@/components/staff/AllStaff";
import { Treatments } from "@/components/treatments/Treatments";
import { Signin } from "@/components/user/Signin";
import { UserProfile } from "@/components/user/UserProfile";
import { theme } from "@/theme";
import { QueryClientProvider } from "@tanstack/react-query"; // import
import { queryClient } from "@/react-query/queryClient"; // import instance of QueryClient
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";  // import dev tools of react-query

export function App() {
  return (
    <ChakraProvider theme={theme}> {/* react-query uses chakra-ui for present error message, so it should be wrapped by ChakraProvider */}
      <QueryClientProvider client={queryClient}>  {/* insert query client provider here */}
        <AuthContextProvider> {/* Auth context uses react-query to refresh user data, so it should be wrapped by QueryClientProvider */}
          <Loading />
          <BrowserRouter>
            <Navbar />
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/Staff" element={<AllStaff />} />
              <Route path="/Calendar" element={<Calendar />} />
              <Route path="/Treatments" element={<Treatments />} />
              <Route path="/signin" element={<Signin />} />
              <Route path="/user/:id" element={<UserProfile />} />
            </Routes>
          </BrowserRouter>
          <ToastContainer />
        </AuthContextProvider>
        <ReactQueryDevtools />  {/* setup react query dev tools */}
      </QueryClientProvider>
    </ChakraProvider>
  );
}
```

```json
~/D/g/s/t/u/base-lazy-days > cat client/tsconfig.json 
{
  "compilerOptions": {
    "noImplicitAny": true,
    "removeComments": true,
    "preserveConstEnums": true,
    "sourceMap": true,
    "target": "es5",
    "module": "esnext",
    "moduleResolution": "node",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],  <------------------- setup alias for paths
      "@shared/*": ["../shared/*"]  <---- setup alias for shared folder
    },
    "esModuleInterop": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "strictNullChecks": false,
    "jsx": "react-jsx",
    "typeRoots": [
      "node_modules/@types",
      "node_modules",
      "node_modules/@testing-library",
      "node_modules/@testing-library/jest-dom"
    ],
    "types": ["node", "vitest/globals", "jest-dom"],
    "lib": ["dom", "dom.iterable", "esnext", "es2018"],
    "allowJs": true,
    "skipLibCheck": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "downlevelIteration": true,
    "strict": true
  },
  "include": ["src/**/*.ts", "src/**/*.tsx", "node_modules/@types"]
}


```
