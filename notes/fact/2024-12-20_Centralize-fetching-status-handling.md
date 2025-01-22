---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Centralize fetching status handling

## Small apps vs. Large apps

* In smaller apps
    * use `isFetching` from `useQuery` return object
    * Reminder: `isLoading` is `isFetching` plus no cached data

* In a larger app
    * Loading spinner whenever any query is `isFetching`
    * `useIsFetching` tells us this!
    * No need for `isFetching` on every custom hook / `useQuery` call


## Coding

```bash
~/D/g/s/t/u/b/client > tree -I 'node_modules'
.
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
│   │   │   ├── App.tsx  # use Loading component
│   │   │   ├── Home.tsx
│   │   │   ├── hooks
│   │   │   │   └── useCustomToast.ts
│   │   │   ├── Loading.tsx # content of Loading component, it's under app/ which means it's a indicator for fetching status of ALL queries
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

```tsx
// src/components/app/App.tsx 

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
import { QueryClientProvider } from "@tanstack/react-query";
import { queryClient } from "@/react-query/queryClient";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

export function App() {
  return (
    <ChakraProvider theme={theme}>
      <QueryClientProvider client={queryClient}>
        <AuthContextProvider>
          <Loading />  {/* Loading component is used here */}
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
        <ReactQueryDevtools />
      </QueryClientProvider>
    </ChakraProvider>
  );
}
```

We want to use `useIsFetching` to tell Loading component if any query is fetching.

```tsx
// src/components/app/Loading.tsx 

import { Spinner, Text } from "@chakra-ui/react";
import { useIsFetching } from "@tanstack/react-query";  // import

export function Loading() {
  const isFetching = useIsFetching();
                  // ^^^^^^^^^^^^^^^^ it'll return a number to indicate how many queries are fetching
                  //                  when there is no fetching query, it'll return 0 (falsy value)
                      
  const display = isFetching ? "inherit" : "none";

  return (
    <Spinner
      thickness="4px"
      speed="0.65s"
      emptyColor="olive.200"
      color="olive.800"
      role="status"
      position="fixed"
      zIndex="9999"
      top="50%"
      left="50%"
      transform="translate(-50%, -50%)"
      display={display}
    >
      <Text display="none">Loading...</Text>
    </Spinner>
  );
}

```

![loading-spinner.png](../assets/imgs/loading-spinner.png)

